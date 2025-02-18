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

**Environment Variables**

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

ingress {
  from_port   = 22
  to_port     = 22
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}

# Terraform Commands and Their Uses

## 1. `terraform init` - Initialize Terraform
**Purpose:**
- Initialises a Terraform project.
- Downloads required provider plugins (e.g., AWS, Azure).
- Configures backend settings (for state storage).

**When to Use:**
- When setting up a new Terraform project.
- After modifying the `provider` block.
- After changing Terraform backends.

**Example:**
```sh
terraform init
```

---

## 2. `terraform plan` - Preview Changes
**Purpose:**
- Displays what Terraform **will do** before applying changes.
- Helps review additions, modifications, and deletions in resources.

**When to Use:**
- Before running `terraform apply` to confirm changes.
- After modifying `.tf` files to check for potential issues.

**Example:**
```sh
terraform plan
```

**Plan Output Indicators:**
- `+` Resource will be created.
- `-` Resource will be destroyed.
- `~` Resource will be modified.

---

## 3. `terraform apply` - Deploy Changes
**Purpose:**
- Applies the planned changes to the infrastructure.
- Creates, updates, or deletes resources as needed.

**When to Use:**
- After reviewing and confirming changes from `terraform plan`.

**Example:**
```sh
terraform apply
```

**To apply without manual confirmation:**
```sh
terraform apply -auto-approve
```

---

## Summary Table

| Command            | Purpose | When to Use |
|--------------------|---------|-------------|
| `terraform init`  | Initialises Terraform, installs providers, and sets up the backend. | First-time setup or after modifying providers. |
| `terraform plan`  | Shows planned changes without applying them. | Before `apply`, to review changes. |
| `terraform apply` | Applies the planned changes to infrastructure. | When ready to deploy changes. |

Would you like additional commands such as `terraform destroy`, `fmt`, or `validate` included?


# Deploying an App and Database Using Terraform

## Introduction
Terraform is an Infrastructure as Code (IaC) tool that allows you to automate and manage cloud infrastructure efficiently. This guide demonstrates how to use Terraform to deploy an application and a database in public and private subnets, along with the necessary networking components such as Network Security Groups (NSGs) and public IPs.

## Benefits of Using Terraform
- **Automation**: Avoids manual infrastructure setup, reducing errors.
- **Version Control**: Infrastructure code can be stored in a repository, enabling collaboration and rollback.
- **Scalability**: Easily scale infrastructure components as needed.
- **Consistency**: Ensures consistent deployment across environments.

## Architecture Overview
We will create:
- A **VPC** (or Virtual Network) to host resources.
- Two **subnets**:
  - **Public subnet**: Hosts the application server with a public IP.
  - **Private subnet**: Hosts the database with restricted access.
- **Network Security Groups (NSGs)**:
  - Controls access to the application and database.
- A **Public IP** for the application server to make it accessible.
- A **Route Table** to manage traffic flow between subnets.

## Terraform Implementation
### 1. Define Provider
```hcl
provider "aws" {
  region = "us-east-1"
}
```

### 2. Create VPC and Subnets
```hcl
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public_subnet" {
  vpc_id            = aws_vpc.my_vpc.id
  cidr_block        = "10.0.1.0/24"
  map_public_ip_on_launch = true
}

resource "aws_subnet" "private_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.2.0/24"
}
```

### 3. Create Internet Gateway and Route Table
```hcl
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.my_vpc.id
}

resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.my_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}
```

### 4. Create Security Groups
```hcl
resource "aws_security_group" "app_sg" {
  vpc_id = aws_vpc.my_vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_security_group" "db_sg" {
  vpc_id = aws_vpc.my_vpc.id

  ingress {
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    security_groups = [aws_security_group.app_sg.id]
  }
}
```

### 5. Deploy Application Server
```hcl
resource "aws_instance" "app" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id
  security_groups = [aws_security_group.app_sg.name]
  associate_public_ip_address = true
}
```

### 6. Deploy Database Server
```hcl
resource "aws_db_instance" "db" {
  engine         = "mysql"
  instance_class = "db.t2.micro"
  allocated_storage = 20
  subnet_id      = aws_subnet.private_subnet.id
  vpc_security_group_ids = [aws_security_group.db_sg.id]
  skip_final_snapshot = true
}
```

## Why These Networking Components Are Needed
- **VPC**: Isolates our cloud resources for security and control.
- **Public Subnet**: Allows the app to be accessed via the internet.
- **Private Subnet**: Restricts the database to internal access only.
- **NSGs**: Ensures only necessary traffic flows to the app and database.
- **Public IP**: Needed to expose the application to users.
- **Route Table**: Controls traffic between subnets and the internet.

## Conclusion
By using Terraform, we can define our infrastructure as code and deploy it consistently. The networking components ensure security, availability, and accessibility of the application and database. Happy coding!
