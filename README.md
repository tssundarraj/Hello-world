## 🚀 **Deploying NGINX "Hello World" in MicroK8s using Docker and Expose it using  Ingress**

This guide will walk you through building a Dockerfile using the
official **NGINX** image, pushing it to the **local MicroK8s Docker
registry**, and deploying it with **Ingress** for external access.
---


![Project Diagram](https://github.com/tssundarraj/hello-nginx/blob/main/Images/docker-inginx_image.png)

## 📖 Project Overview
This project demonstrates how to:
- **Build and run** an Nginx container using Docker.
- **Deploy** the containerized application on a MicroK8s cluster.
- **Expose the application** to access it via a web browser.

---

## 🛠️ Prerequisites
Before running this project, ensure you have the following installed:
- [Docker](https://docs.docker.com/get-docker/)
- [MicroK8s](https://microk8s.io/docs)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

---
## 🐳 Install Docker on Ubuntu
🐳 Install Docker Engine
``` sh
# Update the Packages
sudo apt-get update && sudo apt-get upgrade -y
# Install Docker Engine
sudo apt-get install docker.io

```
🔧 Add User to Docker Group (Run Without sudo)

``` sh
# Create Docker Group (If Not Exists)

sudo groupadd docker

# Add Current User to Docker Group

sudo usermod -aG docker $USER

# Apply Group Changes (Logout and login again or run the command below)
newgrp docker

```
## 🚢 Install MicroK8s

🚢 Install MicroK8s
``` sh
# Install MicroK8s using Snap

sudo snap install microk8s --classic

# Add Current User to MicroK8s Group

sudo usermod -aG microk8s $USER

# Apply Group Changes

newgrp microk8s

# Verify MicroK8s Installation

microk8s status --wait-ready
```


## 🚀 Deployment Steps

---

## **Step 1: Create a Project Structure**

```bash
/hello-world-nginx\
├── index.html\
├── Dockerfile\
├── deployment.yaml\
├── ingress.yaml
```
## **Step 2: Create the HTML File (index.html)**

```bash
<!DOCTYPE html> 
<html> 
<head> 
    <title>NGINX Hello World</title> 
</head> 
<body> 
    <h1>Hello from NGINX in MicroK8s!</h1> 
</body> 
</html>
```

## **Step 3: Create the Dockerfile**

``` sh
FROM nginx:alpine\
COPY index.html /usr/share/nginx/html/index.html\
EXPOSE 80\
CMD \[\"nginx\", \"-g\", \"daemon off;\"\]
```

## **Step 4: Build and Push the Docker Image**

### **1. Build the Image**

``` sh
docker build -t localhost:32000/nginx-hello-world:latest .
```

### **2. Enable the MicroK8s Registry**
 
``` sh
microk8s enable registry
```

### **3. Push the Image to the Local Registry**

``` sh
docker tag localhost:32000/nginx-hello-world:latest
localhost:32000/nginx-hello-world:latest\
docker push localhost:32000/nginx-hello-world:latest

```

## **Step 5: Create the Deployment and Service (deployment.yaml)**

``` sh

apiVersion: apps/v1 
kind: Deployment 
metadata: 
  name: nginx-hello-world 
spec: 
  replicas: 2 
  selector: 
    matchLabels: 
      app: nginx-hello-world 
  template: 
    metadata: 
      labels: 
        app: nginx-hello-world 
    spec: 
      containers: 
      - name: nginx-hello-world 
        image: localhost:32000/nginx-hello-world:latest 
        ports: 
        - containerPort: 80 
--- 
apiVersion: v1 
kind: Service 
metadata: 
  name: nginx-service 
spec: 
  selector: 
    app: nginx-hello-world 
  ports: 
  - protocol: TCP 
    port: 80 
    targetPort: 80 
  ```

## **Step 7: Apply the Resources**

``` sh

microk8s kubectl apply -f deployment.yaml\
microk8s kubectl apply -f ingress.yaml
```

## **Step 8: Update /etc/hosts for Local Access**

``` sh

#Add this entry in your /etc/hosts file:
127.0.0.1 nginx.local
```

## **Step 9: Access the Application**

Open your browser and visit: 👉
[**http://nginx.local**](http://nginx.local)

## **Step 10: Troubleshooting Tips**

✅ **Check Pod Status:**
 ``` sh

microk8s kubectl get pods
```

✅ **Check Ingress Status:**
``` sh

microk8s kubectl get ingress
```

✅ **Check Logs:**
``` sh

microk8s kubectl logs \<pod-name\>
```

If you encounter issues or want to enhance this setup with HTTPS,
scaling, or more advanced configurations, let me know! 🚀
