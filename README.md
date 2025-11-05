# Scalable CI/CD Pipeline for Dockerized 3-Tier Application on AWS EKS
## Overview
This project demonstrates a fully automated CI/CD pipeline for deploying a 3-tier web application (frontend, backend, and database) on AWS EKS using Terraform, Jenkins, Docker, Kubernetes, and Ansible.

The pipeline provisions infrastructure, builds and secures containerized applications, deploys them to Kubernetes, and monitors the system using Prometheus and Grafana.



## 🏗️ Architecture Highlights

- Infrastructure as Code (IaC) using Terraform to build a custom AWS VPC and EKS cluster.

- Configuration Management with Ansible to install and configure essential tools (Jenkins, SonarQube, Prometheus, Grafana, etc.).

- Continuous Integration & Deployment (CI/CD) automated via Jenkins.

- Security Scanning using Trivy for container image vulnerability analysis.

- Containerization of frontend and backend using Docker and deployment on EKS.

- Monitoring and Observability using Prometheus, Grafana, Node Exporter, and Blackbox Exporter.

- Automated Notifications sent via email for build status and Trivy reports.


## Project Structure

```
end-to-end-3tier-devops-pipeline/
│
├── app/                                   # Application source code (frontend, backend, database)
│   ├── frontend/                          # React app built using Node.js and Vite
│   ├── backend/                           # Java Spring Boot backend built with Maven
│   └── database/                          # Database initialization scripts or configs
│
├── Docker/                                # Docker-related files for building container images
│   ├── backend.dockerfile                 # Dockerfile for Spring Boot backend
│   ├── frontend.dockerfile                # Dockerfile for React frontend
│   └── docker-compose.yml                 # Local Docker setup for testing multi-container app
│
├── k8s/                                   # Kubernetes manifests for deployment on EKS
│   ├── backend-deployment.yaml            # Deployment definition for backend service
│   ├── backend-service.yaml               # ClusterIP/LoadBalancer service for backend
│   ├── frontend-deployment.yaml           # Deployment definition for frontend service
│   ├── frontend-service.yaml              # ClusterIP/LoadBalancer service for frontend
│   ├── ingress.yaml                       # Ingress resource to expose frontend/backend externally
│   └── secret.yaml                        # Kubernetes Secret for securely storing DB credentials
│
├── jenkins/                               # Jenkins configuration for CI/CD pipeline
│   └── Jenkinsfile                        # Declarative pipeline defining build, test, deploy stages
│
├── Terraform/                             # Terraform IaC for AWS infrastructure provisioning
│   ├── environments/                      # Environment-specific configurations
│   │   ├── dev/                           # Development environment setup
│   │   │   ├── backend.tf                 # Remote backend configuration (S3, DynamoDB)
│   │   │   ├── main.tf                    # Root module calling reusable Terraform modules
│   │   │   ├── variables.tf               # Variable definitions for dev environment
│   │   │   ├── outputs.tf                 # Outputs for dev environment (cluster name, VPC ID, etc.)
│   │   │   └── terraform.tf               # Terraform settings/configurations
│   │   ├── stage/                         # Staging environment configuration (optional)
│   │   └── prod/                          # Production environment configuration (optional)
│   │
│   └── modules/                           # Reusable Terraform modules for different AWS components
│       ├── vpc/                           # Custom VPC setup (subnets, gateways, route tables)
│       ├── eks-cluster/                   # EKS control plane and IAM roles
│       ├── eks-nodes/                     # EKS worker node group configurations
│       ├── ec2/                           # EC2 instance setup for Jenkins, SonarQube, Monitoring
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── rds/                           # AWS RDS (MariaDB) configuration
│
├── ansible/                               # Configuration management using Ansible
│   ├── inventory.ini                      # Inventory file listing EC2 instance IPs and roles
│   ├── playbook.yml                       # Playbook to install/configure Jenkins, SonarQube, Prometheus, etc.
│   └── Scripts/                           # Custom scripts used in Ansible automation
│
├── .gitignore                             # Files and directories to ignore in version control
└── README.md                              # Detailed documentation of architecture, workflow, and usage


```

<!-- ## Architecture
![Architecture](docs/Architecture.png) -->

## Technology Stack
| Layer                        | Technology                                            | Purpose                                    |
| ---------------------------- | ----------------------------------------------------- | ------------------------------------------ |
| **Frontend**                 | React, Vite, Node.js                                  | Fast, modular UI                           |
| **Backend**                  | Java, Spring Boot, Maven                              | REST API and business logic                |
| **Database**                 | MariaDB (AWS RDS)                                     | Persistent data layer                      |
| **CI/CD**                    | Jenkins, Terraform                                    | Automation and infrastructure provisioning |
| **Configuration Management** | Ansible                                               | Server and tool setup                      |
| **Containerization**         | Docker                                                | Consistent build and runtime environments  |
| **Orchestration**            | Kubernetes (AWS EKS)                                  | Deployment and scalability                 |
| **Monitoring**               | Prometheus, Grafana, Node Exporter, Blackbox Exporter | Observability and alerting                 |
| **Security**                 | Trivy                                                 | Image vulnerability scanning               |
| **Notifications**            | Jenkins Mailer                                        | Email build status and scan reports        |


## Environment Variables

| Variable                    | Used In   | Description                              |
|----------------------------|-----------|------------------------------------------|
| SPRING_DATASOURCE_URL      | Backend   | JDBC URL for MariaDB                     |
| SPRING_DATASOURCE_USERNAME | Backend   | DB username                              |
| SPRING_DATASOURCE_PASSWORD | Backend   | DB password                              |
| VITE_API_URL               | Frontend  | URL to access backend API                |


## 🔁 CI/CD Pipeline Flow

The Jenkins pipeline automates the entire lifecycle:

- Git Checkout – Pulls latest code from GitHub repository.

- SonarQube Scan – Performs code quality analysis on frontend and backend.

- Build & Package – Builds Java backend using Maven and React frontend using npm.

- Docker Build & Push – Builds Docker images and pushes them to DockerHub.

- Trivy Scan – Scans Docker images for vulnerabilities and attaches reports.

- Kubernetes Deploy – Applies Kubernetes manifests to deploy on AWS EKS.

- Notification – Sends an email summary of build status and Trivy results.
<!-- 

## ☁️ Infrastructure Setup
### 🧱 Terraform

Terraform automates AWS resource creation, including:

Custom VPC (private/public subnets, routing, gateways)

EKS Cluster with managed node groups

RDS (MariaDB) for persistent data storage

EC2 Instances for:

Jenkins Master

SonarQube Server

Monitoring Stack (Prometheus, Grafana)

### ⚙️ Ansible

Ansible automates configuration of all EC2 instances:

Installs Jenkins, Docker, Trivy, SonarQube, Prometheus, Grafana.

Configures Node Exporter on Jenkins for monitoring.

Ensures consistent setup across all servers.

## 📦 Kubernetes Deployment

Once Docker images are built and pushed, Kubernetes manifests deploy the application:

Frontend and Backend deployments with services.

Ingress Controller routes external traffic to services.

Secrets store database credentials securely.

Horizontal Pod Autoscaler (optional) for scalability.

## 🔍 Monitoring and Observability

Prometheus collects metrics from:

Application pods

Node Exporter (for Jenkins and EKS nodes)

Blackbox Exporter (for endpoint uptime)

Grafana visualizes metrics using custom dashboards.

Alerts configured for application downtime or resource spikes.

## ✅ Validation Steps
After deployment:

1. Access the application via the Ingress URL.
2. Check all pods are running:
```
kubectl get pods -n <namespace>
```
3. Verify database connectivity.
4. Review monitoring dashboards in Grafana.
5. Check Jenkins job for build reports and email notifications. -->


## 🚀 Key Achievements

✅ Complete CI/CD automation with Jenkins

✅ Infrastructure as Code with Terraform

✅ Configuration as Code using Ansible

✅ Secure & scanned Docker images using Trivy

✅ Scalable deployments on AWS EKS

✅ Real-time monitoring with Prometheus & Grafana

✅ Automated notifications for build and scan results

## Conclusion
This project demonstrates how to design, deploy, and manage a production-grade CI/CD pipeline for a 3-tier application using modern DevOps practices.
It integrates provisioning, deployment, security, and monitoring — enabling a scalable, resilient, and automated delivery platform on AWS.


## Future Improvements
- Implement GitOps deployment using ArgoCD

- Add automated testing (unit/integration) in pipeline

- Use AWS Secrets Manager for managing credentials

- Add autoscaling policies and cost optimization

- Integrate Centralized Logging with ELK or Loki