**Azure DevOps**

**What is Azure DevOps?**

Azure DevOps is a cloud-based DevOps service provided by Microsoft that offers a complete set of tools to plan, develop, test, and deploy software efficiently. It enables Continuous Integration (CI) and Continuous Deployment (CD) through built-in pipelines and supports agile project management, version control, and artifact management.

Azure DevOps is used by development and operations teams to automate workflows, track code changes, and manage deployments to Azure, AWS, GCP, or on-premises environments.

---

**Key Features of Azure DevOps**

**1. Azure Repos (Version Control - Git & TFVC)**

Azure Repos provides source code management using Git and Team Foundation Version Control (TFVC). It allows teams to collaborate using pull requests, branching strategies, and code reviews.

**2. Azure Pipelines (CI/CD Automation)**

Azure Pipelines provides Continuous Integration (CI) and Continuous Deployment (CD) to automate software build, test, and release processes. It supports YAML-based and classic pipeline configurations and integrates with tools like Docker, Kubernetes, Terraform, and Helm.

**3. Azure Boards (Agile Project Management)**

Azure Boards provides Kanban boards, Scrum tools, dashboards, and backlog tracking for teams to plan and manage work items efficiently.

**4. Azure Test Plans (Automated & Manual Testing)**

Azure Test Plans offers manual testing, exploratory testing, and automated test management, ensuring software quality before deployment.

**5. Azure Artifacts (Package Management)**

Azure Artifacts allows teams to store, manage, and share dependencies like NuGet, npm, Maven, and Python packages within projects.

**6. Integration with GitHub, Jenkins, Terraform & Kubernetes**

Azure DevOps integrates seamlessly with GitHub, Jenkins, Terraform, Kubernetes (AKS), Ansible, and other DevOps tools to streamline DevOps workflows.

---

**Azure DevOps vs Other CI/CD Tools (Jenkins, GitHub Actions, GitLab CI/CD)**

**1. Azure DevOps vs Jenkins**

Azure DevOps is a cloud-based CI/CD service, while Jenkins is a self-hosted CI/CD automation tool.
Azure DevOps requires no manual maintenance, whereas Jenkins requires setting up and managing servers.
Azure DevOps has built-in Azure integrations, while Jenkins needs additional plugins for Azure support.

**2. Azure DevOps vs GitHub Actions**

Azure DevOps is an enterprise-grade CI/CD platform, while GitHub Actions is built for GitHub repositories.
Azure DevOps supports project management, testing, and artifacts, whereas GitHub Actions is mainly for CI/CD automation.
Azure DevOps supports multi-cloud deployments, while GitHub Actions is tightly integrated with GitHub repositories.

**3. Azure DevOps vs GitLab CI/CD**

Azure DevOps provides a full DevOps lifecycle suite, while GitLab CI/CD is focused on CI/CD pipeline automation.
Azure DevOps Pipelines are more scalable, whereas GitLab CI/CD is more Git-native.
Azure DevOps has better enterprise support, while GitLab CI/CD is popular for open-source projects.

---

**How to Create an Azure DevOps Project?**

**1. Sign in to Azure DevOps**

Go to Azure DevOps Portal.

Log in with your Microsoft Account or Azure AD credentials.

**2. Create a New Azure DevOps Organization**

Click New Organization if you don’t have one.

Provide an organization name and Azure region.

**3. Create an Azure DevOps Project**

     1.Click New Project.

     2.Enter the Project Name (e.g., MyAzureDevOpsProject).

    3.Choose Visibility:

      Private (Restricted access)
      
      Public (Accessible to anyone)

    4.Select Version Control:

      Git (Recommended)
  
      Team Foundation Version Control (TFVC)
  
    5.Select Work Item Process:

      Agile
  
      Scrum
  
      Basic

    6.Click Create.

---

**Setting Up CI/CD Pipelines in Azure DevOps**

**Azure DevOps CI/CD Pipeline for a Microservice Application**

This guide will help you set up a CI/CD pipeline in Azure DevOps to build, containerize, and deploy a microservice application to Amazon EKS (Elastic Kubernetes Service) using Argo CD.

---

**Architecture Overview**

**1.CI Pipeline (Azure Pipelines)**

Build Stage: Compiles the application code.
    
Containerization Stage: Creates a Docker image and pushes it to Amazon Elastic Container Registry (ECR).

**2.CD Pipeline (Argo CD)**

Deploy Stage: Uses Argo CD to sync manifests with EKS.
    
Helm-based Deployment: Uses Helm charts to manage Kubernetes objects.
    
Monitoring: Uses Prometheus and Grafana for observability.

---

**Prerequisites**

Azure DevOps Project

Amazon EKS Cluster

Argo CD Installed on EKS

Amazon ECR (Elastic Container Registry)

Azure DevOps Service Connection for AWS

Helm Installed

---

**Step 1: Create Azure DevOps Repository**

1.Create a new repository in Azure DevOps (my-microservice-app).

2.Clone the repository and set up the project structure:

```bash
git clone https://dev.azure.com/your-org/my-microservice-app.git
cd my-microservice-app
mkdir -p manifests helm docker src
```
**Step 2: Create a Dockerfile for the Microservice**

Save the following as docker/Dockerfile:

```bash
FROM python:3.9

WORKDIR /app
COPY src/ /app/

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

**Step 3: Create a Helm Chart for Kubernetes Deployment**

**Helm Chart Directory Structure**

```bash
helm/
  ├── charts/
  ├── templates/
  │   ├── deployment.yaml
  │   ├── service.yaml
  │   ├── ingress.yaml
  ├── values.yaml
  ├── Chart.yaml
```

**Deployment YAML (helm/templates/deployment.yaml)**

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-microservice
  labels:
    app: my-microservice
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-microservice
  template:
    metadata:
      labels:
        app: my-microservice
    spec:
      containers:
      - name: my-microservice
        image: <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/my-microservice:latest
        ports:
        - containerPort: 5000
```

**Service YAML (helm/templates/service.yaml)**

```bash
apiVersion: v1
kind: Service
metadata:
  name: my-microservice
spec:
  selector:
    app: my-microservice
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
  type: LoadBalancer
```

**Step 4: Create an Azure DevOps CI/CD Pipeline (YAML)**

**Pipeline File (azure-pipelines.yml)**

```bash
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  AWS_REGION: 'us-east-1'
  AWS_ACCOUNT_ID: 'your-aws-account-id'
  ECR_REPO_NAME: 'my-microservice'
  EKS_CLUSTER_NAME: 'my-eks-cluster'

stages:
  - stage: Build
    displayName: "Build & Containerize"
    jobs:
      - job: Build
        steps:
          - task: Docker@2
            displayName: "Build Docker Image"
            inputs:
              command: build
              Dockerfile: docker/Dockerfile
              repository: "$(AWS_ACCOUNT_ID).dkr.ecr.$(AWS_REGION).amazonaws.com/$(ECR_REPO_NAME)"
              tags: latest

          - task: Docker@2
            displayName: "Push Docker Image to ECR"
            inputs:
              command: push
              repository: "$(AWS_ACCOUNT_ID).dkr.ecr.$(AWS_REGION).amazonaws.com/$(ECR_REPO_NAME)"
              tags: latest

  - stage: Deploy
    displayName: "Deploy to Amazon EKS using Argo CD"
    jobs:
      - job: Deploy
        steps:
          - task: HelmInstaller@1
            displayName: "Install Helm"
            inputs:
              helmVersionToInstall: "latest"

          - script: |
              aws eks update-kubeconfig --region $(AWS_REGION) --name $(EKS_CLUSTER_NAME)
            displayName: "Configure Kubeconfig"

          - script: |
              helm repo add argo https://argoproj.github.io/argo-helm
              helm upgrade --install my-microservice helm/ --namespace default --set image.tag=latest
            displayName: "Deploy Using Helm"
```

**Step 5: Install Argo CD on EKS**

**Install Argo CD on EKS**

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**1.Access Argo CD Dashboard**

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open https://localhost:8080 in a browser.

Get the admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

**Step 6: Configure Argo CD for Automated Deployment**

Create an Application in Argo CD

```bash
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-microservice
  namespace: argocd
spec:
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  project: default
  source:
    path: helm/
    repoURL: "https://github.com/your-org/my-microservice-app.git"
    targetRevision: main
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Apply the Argo CD Application Config

```bash
kubectl apply -f manifests/argocd-application.yaml
```

**Step 7: Verify the Deployment**

Check if the pods are running
```bash
kubectl get pods
```

Get the Service External IP

```bash
kubectl get svc my-microservice
```

**Monitor Argo CD UI**

Login to Argo CD UI
Ensure the my-microservice application is Healthy and Synced.

**Step 8: Automate Rollbacks (Optional)**

If a deployment fails, you can rollback using:

```bash
argocd app rollback my-microservice 1
```

This end-to-end Azure DevOps CI/CD pipeline allows you to automate the build, containerization, and deployment of a microservice application to Amazon EKS using Argo CD.

By using Azure Pipelines, Docker, Helm, and Argo CD, we ensure:
✅ Seamless CI/CD Automation
✅ Scalable Kubernetes Deployments
✅ Automatic Rollbacks & Syncs

**Best Practices for Using Azure DevOps**

Use YAML-based Pipelines for version-controlled CI/CD automation.
Enable Multi-Stage Pipelines for dev, test, staging, and production environments.
Use Service Connections to integrate Azure DevOps with Kubernetes (AKS), AWS, or GCP.
Implement Branching Strategies like GitFlow or Trunk-based development in Azure Repos.
Monitor Pipelines & Logs using Azure Monitor and Application Insights.
Use Azure Artifacts to manage dependencies and ensure secure package distribution.
Enable Security Policies like Role-Based Access Control (RBAC) and Azure DevOps Audit Logs.

**Conclusion**

Azure DevOps is a powerful CI/CD and DevOps platform that simplifies code management, builds, testing, and deployments. It provides Azure-native integrations, making it ideal for cloud-based DevOps workflows.

For DevOps Engineers, Azure DevOps Pipelines + Azure Repos + Terraform + Kubernetes (AKS) is a great combination to automate and scale deployments.
