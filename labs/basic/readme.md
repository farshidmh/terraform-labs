# Terraform Lab: Deploying an EC2 Instance and S3 Bucket on AWS

## Duration

Approximately 30 minutes

## Objective

The objective of this lab is to guide you through the process of using Terraform to deploy an EC2 instance and an S3 bucket on AWS. You will create and manage these resources using Terraform configuration files.

## Prerequisites:

- Basic understanding of Terraform.
- Terraform installed on your machine.
- AWS CLI configured with your AWS account credentials.

## Step-by-step guide:

### Step A: Set up the Terraform Configuration

1. Create a new directory for your Terraform project and navigate into it:

```bash
mkdir terraform-lab
cd terraform-lab
```

2. Create the following files in your project directory:
    - `main.tf`
    - `output.tf`
    - `providers.tf`

### Step B: Define the Terraform Configuration

3. In the `providers.tf` file, specify the required Terraform and AWS provider configuration:

```hcl
# providers.tf

# Example 02-01 - Configuration

terraform {
    required_providers {
        aws = {
            source = "hashicorp/aws"
            version = ">= 3.0"
        }     
    }
    # Required version of terraform
    required_version = ">0.14"
}

provider aws {
    region = "us-east-2"
    profile = "dev"
}
```

### Step C: Define Resources

4. In the `main.tf` file, define the resources for an EC2 instance and an S3 bucket:

```hcl
# main.tf

resource "aws_instance" "myVM" {
    ami = "ami-077e31c4939f6a2f3"
    instance_type = "t2.micro"
    tags = {
        Name = "Example-01"
    }
}

resource "aws_s3_bucket" "myBucket" {
    bucket = "terraform-example-02-01"
}

data "aws_vpc" "default_VPC" {
    default = true
}
```

### Step D: Define Outputs

5. In the `output.tf` file, define the outputs for the EC2 instance's public IP and the default VPC ID:

```hcl
# output.tf

output "EC2_public_ip" {
    description = "Public IP address of 'myVM'"
    value = aws_instance.myVM.public_ip 
}

output "VPC_id" {
    value = data.aws_vpc.default_VPC.id
}
```

### Step E: Initialize and Apply the Configuration

6. Initialize your Terraform project. This step downloads the required provider plugins:

```bash
terraform init
```

7. Apply the Terraform configuration to create the resources:

```bash
terraform apply
```

8. Confirm the apply step by typing `yes` when prompted.

### Step F: Verify the Deployment

9. Once the apply step completes, verify the deployment by checking the AWS Management Console or using the AWS CLI:

```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=Example-01"
aws s3 ls
```

### Step G: Clean Up Resources

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
    required_version = ">0.14"
}

provider aws {
    region = "us-east-2"
    profile = "dev"
}
```

### main.tf

```hcl
resource "aws_instance" "myVM" {
    ami = "ami-077e31c4939f6a2f3"
    instance_type = "t2.micro"
    tags = {
        Name = "Example-01"
    }
}

resource "aws_s3_bucket" "myBucket" {
    bucket = "terraform-example-02-01"
}

data "aws_vpc" "default_VPC" {
    default = true
}
```

### output.tf

```hcl
output "EC2_public_ip" {
    description = "Public IP address of 'myVM'"
    value = aws_instance.myVM.public_ip 
}

output "VPC_id" {
    value = data.aws_vpc.default_VPC.id
}
```

## Summary

This lab provided a hands-on approach to deploying an EC2 instance and an S3 bucket on AWS using Terraform. You learned how to define providers, create resources, and manage outputs through Terraform configuration files. Using Terraform, you can automate the deployment and management of your infrastructure in a consistent and repeatable manner.