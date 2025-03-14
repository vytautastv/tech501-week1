
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

## Conclusion

These steps should help you deploy applications to a Kubernetes environment using Minikube, expose services via Nginx, and troubleshoot common deployment issues. With Kubernetes, you can easily manage the deployment, scaling, and maintenance of your applications while benefiting from containerization. Use the above commands to quickly recover and ensure your applications are running after an EC2 instance restart.

---
