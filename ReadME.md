# 🚀 Automated DevSecOps CI/CD Pipeline for Node.js on AKS

## 📌 Project Overview

This project demonstrates a **production-grade DevSecOps CI/CD pipeline** that automates the entire application lifecycle—from code commit to secure deployment on **Azure Kubernetes Service (AKS)**.

It integrates **CI/CD with security (DevSecOps)** to ensure applications are:

* ✅ Secure
* ✅ Scalable
* ✅ Reliable
* ✅ Production-ready

---

## 🎯 Objectives

* Automate build, test, and deployment process
* Implement **DevSecOps (Security at every stage)**
* Ensure high-quality, vulnerability-free code
* Deploy containerized applications to AKS
* Achieve scalable and resilient architecture
* HELM

---

## 🧠 What This Project Does

This pipeline:

1. Detects code changes via Git push
2. Triggers Jenkins pipeline automatically
3. Performs **SAST (SonarQube)** for code quality
4. Performs **SCA (Snyk)** for dependency vulnerabilities
5. Builds Node.js application using npm
6. Creates Docker image
7. Scans image using **Trivy**
8. Pushes image to Azure Container Registry (ACR)
9. Deploys application to AKS using Helm
10 Verify the APP

---

## 🏗️ Architecture Flow

```
Developer → Git Push → Jenkins Trigger → Checkout
        → SonarQube (SAST) → Quality Gate
        → Snyk (SCA)
        → NPM Build
        → Docker Build
        → Trivy Scan
        → Push to ACR
        → Helm Deploy to AKS
        → Pods → Service → Ingress → HPA
```

---

## 📁 Repository Structure

```
.
├── src/                     # Application source code
├── Dockerfile              # Docker build file
├── package.json            # Node dependencies
├── Jenkinsfile             # CI/CD pipeline
├── helm-chart/             # Helm deployment files
│   ├── templates/
│   ├── values.yaml
│   └── Chart.yaml
└── README.md
```

---

## ⚙️ Prerequisites

Make sure the following are available:

* Azure Subscription
* Azure Kubernetes Service (AKS)
* Azure Container Registry (ACR)
* Jenkins Server
* SonarQube Server
* Snyk Account
* Docker Installed
* Helm Installed

---

## 🔐 Jenkins Credentials

| ID             | Description           |
| -------------- | --------------------- |
| acr-creds      | ACR login credentials |
| aks-kubeconfig | Kubernetes config     |
| snyk-token     | Snyk API token        |
| sonar-token    | SonarQube token       |

---

# 🔎 SonarQube Setup (SAST)

## Run SonarQube

```bash
docker run -d --name sonarqube -p 9000:9000 sonarqube:lts
```

## Access UI

```
http://<server-ip>:9000
```

## Generate Token

* Go to **My Account → Security**
* Generate token

## Configure Jenkins

* Install SonarQube Scanner plugin
* Add SonarQube server in Jenkins
* Add token as credential

---

# 🔐 Snyk Setup (SCA)

## Create Account

https://app.snyk.io

## Generate Token

* Account Settings → API Token

## Install CLI (Optional)

```bash
npm install -g snyk
```

## Authenticate

```bash
snyk auth
```

## Jenkins Setup

* Install Snyk plugin
* Add token as Secret Text (`snyk-token`)

---

# 🛡️ Trivy Setup

## Install

```bash
wget https://github.com/aquasecurity/trivy/releases/latest/download/trivy_0.50.0_Linux-64bit.deb
sudo dpkg -i trivy_0.50.0_Linux-64bit.deb
```

## Verify

```bash
trivy --version
```

---

# 🐳 Docker Setup

```bash
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
```

---

# ☸️ AKS + ACR Setup

## Login

```bash
az login
```

## Create ACR

```bash
az acr create --name <acr-name> --resource-group <rg> --sku Basic
```

## Create AKS

```bash
az aks create --resource-group <rg> --name <aks-name> --node-count 2
```

## Connect AKS

```bash
az aks get-credentials --resource-group <rg> --name <aks-name>
```

---

# ⛴️ Helm Setup

## Install Helm

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

## Create Chart

```bash
helm create helm-chart
```

---

# ⚙️ CI/CD Pipeline Stages

## 1. Checkout

Pull code from Git

## 2. SAST (SonarQube)

Scan code for vulnerabilities

## 3. Quality Gate

Fail build if issues exist

## 4. SCA (Snyk)

Scan dependencies

## 5. NPM Build

```bash
npm install
npm run build
```

## 6. Docker Build

```bash
docker build -t <acr>/app:<tag> .
```

## 7. Trivy Scan

```bash
trivy image <acr>/app:<tag>
```

## 8. Push to ACR

```bash
docker push <acr>/app:<tag>
```

## 9. Deploy to AKS

```bash
helm upgrade --install app ./helm-chart \
  --set image.tag=<tag>
```

---

# 📦 Tagging Strategy

| Environment | Tag          |
| ----------- | ------------ |
| Dev         | Commit SHA   |
| QA          | Build Number |
| Prod        | v1.0.0       |

---

# 🔄 Rollback

```bash
helm rollback app 1
```

---

# ☸️ AKS Deployment

After deployment:

* Pods running containers
* Service exposes application
* Ingress handles routing
* HPA enables autoscaling

---

# 🚨 Failure Conditions

Pipeline fails if:

* SonarQube Quality Gate fails
* Snyk finds high vulnerabilities
* Trivy detects critical issues

---

# 🔐 Best Practices

* Use Azure Key Vault for secrets
* Use multi-stage Docker builds
* Enable RBAC in AKS
* Use namespaces (dev/prod)
* Enable monitoring (Prometheus/Grafana)

---

# 🌟 Features

* Fully automated CI/CD pipeline
* Integrated DevSecOps
* Secure container deployment
* Scalable Kubernetes architecture
* Rollback support
* Production-ready design

---

# 🚀 Future Enhancements

* ArgoCD (GitOps)
* Blue-Green deployment
* Canary deployment
* Monitoring & logging stack
* Multi-microservice support

---

# 👨‍💻 Author

DevSecOps CI/CD Pipeline Project by Rajcosiva +91-9590851069
