# Telescope Shop Microservices

A cloud-native microservices application for an online telescope shop, built with modern DevOps practices.

## Overview

This project is based on the open-source [OpenTelemetry Demo](https://github.com/open-telemetry/opentelemetry-demo) and has been adapted for telescope retail operations. It demonstrates a complete microservices architecture.

## Architecture

The application consists of multiple microservices:

- **Frontend** - Next.js web application
- **Product Catalog** - Go service for product management
- **Cart** - .NET service for shopping cart operations
- **Checkout** - Go service for order processing
- **Payment** - Node.js service for payment processing
- **Shipping** - Rust service for shipping calculations
- **Email** - Ruby service for email notifications
- **Recommendation** - Python service for product recommendations
- **Ad Service** - Java service for advertisements
- **Currency** - C++ service for currency conversion
- **Accounting** - .NET service for financial operations
- **Fraud Detection** - Kotlin service for fraud prevention
- **Quote** - PHP service for price quotes

## Repository Structure

```
telescope-shop-microservices/
├── .github/workflows/           # CI/CD pipelines
├── infra/                       # Infrastructure as Code
│   ├── modules/                 # Terraform modules
│   │   ├── eks/                 # EKS cluster module
│   │   └── vpc/                 # VPC networking module
│   ├── s3_backend/              # Terraform S3 backend
│   ├── main.tf                  # Main infrastructure configuration
│   ├── variables.tf             # Variable definitions
│   ├── outputs.tf               # Output values
│   ├── providers.tf             # Provider configurations
│   └── terraform.tfvars         # Variable values
├── kubernetes/                  # Kubernetes manifests
│   ├── accounting/ ad/ cart/ checkout/ currency/
│   ├── email/ frontend/ payment/ productcatalog/
│   └── recommendation/ shipping/ ...
└── src/                       # Microservices source code
    ├── accounting(.NET)/ ad(Java)/ cart(.NET)/
    ├── checkout(Go)/ currency(C++)/ email(Ruby)/
    ├── frontend(Next.js)/ payment(Node.js)/
    └── product-catalog(Go)/ recommendation(Python)/ shipping(Rust)/ ...
```

## Features

- **Multi-language microservices** - Services built in Go, .NET, Node.js, Python, Java, Rust, Ruby, C++, Kotlin, PHP
- **Container orchestration** - Kubernetes deployment with service discovery
- **Infrastructure as Code** - Terraform for AWS resource management
- **CI/CD automation** - GitHub Actions workflows for each service
- **Load balancing** - AWS Application Load Balancer integration
- **Health checks** - Service health monitoring and readiness probes

## Infrastructure

- **Kubernetes** - Container orchestration
- **Terraform** - Infrastructure as Code
- **GitHub Actions** - CI/CD pipelines
- **Docker** - Containerization
- **AWS Load Balancer Controller** - Application Load Balancer integration
- **ArgoCD** - GitOps continuous deployment
- **Prometheus** - Metrics collection and monitoring
- **Grafana** - Metrics visualization and dashboards

## Getting Started

### Prerequisites

- **Docker** and **Docker Compose** - For containerization
- **Kubernetes cluster** - EKS, GKE, or local cluster (minikube/kind)
- **Terraform** - Infrastructure as Code tool
- **kubectl** - Kubernetes command-line tool
- **Helm** - Package manager for Kubernetes (required for Prometheus stack)
- **AWS account** - With appropriate permissions
- **AWS CLI** - Configured with appropriate permissions
- **eksctl** - For EKS cluster management
- **Docker Hub account** - For container registry

### Deployment

1. **Deploy S3 backend for Terraform state:**
   ```bash
   cd infra/s3_backend
   terraform init
   terraform apply
   ```

2. **Configure main infrastructure variables:**
   Create a `terraform.tfvars` file in the `infra/` directory:
   ```hcl
   # terraform.tfvars
   region             = "us-east-1"
   vpc_cidr           = "10.0.0.0/16"
   private_subnet_cidrs = ["10.0.1.0/24", "10.0.2.0/24"]
   public_subnet_cidrs  = ["10.0.101.0/24", "10.0.102.0/24"]
   cluster_name       = "telescope-shop"
   cluster_version    = "1.33"
   ```

3. **Deploy main infrastructure:**
   ```bash
   cd infra
   terraform init
   terraform apply
   ```

4. **Configure kubectl for EKS:**
   ```bash
   aws eks update-kubeconfig --region us-east-1 --name telescope-shop
   ```

5. **Install AWS Load Balancer Controller:**
   Follow the detailed instructions in `Install_AWS_Load_Balancer_Controller.md` to set up the AWS Load Balancer Controller with proper IAM roles and policies.

6. **Install ArgoCD:**
   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

7. **Install Prometheus Stack:**
   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm repo update
   kubectl create namespace monitoring
   helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring
   ```

8. **Access ArgoCD UI:**
   ```bash
   # Port forward to access ArgoCD UI
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   
   # Get initial admin password
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```
   Access ArgoCD at: https://localhost:8080 (username: admin)

9. **Access Prometheus UI:**
   ```bash
   kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 9090:9090
   ```
   Access Prometheus at: http://localhost:9090

10. **Access Grafana UI:**
   ```bash
   kubectl port-forward svc/prometheus-grafana -n monitoring 3000:80
   
   # Get Grafana admin password
   kubectl get secret -n monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 -d
   ```
   Access Grafana at: http://localhost:3000 (username: admin)

11. **Deploy application to Kubernetes:**
   ```bash
   kubectl apply -f kubernetes/
   ```

12. **Access the application:**
   - Frontend: Available via AWS Application Load Balancer
   - Services: Internal cluster communication

## CI/CD

Each microservice has its own GitHub Actions workflow for:
- Code quality checks
- Testing
- Docker image building
- Kubernetes deployment updates

### Required GitHub Secrets

Configure the following secrets in your GitHub repository (Settings > Secrets and variables > Actions):

- `DOCKERHUB_USERNAME` - Your Docker Hub username
- `DOCKERHUB_TOKEN` - Your Docker Hub access token
- `MY_ACTIONS_TOKEN` - GitHub Personal Access Token with repo permissions for updating Kubernetes manifests
- `USER_EMAIL` - Your Git email for commits
- `USER_NAME` - Your Git username for commits

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests and linting
5. Submit a pull request

## License

Based on the OpenTelemetry Demo project. See original license at https://github.com/open-telemetry/opentelemetry-demo

## Acknowledgments

This project is built upon the excellent work of the OpenTelemetry community and their demo application available at https://github.com/open-telemetry/opentelemetry-demo