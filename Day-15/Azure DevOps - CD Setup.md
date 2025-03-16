**End-to-End CI/CD with Azure DevOps and GitOps Using ArgoCD on Kubernetes**

**Introduction**

In modern DevOps practices, **Continuous Integration and Continuous Delivery (CI/CD)** are essential for automating software deployment. This article covers **how to implement an end-to-end CI/CD pipeline using Azure DevOps and GitOps** with **ArgoCD on Kubernetes (AKS).**

We will walk through:

**•	Setting up Continuous Integration (CI)** to build and push Docker images.

**•	Implementing Continuous Delivery (CD)** using GitOps with **ArgoCD**.

**•	Deploying a microservices-based application** on **Azure Kubernetes Service (AKS).**

**•	Updating application configurations dynamically** using an automated shell script.

**•	Ensuring secure image pull from Azure Container Registry (ACR).**

By the end of this guide, you will have a **fully automated CI/CD pipeline** capable of **deploying changes seamlessly** to Kubernetes.

---

**Project Overview**

We will work with a **multi-microservices application**, which consists of:

**•	Three microservices** written in Python, .NET, and Node.js.

**•	Redis** as an in-memory data store.

**•	PostgreSQL** as a database.

**•	Azure DevOps Pipelines** for **CI/CD.**

**•	ArgoCD** for **GitOps-based continuous deployment.**

**•	AKS (Azure Kubernetes Service)** for running our microservices.

The application follows a **voting-based workflow:**

**1.**	Users vote for one of two options.

**2.**	The vote is stored in **Redis.**

**3.**	A **.NET-based results service** processes the votes and stores them in PostgreSQL.

**4.**	A **Node.js-based microservice** fetches and displays the results.

---

**Step 1: Setting Up Azure Kubernetes Service (AKS)**

First, we create an **Azure Kubernetes Service (AKS) cluster:**

**Create AKS Cluster in Azure**

**1.**	Go to the **Azure Portal.**

**2.**	Navigate to **Kubernetes Services > Create a new cluster.**

**3.**	Configure:

o	**Resource Group**: azure-cicd-cluster

o	**Cluster Name**: azure-devops-cluster

o	**Region**: West US 2

o	**Node Pool**: Autoscale between 1-2 nodes

o	**Enable Public IP**

**4.**	Click **Review + Create** and wait for provisioning.

**Connect to AKS Cluster**

Once the cluster is created, connect to it using **Azure CLI**:

```sh
az aks get-credentials --resource-group azure-cicd-cluster --name azure-devops-cluster --overwrite-existing
```

Verify the connection:

```sh
kubectl get nodes
```

---

**Step 2: Installing ArgoCD for GitOps Deployment**

We will use **ArgoCD** to implement GitOps for continuous deployment.

**Install ArgoCD**

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Check the status:

```sh
kubectl get pods -n argocd
```

**Expose ArgoCD UI**

By default, ArgoCD runs inside the cluster. Expose it externally:

```sh
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```

Find the **external IP** and **port**:

```sh
kubectl get nodes -o wide
kubectl get svc -n argocd
```

Access ArgoCD in a browser using http://<Node-IP>:<NodePort>

**Login to ArgoCD**

Fetch the **admin password**:

```sh
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
```

Login to ArgoCD:

```sh
argocd login <Node-IP>:<NodePort> --username admin --password <password>
```

---

**Step 3: Setting Up Azure DevOps Pipelines for CI/CD**

**CI Pipeline: Building and Pushing Docker Images**

**Azure Pipeline Configuration**

We configure an **Azure DevOps YAML pipeline:**

```sh
trigger:
  - main

stages:
  - stage: Build
    jobs:
      - job: Build
        steps:
          - script: |
              docker build -t $(acr_name)/voting-app:$(Build.BuildId) .
              docker push $(acr_name)/voting-app:$(Build.BuildId)
            displayName: 'Build & Push Docker Image'
```

This pipeline:

**1.	Builds the Docker image.**

**2.	Tags it with the Build ID.**

**3.	Pushes it to Azure Container Registry (ACR).**

---

**Step 4: GitOps-Based Continuous Deployment with ArgoCD**

**Updating Kubernetes Manifests Automatically**

Since we are using GitOps, our **Kubernetes deployment YAML** must be updated dynamically. We achieve this using a **shell script**.

**Shell Script for Image Updates**

```sh
#!/bin/bash
set -x

REPO_URL="https://<PAT>@dev.azure.com/<ORG>/<PROJECT>/_git/<REPO>"
IMAGE_TAG="$1"
CLONE_DIR="/tmp/repo"

# Clone Azure Repo
git clone $REPO_URL $CLONE_DIR
cd $CLONE_DIR

# Update Deployment YAML
sed -i "s|image: .*|image: $(acr_name)/voting-app:$IMAGE_TAG|g" k8s/deployment.yaml

# Commit & Push Changes
git add k8s/deployment.yaml
git commit -m "Update deployment to image tag $IMAGE_TAG"
git push origin main
```

This script:

**•	Clones the Azure Git repository.**

**•	Updates the Kubernetes deployment YAML** with the new image tag.

**•	Commits and pushes changes** to Azure Repos.

**•	Triggers ArgoCD to deploy the changes.**

---

**Step 5: Securely Pull Images from ACR**

Since ACR is a **private registry**, Kubernetes needs credentials to pull images.

**Create a Kubernetes Image Pull Secret**

```sh
kubectl create secret docker-registry acr-secret \
  --docker-server=$(acr_name).azurecr.io \
  --docker-username=$(az acr credential show --name $(acr_name) --query "username" -o tsv) \
  --docker-password=$(az acr credential show --name $(acr_name) --query "passwords[0].value" -o tsv)
```

**Update Kubernetes Deployment YAML**

```sh
containers:
  - name: voting-app
    image: <ACR_NAME>.azurecr.io/voting-app:latest
    imagePullSecrets:
      - name: acr-secret
```

---

**Step 6: Testing the End-to-End CI/CD Pipeline**

**Trigger a Change**

**1.	Modify the application source code** (e.g., change the voting options in app.py).

**2.	Commit and push the changes** to Azure Repos.

**3.	Azure DevOps Pipeline triggers automatically.**

**4.	Build & Push Image to ACR.**

**5.	Shell script updates Kubernetes manifests** in Azure Repo.

**6.	ArgoCD detects the change and deploys to AKS.**

**7.	Application is updated in Kubernetes** with zero downtime.

---

**Conclusion**

In this guide, we implemented a **fully automated CI/CD pipeline** using:

**•	Azure DevOps for Continuous Integration (CI).**

**•	Azure Kubernetes Service (AKS) for deployment.**

**•	ArgoCD for Continuous Delivery (CD) with GitOps.**

**•	Azure Container Registry (ACR) for secure image storage.**

This setup **ensures faster deployments, better security, and automated updates.**
