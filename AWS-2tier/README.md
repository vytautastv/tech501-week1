## App/DB 2 tier aws:

**Keypair in AWS**
1.	Go to AWS Console → EC2 Dashboard
	2.	Click Key Pairs under Network & Security
	3.	Click Create Key Pair
	4.	Enter Key Pair Name (e.g., tech501-key)
	5.	Select Key Type → Choose RSA
	6.	Select Private Key Format → Choose .pem
	7.	Click Create Key Pair
	8.	Download the .pem file and store it securely

**Create EC2 Instances**
Step 1: Open the EC2 Dashboard
	1.	Go to AWS Console → EC2 Dashboard
	2.	Click Launch Instances

Step 2: Configure the Instances
	Name:
	•	App VM → tech501-app
	•	DB VM → tech501-db
	Choose an Amazon Machine Image (AMI):
	•	Select Ubuntu 22.04 LTS (Recommended for most cases)
	Choose Instance Type:
	•	t2.micro (Free-tier eligible) or t3.micro (for better performance)
	Key Pair:
	•	Select tech501-key (created earlier)
	Network Settings:
	•	Select default VPC or create a new one
	Security Group:
	•	App VM: Allow SSH (22), HTTP (80), HTTPS (443)
	•	DB VM: Allow SSH (22), MongoDB (27017 only for App VM)
	Storage:
	•	Keep default 8GB (increase if needed)

Step 3: Launch the Instances
	1.	Click Launch Instance
	2.	Wait for the instances to be Running
	3.	Copy the Private IP of the DB VM (needed for connecting the app)

**App**:

1. SSH into the AWS vm
2. Sudo apt update -y
3. Sudo apt install -y nodejs npm

**Clone repo to vm**:
* ssh-keygen -t rsa -b 4096 -C vytautastv98@gmail.com
* Mkdir repo
* Cd repo
* sudo chown -R ubuntu:ubuntu /repo
* git clone git@github.com:vytautastv/tech501-sparta-app.git

**Start app**:
* cd tech501-sparta-app/
* cd nodejs20-sparta-test-app/
* Cd app
* Node app.js
* Setup db_host export
* echo $DB_HOST
* Node app.js

**Reverse proxy setup**:
- sudo apt update
- sudo apt install -y nginx
- sudo systemctl status nginx
- cd /etc/nginx/sites-available/ (configure forwarding to port 80)
- sudo nano /etc/nginx/sites-available/nodeapp
Add this to the nano file:

server {
    listen 80;
    server_name YOUR_APP_VM_PUBLIC_IP;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

- sudo ln -s /etc/nginx/sites-available/nodeapp /etc/nginx/sites-enabled/ (link config)
- sudo nginx -t (check config for errors)
- sudo systemctl restart nginx (restart to apply changes)
- Create inbound rule for port 80 in aws security group
- http://YOUR_APP_VM_PUBLIC_IP (test)


**DB setup**:
- sudo apt-get install gnupg curl
- curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \ --dearmor
- echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu noble/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
- sudo apt-get update
- sudo apt-get install -y mongodb-org=8.0.4 mongodb-org-database=8.0.4 mongodb-org-server=8.0.4 mongodb-mongosh mongodb-org-mongos=8.0.4 mongodb-org-tools=8.0.4

**Start the DB**:
- sudo systemctl start mongod
- sudo systemctl status mongod
- sudo systemctl enable mongod

**Stop DB**:
- sudo systemctl stop mongod
