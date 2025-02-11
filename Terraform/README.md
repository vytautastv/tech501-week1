# **Terraform Notes**

## **What is Terraform? What is it used for?**
Terraform is an **Infrastructure as Code (IaC)** tool developed by **HashiCorp** that allows users to define, provision, and manage cloud resources using declarative configuration files.

It is primarily used for:
- Automating infrastructure provisioning and management
- Managing cloud services (AWS, Azure, GCP, etc.)
- Ensuring consistent deployments across multiple environments

---

## **Why Use Terraform? The Benefits**
Terraform provides several advantages, including:

**Infrastructure as Code (IaC):** Infrastructure is defined in version-controlled code.
**Multi-Cloud Support:** Works with AWS, Azure, GCP, and many others.
**Declarative Configuration:** Users define the desired state of resources, and Terraform applies changes.
**Automated Resource Management:** Handles provisioning, updating, and destruction of infrastructure.
**State Management:** Tracks infrastructure in a state file for consistency.
**Idempotency:** Ensures changes are applied predictably without unnecessary updates.
**Dependency Management:** Determines resource dependencies and applies them in the correct order.

---

## **Alternatives to Terraform**
Although Terraform is widely used, alternatives include:

| Tool         | Provider Support  | Description |
|-------------|------------------|-------------|
| **AWS CloudFormation** | AWS Only | Native AWS IaC tool using JSON/YAML templates |
| **Pulumi**  | Multi-Cloud | Uses familiar programming languages like TypeScript & Python |
| **Ansible** | Multi-Cloud | Configuration management & orchestration, not declarative IaC |
| **Chef**    | Multi-Cloud | More suited for configuration management |
| **SaltStack** | Multi-Cloud | Uses a master-agent model for infrastructure automation |

---

## **Who is Using Terraform in the Industry?**
Terraform is widely used by **startups**, **large enterprises**, and **cloud-native companies** to manage infrastructure at scale. Some notable companies include:

- **Netflix**: Automates AWS infrastructure with Terraform
- **Uber**: Uses Terraform to manage multi-cloud infrastructure
- **Airbnb**: Implements Terraform for scalable cloud deployments
- **Google**: Uses Terraform internally and promotes Terraform on GCP

---

## **In IaC, What is Orchestration? How Does Terraform Act as an "Orchestrator"?**
**Orchestration** in IaC refers to managing and coordinating the deployment, scaling, and automation of infrastructure resources.

Terraform acts as an **orchestrator** by:
1. **Defining Infrastructure** using `.tf` files
2. **Managing Dependencies** (e.g., ensuring a VPC is created before EC2 instances)
3. **Tracking State** to understand current vs. desired infrastructure
4. **Applying Changes Safely** using `terraform plan` and `terraform apply`

---

## **Best Practice for Supplying AWS Credentials to Terraform**
The recommended way to supply AWS credentials to Terraform is by **using the AWS CLI or IAM roles** rather than hardcoding credentials.

### *Best Practice:**
- Use the **AWS CLI** (`aws configure`) to store credentials in `~/.aws/credentials`
- Use **IAM roles** when running Terraform on AWS services like EC2 or Lambda
- Store secrets securely in **AWS Secrets Manager** or **environment variables**

### **Never Do This:**
- **Never** store credentials in `.tf` files or Terraform state files
- **Never** commit credentials to GitHub or version control
- **Never** hardcode AWS keys inside Terraform configuration files

---

## **How Does Terraform Look Up AWS Credentials? (Order of Precedence)**
Terraform searches for AWS credentials in the following order (highest priority first):

1️**Environment Variables**
   ```sh
   export AWS_ACCESS_KEY_ID="your-access-key"
   export AWS_SECRET_ACCESS_KEY="your-secret-key"

# Terraform Resources and Best Practices

## 1. Overview of Resources Created

### AWS EC2 Instance

We have created an **EC2 instance** in AWS using Terraform. The configuration includes the following attributes:

- **AMI**: Amazon Machine Image (AMI) ID `ami-0c1c30571d2dae5c9` is used to define the operating system and software environment on the instance.
- **Instance Type**: The instance is of type `t3.micro`, which is a low-cost, general-purpose EC2 instance type.
- **Public IP**: The instance is configured to have a **public IP address** assigned, making it accessible over the internet.
- **Security Group**: The EC2 instance is attached to a security group called `tech501-vy-tf-allow-port22-3000-80`.
- **Key Pair**: We use a key pair `tech501-vytautas-aws-key` to securely SSH into the instance.

### Security Group

A **Security Group** named `tech501-vy-tf-allow-port22-3000-80` has been created with the following inbound rules:

- **Port 22 (SSH)**: Only allows connections from the **localhost (127.0.0.1/32)**. This is intended for limiting access only to the local machine. You may want to change this to `0.0.0.0/0` for testing or your specific IP address for production.
- **Port 3000**: Open to **all IPs** (`0.0.0.0/0`) to allow external access on this custom port.
- **Port 80 (HTTP)**: Open to **all IPs** (`0.0.0.0/0`) to allow access to the web server.

### EC2 Security Group Ingress Example
```hcl
ingress {
  from_port   = 22
  to_port     = 22
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}
