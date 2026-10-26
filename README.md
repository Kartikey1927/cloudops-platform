# CloudOps Automation Platform

A beginner-friendly DevOps portfolio project demonstrating Infrastructure as Code, automated Terraform validation, cloud security, and AWS cost awareness.

## Project Overview

This project provisions and manages secure AWS S3 buckets using Terraform. GitHub Actions automatically validates Terraform changes and runs Checkov security scans.

## Architecture

```text
Developer
   |
   v
GitHub Repository
   |
   v
GitHub Actions CI
   |-- Terraform init
   |-- Terraform format check
   |-- Terraform validate
   |-- Checkov security scan
   |
   v
Terraform-managed AWS infrastructure
   |
   |-- Artifacts S3 bucket
   |-- Access logs S3 bucket
   |-- Encryption and versioning
   |-- Public access protection
   |-- Lifecycle rules
   `-- S3 server access logging
```

Note: The current CI workflow validates and scans configuration.
It does not deploy infrastructure from GitHub Actions.

## Technology Stack

- AWS S3
- Terraform
- Git and GitHub
- GitHub Actions
- Checkov
- AWS CLI
- Ubuntu on WSL

## Infrastructure and Security

### Artifacts bucket

- S3 public access blocked
- Server-side encryption using AWS-managed KMS key
- Bucket versioning enabled
- Lifecycle rule to abort incomplete multipart uploads after 7 days
- Server access logging enabled

### Access logs bucket

- S3 public access blocked
- Server-side encryption using AES-256 (SSE-S3)
- Bucket versioning enabled
- Lifecycle rule to abort incomplete multipart uploads after 7 days
- Bucket policy permits the S3 logging service to deliver access logs

Cross-region replication and event notifications are not configured in this project.

## Continuous Integration

The GitHub Actions workflow is located at:

`.github/workflows/terraform-ci.yml`

It runs on relevant pushes to `main` and pull requests targeting `main`.

Checks include:

1. Terraform initialization without a remote backend
2. Terraform formatting
3. Terraform validation
4. Checkov security scanning

The workflow does not require AWS credentials because it does not apply changes to AWS.

## Prerequisites

Install and configure:

- Terraform
- AWS CLI
- Git
- An AWS account with appropriate permissions
- A GitHub account

Configure AWS credentials locally using an appropriate AWS CLI profile or environment configuration. Never commit credentials, access keys, or secret files.

## Run Locally

Clone the repository:

```bash
git clone https://github.com/Kartikey1927/cloudops-platform.git
cd cloudops-platform
```

Initialize Terraform:

```bash
terraform -chdir=terraform init
```

Format-check the configuration:

```bash
terraform -chdir=terraform fmt -check -recursive
```

Validate the configuration:

```bash
terraform -chdir=terraform validate
```

Review the proposed infrastructure changes:

```bash
terraform -chdir=terraform plan
```

Review the plan before making any infrastructure changes. Only run `terraform apply` when you understand and approve the proposed changes.

## Verification Commands

List the project buckets:

```bash
aws s3api list-buckets \
  --query "Buckets[?contains(Name, 'cloudops-platform')].Name" \
  --output table
```

Verify access logging:

```bash
aws s3api get-bucket-logging \
  --bucket cloudops-platform-artifacts-691193789774
```

Check artifacts storage:

```bash
aws s3 ls s3://cloudops-platform-artifacts-691193789774 \
  --recursive --summarize
```

Check access logs storage:

```bash
aws s3 ls s3://cloudops-platform-access-logs-691193789774 \
  --recursive --summarize
```

These verification commands use the bucket names from the original project deployment. Update them if you deploy the project into a different AWS account.

## Cost Awareness

- An AWS Zero Spend Budget is configured to alert on unexpected spending.
- AWS Budgets alerts are not a hard spending limit.
- Cost Explorer data may take time to become available.
- S3 storage, requests, and access logging may incur charges.
- Review AWS billing data regularly and remove resources only when they are no longer needed.

## Terraform State and Secrets

Terraform state can contain sensitive information. Keep local state files, backups, credentials, and secret files out of Git. This project currently uses local Terraform state; back it up securely and do not share it publicly.

## Project Status

- [x] Terraform infrastructure foundation
- [x] GitHub Actions Terraform CI
- [x] Checkov security scanning
- [x] S3 encryption, versioning, and public access protection
- [x] S3 server access logging
- [x] AWS Zero Spend Budget
- [x] Initial storage and infrastructure verification
- [x] Project documentation

## Future Improvements

- Review actual AWS costs when Cost Explorer data is available
- Add additional monitoring where useful
- Consider remote Terraform state with secure locking
- Consider AWS OIDC authentication if deployment is later automated
- Evaluate cross-region replication and event notifications based on requirements and budget

## Author

GitHub: [@Kartikey1927](https://github.com/Kartikey1927)
