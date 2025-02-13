**Terraform for Azure**

**1. What is Terraform?**

Terraform is an **Infrastructure as Code (IaC)** **tool** developed by **HashiCorp**, used to **provision, manage, and automate infrastructure** across cloud providers like **Azure, AWS, and GCP.**

Terraform allows **DevOps engineers** to define infrastructure in a **declarative configuration language (HCL - HashiCorp Configuration Language)**, enabling **automated deployments, version control, and repeatability.**

**Key Features of Terraform:**

✅ **Declarative IaC** → Define infrastructure using code.

✅ **Multi-Cloud Support** → Works with **Azure, AWS, GCP, Kubernetes, and on-prem.**

✅ **State Management** → Maintains an **infrastructure state file** to track deployed resources.

✅ **Modular Infrastructure** → Use **Terraform modules** for reusable configurations.

✅ **Drift Detection** → Detects changes in **Azure resources** and synchronizes them with the desired state.

✅ **Parallel Execution** → Deploys resources efficiently using a **dependency graph.**

---

**2. How to Connect Azure with Terraform?**

**Prerequisites:**

   - **Azure Subscription**

   - **Terraform Installed** (terraform -v to check)

   - **Azure CLI Installed** (az --version to check)

---

**Step 1: Authenticate Terraform with Azure**

1️⃣ **Login to Azure using CLI**

```bash
az login
```

   - This will open a browser for authentication.
     
   - After successful login, it will list subscription details.

2️⃣ **Set the Active Subscription** (If you have multiple Azure subscriptions)

```bash
az account set --subscription "<your-subscription-id>"
```

---

**Step 2: Create a Service Principal for Terraform Authentication**

Terraform **requires a Service Principal** to authenticate with Azure.

```bash
az ad sp create-for-rbac --name "terraform-sp" --role="Contributor" --scopes="/subscriptions/<your-subscription-id>"
```

   - This will return a JSON response containing:

   - **Client ID**
    
   - **Client Secret**

   - **Tenant ID**

   - **Subscription ID**

---

**Step 3: Set Environment Variables for Terraform Authentication**

To avoid hardcoding credentials in Terraform files, export them as **environment variables:**

```bash
export ARM_CLIENT_ID="<Client-ID>"
export ARM_CLIENT_SECRET="<Client-Secret>"
export ARM_SUBSCRIPTION_ID="<Subscription-ID>"
export ARM_TENANT_ID="<Tenant-ID>"
```

Alternatively, **store credentials securely in Azure Key Vault** and fetch them dynamically.

---

**3. How to Create Resources on Azure with Terraform?**

**Project Goal: Deploy an Azure Virtual Machine using Terraform**

---

**Step 1: Define Terraform Configuration** (main.tf)

```bash
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "my_rg" {
  name     = "my-terraform-rg"
  location = "East US"
}

resource "azurerm_virtual_network" "my_vnet" {
  name                = "my-vnet"
  location            = azurerm_resource_group.my_rg.location
  resource_group_name = azurerm_resource_group.my_rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "my_subnet" {
  name                 = "my-subnet"
  resource_group_name  = azurerm_resource_group.my_rg.name
  virtual_network_name = azurerm_virtual_network.my_vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

resource "azurerm_network_interface" "my_nic" {
  name                = "my-nic"
  location            = azurerm_resource_group.my_rg.location
  resource_group_name = azurerm_resource_group.my_rg.name

  ip_configuration {
    name                          = "my-ipconfig"
    subnet_id                     = azurerm_subnet.my_subnet.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_linux_virtual_machine" "my_vm" {
  name                = "my-vm"
  resource_group_name = azurerm_resource_group.my_rg.name
  location            = azurerm_resource_group.my_rg.location
  size                = "Standard_DS1_v2"
  admin_username      = "adminuser"

  network_interface_ids = [azurerm_network_interface.my_nic.id]

  admin_ssh_key {
    username   = "adminuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }
}
```

---

**Step 2: Initialize Terraform**

```bash
terraform init
```

This downloads the necessary **Azure provider plugins.**

---

**Step 3: Validate Terraform Configuration**

```bash
terraform validate
```

Ensures the configuration has no syntax errors.

---

**Step 4: Preview Infrastructure Changes**

```bash
terraform plan
```

This generates an execution plan and shows which resources will be created.

---

**Step 5: Apply Terraform Configuration**

```bash
terraform apply -auto-approve
```

This command deploys the infrastructure on Azure.

---

**Step 6: Destroy Resources (Cleanup)**

```bash
terraform destroy -auto-approve
```

This removes all resources created by Terraform.

---

**4. Terraform State File Management in Azure**

Terraform stores **state information** in a file called terraform.tfstate. This state file **tracks deployed resources** to allow Terraform to manage infrastructure effectively.

**Why Use Remote State?**

**State locking** → Prevents conflicts when multiple engineers modify infrastructure.

**Collaboration** → Allows multiple users to manage infrastructure.

**Security** → Keeps sensitive information encrypted in **Azure Storage** instead of local files.

---

**Step 1: Create an Azure Storage Account for State Management**

```bash
az storage account create --name mystorageaccount --resource-group MyResourceGroup --location eastus --sku Standard_LRS
```

**Step 2: Create a Storage Container**

```bash
az storage container create --name terraformstate --account-name mystorageaccount
```

**Step 3: Configure Remote State in Terraform**

Modify main.tf to use **Azure Blob Storage for state management:**

```bash
terraform {
  backend "azurerm" {
    resource_group_name   = "MyResourceGroup"
    storage_account_name  = "mystorageaccount"
    container_name        = "terraformstate"
    key                   = "terraform.tfstate"
  }
}
```

**Step 4: Reinitialize Terraform to Migrate State**

```bash
terraform init -migrate-state
```

   - Now, Terraform stores the state in Azure Storage instead of local files.

---

**5. Best Practices for Terraform in Azure Cloud**

✅ **Use Remote State with State Locking**

   - Store state in Azure Storage with locking enabled.

✅ **Use Terraform Modules for Reusability**

   - Organize code into modules for Virtual Networks, VMs, and Databases.

✅ **Use Variables & Secrets Management**

   - Store sensitive credentials in Azure Key Vault instead of hardcoding them.
     
✅ **Enable Azure RBAC & Least Privilege Access**

   - Assign minimum required permissions to the Terraform Service Principal.

✅ **Automate Terraform with CI/CD Pipelines**

   - Use Azure DevOps Pipelines or GitHub Actions to deploy Terraform automatically.

✅ **Implement Drift Detection**

   - Regularly run terraform plan to detect manual changes in Azure resources.

✅ **Enable Logging & Monitoring**

   - Use Azure Monitor to track Terraform deployment activities.

---

6. **Conclusion**

Terraform simplifies **infrastructure automation in Azure** by providing **scalable, repeatable, and version-controlled deployments.**

🔹 **Key Takeaways:**

   - Connect **Terraform with Azure** using **Service Principal authentication.**

   - **Use Terraform to provision Azure VMs, Networking, and Storage.**

   - **Manage Terraform state in Azure Storage** for collaboration.

   - **Follow best practices** to secure, scale, and optimize Terraform deployments.
