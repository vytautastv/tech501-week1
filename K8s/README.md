
# Kubernetes App Deployment Notes

## Overview
These notes detail the steps and necessary configuration for deploying applications on a Kubernetes cluster using Minikube. The tasks focus on setting up a basic environment with a few applications and the necessary configurations to deploy them, along with Nginx acting as a reverse proxy.

## Prerequisites
- **Minikube** installed and running.
- **kubectl** installed and configured to interact with Minikube.
- **Nginx** installed on an EC2 instance to act as a reverse proxy.

---

## Steps to Deploy Applications

### 1. Start Minikube (If Restarted)
If your EC2 instance or the system running Minikube is restarted, use the following commands to quickly start up Minikube and deploy the applications.

```bash
minikube start  # Start Minikube
kubectl apply -f <your-kubernetes-manifest-file>.yaml  # Apply your Kubernetes configurations (deployment, service, etc.)
```

- Make sure to replace `<your-kubernetes-manifest-file>.yaml` with your Kubernetes YAML file that contains the deployment and service definitions.

### 2. Expose Services
If you are using **NodePort** or **LoadBalancer** to expose your services, use the following commands to check and get the external IP or port for access.

```bash
kubectl get svc  # Get the services to find external IP and port
```

- For **NodePort** services, you can access them via `http://<minikube_ip>:<nodeport>`.
- For **LoadBalancer** services, the IP will be provided automatically.

### 3. Verify Pods are Running
To ensure that the pods are running properly, use the following command:

```bash
kubectl get pods -o wide  # Check the status of all the pods in the cluster
```

### 4. Check Logs (If Needed)
If any of your applications are not functioning as expected, you can check the logs of the pods to identify the issue:

```bash
kubectl logs <pod_name>  # Replace <pod_name> with the name of the pod to check logs
```

### 5. Configure Nginx Reverse Proxy (On EC2)
To access the applications through a reverse proxy, configure Nginx. For example, edit the Nginx default configuration file to set up the reverse proxy:

```bash
sudo nano /etc/nginx/sites-available/default
```

Add the following block for each app (e.g., for `hello-minikube`):

```nginx
server {
    listen 80;

    # Proxy for Hello-Minikube app (LoadBalancer)
    location /hello {
        proxy_pass http://<minikube_ip>:<loadbalancer_port>;  # Replace with the actual minikube IP and port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Once the configuration is updated, reload Nginx:

```bash
sudo nginx -t  # Test configuration
sudo systemctl restart nginx  # Restart Nginx to apply changes
```

### 6. Test the Application
After configuring the reverse proxy and restarting Nginx, use `curl` or a browser to access the applications:

```bash
curl -v http://localhost/hello/  # Test accessing the application through Nginx
```

---

## Troubleshooting
- **502 Bad Gateway**: This usually happens when the reverse proxy cannot reach the upstream server (application). Ensure the services are running and the correct IP/port is used in the Nginx configuration.
- **CrashLoopBackOff**: This means a pod is continuously failing. Check the pod logs to understand why:

```bash
kubectl logs <pod_name>
```

If necessary, delete the pod, and Kubernetes will automatically create a new one:

```bash
kubectl delete pod <pod_name>
```

---

## Commands to Quickly Get the Apps Running After EC2 Restart

1. **Start Minikube:**

   ```bash
   minikube start
   ```

2. **Apply your Kubernetes YAML file (to deploy apps):**

   ```bash
   kubectl apply -f <your-kubernetes-manifest-file>.yaml
   ```

3. **Expose services:**

   ```bash
   kubectl get svc
   ```

4. **Check the status of the pods:**

   ```bash
   kubectl get pods -o wide
   ```

5. **Check the logs for troubleshooting (if needed):**

   ```bash
   kubectl logs <pod_name>
   ```

---

## Kubernetes Overview

Kubernetes is a container orchestration platform that automates the deployment, scaling, and management of containerized applications. It abstracts away the underlying infrastructure and allows users to run applications in isolated, containerized environments called **pods**. These pods can contain one or more containers and are managed by **Deployments** to ensure scalability and availability.

Key concepts include:
- **Pods**: The smallest and simplest Kubernetes object that represents a set of running containers.
- **Deployments**: Manage the lifecycle of pods, ensuring the correct number of replicas are running at all times.
- **Services**: Expose and manage access to the pods (e.g., LoadBalancer, NodePort, ClusterIP).
- **Ingress**: Manages external HTTP and HTTPS access to services in a Kubernetes cluster, often used with Nginx or other reverse proxies.

By using Kubernetes, you can manage applications efficiently, automatically scale them based on demand, and easily roll back or update them when needed.

---

## Nginx Configuration for Reverse Proxy

### Default Configuration
The default Nginx configuration file, typically found at `/etc/nginx/sites-available/default`, can be updated to route traffic to the appropriate Kubernetes services running on Minikube.

For instance:

```nginx
server {
    listen 80;

    # Proxy for Hello-Minikube app (LoadBalancer)
    location /hello {
        proxy_pass http://<minikube_ip>:<loadbalancer_port>;  # Replace with the actual minikube IP and port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Proxy for App2 (LoadBalancer)
    location /app2 {
        proxy_pass http://<minikube_ip>:<app2_port>;  # Replace with the actual app2 service details
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Applying Configuration Changes

After making the changes to the configuration file, test the configuration and restart Nginx to apply:

```bash
sudo nginx -t  # Test the configuration for errors
sudo systemctl restart nginx  # Restart Nginx to apply the changes
```

---

# Kubernetes Deployment Notes

## Day 1

### 1. Research Kubernetes
Kubernetes (K8s) is an open-source container orchestration platform designed to automate deployment, scaling, and operations of application containers.

- Manages containerised applications across multiple hosts
- Provides self-healing, load balancing, and service discovery
- Works with Docker and other container runtimes

Useful resources:
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)

---

### 2. Get Kubernetes Running Using Docker Desktop

#### Steps:
1. Install **Docker Desktop** from [Docker website](https://www.docker.com/products/docker-desktop/).
2. Enable **Kubernetes** in Docker Desktop:
   - Open Docker Desktop settings
   - Navigate to **Kubernetes** tab
   - Check **Enable Kubernetes**
   - Apply changes and wait for Kubernetes to start

3. Verify Kubernetes is running:
   ```sh
   kubectl get nodes
   kubectl cluster-info
   ```

---

### 3. Nginx Deployment with NodePort Service

#### a. Create Nginx Deployment
```sh
kubectl create deployment nginx --image=nginx
```

#### b. Expose Nginx using a NodePort Service
```sh
kubectl expose deployment nginx --port=80 --type=NodePort
```

#### c. See What Happens When We Delete a Pod
```sh
kubectl get pods  # List pods
kubectl delete pod <nginx-pod-name>  # Delete a pod
kubectl get pods  # Verify a new pod is created automatically
```

#### d. Increase Replicas with No Downtime
```sh
kubectl scale deployment nginx --replicas=3
kubectl get pods  # Check new replicas
```

#### e. Delete Kubernetes Deployments and Services
```sh
kubectl delete deployment nginx
kubectl delete service nginx
```

---

### 4. Deploy NodeJS Sparta Test App

#### Clone the Repository & Deploy
```sh
git clone https://github.com/your-org/sparta-app.git
cd sparta-app
kubectl apply -f k8s/sparta-deployment.yaml
kubectl apply -f k8s/sparta-service.yaml
```

## Day 2

### 1. Persisting Data for Database Deployment

#### a. Create 2-Tier Deployment with Persistent Volume (PV)
1. Define **Persistent Volume (PV) and Persistent Volume Claim (PVC)** in YAML
2. Deploy a database pod that uses the PVC

Example PV & PVC YAML:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: db-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"

---

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: db-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Apply configuration:
```sh
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
```

#### b. Research Types of Autoscaling in Kubernetes
- **Horizontal Pod Autoscaler (HPA)** – Scales pods based on CPU/memory utilisation.
- **Vertical Pod Autoscaler (VPA)** – Adjusts CPU/memory requests of pods dynamically.
- **Cluster Autoscaler** – Adds/removes nodes based on resource demand.

---

### 2. Autoscaling

#### a. Use HPA to Scale the App
Enable autoscaling for the Sparta test app:
```sh
kubectl autoscale deployment sparta-app --cpu-percent=50 --min=2 --max=10
```

Check HPA status:
```sh
kubectl get hpa
```

---

### 3. Persisting Data for Database Deployment (Continued)

#### a. Remove PVC and Retain Data in PV
```sh
kubectl delete pvc db-pvc  # Deletes PVC but retains PV data
```

## Day 3

### 1. Deploy the Sparta Test App on the Cloud

#### a. Setup Minikube on a Cloud Instance
```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube start --driver=none
```

#### b. Deploy Three Apps on One Cloud Instance
```sh
kubectl apply -f app1.yaml
kubectl apply -f app2.yaml
kubectl apply -f app3.yaml
```

#### c. Deploy Sparta Test App in the Cloud
```sh
kubectl apply -f sparta-cloud.yaml
```

## Restart EC2 & Quickly Restore Kubernetes Apps

### Step 1: Start Minikube on EC2
```sh
minikube start --driver=none
```

### Step 2: Reapply Deployments & Services
```sh
kubectl apply -f k8s/
```

### Step 3: Verify Everything is Running
```sh
kubectl get pods
kubectl get svc
```
