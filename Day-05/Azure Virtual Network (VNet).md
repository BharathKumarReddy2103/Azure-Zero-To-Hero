# Azure Virtual Network (VNet):

## Introduction  

Azure **Virtual Network (VNet)** is one of the core networking services in Microsoft Azure. It provides **network isolation, security, and scalability** for cloud resources, ensuring that different workloads remain separate while still being able to communicate when required.  

Understanding Azure networking concepts like **VNets, subnets, Network Security Groups (NSGs), and Application Security Groups (ASGs)** is crucial for cloud engineers, DevOps professionals, and system architects.  

In this guide, we will break down these concepts into simple, real-world analogies and practical use cases, ensuring a strong foundation in **Azure Networking**.

---

##   Why Do We Need Azure Virtual Network (VNet)?  

To understand why VNets are essential, let's consider a **real-world problem** without VNets.  

- Imagine two **DevOps engineers**, one at **Nike** and another at **Puma**, both working on Azure.  
- They both request a **Virtual Machine (VM)** in the same **Azure Region (East US)** and the same **Availability Zone (AZ1)**.  
- Without virtual networks, Azure would **deploy both VMs on the same physical server** within the data center.  
- If one VM is compromised (e.g., hacked), the hacker could **potentially access** the other VM, posing a **security risk**.

To solve this, **cloud providers introduced Virtual Networks (VNets)**:  
- **AWS calls it VPC (Virtual Private Cloud)**  
- **Azure calls it Virtual Network (VNet)**  

A **VNet** ensures that resources within a company (e.g., Nike) are **isolated** from other customers (e.g., Puma). This **prevents unauthorized access** and **secures network traffic**.

---

##   What is Azure Virtual Network (VNet)?  

An **Azure Virtual Network (VNet)** is a **logical isolation of network resources** in Azure. It allows cloud services like **VMs, databases, and applications** to communicate securely within a defined boundary.  

###   Key Features of VNet  
  **Network Isolation** – Each VNet is isolated from others unless explicitly connected.  
  **Custom IP Addressing** – Define your own **CIDR blocks** to allocate IP addresses.  
  **Subnets** – Divide a VNet into smaller **logical sections**.  
  **Security Controls** – Use **NSGs and ASGs** to control traffic.  
  **Routing** – Define **custom routes** using **Route Tables**.  

---

##   Understanding Subnets in Azure  

A **VNet can be divided into multiple subnets**, where each **subnet** is a smaller portion of the network.  

###   Why Do We Need Subnets?  
Subnets help in **logical segmentation** of workloads, improving **security and performance**.  

#### Example Subnet Structure  

| Subnet Name | Purpose | Example Workloads |
|------------|---------|----------------|
| `Web Subnet` | Public-facing applications | Web servers, Load Balancers |
| `App Subnet` | Internal applications | Backend applications, APIs |
| `DB Subnet` | Secure database tier | SQL databases, NoSQL databases |

Each **subnet** can have different **security rules**.  
For example:  
- **Web applications** in `Web Subnet` are **accessible from the internet**.  
- **Databases** in `DB Subnet` should **not** be accessible from the internet.  
- **Only backend applications** in `App Subnet` should be able to access `DB Subnet`.  

---

##   Network Security Groups (NSGs)  

**Network Security Groups (NSGs)** allow us to **control inbound and outbound traffic** to Azure resources.  

###   How NSGs Work?  
NSGs contain **security rules** that filter **network traffic** based on:  
- **Source & Destination** (IP, Subnet, or VNet)  
- **Port Number** (e.g., SSH - 22, HTTP - 80, HTTPS - 443)  
- **Protocol** (TCP, UDP)  

#### Example NSG Rule  

| Priority | Source | Destination | Port | Action |
|----------|--------|-------------|------|--------|
| 100 | Internet | `Web Subnet` | 80, 443 | Allow |
| 200 | `App Subnet` | `DB Subnet` | 1433 (SQL) | Allow |
| 300 | `Internet` | `DB Subnet` | Any | **Deny** |

---

##   Application Security Groups (ASGs)  

**Application Security Groups (ASGs)** provide a **more flexible** way to manage security rules in Azure. Instead of defining security rules using **IP addresses or CIDR blocks**, we can define them based on **application roles**.

###   Why Use ASGs?  
Instead of managing **IP addresses manually**, **ASGs** allow us to:  
  **Group API servers into an "API ASG"**  
  **Group Database servers into a "DB ASG"**  
  **Create security rules based on roles**  

---

##   Route Tables and User-Defined Routes (UDRs)  

A **Route Table** defines how **network traffic flows** between subnets and external services.

###   Custom User-Defined Routes (UDRs)  
We can override default Azure routes using **User-Defined Routes (UDRs)** to:  
1. **Force traffic through a firewall or VPN Gateway**  
2. **Ensure internal traffic follows a specific network path**  
3. **Restrict traffic between subnets**  

#### Example UDR Rule  

| Destination | Next Hop Type |
|-------------|--------------|
| `0.0.0.0/0` (Internet) | **VPN Gateway** |
| `10.0.4.0/24` (DB Subnet) | **Firewall Appliance** |

---

##   Best Practices for Azure Networking  

  **Use VNets for Network Segmentation**  
  **Leverage Subnets for Logical Separation**  
  **Apply NSGs at the Subnet Level**  
  **Use ASGs for Role-Based Access Control**  
  **Define Custom Routes to Control Traffic Flow**  
  **Monitor Network Traffic with Azure Monitor**  

---

##   Conclusion  

Azure Virtual Networks (VNets) form the **foundation of secure cloud networking**. By leveraging **subnets, NSGs, ASGs, and Route Tables**, we can design scalable and secure network architectures.  

If you're working with **Azure DevOps, Cloud Security, or Kubernetes Networking**, mastering **Azure Networking** is essential for deploying and securing cloud-based applications.  

---

##   Further Reading  

  [Azure Virtual Network (VNet) Documentation](https://learn.microsoft.com/en-us/azure/virtual-network/)  
  [Network Security Groups (NSG) Best Practices](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview)  
  [CIDR Notation Explained](https://www.ibm.com/docs/en/aix/7.2?topic=protocols-classless-interdomain-routing-cidr)  

---

##   Contributing  

  Found a mistake? Have suggestions? Feel free to open a **Pull Request** or raise an **Issue** in this repository.  

  **Star this repository**   and share it with your network to help more engineers master Azure Networking. 
