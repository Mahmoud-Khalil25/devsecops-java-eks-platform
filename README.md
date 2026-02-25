# DevSecOps GitOps CI/CD Platform on AWS EKS

End-to-end DevSecOps CI/CD pipeline that builds, scans, containerizes, and continuously deploys a Java application to Amazon EKS using GitOps with ArgoCD.

---

## 🚀 Project Overview

This project demonstrates a production-style DevSecOps workflow integrating CI automation, security scanning, container registry, and GitOps-based Continuous Deployment to Kubernetes on AWS.

The pipeline automates secure application delivery from source code to Kubernetes with zero manual deployment steps.

---

## 🏗 Architecture

CI/CD flow:

1. Developer pushes code to GitHub  
2. Jenkins pipeline builds and tests application  
3. SonarQube performs static code analysis  
4. Trivy scans filesystem and container image  
5. Docker image pushed to AWS ECR  
6. Jenkins updates GitOps manifests repository  
7. ArgoCD detects change and deploys to EKS  
8. Prometheus and Grafana monitor workload  

---

# 🔁 Continuous Deployment Implementation (GitOps + ArgoCD)

## CD Strategy

This project implements GitOps-based Continuous Deployment:

- Kubernetes manifests stored in a separate Git repository  
- Jenkins updates only the container image tag  
- ArgoCD continuously watches the repository  
- ArgoCD reconciles cluster state automatically  

Git becomes the single source of truth for deployments.

---

## 📂 GitOps Repository

Deployment manifests are stored in a dedicated repository:

devsecops-java-eks-platform-manifests

Example Deployment image reference:

image: <AWS_ACCOUNT_ID>.dkr.ecr.eu-west-3.amazonaws.com/devsecops-java-app:<TAG>

---

## ⚙ Jenkins CD Implementation

The Jenkins pipeline triggers deployment by updating the container image tag inside the GitOps repository.

Steps performed by Jenkins:

1. Clone GitOps repository  
2. Update Deployment image tag  
3. Commit change  
4. Push to GitHub  

Conceptual implementation:

- git clone manifests repo  
- update image tag in deployment.yaml  
- git commit with new version  
- git push  

Only the image version changes while manifests remain immutable.

---

## 🤖 ArgoCD Continuous Deployment

ArgoCD is configured with:

- Git repository URL (manifests repo)  
- Kubernetes cluster (EKS)  
- Auto-sync enabled  

ArgoCD behavior:

- Detects new commit in manifests repo  
- Compares desired vs live state  
- Updates Deployment image  
- Performs rolling update in EKS  

No manual kubectl or deployment scripts required.

---

## 🔄 End-to-End Deployment Flow

1. CI builds image devsecops-java-app:<BUILD_NUMBER>  
2. Image pushed to AWS ECR  
3. Jenkins updates GitOps repo image tag  
4. Git commit triggers ArgoCD  
5. ArgoCD syncs Kubernetes Deployment  
6. Pods roll to new version  

This enables zero-touch Continuous Deployment.

---

# 🔐 DevSecOps Controls in CD

Continuous Deployment is protected by:

- SonarQube quality analysis  
- Trivy filesystem scan  
- Trivy container image scan  
- Versioned immutable container images  
- GitOps audit trail  

Only scanned and versioned images are deployed to Kubernetes.

---

# ☸ Kubernetes Deployment

Application deployed to EKS as:

- Deployment  
- Service  
- Namespace  

ArgoCD continuously maintains desired state.

---

# 📊 Observability

Monitoring stack integrated with Kubernetes:

- Prometheus — metrics collection  
- Grafana — dashboards  
- Kubernetes workload monitoring  

Provides visibility into application health after deployment.

---

# 🧪 CI/CD Pipeline Stages

1. Checkout source code  
2. Maven build and unit tests  
3. SonarQube static analysis  
4. Trivy filesystem scan  
5. Package application  
6. Docker image build  
7. Trivy image scan  
8. Push image to AWS ECR  
9. Update GitOps manifests repo  
10. ArgoCD deploy to EKS  

---

# ☁ AWS Services Used

- Amazon EKS — Kubernetes cluster  
- Amazon ECR — container registry  
- Amazon EC2 — Jenkins and SonarQube  
- IAM — access control  
- VPC — networking  

---

# 📁 Repositories

Application source repository:  
devsecops-java-eks-platform  

GitOps manifests repository:  
devsecops-java-eks-platform-manifests  

---

# ✅ Key DevOps Practices Demonstrated

- GitOps Continuous Deployment  
- Immutable container releases  
- Automated Kubernetes delivery  
- Integrated DevSecOps scanning  
- Continuous reconciliation with ArgoCD  
- Cloud-native deployment on AWS  

---

# 📌 Project Status

AWS infrastructure and EKS cluster were fully provisioned and validated during development.  
Environment has been decommissioned to optimize cloud cost.  
Pipeline code, manifests, and documentation remain available.

---

# 👨‍💻 Author

Mahmoud Khalil  
DevOps Engineer — Kubernetes & AWS  
GitHub: https://github.com/Mahmoud-Khalil25
