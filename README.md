# Terraform Quick Start (Beginner Friendly)

This repo contains a minimal Terraform setup to create an AWS VPC, subnet, security group, and an Ubuntu EC2 instance.

## Prerequisites
- An AWS account with programmatic access (Access Key ID and Secret Access Key).
- Terraform installed (1.9+ recommended). Download: https://developer.hashicorp.com/terraform/downloads
- An existing EC2 key pair in your target region (to SSH into the instance).

## Configure AWS Credentials
Choose one of the following:
- Environment variables:
  ```bash
  export AWS_ACCESS_KEY_ID=YOUR_KEY_ID
  export AWS_SECRET_ACCESS_KEY=YOUR_SECRET
  export AWS_DEFAULT_REGION=us-east-1
  ```
- AWS CLI profile (recommended):
  ```bash
  aws configure
  # then: terraform plan -var 'key_name=YOUR_KEYPAIR' -var 'aws_region=us-east-1'
  ```

## Files in this project
- `main.tf` – provider, VPC, subnet, route table, SG, EC2.
- `variables.tf` – input variables (region, CIDRs, instance type, key name).
- `outputs.tf` – useful outputs (VPC ID, subnet ID, instance IP, etc.).

## First run
From the repo root:
1) Initialize providers and modules
```bash
terraform init -upgrade
```

2) Preview changes (replace the key name with your EC2 key pair)
```bash
terraform plan -var 'key_name=YOUR_KEYPAIR_NAME'
```

3) Apply changes
```bash
terraform apply -auto-approve -var 'key_name=YOUR_KEYPAIR_NAME'
```

4) Get the public IP and SSH
```bash
terraform output -raw instance_public_ip
ssh -i /path/to/YOUR_KEYPAIR.pem ubuntu@$(terraform output -raw instance_public_ip)
```

## Customizing
- Region:
  ```bash
  terraform plan -var 'aws_region=us-east-1'
  ```
- Instance type:
  ```bash
  terraform plan -var 'instance_type=t3.micro'
  ```
- VPC/Subnet CIDR blocks: edit defaults in `variables.tf` or pass via `-var`.

## Cleaning up (avoid charges)
```bash
terraform destroy -auto-approve -var 'key_name=YOUR_KEYPAIR_NAME'
```

## How Terraform works (in a nutshell)
- `terraform init` downloads providers and sets up the working directory.
- `terraform plan` shows what will be created/changed/destroyed.
- `terraform apply` executes the plan and updates the state.
- `terraform destroy` removes managed resources.
- State is stored locally in `terraform.tfstate` by default (treat it like a secret). For teams, consider a remote backend (e.g., S3 + DynamoDB).

## Troubleshooting
- "provider credentials not found": set AWS env vars or `aws configure`.
- "invalid key_name": ensure the key pair exists in the chosen region.
- Network blocked: security group opens 22/80/443 to the world by default; tighten as needed.
- AMI not found: region may not have the filter; try another region.

## Next steps
- Learn variables/outputs: https://developer.hashicorp.com/terraform/language/values
- Use workspaces and backends for teams: https://developer.hashicorp.com/terraform/language/state
- Explore the AWS provider docs: https://registry.terraform.io/providers/hashicorp/aws/latest
