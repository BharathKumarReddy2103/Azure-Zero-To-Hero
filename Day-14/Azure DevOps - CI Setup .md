**Implementing CI for a Multi-Microservice Application Using Azure DevOps Pipelines**

**Introduction**

Continuous Integration (CI) is a crucial part of DevOps practices, ensuring that code changes are continuously built, tested, and integrated into a shared repository. In this guide, we will implement **CI pipeline using Azure DevOps** for a **multi-microservice** application written in **Python, Node.js, and .NET**, with **Redis and PostgreSQL** as data stores.

This project will walk through:

**•	Understanding the architecture of the application**

**•	Writing Dockerfiles for microservices**

**•	Building CI pipeline in Azure DevOps**

**•	Configuring Azure DevOps Repositories (Azure Repos)**

**•	Using Azure Container Registry (ACR) for image storage**

**•	Implementing path-based CI triggers for optimized pipeline execution**

---

**Application Architecture Overview**

The application is a **voting system** where users vote between two options (e.g., **Cats vs. Dogs**) and view real-time results. It consists of multiple microservices:

Microservices and Their Roles

```sh
| Microservice    | Language   | Role |
|----------------|-----------|------|
| **Voting Service** | Python | Accepts user votes |
| **Worker Service** | .NET | Processes votes and stores data |
| **Results Service** | Node.js | Displays voting results |
| **Redis** | In-Memory DB | Temporary storage for votes |
| **PostgreSQL** | Database | Stores finalized vote counts |
```

---

**Setting Up the CI Pipelines**

**Step 1: Importing the GitHub Repository into Azure Repos**

To keep all configurations within Azure DevOps, import the GitHub repository into **Azure Repos:**

**1.**	Navigate to **Azure DevOps → Repos → Import Repository**

**2.**	Provide the **GitHub repository URL** and import the project

**3.**	Verify that all source code files are now available in Azure Repos

**4.**	Ensure the **default branch** is set to **main**

---

**Step 2: Writing Dockerfiles for Each Microservice**

Each microservice requires a **Dockerfile** to containerize the application. Below are the Dockerfiles for each service:

**Voting Service (Python) – Dockerfile**

```sh
FROM python:3.9
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

**Worker Service (.NET) – Dockerfile**

```sh
FROM mcr.microsoft.com/dotnet/sdk:6.0
WORKDIR /app
COPY . .
RUN dotnet restore
RUN dotnet build -c Release
CMD ["dotnet", "WorkerApp.dll"]
```

**Results Service (Node.js) – Dockerfile**

```sh
FROM node:16
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD ["node", "server.js"]
```

---

**Step 3: Creating Azure Container Registry (ACR)**

Since we need to store Docker images, create an **Azure Container Registry (ACR):**

**1.**	In **Azure Portal**, search for **Container Registry**

**2.**	Click **Create** and provide:

**o	Resource Group**: azure-cicd

**o	Registry Name**: yourregistryname

**o	SKU**: Standard

**3.**	Click **Create** and wait for deployment

---

**Step 4: Creating CI Pipelines in Azure DevOps**

Each microservice gets its own **CI pipeline**, ensuring independent deployments.

**1. Creating the CI Pipeline for the Results Service**

**1.**	Navigate to **Azure DevOps → Pipelines → New Pipeline**

**2.**	Select **Azure Repos Git**

**3.**	Choose **Build and Push Docker Image to Azure Container Registry**

**4.**	Configure the pipeline YAML:

**Azure Pipelines YAML (azure-pipelines-results.yml)**

```sh
trigger:
  paths:
    include:
      - results/*  # Trigger CI only if changes occur in the results service

variables:
  dockerRegistryServiceConnection: '<your-acr-service-connection>'
  imageRepository: 'results-app'
  containerRegistry: '<yourregistryname>.azurecr.io'
  dockerfilePath: 'results/Dockerfile'

stages:
- stage: Build
  jobs:
  - job: BuildDockerImage
    pool:
      name: AzureAgent  # Custom self-hosted agent
    steps:
    - task: Docker@2
      displayName: 'Build Docker Image'
      inputs:
        command: 'build'
        dockerfile: $(dockerfilePath)
        repository: $(imageRepository)
        containerRegistry: $(dockerRegistryServiceConnection)

- stage: Push
  jobs:
  - job: PushDockerImage
    pool:
      name: AzureAgent
    steps:
    - task: Docker@2
      displayName: 'Push Docker Image'
      inputs:
        command: 'push'
        repository: $(imageRepository)
        containerRegistry: $(dockerRegistryServiceConnection)
```

**Key Features:**

**•	Path-based triggers** to run CI only when relevant microservice files are updated

**•	Docker build and push steps** executed separately for modularity

**•	Self-hosted agent** (AzureAgent) to execute pipelines

---

**Step 5: Configuring Self-Hosted Agents for Azure Pipelines**

Azure DevOps requires an **agent** to run CI tasks. Follow these steps to configure an **Ubuntu-based self-hosted agent:**

**1. Create a Virtual Machine in Azure**

**•	Resource Group**: azure-cicd

**•	VM Name**: AzureAgent

**•	OS**: Ubuntu 20.04 LTS

**•	Authentication**: SSH Key

**2. Install and Configure the Agent**

SSH into the VM and execute the following commands:

```sh
# Update system and install Docker
sudo apt update && sudo apt install -y docker.io

# Create Agent Directory
mkdir myagent && cd myagent

# Download Azure DevOps Agent
wget https://vstsagentpackage.azureedge.net/agent/3.220.2/vsts-agent-linux-x64-3.220.2.tar.gz
tar zxvf vsts-agent-linux-x64-3.220.2.tar.gz

# Configure the Agent
./config.sh --url https://dev.azure.com/your-org-name --auth pat --token your-personal-access-token --pool AzureAgent
```

---

**Step 6: Running and Testing the Pipelines**

Once all configurations are in place:

**1.	Verify that the agent is online** in Azure DevOps → **Agent Pools**

**2.**	Trigger a pipeline by committing a small change in the results/ directory

**3.**	Check pipeline logs to ensure:

o	The **Docker image is built** successfully

o	The **image is pushed to ACR**

**4.**	Repeat the above steps for **Voting** and **Worker** microservices

---

**Conclusion**

In this guide, we successfully:

**•	Configured Azure DevOps repositories** for source code management

**•	Created CI pipelines with path-based triggers** for efficient builds

**•	Built and pushed Docker images** using self-hosted agents

**•	Deployed a self-hosted agent** for running pipelines in Azure


**Next Steps:**

•	Implement **Continuous Deployment (CD)** to **automate deployments** to Kubernetes or VMs

•	Add **Unit Tests, Static Code Analysis**, and **Security Scanning** in CI pipelines

•	Configure **Multi-architecture Builds** for different deployment targets

This guide provides a **real-world DevOps use case** applicable to enterprise environments. Feel free to **contribute** or **fork the project** in the GitHub repository.

---
