Markdown

# Cloning a GitHub Repository to an Azure VM

## 1. Install Git on the VM

1. **Update package lists:**
   ```bash
   sudo apt update

Install Git:
sudo apt install git -y

Verify installation:
git --version

2. **Clone a GitHub Repository**

SSH into your VM:
ssh -i ~/.ssh/<your_key.pem> adminuser@<your_VM_IP>

Clone the repository:
git clone <repository_url>

Navigate to the repository directory:
cd <repository_name>

Install dependencies:
npm install

Start the application:
npm start

Access the server:
Access the server in your web browser by navigating to: http://<your_VM_IP>:3000

Note: Replace <your_key.pem>, <your_VM_IP>, and <repository_url> with your actual values.
