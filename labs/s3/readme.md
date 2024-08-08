# Terraform Lab: Creating an S3 Bucket, Uploading a File, and Making It Public

## Duration

Approximately 30 minutes

## Objective

The objective of this lab is to guide you through the process of using Terraform to create an S3 bucket, upload a file to it, and make the file publicly accessible.

## Prerequisites

- Basic understanding of Terraform.
- Terraform installed on your machine.
- AWS CLI configured with your AWS account credentials.

## Step-by-step Guide

### Step A: Set up the Terraform Configuration

1. Create a new directory for your Terraform project and navigate into it:

```bash
mkdir terraform-s3-lab
cd terraform-s3-lab
```

2. Create the following files in your project directory:
    - `main.tf`
    - `outputs.tf`
    - `providers.tf`
    - `variables.tf`

### Step B: Define the Terraform Configuration

3. In the `providers.tf` file, specify the required Terraform and AWS provider configuration:

```hcl
# providers.tf

terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = ">= 3.0"
    }
  }
  required_version = ">= 0.14"
}

provider "aws" {
  region  = var.region
  profile = var.profile
}
```

### Step C: Define Variables

4. In the `variables.tf` file, define variables for the AWS region, profile, S3 bucket name, and the local file path to upload:

```hcl
# variables.tf

variable "region" {
  description = "The AWS region to deploy resources in"
  type        = string
  default     = "us-east-2"
}

variable "profile" {
  description = "The AWS CLI profile to use"
  type        = string
  default     = "default"
}

variable "bucket_name" {
  description = "The name of the S3 bucket"
  type        = string
}

variable "local_file_path" {
  description = "The path to the local file to upload"
  type        = string
}
```

### Step D: Define Resources

5. In the `main.tf` file, define the resources for creating an S3 bucket, uploading a file to it, and making the file publicly accessible:

```hcl
# main.tf

resource "aws_s3_bucket" "bucket" {
  bucket = var.bucket_name

  tags = {
    Name = var.bucket_name
  }
}

resource "aws_s3_bucket_object" "file" {
  bucket = aws_s3_bucket.bucket.bucket
  key    = "uploaded-file"
  source = var.local_file_path
  acl    = "public-read"

  tags = {
    Name = "uploaded-file"
  }
}

resource "aws_s3_bucket_public_access_block" "public_access_block" {
  bucket = aws_s3_bucket.bucket.bucket

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}
```

### Step E: Define Outputs

6. In the `outputs.tf` file, define the output for the URL of the uploaded file:

```hcl
# outputs.tf

output "file_url" {
  description = "The URL of the uploaded file"
  value       = "https://${aws_s3_bucket.bucket.bucket}.s3.amazonaws.com/${aws_s3_bucket_object.file.key}"
}
```

### Step F: Initialize and Apply the Configuration

7. Initialize your Terraform project. This step downloads the required provider plugins:

```bash
terraform init
```

8. Apply the Terraform configuration. Terraform will prompt you to enter values for the variables:

```bash
terraform apply
```

When prompted, provide the values for each variable:

```
var.bucket_name
  The name of the S3 bucket

  Enter a value: my-unique-bucket-name

var.local_file_path
  The path to the local file to upload

  Enter a value: /path/to/your/local/file.txt
```

### Step G: Verify the Deployment

9. Once the apply step completes, verify the deployment by accessing the URL of the uploaded file. The URL will be displayed as output by Terraform.

```bash
terraform output file_url
```

### Step H: Clean Up Resources

10. To avoid incurring charges, destroy the resources created by Terraform once you are done:

```bash
terraform destroy
```

11. Confirm the destroy step by typing `yes` when prompted.

## Final Configuration Files

Ensure your files look like this:

### providers.tf

```hcl
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = ">= 3.0"
    }
  }
  required_version = ">= 0.14"
}

provider "aws" {
  region  = var.region
  profile = var.profile
}
```

### variables.tf

```hcl
variable "region" {
  description = "The AWS region to deploy resources in"
  type        = string
  default     = "us-east-2"
}

variable "profile" {
  description = "The AWS CLI profile to use"
  type        = string
  default     = "default"
}

variable "bucket_name" {
  description = "The name of the S3 bucket"
  type        = string
}

variable "local_file_path" {
  description = "The path to the local file to upload"
  type        = string
}
```

### main.tf

```hcl
resource "aws_s3_bucket" "bucket" {
  bucket = var.bucket_name

  tags = {
    Name = var.bucket_name
  }
}

resource "aws_s3_bucket_object" "file" {
  bucket = aws_s3_bucket.bucket.bucket
  key    = "uploaded-file"
  source = var.local_file_path
  acl    = "public-read"

  tags = {
    Name = "uploaded-file"
  }
}

resource "aws_s3_bucket_public_access_block" "public_access_block" {
  bucket = aws_s3_bucket.bucket.bucket

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}
```

### outputs.tf

```hcl
output "file_url" {
  description = "The URL of the uploaded file"
  value       = "https://${aws_s3_bucket.bucket.bucket}.s3.amazonaws.com/${aws_s3_bucket_object.file.key}"
}
```

## Summary

This lab provided a hands-on approach to creating an S3 bucket, uploading a file to it, and making the file publicly accessible using Terraform. You learned how to define providers, create resources, and manage outputs through Terraform configuration files. Using Terraform, you can automate the deployment and management of your infrastructure in a consistent and repeatable manner.