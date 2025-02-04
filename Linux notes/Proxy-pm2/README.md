## 📌 PM2 - Process Manager for Node.js

PM2 is a production process manager for Node.js applications that helps keep your application running at all times.

### ✅ Install PM2 Globally
To install PM2 globally on your system, run:

```bash
sudo npm install -g pm2

### start app using pm2:
- pm2 start app.js --name my-app

list processes:
- pm2 list

restart app:
- pm2 restart my-app

stop app:
- pm2 stop my-app

auto-start on reboot:
- pm2 save
- pm2 startup systemd


NGINX:
- sudo apt update
- sudo apt install nginx -y

config file:
sudo nano /etc/nginx/sites-available/my-node-app

add config:
server {
    listen 80;
    server_name your-app-domain-or-ip;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

enable config and restart:
sudo ln -s /etc/nginx/sites-available/my-node-app /etc/nginx/sites-enabled/
sudo nginx -t  # Test Nginx configuration
sudo systemctl restart nginx

visit site:
http://your-app-domain-or-ip/
