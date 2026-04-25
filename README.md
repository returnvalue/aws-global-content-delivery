# AWS Global Content Delivery (Terraform)

This repository demonstrates a production-grade Global Content Delivery Network (CDN) provisioned using **Terraform**. It follows the **Global Content Delivery Pattern**, a core requirement for the AWS Certified Solutions Architect - Associate (SAA-C03) exam.

## 🏗️ Architecture Overview

The infrastructure is designed to serve static content with ultra-low latency to users worldwide while keeping the source data completely secure. The architecture consists of:

1.  **Origin Layer:** An **Amazon S3** bucket used as the private source of truth for static website assets (HTML, CSS, images).
2.  **Security Layer:** **Origin Access Control (OAC)**, the modern AWS standard for ensuring that only CloudFront can access the S3 origin.
3.  **Distribution Layer:** An **Amazon CloudFront** distribution that caches and serves content from over 400+ Edge Locations globally.
4.  **Policy Layer:** A granular **S3 Bucket Policy** that restricts access specifically to the authorized CloudFront distribution ARN.

## 🛠️ SAA-C03 Design Patterns Covered

- **Design High-Performing Architectures (Domain 3):** Use of CloudFront CDN reduces latency by caching content closer to the end-user (Edge Computing).
- **Design Secure Architectures (Domain 2):** 
    - **Origin Cloaking:** The S3 bucket is not configured for public website hosting; instead, it is a private bucket accessible only via OAC.
    - **Identity-Based Access:** Uses IAM service principals and condition keys to enforce secure service-to-service communication.
- **Design Resilient Architectures (Domain 1):** S3 provides 11 nines of durability, and CloudFront provides a globally distributed, DDoS-resilient entry point.

## 🚀 Technical Components

- **Storage:** S3 Bucket with private access settings.
- **CDN:** CloudFront Distribution with custom cache behaviors and TTL settings.
- **Security:** CloudFront Origin Access Control (OAC) and S3 JSON Resource Policies.
- **Infrastructure as Code:** 100% automated via Terraform with a state-managed lifecycle.

## 💻 Local Development

This project is optimized for testing using **LocalStack**.

### Prerequisites
- [Terraform](https://www.terraform.io/downloads)
- [Docker](https://www.docker.com/products/docker-desktop)
- [LocalStack](https://localstack.cloud/)

### Deployment
1. Start LocalStack: `docker compose up -d`
2. Initialize Terraform: `terraform init`
3. Deploy Infrastructure: `terraform apply -auto-approve`

---

💡 **Pro Tip: Using `aws` instead of `awslocal`**

If you prefer using the standard `aws` CLI without the `awslocal` wrapper or repeating the `--endpoint-url` flag, you can configure a dedicated profile in your AWS config files.

### 1. Configure your Profile
Add the following to your `~/.aws/config` file:
```ini
[profile localstack]
region = us-east-1
output = json
# This line redirects all commands for this profile to LocalStack
endpoint_url = http://localhost:4566
```

Add matching dummy credentials to your `~/.aws/credentials` file:
```ini
[localstack]
aws_access_key_id = test
aws_secret_access_key = test
```

### 2. Use it in your Terminal
You can now run commands in two ways:

**Option A: Pass the profile flag**
```bash
aws iam create-user --user-name DevUser --profile localstack
```

**Option B: Set an environment variable (Recommended)**
Set your profile once in your session, and all subsequent `aws` commands will automatically target LocalStack:
```bash
export AWS_PROFILE=localstack
aws iam create-user --user-name DevUser
```

### Why this works
- **Precedence**: The AWS CLI (v2) supports a global `endpoint_url` setting within a profile. When this is set, the CLI automatically redirects all API calls for that profile to your local container instead of the real AWS cloud.
- **Convenience**: This allows you to use the standard documentation commands exactly as written, which is helpful if you are copy-pasting examples from AWS labs or tutorials.
