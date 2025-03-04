# Infrastructure Notes: App & DB VMs with Terraform

## 1. Building App & DB VMs Using Terraform

### **Terraform Configuration for EC2 Instances**
Terraform script to provision an **App VM** and a **DB VM** in AWS:

```hcl
provider "aws" {
  region = "eu-west-1"
}

resource "aws_instance" "app" {
  ami           = "ami-0abc123456789xyz"  # Replace with a valid AMI ID
  instance_type = "t2.micro"
  key_name      = "my-key"
  security_groups = ["app-sg"]

  user_data = file("app-userdata.sh")
  tags = {
    Name = "App-Server"
  }
}

resource "aws_instance" "db" {
  ami           = "ami-0abc123456789xyz"
  instance_type = "t2.micro"
  key_name      = "my-key"
  security_groups = ["db-sg"]

  user_data = file("db-userdata.sh")
  tags = {
    Name = "DB-Server"
  }
}
```

### **Terraform Deployment Commands**
```sh
terraform init
terraform apply -auto-approve
```

## 2. Automation Scripts for App Deployment

### **app-userdata.sh** (For App Server)
```sh
#!/bin/bash
sudo apt update -y
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
```

### **db-userdata.sh** (For Database Server)
```sh
#!/bin/bash
sudo apt update -y
sudo apt install -y mysql-server
sudo systemctl start mysql
sudo systemctl enable mysql
```

## 3. Creating an Image from the App Server

### **Using AWS CLI to Create an Image**
```sh
aws ec2 create-image --instance-id i-0741c421e5763daf6 --name "tech501-vytautas-app-CustomImage" --region eu-west-1 --no-reboot
```

### **Using Packer to Automate Image Creation**
Create a `packer.pkr.hcl` file:

```hcl
source "amazon-ebs" "app-image" {
  ami_name      = "app-server-image-{{timestamp}}"
  instance_type = "t2.micro"
  region        = "eu-west-1"
  source_ami    = "ami-0abc123456789xyz"
  ssh_username  = "ubuntu"
}

build {
  sources = ["source.amazon-ebs.app-image"]
  provisioner "shell" {
    script = "app-userdata.sh"
  }
}
```

### **Run Packer to Build Image**
```sh
packer init .
packer build packer.pkr.hcl
```

## Summary
- Used **Terraform** to create App & DB VMs.
- Used **User Data Scripts** to automate app & DB setup.
- Created an **Image** using AWS CLI and **Packer** for automation.
