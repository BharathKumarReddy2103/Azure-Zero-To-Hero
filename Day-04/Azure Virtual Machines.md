**Virtualization:**

**Virtualization** is the process of creating a virtual version of a physical resource, such as servers, storage devices, or networks, using software. It allows multiple virtual resources to operate independently on a single physical system.

**Components of Virtualization**

1. **Hypervisor:**

   The key component that enables virtualization.

   It abstracts physical hardware and allows multiple virtual machines (VMs) to run on a single host.

**Types of hypervisors:**

 Type 1 (Bare-metal): Runs directly on hardware (e.g., VMware ESXi, Microsoft Hyper-V).

 Type 2 (Hosted): Runs on a host operating system (e.g., Oracle VirtualBox).

2. **Virtual Machines (VMs):**

   Virtualized instances that mimic physical computers.

   Each VM has its own OS, applications, and resources.

3. **Guest OS:**

   The operating system running inside a VM.

4. **Virtual Network:**

   Virtualization of network components like routers and switches for communication between VMs.

5. **Storage Virtualization:**

   Abstracting physical storage into virtual storage pools accessible by VMs.

**Key Concepts of Virtualization**

1. **Resource Abstraction:**

   Resources like CPU, memory, and storage are abstracted and allocated to VMs.

2. **Isolation:**

   VMs operate independently, ensuring that the failure or security issues of one VM do not impact others.

3. **Encapsulation:**

   VMs are encapsulated into files, making them portable and easy to back up or restore.

4. **Live Migration:**

  VMs can be moved between hosts without downtime.

**Benefits of Virtualization**

1. **Cost Efficiency:**

  Reduces hardware costs by running multiple VMs on a single physical server.

2. **Scalability:**

   Easily add or remove virtual resources as needed.

3. **Flexibility and Agility:**

   Quickly deploy VMs and adapt to changing workloads.

4. **High Availability:**

   Minimize downtime with features like failover and load balancing.

5. **Better Resource Utilization:**

   Optimizes hardware utilization by sharing resources among VMs.

6. **Simplified Management:**

   Centralized management tools streamline resource provisioning and monitoring.

**Azure Virtual Machine (VM)**

  Azure Virtual Machines provide IaaS (Infrastructure as a Service), allowing you to run virtualized instances of Windows 
  or Linux in the Azure Cloud.

**Types of Azure Virtual Machines**

1. **General-Purpose VMs:**

   Balanced CPU-to-memory ratio.

   Suitable for testing, development, and small to medium applications.

   Example: D-series, B-series.

2. **Compute-Optimized VMs:**

   High CPU-to-memory ratio.

   Best for batch processing, web servers, and analytics.

   Example: F-series.

3. **Memory-Optimized VMs:**

   High memory-to-CPU ratio.

   Ideal for large databases and in-memory analytics.

   Example: E-series, M-series.

4. **Storage-Optimized VMs:**

   High disk throughput and IOPS.

   Suitable for big data and NoSQL databases.

   Example: L-series.

5. **GPU-Optimized VMs:**

   High-performance GPUs for AI, ML, and rendering.

   Example: NV-series, NC-series.

6. **High-Performance VMs:**

   Designed for high-performance computing (HPC) workloads.

   Example: H-series.

**How to Create an Azure Virtual Machine**

1. **Via Azure Portal:**

   Go to the Azure Portal (https://portal.azure.com).

   Click Create a resource → Compute → Virtual Machine.

   Configure details like:

   Subscription: Select your Azure subscription.

   Resource Group: Choose or create one.

   VM Name: Enter a name.

   Region: Select a region.

   Image: Choose the OS (Windows/Linux).

   Size: Select VM size.

   Authentication: Use SSH key or password.

   Click Review + Create and then Create.

2. **Via Azure CLI:**

   az vm create \
      --resource-group MyResourceGroup \
      --name MyVM \
      --image UbuntuLTS \
      --admin-username azureuser \
      --generate-ssh-keys

**How to Connect to Azure VM**

1. **Windows VM:**

    Download the RDP file from the Azure Portal.

    Open the RDP file and connect using the username and password.

2. **Linux VM:**

   Use SSH to connect:

   ssh azureuser@<VM_Public_IP>

**How to Deploy Jenkins on Azure VM**

1. **Create a VM:**

   Follow the steps to create a Linux VM (preferably Ubuntu).

2. **Install Jenkins:**

   Connect to the VM via SSH:

   ssh azureuser@<VM_Public_IP>

   pdate the package list:

   sudo apt update

   Install Java:

   sudo apt install openjdk-11-jdk -y

   Add the Jenkins repository and install Jenkins:

    curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null

   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

   sudo apt update
   sudo apt install jenkins -y

   Start Jenkins:

   sudo systemctl start jenkins
   sudo systemctl enable jenkins

Access Jenkins in the browser:

Open http://<VM_Public_IP>:8080.

Follow the setup wizard.

**VM Scale Sets (for Auto-Scaling)**

  VM Scale Sets allow you to deploy and manage a set of identical VMs for automatic scaling.

**Features:**

  Automatic scaling based on demand or schedules.

  Load balancing to distribute traffic evenly.

  Integration with Azure Monitor for performance tracking.

**How to Create VM Scale Sets:**

1. **Via Azure Portal:**

   Search for "Virtual Machine Scale Sets" → Create.

   Configure parameters like instance size, autoscaling rules, and load balancer settings.

   Deploy the scale set.

2. **Via Azure CLI:**

az vmss create \
    --resource-group MyResourceGroup \
    --name MyScaleSet \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
