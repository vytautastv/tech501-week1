# Setting Up Nginx with a Bash Script
---

## Steps

### 1. Create a Bash Script
- Open a terminal and create a new file for the bash script:
  ```bash
  nano provision.sh

# Update and upgrade system packages
echo "Updating and upgrading system packages..."
sudo apt update && sudo apt upgrade -y

# Install Nginx
echo "Installing Nginx..."
sudo apt install nginx -y

# Restart Nginx
echo "Restarting Nginx service..."
sudo systemctl restart nginx

# Enable Nginx to start on boot
echo "Enabling Nginx service to start on boot..."
sudo systemctl enable nginx

# Print completion message
echo "Nginx installation and setup completed successfully!"

### 2. Make the Script Executable
chmod +x provision.sh

### 3. Run the Script
./provision.sh

### 4. Verify nginx installation
sudo systemctl status nginx

### 5. Restart if needed
sudo systemctl restart nginx

### 6. Access the server
After completing the setup, visit your server’s IP address in a web browser.
