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

Final Thoughts
Azure CLI is an essential tool for DevOps Engineers to manage Azure infrastructure efficiently. It helps automate deployments, reduce manual effort, and integrate with CI/CD pipelines.
