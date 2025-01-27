**How to Create an Azure Virtual Network (VNet) in Azure Portal**

**Azure Virtual Network (VNet)** allows you to create isolated, secure, and scalable networks within the Azure cloud. Follow the detailed steps below to create an Azure VNet using the Azure Portal.

**Step 1: Sign In to the Azure Portal**

1.Open your web browser and go to the Azure Portal.

2.Log in with your Azure credentials.

**Step 2: Navigate to Virtual Networks**

1.In the search bar at the top, type "Virtual Networks" and select the Virtual Networks option from the results.

2.Click on the + Create button to start creating a new Virtual Network.

**Step 3: Configure Basics**

**1.Subscription:** Choose the appropriate subscription under which the VNet will be created.

**2.Resource Group:**

Select an existing resource group, or click Create new to create a new resource group.

Example: MyResourceGroup.

**3.Name:** Provide a name for your Virtual Network.

Example: MyVNet.

**4.Region:** Select the Azure region where you want to deploy the VNet.

Example: East US.

Click **Next: IP Addresses.**

**Step 4: Configure Address Space**

1.In the IPv4 address space field, specify the address range for your VNet in CIDR notation.

Example: 10.0.0.0/16 (This provides 65,536 IP addresses).

If needed, add additional address ranges for the VNet by clicking + Add an address space.

Click **Next: Security.**

**Step 5: Configure Subnets**

1. Click + Add Subnet to define the subnets within the VNet.
   
**Name:** Provide a name for the subnet. Example: MySubnet.

**Subnet Address Range:** Specify the range of IP addresses for the subnet (CIDR).

Example: 10.0.1.0/24 (This provides 256 IP addresses for the subnet).

2.Add additional subnets if needed by repeating the above steps.

Click **Next: Security.**

**Step 6: Configure Security**

**1.BastionHost:** If you plan to use Azure Bastion, select Yes to enable it. Otherwise, leave it as No.

**2.Firewall:** Choose to enable or disable Azure Firewall. (Optional).

**3.DDos Protection:** Enable Standard DDoS Protection if required (for enhanced security).

Click **Next: Tags.**

**Step 7: Add Tags**

1.Add tags to organize and manage your VNet resources. Tags are key-value pairs, such as:

**Key:** Environment

**Value:** Production

Click **Next: Review + Create.**

**Step 8: Review and Create**

1.Review all the configuration details for your VNet.

2.Click Create to deploy the Virtual Network.

**Step 9: Validate and Monitor**

After deployment is complete, navigate back to **Virtual Networks** in the Azure Portal.

Verify your VNet configuration, subnets, and address ranges.

**Advanced Configurations**

**1.Network Security Groups (NSGs):** Secure your VNet by associating NSGs with subnets or VMs.

**2.Route Tables:** Define custom routes to control network traffic.

**3.Peering:** Connect VNets within the same or different regions.

**4.DNS Configuration:** Use Azure DNS or custom DNS servers for name resolution.
