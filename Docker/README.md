# Docker Learning Guide

## 1. Install Docker Desktop
### **Steps to Install Docker Desktop:**
1. **Download Docker Desktop** from [Docker's official site](https://www.docker.com/products/docker-desktop/).
2. Run the installer and follow the on-screen instructions.
3. After installation, restart your computer if required.
4. Open Docker Desktop and ensure it is running.
5. Verify installation by running:
   ```sh
   docker --version
   ```

## 2. Research Microservices, Containers, and Docker
### **Task:**
- Understand **Microservices**: Small, independent services that work together.
- Learn about **Containers**: Lightweight, portable environments for applications.
- Study **Docker**: A tool for managing containers efficiently.

Useful resources:
- [Docker Documentation](https://docs.docker.com/)
- [Microservices on AWS](https://aws.amazon.com/microservices/)
- [Containers Explained](https://kubernetes.io/docs/concepts/)

## 3. Learn to Manage Docker Containers Locally
### **a. Run and Pull Your First Image**
1. Open a terminal and run:
   ```sh
   docker run hello-world
   ```
2. This will download and execute the `hello-world` image.

### **b. Run Nginx Web Server in a Docker Container**
1. Pull and run the official Nginx image:
   ```sh
   docker run --name my-nginx -d -p 8080:80 nginx
   ```
2. Access the web server at `http://localhost:8080`.

### **c. Remove a Container**
1. Stop the container:
   ```sh
   docker stop my-nginx
   ```
2. Remove the container:
   ```sh
   docker rm my-nginx
   ```

### **d. Modify the Nginx Default Page in Running Container**
1. Copy the default page from the container:
   ```sh
   docker cp my-nginx:/usr/share/nginx/html/index.html ./index.html
   ```
2. Edit `index.html` and modify the content.
3. Copy it back into the running container:
   ```sh
   docker cp index.html my-nginx:/usr/share/nginx/html/index.html
   ```
4. Refresh `http://localhost:8080` to see changes.

### **e. Run a Different Container on a Different Port**
1. Run an Nginx container on port `9090`:
   ```sh
   docker run --name nginx-9090 -d -p 9090:80 nginx
   ```
2. Access the new instance at `http://localhost:9090`.

## 4. Use Docker Hub to Host Custom Images
### **a. Push Custom Static Webpage Container Image to Docker Hub**
1. Log in to Docker Hub:
   ```sh
   docker login
   ```
2. Tag your image:
   ```sh
   docker tag my-static-site YOUR_DOCKERHUB_USERNAME/my-static-site:latest
   ```
3. Push the image:
   ```sh
   docker push YOUR_DOCKERHUB_USERNAME/my-static-site:latest
   ```

### **b. Automate Docker Image Creation Using a Dockerfile**
1. Create a `Dockerfile`:
   ```dockerfile
   FROM nginx:latest
   COPY index.html /usr/share/nginx/html/index.html
   ```
2. Build the image:
   ```sh
   docker build -t my-custom-nginx .
   ```
3. Run the container:
   ```sh
   docker run -d -p 8080:80 my-custom-nginx
   ```
4. Access it at `http://localhost:8080`.

## 5. Research and Document

### **Differences Between Virtualisation and Containerisation**
#### **What is Usually Included in a Container vs Virtual Machine?**
- **Virtual Machines (VMs)** include a full operating system, virtualized hardware, and an application.
- **Containers** include only the application and its dependencies, sharing the host OS kernel.

#### **Benefits of Each**
- **Virtual Machines**
  - Provide complete OS isolation.
  - Useful for running different OS environments on the same hardware.
  - More secure but heavier on resources.
- **Containers**
  - Lightweight and faster to start.
  - Portable across different environments.
  - Efficient use of system resources.

### **Microservices**
#### **What Are They?**
- A software architecture pattern where an application is broken into small, independent services that communicate over a network.

#### **How Are They Made Possible?**
- By using APIs, containerization, and cloud-native technologies such as Kubernetes and Docker.

#### **Benefits**
- Scalability and flexibility.
- Easier maintenance and deployment.
- Improved fault tolerance.

### **Docker**
#### **What is it?**
- A platform that allows developers to create, deploy, and run applications in containers.

#### **Alternatives**
- **Podman** – A daemonless container engine.
- **LXC (Linux Containers)** – Lightweight virtualization at the OS level.
- **Kubernetes** – Orchestration system for containerized applications.

#### **How it Works (Docker Architecture/API)**
- **Docker Engine** runs and manages containers.
- **Docker CLI/API** interacts with the engine.
- **Docker Hub/Registry** stores container images.

#### **Success Story Using Docker**
- **Netflix, Spotify, and PayPal** use Docker to scale microservices, speed up deployments, and enhance DevOps efficiency.

---
### **Summary**
- Installed **Docker Desktop**.
- Researched **microservices, containers, and Docker**.
- Learned **basic Docker container management**.
- Deployed and modified **Nginx in Docker**.
- Pushed **custom images to Docker Hub**.
- Automated image creation using a **Dockerfile**.
- Researched **virtualisation vs. containerisation**, **microservices**, and **Docker architecture**.


# Docker Notes & Cheatsheet

### 🔹 Managing Images
```sh
# List all locally available images
docker images

# Pull an image from Docker Hub
docker pull <image-name>

# Remove an image
docker rmi <image-name>
```

### 🔹 Running Containers
```sh
# Run a container from an image
docker run -d -p 8080:80 --name mycontainer nginx

# List running containers
docker ps

# List all containers (including stopped ones)
docker ps -a

# Stop a running container
docker stop <container-id>

# Remove a container
docker rm <container-id>
```

### 🔹 Working with Containers
```sh
# View logs of a running container
docker logs <container-id>

# Access a running container’s shell
docker exec -it <container-id> /bin/sh  # or /bin/bash for Ubuntu-based images
```

### 🔹 Managing Volumes
```sh
# List all volumes
docker volume ls

# Create a volume
docker volume create myvolume

# Remove a volume
docker volume rm myvolume
```

### 🔹 Cleaning Up
```sh
# Remove all stopped containers
docker container prune

# Remove all unused images
docker image prune

# Remove all unused volumes
docker volume prune
```

---

## 🛠️ Using Docker Compose
Docker Compose allows you to define multi-container applications in a `docker-compose.yaml` file.

### 🔹 Installation
Ensure Docker Compose is installed:
```sh
docker-compose --version
```
If not installed, follow the official [Docker documentation](https://docs.docker.com/compose/install/).

### 🔹 Example `docker-compose.yaml`
```yaml
version: '3.8'
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: example
```

### 🔹 Common Docker Compose Commands
```sh
# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# View running services
docker-compose ps

# View logs
docker-compose logs -f
```

### 🔹 Running Commands Inside a Service
```sh
# Execute a command inside a running service
docker-compose exec <service-name> <command>
```
Example:
```sh
docker-compose exec web sh
```

---

## 🎯 Summary
- Docker simplifies application deployment with **containers**.
- Use `docker` commands to manage **images, containers, and volumes**.
- **Docker Compose** helps manage multi-container applications efficiently
