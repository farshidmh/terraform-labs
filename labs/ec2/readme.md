# Terraform Lab: Creating a VPC, Subnet, Internet Gateway, Route Table, and EC2 Instances on AWS

## Duration

Approximately 45 minutes

## Objective

The objective of this lab is to guide you through the process of using Terraform to create a Virtual Private Cloud (VPC), a subnet within that VPC, an Internet Gateway (IGW), a Route Table, and EC2 instances on AWS.

## Prerequisites

- Basic understanding of Terraform.
- Terraform installed on your machine.
- AWS CLI configured with your AWS account credentials.

## Step-by-step Guide

### Step A: Set up the Terraform Configuration

1. Create a new directory for your Terraform project and navigate into it:

```bash
mkdir terraform-ec2-lab
cd terraform-ec2-lab
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

4. In the `variables.tf` file, define variables for the VPC CIDR block, subnet CIDR block, region, profile, VPC name prefix, subnet name prefix, AMI ID for the EC2 instances, and the number of instances:

```hcl
# variables.tf

variable "vpc_cidr" {
  description = "The CIDR block for the VPC"
  type        = string
}

variable "subnet_cidr" {
  description = "The CIDR block for the subnet"
  type        = string
}

variable "region" {
  description = "The AWS region to deploy resources in"
  type        = string
}

variable "profile" {
  description = "The AWS CLI profile to use"
  type        = string
}

variable "vpc_name_prefix" {
  description = "The prefix for the VPC name"
  type        = string
}

variable "subnet_name_prefix" {
  description = "The prefix for the Subnet name"
  type        = string
}

variable "ami_id" {
  description = "The AMI ID for the EC2 instances"
  type        = string
}

variable "instance_type" {
  description = "The instance type for the EC2 instances"
  type        = string
  default     = "t2.micro"
}

variable "instance_count" {
  description = "The number of EC2 instances to create"
  type        = number
}
```

### Step D: Define Resources

5. In the `main.tf` file, define the resources for creating a VPC, a subnet, an Internet Gateway, a Route Table, and the EC2 instances:

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

resource "aws_instance" "ec2" {
  count         = var.instance_count
  ami           = var.ami_id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.main.id

  tags = {
    Name = "EC2-Instance-${count.index + 1}"
  }
}
```

### Step E: Define Outputs

6. In the `outputs.tf` file, define the outputs for the VPC ID, subnet ID, Internet Gateway ID, Route Table ID, and the public IP addresses of the EC2 instances:

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

output "ec2_public_ips" {
  description = "The public IP addresses of the EC2 instances"
  value       = [for instance in aws_instance.ec2 : instance.public_ip]
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
var.vpc_cidr
  The CIDR block for the VPC

  Enter a value: 10.0.0.0/16

var.subnet_cidr
  The CIDR block for the subnet

  Enter a value: 10.0.1.0/24

var.region
  The AWS region to deploy resources in

  Enter a value: us-east-2

var.profile
  The AWS CLI profile to use

  Enter a value: default

var.vpc_name_prefix
  The prefix for the VPC name

  Enter a value: myvpc

var.subnet_name_prefix
  The prefix for the Subnet name

  Enter a value: mysubnet

var.ami_id
  The AMI ID for the EC2 instances

  Enter a value: ami-xxxxxxxxxxxxxxx

var.instance_count
  The number of EC2 instances to create

  Enter a value: 2
```

### Step G: Verify the Deployment

9. Once the apply step completes, verify the deployment by checking the AWS Management Console or using the AWS CLI:

```bash
aws ec2 describe-vpcs --vpc-ids $(terraform output -raw vpc_id)
aws ec2 describe-subnets --subnet-ids $(terraform output -raw subnet_id)
aws ec2 describe-internet-gateways --internet-gateway-ids $(terraform output -raw internet_gateway_id)
aws ec2 describe-route-tables --route-table-ids $(terraform output -raw route_table_id)
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
variable "vpc_cidr" {
  description = "The CIDR block for the VPC"
  type        = string
}

variable "subnet_cidr" {
  description = "The CIDR block for the subnet"
  type        = string
}

variable "region" {
  description = "The AWS region to deploy resources in"
  type        = string
}

variable "profile" {
  description = "The AWS CLI profile to use"
  type        = string
}

variable "vpc_name_prefix" {
  description = "The prefix for the VPC name"
  type        = string
}

variable "subnet_name_prefix" {
  description = "The prefix for the Subnet name"
  type        = string
}

variable "ami_id" {
  description = "The AMI ID for the EC2 instances"
  type        = string
}

variable "instance_type" {
  description = "The instance type for the EC2 instances"
  type        = string
  default     = "t2.micro"
}

variable "instance_count" {
  description = "The number of EC2 instances to create"
  type        = number
}
```

### main.tf

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name =

 "${var.vpc_name_prefix}_vpc"
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

resource "aws_instance" "ec2" {
  count         = var.instance_count
  ami           = var.ami_id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.main.id

  tags = {
    Name = "EC2-Instance-${count.index + 1}"
  }
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

output "ec2_public_ips" {
  description = "The public IP addresses of the EC2 instances"
  value       = [for instance in aws_instance.ec2 : instance.public_ip]
}
```

## Summary

This lab provided a hands-on approach to creating a VPC, a subnet, an Internet Gateway, a Route Table, and EC2 instances on AWS using Terraform. You learned how to define providers, create resources, and manage outputs through Terraform configuration files. Using Terraform, you can automate the deployment and management of your infrastructure in a consistent and repeatable manner.