**Azure CLI**

**Introduction**

As a **DevOps Engineer**, managing cloud infrastructure efficiently is crucial. While the **Azure Portal** provides a user-friendly interface for resource creation, it becomes **time-consuming and inefficient** when handling **multiple projects, automation, and bulk operations.**

This is where **Azure CLI (Command-Line Interface)** becomes an essential tool. In this guide, we will explore:

•	What is **Azure CLI**, and why use it?

•	Installing and configuring Azure CLI on **Windows, macOS, and Linux**

•	Creating **Azure resources** (Resource Groups, Virtual Machines, Virtual Networks, and Storage Accounts)

•	Automating repetitive tasks using **Azure CLI scripts**

•	Best practices and real-world **use cases** for DevOps engineers

---

**Why Should DevOps Engineers Use Azure CLI?**

**1. Speed and Efficiency**

Using the **Azure Portal** for resource creation is fine for a **single virtual machine**, but for large-scale operations, **CLI is significantly faster.**

Example:

•	If you need to check **all running VMs** across 10 projects, using the portal requires **navigating multiple pages**.

•	With **Azure CLI**, a single command provides the required details instantly.

**2. Automation and Consistency**

DevOps engineers work on **repetitive tasks** such as setting up **Azure Kubernetes Service (AKS)** clusters, **networking components**, and **storage accounts.**

•	Manual setup via UI can lead to **human errors** (missing configurations).

•	Azure CLI **scripts** ensure consistency and **version control** (stored in Git).

**3. Scalability**

When a project requires **multiple environments (dev, test, prod)**, setting them up manually in the portal is **not scalable**.

With **Azure CLI**, you can:

•	Define infrastructure using **scripts**.

•	Reuse the same commands for multiple environments.

•	Easily modify parameters instead of manually clicking through UI.

---

**Installing Azure CLI**

**Windows Installation**

**1.**	Download the **Azure CLI MSI Installer** from the official documentation (https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?pivots=winget).

**2.**	Run the installer and follow the **setup wizard**.

**3.**	Verify the installation:

```sh
az --version
```

**macOS Installation**

Use **Homebrew** to install Azure CLI:

```sh
brew update && brew install azure-cli
```

**Linux Installation (Ubuntu/Debian)**

```sh
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Verify installation for all platforms:

```sh
az --version
```

---

**Configuring Azure CLI**

After installation, authenticate your **Azure account:**

```sh
az login
```

This command:

•	Opens a **browser window** to sign in.

•	Retrieves your **Azure subscription details.**

Check available **Azure subscriptions:**

```sh
az account list --output table
```

Set a default subscription:

```sh
az account set --subscription "<SUBSCRIPTION_ID>"
```

---

**Creating Azure Resources Using CLI**

**1. Create a Resource Group**

A **Resource Group** is a container that holds related **Azure resources.**

```sh
az group create --name myResourceGroup --location eastus
```

**2. Create a Virtual Machine**

```sh
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

This command:

•	Creates a **VM** named myVM.

•	Uses the **Ubuntu LTS** image.

•	Generates **SSH keys** for authentication.

**3. Create a Virtual Network and Subnet**

```sh
az network vnet create \
    --resource-group myResourceGroup \
    --name myVNet \
    --subnet-name mySubnet
```

**4. Create an Azure Storage Account**

```sh
az storage account create \
    --name mystorageaccount2025 \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS
```

**5. Deleting Resources**

To **delete a Resource Group** and all associated resources:

```sh
az group delete --name myResourceGroup --yes --no-wait
```

---

**Best Practices for Using Azure CLI**

**1. Store CLI Scripts in Git**

•	Save frequently used Azure CLI commands in a **Git repository.**

•	Helps **team members reuse scripts** instead of manually navigating the Azure portal.

**2. Use Parameters for Reusability**

Instead of hardcoding values, use variables:

```sh
RESOURCE_GROUP="myResourceGroup"
VM_NAME="myVM"
az vm create --resource-group $RESOURCE_GROUP --name $VM_NAME --image UbuntuLTS
```

**3. Automate Infrastructure with CI/CD**

Integrate **Azure CLI** in CI/CD pipelines:

**•	GitHub Actions**

**•	GitLab CI/CD**

**•	Azure DevOps Pipelines**

Example:

```sh
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Install Azure CLI
        run: sudo apt-get install -y azure-cli
      - name: Login to Azure
        run: az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_SECRET --tenant $AZURE_TENANT_ID
      - name: Create Resource Group
        run: az group create --name myResourceGroup --location eastus
```

**4. Avoid Manual Configurations**

•	Always **document CLI commands** for onboarding new engineers.

•	Reduce reliance on **Azure Portal** for repetitive tasks.

---

**Real-World Use Cases for DevOps Engineers**

**1. Deploying AKS (Azure Kubernetes Service) with Azure CLI**

```sh
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys
```

**2. Managing Azure Resources Across Teams**

Instead of navigating the UI, list all virtual machines:

```sh
az vm list --output table
```
Find active VMs for a specific project:

```sh
az vm list --query "[?tags.project=='ProjectX']" --output table
```

**3. Cost Optimization Using Azure CLI**

Find idle VMs and stop them to save costs:

```sh
az vm list --query "[?powerState=='VM running']" --output table
az vm stop --resource-group myResourceGroup --name myVM
```

---

**Conclusion**

Using **Azure CLI** allows **DevOps engineers** to:

**•	Automate infrastructure deployment.**

**•	Reduce human errors** caused by manual configurations.

**•	Improve efficiency** by quickly executing commands.

**•	Enable team collaboration** by storing CLI scripts in Git repositories.
