# Terraform Lab: Creating a VPC, Subnet, Internet Gateway, and Route Table on AWS

## Duration

Approximately 30 minutes

## Objective

The objective of this lab is to guide you through the process of using Terraform to create a Virtual Private Cloud (VPC), a subnet within that VPC, an Internet Gateway (IGW), and a Route Table on AWS.

## Prerequisites

- Basic understanding of Terraform.
- Terraform installed on your machine.
- AWS CLI configured with your AWS account credentials.

## Step-by-step Guide

### Step A: Set up the Terraform Configuration

1. Create a new directory for your Terraform project and navigate into it:

```bash
mkdir terraform-vpc-lab
cd terraform-vpc-lab
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

4. In the `variables.tf` file, define variables for the VPC CIDR block, subnet CIDR block, region, profile, VPC name prefix, and subnet name prefix:

```hcl
# variables.tf

variable "vpc_cidr" {
  description = "The CIDR block for the VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "subnet_cidr" {
  description = "The CIDR block for the subnet"
  type        = string
  default     = "10.0.1.0/24"
}

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

variable "vpc_name_prefix" {
  description = "The prefix for the VPC name"
  type        = string
  default     = "main"
}

variable "subnet_name_prefix" {
  description = "The prefix for the Subnet name"
  type        = string
  default     = "main"
}
```

### Step D: Define Resources

5. In the `main.tf` file, define the resources for creating a VPC, a subnet, an Internet Gateway, and a Route Table:

```hcl
# main.tf

resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = "${var.vpc_name_prefix}_vpc"
  }
}

resource "aws_subnet" "main" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.subnet_cidr
  availability_zone = "${var.region}a"

  tags = {
    Name = "${var.subnet_name_prefix}_subnet"
  }
}

resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main_igw"
  }
}

resource "aws_route_table" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main_route_table"
  }
}

resource "aws_route" "default_route" {
  route_table_id         = aws_route_table.main.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.main.id
  depends_on             = [aws_route_table.main]
}

resource "aws_route_table_association" "main" {
  subnet_id      = aws_subnet.main.id
  route_table_id = aws_route_table.main.id
}
```

### Step E: Define Outputs

6. In the `outputs.tf` file, define the outputs for the VPC ID, subnet ID, Internet Gateway ID, and Route Table ID:

```hcl
# outputs.tf

output "vpc_id" {
  description = "The ID of the VPC"
  value       = aws_vpc.main.id
}

output "subnet_id" {
  description = "The ID of the subnet"
  value       = aws_subnet.main.id
}

output "internet_gateway_id" {
  description = "The ID of the Internet Gateway"
  value       = aws_internet_gateway.main.id
}

output "route_table_id" {
  description = "The ID of the Route Table"
  value       = aws_route_table.main.id
}
```

### Step F: Initialize and Apply the Configuration

7. Initialize your Terraform project. This step downloads the required provider plugins:

```bash
terraform init
```

8. Apply the Terraform configuration to create the resources:

```bash
terraform apply
```

9. Confirm the apply step by typing `yes` when prompted.

### Step G: Verify the Deployment

10. Once the apply step completes, verify the deployment by checking the AWS Management Console or using the AWS CLI:

```bash
aws ec2 describe-vpcs --vpc-ids $(terraform output -raw vpc_id)
aws ec2 describe-subnets --subnet-ids $(terraform output -raw subnet_id)
aws ec2 describe-internet-gateways --internet-gateway-ids $(terraform output -raw internet_gateway_id)
aws ec2 describe-route-tables --route-table-ids $(terraform output -raw route_table_id)
```

### Step H: Clean Up Resources

11. To avoid incurring charges, destroy the resources created by Terraform once you are done:

```bash
terraform destroy
```

12. Confirm the destroy step by typing `yes` when prompted.

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
variable "vpc_cidr" {
  description = "The CIDR block for the VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "subnet_cidr" {
  description = "The CIDR block for the subnet"
  type        = string
  default     = "10.0.1.0/24"
}

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

variable "vpc_name_prefix" {
  description = "The prefix for the VPC name"
  type        = string
  default     = "main"
}

variable "subnet_name_prefix" {
  description = "The prefix for the Subnet name"
  type        = string
  default     = "main"
}
```

### main.tf

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = "${var.vpc_name_prefix}_vpc"
  }
}

resource "aws_subnet" "main" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.subnet_cidr
  availability_zone = "${var.region}a"

  tags = {
    Name = "${var.subnet_name_prefix}_subnet"
  }
}

resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main_igw"
  }
}

resource "aws_route_table" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main_route_table"
  }
}

resource "aws_route" "default_route" {
  route_table_id         = aws_route_table.main.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.main.id
  depends_on             = [aws_route_table.main]
}

resource "aws_route_table_association" "main" {
  subnet_id      = aws_subnet.main.id
  route_table_id = aws_route_table.main.id
}
```

### outputs.tf

```hcl
output "vpc_id" {
  description = "The ID of the VPC"
  value       = aws_vpc.main.id
}

output "subnet_id" {
  description = "The ID of the subnet"
  value       = aws_subnet.main.id
}

output "internet_gateway_id" {
  description = "The ID of the Internet Gateway"
  value       = aws_internet_gateway.main.id
}

output "route_table_id" {
  description = "The ID of the Route Table"
  value       = aws_route_table.main.id
}
```

## Summary

This lab provided a hands-on approach to creating a VPC, a subnet, an Internet Gateway, and a Route Table on AWS using Terraform