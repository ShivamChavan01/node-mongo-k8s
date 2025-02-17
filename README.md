# Node.js & MongoDB Kubernetes Deployment

## Overview
This project is a Node.js application connected to a MongoDB database, both running as separate containers within a Kubernetes cluster. The application exposes an API to store and retrieve emails in MongoDB.

## Technologies Used
- **Node.js**: Backend API development
- **MongoDB**: NoSQL Database
- **Docker**: Containerization
- **Kubernetes**: Orchestration and deployment
- **Express.js**: Web framework for Node.js
- **Mongoose**: MongoDB object modeling for Node.js

## Prerequisites
Ensure you have the following installed:
- **Node.js** and **npm**
- **Docker**
- **Kubernetes (kubectl & minikube or a cluster provider)**
- **Git**

## Project Structure
```
node-mongo-k8s/
│── src/
│   ├── index.js  # Main server file
│── index.html    # Simple frontend
│── Dockerfile    # Container image instructions
│── deployment.yaml  # Kubernetes deployment and service config
│── configmap.yaml  # MongoDB configuration variables
│── pv.yaml  # Persistent Volume configuration
│── pvc.yaml  # Persistent Volume Claim configuration
│── README.md  # Project documentation
```

## Setup Instructions
### 1️⃣ Clone the Repository
```sh
git clone https://github.com/ShivamChavan01/node-mongo-k8s.git
cd node-mongo-k8s
```

### 2️⃣ Build and Run with Docker (Optional for local testing)
```sh
docker build -t shivamchavan01/db-demo-app .
docker run -p 3000:3000 -e MONGO_HOST=localhost -e MONGO_PORT=27017 shivamchavan01/db-demo-app
```

### 3️⃣ Deploy to Kubernetes
#### Create a ConfigMap for MongoDB connection details
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongo-config
  namespace: default
data:
  mongoHost: mongo-db
  mongoPort: "27017"
```
Apply the ConfigMap:
```sh
kubectl apply -f configmap.yaml
```

#### Create Persistent Volume and Persistent Volume Claim
**Persistent Volume (PV)**:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/
    type: DirectoryOrCreate
```
Apply the PV:
```sh
kubectl apply -f pv.yaml
```

**Persistent Volume Claim (PVC)**:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-pvc
spec:
  volumeName: host-pv
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: ""
```
Apply the PVC:
```sh
kubectl apply -f pvc.yaml
```

#### Apply Deployment and Service
```sh
kubectl apply -f deployment.yaml
```

#### Verify Everything is Running
```sh
kubectl get pods
kubectl get services
kubectl get pv,pvc  # Check if the PVC is bound to the PV
```

### 4️⃣ Access the Application
If using Minikube:
```sh
minikube service my-service-node-app
```
Otherwise, find the external IP:
```sh
kubectl get services my-service-node-app
```

## Troubleshooting
- **Pod CrashLoopBackOff**: Check logs with `kubectl logs <pod-name>`
- **Service Not Accessible**: Ensure proper port mapping in `deployment.yaml`
- **Persistent Volume Not Bound**: Check PVC status with `kubectl describe pvc host-pvc`
- **Git Push Rejected**: Use `git pull --rebase origin main` before pushing

