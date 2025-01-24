**Azure Virtual Network (VNet):**

Azure Virtual Network (VNet) is the foundational building block for securely networking Azure resources. It allows you to
create your own private network within Azure, providing full control over IP addresses, subnets, and communication between 
resources within and outside of Azure.

**Key Features of Azure VNet**

**Isolation and Segmentation:**

Isolate resources within a private network.

Use subnets for segmentation.

**Secure Communication:**

Control traffic flow using Network Security Groups (NSGs).

Use encryption for data in transit.

**Extend and Connect:**

Enable hybrid connectivity using VPN Gateway or ExpressRoute.

**High Availability:**

Load balancing and redundancy ensure application availability.

**Components of Azure VNet**

**1. Subnets**

**Definition:** Logical divisions within a VNet to segment the network.

**Purpose:** Group related resources for management and security.

**CIDR Block:** Each subnet is assigned a range of IP addresses from the VNet.

**Example:** A VNet with a CIDR block of 10.0.0.0/16 can have subnets like:

10.0.1.0/24 for web servers.

10.0.2.0/24 for databases.

**2. CIDR (Classless Inter-Domain Routing)**

Used to define IP address ranges for VNets and subnets.

Example: 10.0.0.0/16 means:

Network: 10.0.0.0.

Total IPs: 65,536 (for /16 mask).

Smaller subnets can be carved from this range using a larger CIDR value.

**3. Routes and Route Tables**

**Route Tables:** Define how traffic is routed within a VNet or outside it.

**Default Routes:** Azure automatically creates routes for communication within a VNet.

**Custom Routes:** Used to route traffic to:

Virtual Appliances.

Internet.

On-premises networks via VPN Gateway.

**4. Network Security Groups (NSGs)**

**Definition:** Firewall rules applied to subnets or NICs to control inbound/outbound traffic.

**Rules:**

Allow or deny traffic based on IP, port, and protocol.

Default rules allow VNet communication and block all other traffic.

**Example:** Allow inbound traffic to port 80 (HTTP):

  Source: Any  
  Source Port: *  
  Destination: Any  
  Destination Port: 80   
  Protocol: TCP  
  Action: Allow

**5. Application Security Groups (ASGs)**

Group VMs logically for simplified NSG management.

**Example:**

**WebASG:** All web servers.

**DBASG:** All database servers.

NSG Rule: Allow traffic from WebASG to DBASG on port 1433 (SQL).

**6. Azure Application Gateway and Web Application Firewall (WAF)**

**Application Gateway:**

  Layer 7 load balancer.
  
  Routes traffic based on URLs, headers, etc. (Path-based routing).
  
  Supports SSL termination.

**WAF:**

  Protects against OWASP threats like SQL injection, XSS, etc.

  Integrated with Application Gateway.

**7. Azure Load Balancer**

**Definition:** Layer 4 (TCP/UDP) load balancer.

**Types:**

**Public Load Balancer:** Routes traffic from the internet.

**Internal Load Balancer:** Routes traffic within the VNet.

**Features:**

High availability.

Health probes to monitor backend instances.

**8. Azure DNS**

Host and manage custom DNS domains in Azure.

Enable name resolution for VMs and other resources.

**9. Azure Firewall**

A cloud-native network security service.

**Features:**

**Application rules:** Control outbound traffic by FQDN.

**Network rules:** Control traffic by IP and protocol.

**Threat intelligence:** Block traffic from malicious IPs.

**10. VNet Peering**

**Definition:** Connects two VNets, enabling traffic between them.

**Features:**

Low latency.

No public internet required.

**Types:**

**Regional Peering:** Between VNets in the same region.

**Global Peering:** Between VNets in different regions.

**11. VNet Gateway**

Enables communication between an Azure VNet and other networks.

**Types:**

**VPN Gateway:** For encrypted connections to on-premises networks over the internet.

**ExpressRoute Gateway:** For private connections using ExpressRoute.

**12. VPN Gateway**

**Definition:** Provides secure communication between Azure and on-premises networks.

**Connection Types:**

**Site-to-Site (S2S):** Connects on-premises to Azure.

**Point-to-Site (P2S):** Individual devices connect to Azure.

**Conclusion**

Azure VNets offer a highly customizable and secure networking solution. By combining subnets, route tables, NSGs, load
balancers, and other services, you can create a robust infrastructure for your Azure environment.
