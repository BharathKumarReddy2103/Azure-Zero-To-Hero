**Azure Virtual Machines:**

**Introduction**

In this article, we will cover **Azure Virtual Machines (VMs)** in detail, including virtualization concepts, VM creation, deployment of a DevOps application, and **Virtual Machine Scale Sets (VMSS)** for **auto-scaling.** This guide is part of the **Azure Zero to Hero series** and is structured for **DevOps Engineers, Cloud Engineers, and Architects** who want to understand Azure VM fundamentals.

By the end of this guide, you will learn:

**•	Virtualization** concepts and how they work in cloud computing.

•	How to **create and configure a virtual machine** on Azure.

•	How to **deploy a DevOps application (Jenkins)** on an Azure VM.

**•	Networking and Security Groups** for VM access.

**•	Auto-scaling virtual machines** using **Virtual Machine Scale Sets (VMSS).**

---

**1. Understanding Virtualization in Cloud Computing**

**What is Virtualization?**

Before cloud computing, **physical servers** were the standard for hosting applications. Each organization would purchase **physical servers**, and **system administrators** would manage them. However, this approach led to **resource underutilization** and **high operational costs.**

To solve this problem, **virtualization** was introduced. Virtualization allows a single **physical server** to be **logically divided** into multiple **virtual machines (VMs)** using a **hypervisor.**

**How Virtualization Works in Azure**

**•	Azure purchases** large numbers of **physical servers** from vendors (e.g., IBM, Dell).

•	Inside Azure **data centers** (e.g., **East US Zone 1**), multiple **physical servers** are deployed.

•	A **hypervisor** is installed on each **physical server** to create **multiple VMs.**

•	When a user requests a VM, Azure dynamically assigns **CPU, RAM, and storage** from an available hypervisor.

**Example:**

If a DevOps Engineer requests a **1 CPU, 2 GB RAM VM in East US**, Azure’s **Resource Manager** checks availability and assigns the required resources from a physical server.

---

**2. Creating a Virtual Machine on Azure**

To create a virtual machine on Azure, follow these steps:

**Step 1: Navigate to Virtual Machines**

**1.**	Log in to **Azure Portal.**

**2.**	Search for **Virtual Machines** in the search bar.

**3.**	Click **Create** → **Virtual Machine.**

**Step 2: Configure VM Settings**

| **Parameter**         | **Description** |
|----------------------|----------------|
| **Subscription**     | Select **Free Trial** or your existing subscription. |
| **Resource Group**   | Create a new or select an existing **resource group**. |
| **Virtual Machine Name** | Provide a meaningful name, e.g., `devops-vm`. |
| **Region**          | Choose a region **close to your users** (e.g., `East US`). |
| **Availability Zone** | Select `Zone 1`, `Zone 2`, or `Zone 3` (for HA). |
| **Image**           | Choose **Ubuntu Server 22.04 LTS** (recommended). |
| **Size**            | Choose **B1s (Free Tier Eligible)** for demos. |
| **Authentication**  | Use **SSH key** (recommended). |

**Step 3: Review and Deploy**

**1.**	Click **Review + Create.**

**2.**	Download the **private key** for SSH access.

**3.**	Click **Create** and wait for the VM to be provisioned.

---

**3. Connecting to an Azure Virtual Machine**

Once the VM is created, you can connect to it using SSH.

**1.	Copy the Public IP Address** from the Azure Portal.

**2.**	Open **Git Bash** (Windows) or **iTerm** (Mac/Linux).

**3.**	Navigate to the directory where the private key is stored.

```sh
ssh -i ~/Downloads/mykey.pem azureuser@<public-ip>
```

1.	If you get a **permissions error**, run:

```sh
chmod 600 ~/Downloads/mykey.pem
```
1.	Once connected, you should see:

```sh
azureuser@devops-vm:~$
```

---

**4. Deploying Jenkins on the Virtual Machine**

**Step 1: Update the System**

```sh
sudo apt update && sudo apt upgrade -y
```

**Step 2: Install Java (Required for Jenkins)**

```sh
sudo apt install openjdk-11-jdk -y
```

**Step 3: Install Jenkins**

```sh
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
```

**Step 4: Start and Enable Jenkins**

```sh
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

**Step 5: Check Jenkins Status**

```sh
sudo systemctl status jenkins
```

**Step 6: Open Jenkins in Browser**

**1.**	Copy the **public IP address** of your VM.

**2.**	Open the browser and go to:

```sh
http://<public-ip>:8080
```

**Note:** If the page does not load, **open port 8080** in Azure’s **Network Security Group (NSG).**

```sh
az network nsg rule create --resource-group demo --nsg-name myNSG --name AllowJenkins --protocol tcp --direction inbound --priority 100 --source-address-prefixes '*' --source-port-ranges '*' --destination-port-ranges 8080 --access allow
```

---

**5. Understanding Virtual Machine Scale Sets (VMSS)**

**What is VMSS?**

Virtual Machine Scale Sets (VMSS) allow **automatic scaling** of VMs based on **traffic load**. This is useful for **handling sudden spikes in user requests.**

**Example:**

•	Your Jenkins server usually gets **1,000 requests per day.**

•	During a software release, it spikes to **50,000 requests.**

•	With VMSS, Azure **automatically scales** the number of VMs **based on demand.**

**Key Benefits of VMSS**

✅ **Auto-scaling:** Automatically increases or decreases VM count.

✅ **Load Balancing:** Evenly distributes traffic across VMs.

✅ **Cost Optimization:** Only scales **when needed,** reducing cost.

✅ **Fault Tolerance:** Ensures high availability by distributing VMs across zones.

**Creating a VMSS**

```sh
az vmss create \
  --resource-group demo \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys \
  --instance-count 2 \
  --vm-sku Standard_DS1_v2 \
  --upgrade-policy-mode automatic
```

**Verify Scaling Policy**

```sh
az monitor autoscale show --resource-group demo --name myScaleSet
```

---

**Conclusion**

In this guide, we covered:

**•	Virtualization concepts** in Azure.

•	How to **create, configure, and connect** to an Azure VM.

•	Deploying a **DevOps application (Jenkins)** on a VM.

**•	Networking & security** for VM access.

**•	Virtual Machine Scale Sets (VMSS)** for auto-scaling.

This knowledge is essential for **DevOps Engineers and Cloud Architects** to efficiently manage infrastructure in Azure.

🚀 **Next Steps:**

**•	Explore Azure Load Balancers** to distribute traffic across VMs.

**•	Implement Infrastructure as Code (IaC)** using **Terraform** or **Bicep.**

**•	Integrate CI/CD pipelines** with Azure DevOps & Kubernetes.

📌 **Follow this repository for more Azure Zero to Hero content** ⭐
