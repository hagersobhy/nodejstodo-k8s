# 📝 Node.js Todo App - DevOps Internship Project

A complete DevOps pipeline demo using a Node.js Todo app, MongoDB Atlas, Docker, GitHub Actions CI/CD, and Kubernetes manifests – built for DevOps internship evaluation.

---

## 📌 Project Overview

This is a simple CRUD Todo List app built using **Node.js**, containerized using **Docker**, connected to **MongoDB Atlas**, and deployed using **Kubernetes** manifests.  
CI/CD is implemented via **GitHub Actions** to build and (optionally) push Docker images.

---

## 🛠️ Technologies Used

| Tool            | Purpose                            |
|-----------------|-------------------------------------|
| Node.js         | Backend Application                |
| MongoDB Atlas   | Cloud NoSQL Database               |
| Docker          | Containerization                   |
| Docker Hub      | Image Registry                     |
| GitHub Actions  | CI/CD (Build & Push on every Push) |
| Kubernetes (K8s)| Deployment + Service               |
| ArgoCD *(optional)* | GitOps Continuous Delivery |

---

## 📁 Repository Structure

```
├── Dockerfile # Docker build configuration
├── .env # Environment variables (MongoDB URI + PORT)
├── .dockerignore # Excludes node_modules, .env from Docker context
├── .github/workflows/ # GitHub Actions CI workflow
├── config/ # Mongoose DB connection
├── k8s/ # Kubernetes deployment manifests
├── README.md 
```
---
## 📌 Part 2 – VM Provision & Ansible Automation
- **Create a Linux VM on your local machine 
- **Use Ansible to configure the machine and install the needed packages such as Docker, Git, Kubernetes tools.
- **Ansible must run from your local machine and configure the target VM remotely.
---
## ▶️ Run the Playbook:
```
ansible-playbook -i inventory.ini install-devops-tools-centos.yml
```
---
## 📌 Outcome:
- **Docker running
- **Git installed
- **Kubernetes tools ready for cluster bootstrapping
- **SELinux in permissive mode
- **Swap disabled (required for kubeadm)
---
## 📌 Part 1 
### 1. Clone the Repo
```
git clone https://github.com/hagersobhy/nodejstodo-k8s.git
cd nodejstodo-k8s
```
### 2. Using mongoDB atlas 
Create a .env file
```
MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/todo
PORT=4000
```
### 3. Build and Run Docker Image
```
docker build -t nodejs-todo-app .
docker run -p 4000:4000 --env-file .env nodejs-todo-app
```
- ** Visit: http://localhost:4000
### 4. CI/CD Pipeline (GitHub Actions)
- Checkout source code.
- Login to Docker Hub (via Secrets).
- Build Docker image from Dockerfile.
- (Optional) Push image to Docker Hub.

## 🔐 Secrets Required
| Describe            | SecretName                            |
|-----------------|-------------------------------------|
| your Docker Hub username         |  DOCKER_USERNAME               |
| your Docker Hube password/token   | DOCKER_PASSWORD               |

Workflow file path:
```
.github/workflows/docker-build-push.yml
```
---
## 📌 Part 3
### 🐳 Docker Compose Setup
make docker-compose.yam
### 🚦 Healthcheck Explanation
- test: Uses curl to check if app is reachable.

- interval: Run healthcheck every 30 seconds.

- timeout: Give it 10s to respond.

- retries: Fail after 3 bad responses.
This helps Docker track whether the app is healthy and restart it if needed.
### 🔄 Auto-Update Using Watchtower
We use Watchtower – a lightweight container that checks your Docker Hub image and restarts the container if a new version is available.

## 🔧 Why Watchtower?
- ✅ Docker-native: Runs inside a Docker container and manages other containers seamlessly.
- 🔄 Auto-update mechanism: Detects new versions of images from Docker Hub and updates running containers automatically.
- ⏱️ Customizable polling: Allows setting polling intervals (WATCHTOWER_POLL_INTERVAL) to control how often it checks for updates.
- 🔐 Secure and isolated: Requires minimal privileges (only Docker socket access).

- 🌍 Community-trusted: Open-source, widely used in production and DevOps workflows.

- 🛠️ Lightweight: Adds minimal overhead and works well in small VMs or edge deployments.
### ▶️ How to Run Docker compose
```
docker compose up -d
```
To check logs:
```
docker logs -f watchtower
```
---
## Part 4
### ☸️ Kubernetes Deployment
K8s manifests are stored under k8s/ and include:
- You can deploy these using:
```
kubectl apply -f k8s/
kubectl get pods
kubectl get service
```
### ArgoCD Deployment
```
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
### ArgoCD (Dashboard)
- visit: http://localhost:8080
- username: admin
- To know password:
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```
### Track app in ArgocD
- Make an app.yaml:
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nodejs-todo-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/hagersobhy/nodejstodo-k8s
    targetRevision: main
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
### 🤖 Expected Outcome
- ArgoCD will display the application in its web UI.
- Any changes made to the Kubernetes manifests inside the k8s/ folder on GitHub will be automatically deployed to the Kubernetes cluster.
---

## 🙋🏻 Author
- Hager Sobhy
- Docker Hub: hagersobhy728

