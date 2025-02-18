### 1. Provider Setup

```hcl
provider "aws" {
  region = "eu-west-1"  # Specify the region you want to launch the EC2 in
}
```

Explanation: This block configures the AWS provider, specifying the region where the EC2 instance will be launched. `eu-west-1` refers to the Ireland region.
**Important:** Choose the region closest to you for better latency.

### 2. Security Group

```hcl
resource "aws_security_group" "tech501_vy_jenkins_sg" {
  name        = "tech501-vy-jenkins-sg"
  description = "Allow inbound traffic for Jenkins server"
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # SSH access
  }

  ingress {
    from_port   = 3000
    to_port     = 3000
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # Jenkins HTTP access on port 3000
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # Allow all outbound traffic
  }
}
```

Explanation: This block defines a security group named `tech501-vy-jenkins-sg`. Security groups act as virtual firewalls for your EC2 instances.

- **ingress blocks:** Define inbound rules.
  - **Port 22:** Allows SSH access from anywhere (`0.0.0.0/0`).
    - **Security Best Practice:** Restrict this to your IP address or a known CIDR block for better security.
  - **Port 3000:** Allows HTTP access to Jenkins from anywhere.
    - **Security Best Practice:** Restrict access after initial setup.
- **egress block:** Defines outbound rules. This allows all outbound traffic, which is generally the default.

### 3. EC2 Instance

```hcl
resource "aws_instance" "jenkins_instance" {
  ami           = "ami-0417261d92cf9faf0"  # Ubuntu 22.04 LTS AMI (update this to the latest version)
  instance_type = "t3.micro"  # Instance size (update according to your needs)
  key_name      = "tech501-vytautas-aws-key" # Specify your EC2 key pair name
  security_groups = [aws_security_group.tech501_vy_jenkins_sg.name]
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update -y",
      "sudo apt-get install openjdk-11-jdk -y",
      "sudo apt-get install wget -y",
      "wget -q -O - https://pkg.jenkins.io/keys/jenkins.io.key | sudo tee /etc/apt/trusted.gpg.d/jenkins.asc",
      "echo deb http://pkg.jenkins.io/debian/ stable main | sudo tee /etc/apt/sources.list.d/jenkins.list",
      "sudo apt-get update -y",
      "sudo apt-get install jenkins -y",
      "sudo systemctl enable jenkins",
      "sudo systemctl start jenkins",
      "sudo ufw allow 3000/tcp",
      "sudo ufw reload"
    ]
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("/Users/vytautastvarijonas/.ssh/tech501-vytautas-aws-key.pem")
      host        = self.public_ip
    }
  }
  tags = {
    Name = "Jenkins-EC2"
  }
}
```

Explanation: This block creates the EC2 instance.

- **ami:** Specifies the Amazon Machine Image (AMI). `ami-0417261d92cf9faf0` is an example (Ubuntu 22.04 LTS).
  - **Important:** Always use the latest, recommended AMI for your chosen operating system. You can find the latest AMIs in the AWS console or using the AWS CLI.
- **instance_type:** Defines the instance size. `t3.micro` is a small, cost-effective instance. Choose an instance type appropriate for your Jenkins workload.
- **key_name:** Specifies the EC2 key pair for SSH access. You must have this key pair created in your AWS account.
- **security_groups:** Attaches the security group created earlier to the instance.
- **tags:** Adds tags for easier identification and management.

### 4. Provisioner (Inside aws_instance)

Explanation: This block uses a `remote-exec` provisioner to run commands on the EC2 instance after it's created.

Contains the commands to execute:

- `sudo apt-get update -y`: Updates the package list.
- `sudo apt-get install openjdk-11-jdk -y`: Installs Java (required for Jenkins).
- `sudo apt-get install wget -y`: Installs wget (for downloading the Jenkins key).
- `wget ... | sudo tee ...`: Downloads and adds the Jenkins repository key.
- `echo ... | sudo tee ...`: Adds the Jenkins repository to the apt sources.
- `sudo apt-get update -y`: Updates the package list again.
- `sudo apt-get install jenkins -y`: Installs Jenkins.
- `sudo systemctl enable jenkins`: Enables Jenkins to start on boot.
- `sudo systemctl start jenkins`: Starts the Jenkins service.
- `sudo ufw allow 3000/tcp`: Opens port 3000 in the Uncomplicated Firewall (ufw).
- `sudo ufw reload`: Reloads the firewall rules.

**connection:** Defines how to connect to the instance.

- `type = "ssh"`: Uses SSH.
- `user = "ubuntu"`: The username on the EC2 instance.
- `private_key`: The path to your private key file.
  - **Important:** Ensure this path is correct.
- `host = self.public_ip`: The public IP address of the EC2 instance.

### 5. Output

```hcl
output "jenkins_public_ip" {
  value = aws_instance.jenkins_instance.public_ip
}
```

Explanation: This block outputs the public IP address of the Jenkins instance after it's created, making it easy to access.

### Key Improvements and Considerations

- **Security:** Restrict SSH access to your IP address or a known CIDR block. After initial Jenkins setup, consider restricting access to port 3000 as well. Implement proper authentication and authorization within Jenkins itself.
- **AMI:** Always use the latest, recommended AMI.
- **Instance Type:** Choose an appropriate instance type based on your needs. `t3.micro` is suitable for testing, but you might need a larger instance for production.
- **Key Pair:** Ensure you have the correct key pair and that the path to the private key is accurate.
- **Provisioner:** While `remote-exec` works, consider using a more robust configuration management tool like Ansible or Chef for complex setups. This will make your infrastructure more manageable and repeatable.
- **User Data:** For simple installations, you can also use user data instead of provisioners. This is a cloud-init script that runs when the instance starts.
