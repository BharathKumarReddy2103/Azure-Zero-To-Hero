# **Azure CLI (Command-Line Interface)**  

Azure CLI (**Azure Command-Line Interface**) is a cross-platform command-line tool used to manage **Azure resources**. It allows **DevOps Engineers** to automate cloud operations, deploy applications, and manage infrastructure directly from the command line.

---

## **Why Use Azure CLI?**  

- **Cross-platform** – Works on Windows, macOS, and Linux.  
- **Scriptable** – Can be used in shell scripts (Bash, PowerShell) for automation.  
- **Faster Deployment** – Manage resources quickly without using the Azure Portal.  
- **Supports JSON, Table, TSV Output** – Enables structured data processing.  

---

## **How to Install Azure CLI?**  

### **1. Windows:**  

Download and install from [Microsoft Docs](https://aka.ms/installazurecliwindows) or use PowerShell:  

```bash
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -ArgumentList '/I AzureCLI.msi /quiet' -NoNewWindow -Wait
```

**2. macOS:**

Use Homebrew:

```bash
brew update && brew install azure-cli
```

**3. Linux (Ubuntu/Debian):**

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

**4. Check Installation:**

```bash
az --version
```

**How to Log in to Azure Using CLI?**

Before running any Azure CLI commands, authenticate using:

```bash
az login
```

It will open a browser for authentication. After successful login, you will see a list of subscriptions.

To set a default Azure subscription:

```bash
az account set --subscription "YOUR_SUBSCRIPTION_NAME"
```

**Creating Azure Resources Using Azure CLI**

**1. Create a Resource Group**

```bash
az group create --name MyResourceGroup --location eastus
```
--name → Name of the resource group

--location → Azure region where resources will be created

**2. Create a Virtual Network (VNet)**

```bash
az network vnet create \
  --name MyVNet \
  --resource-group MyResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name MySubnet \
  --subnet-prefix 10.0.1.0/24
```

**3. Create an Azure Virtual Machine (VM)**

```bash
az vm create \
  --resource-group MyResourceGroup \
  --name MyVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

--image UbuntuLTS → Uses Ubuntu as the OS

--generate-ssh-keys → Automatically generates SSH keys

**4. Create an Azure Storage Account**

```bash
az storage account create \
  --name mystorageaccountxyz \
  --resource-group MyResourceGroup \
  --location eastus \
  --sku Standard_LRS
```

**5. Create an Azure Kubernetes Service (AKS) Cluster**

```bash
az aks create \
  --resource-group MyResourceGroup \
  --name MyAKSCluster \
  --node-count 2 \
  --enable-addons monitoring \
  --generate-ssh-keys
```

**6. Deploy an Azure Function App**

```bash
az functionapp create \
  --name MyFunctionApp \
  --resource-group MyResourceGroup \
  --storage-account mystorageaccountxyz \
  --consumption-plan-location eastus \
  --runtime python
```

**7. Deploy an Azure Web App**

```bash
az webapp create \
  --resource-group MyResourceGroup \
  --plan MyAppServicePlan \
  --name MyWebApp \
  --runtime "PYTHON|3.8"
```

**Managing Azure Resources with Azure CLI**

**1. List All Resource Groups**

```bash
az group list --output table
```

**2. List All Virtual Machines**

```bash
az vm list --output table
```

**3. Start a Virtual Machine**

```bash
az vm start --resource-group MyResourceGroup --name MyVM
```

**4. Stop a Virtual Machine**

```bash
az vm stop --resource-group MyResourceGroup --name MyVM
```

**5. Delete a Resource Group (Deletes all resources inside it!)**

```bash
az group delete --name MyResourceGroup --yes --no-wait
```

---

**Azure CLI vs Azure Portal vs Terraform - Understanding the Differences**

Azure CLI, Azure Portal, and Terraform are three different ways to manage and deploy resources in Azure. Each has its strengths and is suited for different use cases.

**1. Overview**

Azure CLI is a command-line tool used to manage Azure resources via commands and scripts. It is best suited for automation, scripting, and quick resource management.

Azure Portal is a web-based graphical interface where users can manually create, manage, and monitor Azure resources. It is ideal for beginners and one-time configurations.

Terraform is an Infrastructure as Code (IaC) tool that allows you to define and manage infrastructure through code. It is best for large-scale, repeatable deployments and automation.

---

**2. Comparison**

**Azure CLI (Command-Line Interface)**

Azure CLI is a command-line tool that interacts with Azure resources via terminal commands. It supports Windows, macOS, and Linux and is widely used for automation.

**Some key benefits of Azure CLI:**

It is faster than using the Azure Portal for creating and managing resources.

It supports scripting with Bash, PowerShell, and Python for automation.

It provides JSON, Table, and TSV output formats for easy parsing.

A downside of Azure CLI is that it does not track infrastructure state like Terraform, making it less suitable for managing large-scale deployments.

**Example: Creating a Virtual Machine using Azure CLI**

```bash
az vm create --resource-group MyResourceGroup --name MyVM --image UbuntuLTS --admin-username azureuser --generate-ssh-keys
```

This command quickly provisions a VM without navigating through the Azure Portal.

---

**Azure Portal**

Azure Portal is a web-based interface that allows users to manage Azure resources through a visual dashboard. It is the easiest way for beginners to get started with Azure.

**Some advantages of Azure Portal:**

Provides an easy-to-use graphical interface.

No need to remember or write commands.

Real-time monitoring and dashboards for resources.

However, Azure Portal is not ideal for automation. Since configurations are done manually, it lacks repeatability and can be time-consuming for large-scale deployments.

**Example: Creating a Virtual Machine using Azure Portal**

Log in to Azure Portal.

Navigate to Virtual Machines and click Create.

Choose Subscription, Resource Group, Image, and VM Size.

Configure Networking, Disks, and Security settings.

Click Review + Create to deploy the VM.

This method is user-friendly but slower than Azure CLI and Terraform for repetitive tasks.

---

**Terraform**

Terraform is an Infrastructure as Code (IaC) tool that allows you to define Azure infrastructure in a declarative way. Unlike Azure CLI and Portal, Terraform maintains a state file that tracks infrastructure changes, making it easier to manage and replicate environments.

**Some key benefits of Terraform:**

Infrastructure as Code allows defining resources in a version-controlled format.

Supports multi-cloud deployments, unlike Azure CLI and Portal.

Ensures consistency and repeatability in infrastructure provisioning.

Enables collaboration by storing configurations in a Git repository.

**Example: Creating a Virtual Machine using Terraform**

```bash
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "MyResourceGroup"
  location = "East US"
}

resource "azurerm_virtual_machine" "example" {
  name                  = "MyVM"
  resource_group_name   = azurerm_resource_group.example.name
  location              = azurerm_resource_group.example.location
  vm_size               = "Standard_DS1_v2"
  admin_username        = "azureuser"
}
```
After writing the configuration, deploy the infrastructure using:

```bash
terraform init
terraform apply
```
Terraform ensures that the same infrastructure can be recreated whenever needed.

---

**3. When to Use What?**

Azure CLI is best for quick commands, automation, and scripting tasks. It is ideal for DevOps engineers who need to perform frequent actions on Azure resources.

Azure Portal is best for beginners and for visualizing and managing Azure resources manually. It is suitable for one-time configurations and monitoring workloads.

Terraform is best for large-scale, automated, and repeatable deployments. It is essential for Infrastructure as Code (IaC) and multi-cloud environments.

---

**4. Conclusion**

For a DevOps Engineer, Azure CLI and Terraform are the best tools for managing Azure infrastructure efficiently. Azure CLI is great for quick tasks and automation, while Terraform is essential for defining infrastructure as code and ensuring repeatability.
