# Azure Networking – Azure Virtual Network

## Introduction  

Azure networking is one of the most crucial concepts in **Microsoft Azure** and plays a fundamental role in securing and managing cloud-based infrastructure. Many learners struggle with **Virtual Networks (VNet)**, **Subnets**, **Network Security Groups (NSG)**, **Application Security Groups (ASG)**, and **Route Tables**.  

In this guide, we will break down these concepts in a **simple and practical manner**, correlate them with **real-world use cases**, and provide actionable insights to help **DevOps and Cloud Engineers** master Azure networking.  

---

## 1. What is a Virtual Network (VNet) in Azure?  

A **Virtual Network (VNet)** in Azure is a **logical isolation of the network within the physical infrastructure** of Microsoft Azure. It acts as a **private network** where resources like **Virtual Machines (VMs), Databases, and Applications** communicate securely.  

###   Why is VNet Required?  

Let's imagine two companies, **Nike** and **Puma**, both deploying Virtual Machines (VMs) in the **East US region** within **Availability Zone 1**.  

Without VNets:  

- Both companies' **VMs could end up on the same physical server**.  
- If one VM is **compromised**, the attack can potentially spread.  

With VNets:  

- Each company gets an **isolated network environment**, ensuring **data security**.  
- **Nike’s VMs** are kept separate from **Puma’s VMs**, even if they are hosted in the same region.  

###   How Many VNets Can You Create?  

- **Unlimited VNets** can be created within an Azure **Subscription**.  
- A **single organization** can have **multiple VNets** depending on its **project or application requirements**.  

---

## 2. Subnets in Azure Networking  

A **Subnet** is a smaller segment of a VNet that allows you to **logically separate resources**.  

###   Why Use Subnets?  

- **Security Isolation**: Keep **web apps, databases, and business logic** separate.  
- **Access Control**: Control who can access specific services.  
- **Optimized Network Traffic**: Ensures efficient **routing and firewall rules**.  

###   Real-World Example  

A company may create **three subnets** inside a VNet:  

| Subnet Name | Purpose | Accessibility |
|-------------|---------|---------------|
| Web Subnet  | Hosts web applications | Accessible from the Internet |
| App Subnet  | Hosts business logic apps | Only accessible within the network |
| DB Subnet  | Stores databases | Restricted; not accessible from the Internet |

Each **subnet** is assigned a **CIDR block** that defines the number of **IP addresses** available.  

For example:  

- **/16 → 65,536 IPs**  
- **/24 → 256 IPs**  

  *Understanding CIDR notation is crucial in designing efficient network architectures.*  

---

## 3. Network Security Groups (NSG) – Azure’s Firewall  

An **NSG (Network Security Group)** acts as a **firewall** to filter incoming and outgoing traffic to Azure resources.  

###   Key Features of NSGs  

  Apply security rules to **subnets** or **individual VMs**.  
  Allow or deny access based on **source IP, destination IP, port number, and protocol**.  
  By default, **Azure blocks inbound traffic** unless explicitly allowed.  

###   Example NSG Rules  

| Rule Name | Priority | Source | Destination | Protocol | Action |
|-----------|----------|---------|-------------|-----------|---------|
| Allow Web Traffic | 100 | Any | Web Subnet | HTTP (80) | Allow |
| Allow SSH | 200 | Company Network | App Subnet | SSH (22) | Allow |
| Deny All | 300 | Any | DB Subnet | Any | Deny |

  **Best Practice:** Apply NSGs at the **subnet level** instead of individual VMs to ensure **consistent security policies**.  

---

## 4. Application Security Groups (ASG) – Advanced Access Control  

**Application Security Groups (ASG)** provide a **more dynamic and scalable** way to control network security compared to NSGs.  

###   Difference Between NSG and ASG  

| Feature | NSG | ASG |
|---------|-----|-----|
| Controls Traffic at | Subnet / VM Level | Application Level |
| Rule-Based | IP Address-Based | Group-Based |
| Scalability | Manual Updates Required | Auto-Updates Based on Groups |

###   Real-World Example  

Imagine a scenario where **multiple business logic applications** and **web applications** exist within the same subnet.  

Using NSG:  
- You would have to manually list **each VM’s IP address** to allow access.  

Using ASG:  
- You can group all **business logic applications** into an **ASG** and grant access to **only those instances**.  

  **Best Practice:** Use a **combination of NSG + ASG** to enhance security and **simplify network management**.  

---

## 5. Route Tables and User-Defined Routes (UDR)  

A **Route Table** in Azure defines **how network traffic flows** between subnets, VNets, and external networks.  

###   Why Use Custom Routes?  

- By default, **Azure automatically handles routing**.  
- In **complex architectures**, **User-Defined Routes (UDR)** allow **custom traffic routing** for **security, compliance, or performance reasons**.  

###   Example Use Case  

- **Force all outbound traffic through a firewall or security appliance**.  
- **Prevent traffic from moving between subnets that shouldn't communicate**.  
- **Control how internet-bound traffic is routed** in hybrid cloud setups.  

  **Best Practice:** Configure **UDRs** to ensure traffic **flows through a secure and compliant path**.  

---

## Conclusion  

Understanding **Azure Networking** is essential for **DevOps Engineers, Cloud Architects, and Security Professionals**.  

  **Virtual Networks (VNet)** ensure logical network isolation.  
  **Subnets** allow logical separation and security.  
  **Network Security Groups (NSG)** enforce firewall rules.  
  **Application Security Groups (ASG)** simplify access control.  
  **Route Tables and UDRs** define **custom traffic paths**.  

By implementing **these best practices**, organizations can build a **secure, scalable, and high-performance cloud network**.  

  **Next Steps:**  
- Explore **CIDR Notation** in detail.  
- Implement a **real-world Azure networking project**.  
- Read **Microsoft Azure documentation** on **VNets, NSGs, and ASGs**.  

  *Found this guide helpful? Star this repository on GitHub and contribute to open-source cloud networking projects!*  
