**Managing Azure Resources Using Terraform**

**Introduction**

In the world of **Infrastructure as Code (IaC), Terraform** has become a widely adopted tool for managing cloud infrastructure efficiently. This article focuses on **managing Azure resources using Terraform**, explaining how to:

•	Connect Terraform with Azure

•	Create and manage Azure resources using Terraform

•	Configure Terraform in **Visual Studio Code (VS Code)**

•	Store Terraform state files in **Azure Blob Storage**

•	Follow best practices for **Terraform with Azure**

By the end of this guide, you will have a clear understanding of how **Terraform integrates with Azure**, making infrastructure provisioning **automated, scalable, and efficient.**

---

**Why Use Terraform for Azure Resource Management?**

You might wonder, **why use Terraform when Azure provides its own automation tools** like:

**•	Azure Resource Manager (ARM) Templates**

**•	Azure CLI**

**•	Azure SDK**

**•	Bicep**

The answer is **portability and flexibility.** Unlike Azure-specific tools, **Terraform** supports multiple cloud providers (**AWS, GCP, OpenStack, DigitalOcean, etc.**) with **one common language - HashiCorp Configuration Language (HCL).**

**Key Benefits of Terraform:**

**•	Multi-cloud support** – Manage Azure, AWS, and GCP infrastructure with a single language.

**•	Declarative syntax** – Define the desired state, and Terraform ensures infrastructure matches it.

**•	State management** – Keeps track of infrastructure changes via a state file.

**•	Modular and reusable** – Terraform configurations can be **modularized and reused** across projects.

**•	API abstraction** – No need to interact directly with cloud provider APIs; Terraform does the heavy lifting.

---

**Setting Up Terraform with Azure**

**1. Install Visual Studio Code & Terraform Extension**

If you haven’t already, install **VS Code** and the **Terraform extension** to simplify your development workflow.

```sh
# Download and install Terraform (Linux/Mac)
wget https://releases.hashicorp.com/terraform/X.Y.Z/terraform_X.Y.Z_linux_amd64.zip
unzip terraform_X.Y.Z_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform --version
```

For **Windows,** download Terraform from the official **Terraform downloads page** (https://developer.hashicorp.com/terraform/install).

**2. Install Azure CLI**

Terraform uses **Azure CLI** for authentication. Install it using the following commands:

**For Ubuntu/Debian**

```sh
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

**For Windows**

Download and install Azure CLI from the **official Microsoft website**.

**3. Authenticate Terraform with Azure**

Run the following command to authenticate Terraform with Azure:

```sh
az login
```

This will open a browser window prompting you to log into your **Azure account**. Once logged in, Terraform will have the required authentication.

---

**Writing Terraform Configuration for Azure**

Now, let’s write a Terraform script to **provision a virtual machine in Azure** along with supporting resources like a **Virtual Network (VNet), Subnet, Network Interface (NIC), and Public IP.**

**1. Define the Terraform Provider**

Create a file main.tf and define the **Terraform provider for Azure:**

```sh
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~>3.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

**2. Create an Azure Resource Group**

```sh
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "East US"
}
```

**3. Define a Virtual Network (VNet) and Subnet**

```sh
resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "example" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"]
}
```

**4. Create a Network Interface (NIC)**

```sh
resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
  }
}
```

**5. Create a Virtual Machine (VM)**

```sh
resource "azurerm_linux_virtual_machine" "example" {
  name                = "example-vm"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  network_interface_ids = [azurerm_network_interface.example.id]

  size               = "Standard_B1s"
  admin_username     = "adminuser"
  disable_password_authentication = true

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

**6. Create and Attach a Public IP**

```sh
resource "azurerm_public_ip" "example" {
  name                = "example-public-ip"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  allocation_method   = "Dynamic"
}

resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "public"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.example.id
  }
}
```

---

**Storing Terraform State in Azure Blob Storage**

By default, Terraform stores its **state file locally**. This is **not ideal for team collaboration**. Instead, we can **store it in Azure Blob Storage** for shared access.

**1. Create a Storage Account & Container**

```sh
resource "azurerm_storage_account" "example" {
  name                     = "terraformstateexample"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "example" {
  name                  = "tfstate"
  storage_account_name  = azurerm_storage_account.example.name
  container_access_type = "private"
}
```

**2. Configure Terraform Backend**

Modify main.tf to store Terraform state remotely:

```sh
terraform {
  backend "azurerm" {
    resource_group_name   = "example-resources"
    storage_account_name  = "terraformstateexample"
    container_name        = "tfstate"
    key                   = "terraform.tfstate"
  }
}
```

Run terraform init again to initialize the backend.

---

**Applying the Terraform Configuration**

Once everything is set up, execute the following commands:

```sh
terraform init  # Initialize Terraform
terraform plan  # Check changes before applying
terraform apply # Apply the Terraform configuration
```

To destroy the infrastructure:

```sh
terraform destroy
```

---

**Conclusion**

In this guide, we successfully:

✅ Connected **Terraform with Azure**

✅ Created **Azure resources** using **Terraform HCL**

✅ Configured **Terraform backend** to store state in **Azure Blob Storage**

✅ Applied **best practices** for **Azure DevOps automation**

This approach ensures **scalability, security, and efficiency** in managing Azure infrastructure.

🚀 **Happy DevOps Engineering**
