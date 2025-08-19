# 📝 Notes App Deployment with Kubernetes

This project demonstrates how I containerized and deployed a Python/Django Notes Application using **Docker** and **Kubernetes** on an AWS EC2 instance.  

---

## 📌 Project Architecture

![Architecture Diagram](./)

---

## 🚀 Tech Stack
- **Python 3.9 / Django** (Backend Application)
- **Docker** (Containerization)
- **DockerHub** (Image Registry)
- **Kubernetes** (Deployment, Service, Namespace)
- **AWS EC2** (Cloud Infrastructure)
- **GitHub** (Source Code Management)

---

## ⚙️ Steps to Run

### 1️⃣ Clone the Repository
```bash
git clone <repo-url>
cd <repo-folder>
git checkout dev

2️⃣ Create Docker Image

# Dockerfile
FROM python:3.9

WORKDIR /app/backend

COPY requirements.txt /app/backend
RUN pip install -r requirements.txt

COPY . /app/backend

EXPOSE 8000
CMD python /app/backend/manage.py runserver 0.0.0.0:8000

# Build Docker image
docker build -t notes-app-k8s .

# Tag image for DockerHub
docker tag notes-app-k8s:latest intiquab1404/notes-app-k8s:latest

# Push to DockerHub
docker push intiquab1404/notes-app-k8s:latest

3️⃣ Kubernetes Setup

Create a folder for Kubernetes manifests:

mkdir k8s && cd k8s

Namespace (namespace.yml)

apiVersion: v1
kind: Namespace
metadata:
  name: notes-app

Deployment (deployment.yml)

apiVersion: apps/v1
kind: Deployment
metadata:
  name: notes-app-deployment
  namespace: notes-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: notes-app
  template:
    metadata:
      labels:
        app: notes-app
    spec:
      containers:
      - name: notes-app
        image: intiquab1404/notes-app-k8s:latest
        ports:
        - containerPort: 8000

Service (service.yml)

apiVersion: v1
kind: Service
metadata:
  name: notes-app-service
  namespace: notes-app
spec:
  selector:
    app: notes-app
  ports:
    - protocol: TCP
      port: 8000
      targetPort: 8000
  type: ClusterIP

4️⃣ Apply Kubernetes Manifests

kubectl apply -f namespace.yml
kubectl apply -f deployment.yml
kubectl apply -f service.yml

5️⃣ Verify Pods & Services

kubectl get pods -n notes-app
kubectl get svc -n notes-app

6️⃣ Access Application

Forward port to local machine:

kubectl port-forward service/notes-app-service -n notes-app 8000:8000 --address=0.0.0.0

➡️ Update AWS EC2 inbound rules to allow port 8000
➡️ Access the app in browser:

http://<EC2-Public-IP>:8000

🎯 Outcome

✅ Django Notes App running successfully on Kubernetes
✅ Accessible via AWS EC2 Public IP at port 8000
📖 Learnings

    Containerization with Docker

    DockerHub registry usage

    Kubernetes Namespaces, Deployments, and Services

    Cloud networking with AWS EC2


--
