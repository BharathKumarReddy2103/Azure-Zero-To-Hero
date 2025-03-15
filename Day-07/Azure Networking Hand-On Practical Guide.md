**Azure Virtual Network: Hands-On Practical Guide**

**Introduction**

In modern cloud architectures, **network security** and **access management** are critical components. When deploying virtual machines (VMs) in **Azure**, setting up a **Virtual Network (VNet)** with **firewalls** and **Bastion** helps ensure that only authorized users can access resources securely.

This guide walks you through a **step-by-step hands-on implementation** of:

•	Creating an **Azure Virtual Network (VNet)**

•	Deploying a **firewall** and configuring rules

•	Setting up **subnets** for a web application

•	Deploying an **NGINX server** on a private VM

•	Configuring **Azure Bastion** for secure SSH access

•	Understanding **Network Security Groups (NSG)** and **firewall policies**

This **practical tutorial** is suitable for **DevOps Engineers, Cloud Architects, and Security Engineers** looking to master **Azure networking concepts.**

---

**1. Understanding the Azure Networking Setup**

Before jumping into the implementation, let's define the **high-level architecture:**

**1.	Create an Azure Virtual Network (VNet)**

**2.	Set up a subnet for the web application**

**3.	Deploy a Virtual Machine (VM) inside the subnet**

**4.	Install and configure NGINX** to serve a static web page

**5.	Deploy an Azure Firewall** for controlled access

**6.	Use Azure Bastion** to SSH into the VM securely

**7.	Implement Network Security Groups (NSG) and Firewall Rules**

**Diagram Overview**

```sh
+----------------------+  
| End User            |  
+----------------------+  
           |  
           v  
+----------------------+  
| Azure Firewall       | <-- Public IP  
+----------------------+  
           |  
           v  
+----------------------+  
| Virtual Network (VNet) |  
+----------------------+  
           |  
           v  
+----------------------+  
| Web Application Subnet |  
+----------------------+  
           |  
           v  
+----------------------+  
| Virtual Machine (VM)  |  
| (NGINX Server)        |  
+----------------------+
```

---

**2. Step-by-Step Hands-On Guide**

**Step 1: Create an Azure Resource Group**

In **Azure**, every resource must be placed inside a **Resource Group.**

**1.**	Navigate to **Azure Portal → Resource Groups**

**2.**	Click **Create** and enter:

**o	Resource Group Name**: azure-network-demo

**o	Region**: East US (or nearest to you)

**3.**	Click **Create**

Alternatively, using **Azure CLI:**

```sh
az group create --name azure-network-demo --location eastus
```

---

**Step 2: Create a Virtual Network (VNet) and Subnets**

**1.**	Navigate to **Azure Portal → Virtual Networks**

**2.**	Click **Create**

**3.**	Enter details:

**o	VNet Name**: demo-vnet

**o	Resource Group**: azure-network-demo

**o	Address Space**: 10.0.0.0/16

**4.**	Add **subnets:**

**o	Web Subnet**: 10.0.1.0/24

**o	Firewall Subnet**: 10.0.2.0/24

**o	Bastion Subnet**: 10.0.3.0/24

**Azure CLI Command:**

```sh
az network vnet create --resource-group azure-network-demo --name demo-vnet --address-prefix 10.0.0.0/16
az network vnet subnet create --resource-group azure-network-demo --vnet-name demo-vnet --name web-subnet --address-prefix 10.0.1.0/24
```

---

**Step 3: Deploy a Virtual Machine (VM) in the Subnet**

**1.**	Navigate to **Azure Portal → Virtual Machines → Create VM**

**2.**	Select:

**o	Name**: nginx-web-vm

**o	Resource Group**: azure-network-demo

**o	Image**: Ubuntu 20.04

**o	Size**: Standard_B1ls (Free Tier)

**o	Authentication**: SSH Key

**o	Public IP**: None (Private VM)

**o	Subnet**: web-subnet

**3.**	Click **Create** and download the SSH key

**Azure CLI Command:**

```sh
az vm create --resource-group azure-network-demo --name nginx-web-vm --vnet-name demo-vnet --subnet web-subnet --image UbuntuLTS --size Standard_B1ls --admin-username azureuser --generate-ssh-keys --public-ip-address ""
```

---

**Step 4: Install NGINX and Host a Static Website**

Connect to the VM via **Bastion** or Azure CLI:

```sh
az ssh vm --name nginx-web-vm --resource-group azure-network-demo
```

Inside the VM, install NGINX:

```sh
sudo apt update
sudo apt install -y nginx
echo "<h1>I learned Azure networking today!</h1>" | sudo tee /var/www/html/index.html
sudo systemctl restart nginx
```

---

**Step 5: Configure Azure Firewall for Secure Access**

**1.	Go to Azure Portal → Firewall**

**2.**	Create a new **Azure Firewall** in azure-network-demo

**3.**	Assign a **Public IP Address** to the firewall

**4.**	Configure **D-NAT (Destination Network Address Translation) Rule:**

**o	Source**: Your Public IP

**o	Destination**: **Firewall Public IP:4000**

**o	Target IP**: nginx-web-vm Private IP

**o	Target Port**: 80 (NGINX)

**Verify Access:**

```sh
curl http://<firewall-public-ip>:4000
```

or open in a browser:

```sh
http://<firewall-public-ip>:4000
```

---

**Step 6: Secure SSH Access with Azure Bastion**

**1.**	Navigate to **Azure Portal → Bastion**

**2.**	Click **Create** and select:

**o	Subnet**: bastion-subnet

**o	Public IP**: Enabled

**3.**	Click **Create**

Once deployed, connect to the VM via **Bastion:**

•	Select nginx-web-vm → Click **Connect** → **Bastion**

•	Enter the SSH **private key** and connect securely

---

**3. Best Practices and Security Considerations**

**•	Restrict Firewall Access:** Only allow specific IPs to access services

**•	Use Bastion for Secure SSH:** Never expose VMs with **public IPs**

**•	Enable Network Security Groups (NSG):** Limit inbound and outbound traffic

**•	Implement Logging and Monitoring:** Use **Azure Monitor** for tracking access

**•	Automate Deployments:** Use **Terraform or Bicep** for scalable networking

---

**Conclusion**

This hands-on **Azure networking tutorial** covered **VNet, Firewall, Subnets, Virtual Machines, Bastion, and Security Groups**. By implementing these **best practices**, you ensure a **secure, scalable, and efficient** network infrastructure in **Azure Cloud.**
