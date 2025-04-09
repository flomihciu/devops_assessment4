# 🐦 Twoge – Twitter Clone Powered by Flask & Kubernetes

Twoge is a simple microblogging platform built using **Flask** and **PostgreSQL**, containerized with **Docker**, deployed with **Kubernetes**, and running on **AWS EKS** using a full CI/CD pipeline powered by **GitHub Actions** and **AWS ECR**.

---

### 🧱 Architecture

```text
                        GitHub Actions
                             |
                        Docker Build
                             |
                            ECR
                             |
                         Kubernetes (EKS)
                        ┌──────────────┐
                        │              │
         ┌─────────────▶│   Flask App  │◀──────────────┐
         │              │  twoge-app   │               │
         │              └──────────────┘               │
         │                                              │
         │                                              ▼
   ┌──────────────┐                              ┌──────────────┐
   │ LoadBalancer │ ───────────────────────────▶ │    Users     │
   └──────────────┘                              └──────────────┘
         ▲
         │
         ▼
  ┌────────────────┐
  │   PostgreSQL    │ (twoge-db)
  └────────────────┘

🚀 Tech Stack
---Backend: Flask + SQLAlchemy + Gunicorn
---Database: PostgreSQL
---Infrastructure: Kubernetes on AWS EKS
---CI/CD: GitHub Actions → ECR → EKS
---Containerization: Docker
---Monitoring: Liveness/Readiness probes

📁 Project Structure

assessment4/
├── twoge/                      # Flask application
│   ├── app.py
│   ├── gunicorn_config.py
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── static/
│   └── templates/
│
├── twoge-k8s/                  # Kubernetes manifests
│   ├── configmap.yml
│   ├── namespace.yml
│   ├── database/
│   │   ├── db-deployment.yml
│   │   └── db-service.yml
│   ├── flo-k8-app/
│   │   ├── twoge-deployment.yml
│   │   └── twoge-service.yml
│   └── storage/
│       └── pvc.yml
│
└── .github/workflows/
    └── deploy.yml             # GitHub Actions pipeline

⚙️ Deployment via GitHub Actions
🧾 Prerequisites
AWS account with:
---EKS cluster
---ECR repository named twoge
---Docker image built and pushed to:
536697237321.dkr.ecr.us-east-1.amazonaws.com/twoge:latest (replace with your ECR)

🔐 Required GitHub Secrets
Secret Name	Description
AWS_ACCESS_KEY_ID	IAM user access key
AWS_SECRET_ACCESS_KEY	IAM user secret
AWS_REGION	AWS region (e.g. us-east-1)
EKS_CLUSTER_NAME	Your EKS cluster name
ECR_REPO_URI	Your full ECR image repo URI
DB_USER	PostgreSQL username (twoge_user)
DB_PASSWORD	PostgreSQL password (secure_password)
These secrets are used to authenticate AWS + inject Kubernetes Secrets at deploy time.

🚀 How to Deploy
Push to the main branch
OR
Run the workflow manually from GitHub Actions → Deploy Twoge App to EKS

GitHub Actions will:

Build the Docker image from twoge/

Push to ECR

Connect to your EKS cluster

Apply all Kubernetes manifests

Inject PostgreSQL secrets into the cluster

After deployment, AWS will provision a LoadBalancer for public access.
Run this to get the external IP:

kubectl get svc twoge-service -n twoge
🧪 Health Checks
Your Flask app includes Kubernetes-friendly probes:

Liveness Probe: /health

Readiness Probe: /ready

These endpoints return HTTP 200 OK for EKS to monitor container health.