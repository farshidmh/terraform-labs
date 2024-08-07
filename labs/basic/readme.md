# Terraform Lab: Creating a Windows EC2 Instance in a Custom VPC and Subnet on AWS

## Duration

Approximately 45 minutes

## Objective

The objective of this lab is to guide you through the process of using Terraform to create a Windows EC2 instance in a custom VPC (`farshid-a-vpc`) and subnet (`farshid-a-public`) on AWS.

## Prerequisites

- Basic understanding of Terraform.
- Terraform installed on your machine.
- AWS CLI configured with your AWS account credentials.
- Existing VPC and Subnet with the names `farshid-a-vpc` and `farshid-a-public`.

## Step-by-step Guide

### Step A: Set up the Terraform Configuration

1. Create a new directory for your Terraform project and navigate into it:

```bash
mkdir terraform-windows-lab
cd terraform-windows-lab
```

2. Create the following files in your project directory:
    - `main.tf`
    - `outputs.tf`
    - `providers.tf`

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
  region  = "us-east-2"
  profile = "default"
}
```

### Step C: Define Data Sources and Resources

4. In the `main.tf` file, define the data sources to fetch the VPC and subnet details, and create the EC2 instance:

```hcl
# main.tf

data "aws_vpc" "selected" {
  filter {
    name   = "tag:Name"
    values = ["farshid-a-vpc"]
  }
}

data "aws_subnet" "selected" {
  filter {
    name   = "tag:Name"
    values = ["farshid-a-public"]
  }

  vpc_id = data.aws_vpc.selected.id
}

resource "aws_instance" "windows" {
  ami           = "ami-05804339cc1cf98c0"
  instance_type = "t2.micro"
  subnet_id     = data.aws_subnet.selected.id

  tags = {
    Name = "Windows-Instance"
  }
}
```

### Step D: Define Outputs

5. In the `outputs.tf` file, define the outputs for the instance ID and public IP:

```hcl
# outputs.tf

output "instance_id" {
  description = "The ID of the Windows EC2 instance"
  value       = aws_instance.windows.id
}

output "public_ip" {
  description = "The public IP address of the Windows EC2 instance"
  value       = aws_instance.windows.public_ip
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
aws ec2 describe-instances --instance-ids $(terraform output -raw instance_id)
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
  required_version = ">= 0.14"
}

provider "aws" {
  region  = "us-east-2"
  profile = "default"
}
```

### main.tf

```hcl
data "aws_vpc" "selected" {
  filter {
    name   = "tag:Name"
    values = ["farshid-a-vpc"]
  }
}

data "aws_subnet" "selected" {
  filter {
    name   = "tag:Name"
    values = ["farshid-a-public"]
  }

  vpc_id = data.aws_vpc.selected.id
}

resource "aws_instance" "windows" {
  ami           = "ami-05804339cc1cf98c0"
  instance_type = "t2.micro"
  subnet_id     = data.aws_subnet.selected.id
  
  associate_public_ip_address = true

  tags = {
    Name = "Windows-Instance"
  }
}
```

### outputs.tf

```hcl
output "instance_id" {
  description = "The ID of the Windows EC2 instance"
  value       = aws_instance.windows.id
}

output "public_ip" {
  description = "The public IP address of the Windows EC2 instance"
  value       = aws_instance.windows.public_ip
}
```

## Summary

This lab provided a hands-on approach to creating a Windows EC2 instance in a custom VPC and subnet on AWS using Terraform. You learned how to define providers, create resources, and manage outputs
through Terraform configuration files. Using Terraform, you can automate the deployment and management of your infrastructure in a consistent and repeatable manner.