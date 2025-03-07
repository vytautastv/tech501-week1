# Ansible Basics and Cheatsheet

## What is Ansible?
Ansible is an open-source automation tool used for configuration management, application deployment, and task automation. It uses YAML-based playbooks to define automation workflows.

### Key Features:
- Agentless (uses SSH to connect to remote machines)
- Declarative configuration management
- Idempotent (ensures tasks only change state when needed)
- Scalable and easy to learn

---

## Getting Started with Ansible

### Installation
Install Ansible on a control node (typically your local machine or a dedicated server):
```bash
sudo apt update
sudo apt install ansible -y
```
Verify installation:
```bash
ansible --version
```

### Inventory
Ansible uses an inventory file to define remote hosts:
```ini
[web]
34.253.182.108 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

[db]
172.31.62.47 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

### Ad-hoc Commands
Run commands on remote hosts without a playbook:
```bash
ansible all -m ping
ansible web -a "uptime"
```

### Playbook Structure
A playbook is a YAML file that defines automation steps.
```yaml
- name: Install Nginx
  hosts: web
  become: true
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
```
Run the playbook:
```bash
ansible-playbook playbook.yml -i inventory.ini
```

---

## Application and Database Provisioning

### Application Server Provisioning (Node.js App)
#### Steps:
1. Install dependencies (Node.js, Git, PM2, UFW)
2. Clone the repository into `/home/ubuntu/sparta-app`
3. Install npm dependencies
4. Set environment variables
5. Start the app using PM2
6. Configure a reverse proxy using PM2

#### Ansible Playbook (Snippet)
```yaml
- name: Provision Application Server
  hosts: web
  become: true
  tasks:
    - name: Install required packages
      apt:
        name:
          - curl
          - nodejs
          - npm
          - git
          - ufw
        state: present

    - name: Clone app repository
      git:
        repo: "https://github.com/your-repo.git"
        dest: "/home/ubuntu/sparta-app"

    - name: Install npm dependencies
      shell: npm install
      args:
        chdir: "/home/ubuntu/sparta-app"

    - name: Set DB_HOST environment variable
      lineinfile:
        path: "/home/ubuntu/sparta-app/.env"
        line: "DB_HOST=your-db-host"
        create: yes

    - name: Install PM2 globally
      npm:
        name: pm2
        global: yes

    - name: Start the app with PM2
      shell: "pm2 start app.js --name sparta-app"
      args:
        chdir: "/home/ubuntu/sparta-app"
```

### Database Provisioning (MongoDB)
#### Steps:
1. Install MongoDB
2. Enable and start the service
3. Allow remote access (if needed)
4. Create a database user

#### Ansible Playbook (Snippet)
```yaml
- name: Install and Configure MongoDB
  hosts: db
  become: true
  tasks:
    - name: Install MongoDB
      apt:
        name: mongodb
        state: present

    - name: Start and enable MongoDB service
      service:
        name: mongodb
        state: started
        enabled: yes

    - name: Allow remote access
      lineinfile:
        path: /etc/mongodb.conf
        line: "bind_ip = 0.0.0.0"
        regexp: "^bind_ip"

    - name: Restart MongoDB
      service:
        name: mongodb
        state: restarted
```

### Notes on Writing Ansible Scripts
- Use `become: true` for root privileges.
- Always define hosts in `inventory.ini`.
- Test playbooks with `--check` mode before running them.
- Use `handlers` for tasks that should only run when changes occur.
- Store secrets securely using Ansible Vault.

---

