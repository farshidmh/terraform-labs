
# Lab Instruction: Installing Terraform on Ubuntu

## Duration

Approximately 10 minutes

## Objective

The objective of this lab is to guide you through the process of installing Terraform on an Ubuntu system. By the end of this lab, you will have Terraform installed and ready to use.

## Prerequisites

- A running instance of Ubuntu (either physical or virtual machine).
- Basic understanding of using the terminal in Ubuntu.

## Step-by-step Guide

### Step A: Update and Upgrade the System

1. Open a terminal on your Ubuntu system.
2. Update and upgrade your system's package list to ensure all packages are up-to-date.

```bash
sudo apt update
```

### Step B: Install Required Dependencies

3. Install required dependencies for adding new repositories and fetching packages over HTTPS.

```bash
sudo apt install -y gnupg software-properties-common curl
```

### Step C: Add HashiCorp GPG Key

4. Add the HashiCorp GPG key to your system.

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
```

### Step D: Add the HashiCorp Linux Repository

5. Add the official HashiCorp Linux repository to your system.

```bash
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
```

### Step E: Update the Package List

6. Update the package list to include the new repository.

```bash
sudo apt update
```

### Step F: Install Terraform

7. Install Terraform using the apt package manager.

```bash
sudo apt install -y terraform
```

### Step G: Verify the Installation

8. Verify that Terraform is installed correctly by checking its version.

```bash
terraform -v
```

You should see output similar to the following:

```
Terraform v1.x.x
```

## Summary

In this lab, you successfully installed Terraform on an Ubuntu system. You updated the system, added the HashiCorp GPG key and repository, and installed Terraform using the apt package manager. Terraform is now ready for you to use in managing your infrastructure as code.