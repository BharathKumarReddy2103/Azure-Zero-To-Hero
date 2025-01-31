# Azure Storage Services

## What is Azure Storage?
Azure Storage is Microsoft's cloud storage solution that offers durable, scalable, and secure data storage options. It supports structured and unstructured data, providing different storage types to meet various needs, such as Blob, File Table, Queue storage.

## Key Features of Azure Storage
- **Durability & Availability:** Data is replicated across multiple locations to ensure high availability.
- **Scalability:** Supports massive amounts of data with the ability to scale automatically.
- **Security:** Provides encryption, role-based access control (RBAC), and private endpoints.
- **Data Redundancy Options:** LRS, ZRS, GRS, and RA-GRS.
- **Cost-Effective:** Pay-as-you-go pricing with different storage tiers (Hot, Cool, Archive).

## Types of Azure Storage Services

Azure provides five primary storage types:

### 1. Azure Blob Storage (Object Storage)
- Used for storing unstructured data such as images, videos, and backups.
- Supports different access tiers: **Hot, Cool, and Archive**.
- Ideal for big data analytics, backup & disaster recovery, and media streaming.

#### How to Create Azure Blob Storage

1. Go to Azure Portal → Storage Accounts → Create.
2. Select Subscription, Resource Group, and enter a unique Storage Account Name.
3. Choose Region, Performance Tier (Standard/Premium), and Replication Type (LRS, ZRS, GRS, RA-GRS).
4. Click "Next: Advanced" → Enable Blob public access if needed.
5. Click "Review + Create" → Create.
6. Once deployed, navigate to Containers → Create a Container → Upload files.

**2. Azure Files (File Storage - SMB & NFS)**

Fully managed file shares in the cloud, accessible via SMB or NFS.
Can be mounted by multiple virtual machines (VMs).
Supports Azure File Sync for hybrid cloud storage.

**How to Create Azure Files Storage**

1. Go to Azure Portal → Storage Account → File Shares.
2. Click "+ File Share", enter a Name and select a Quota.
3. Click "Create".
4. Once created, click "Connect" → Use the provided PowerShell or CLI script to mount the file share.
   
**3. Azure Table Storage (NoSQL Key-Value Store)**
   
A NoSQL key-value store for structured, non-relational data.
Suitable for storing large-scale structured data like logs and IoT data.

**How to Create Azure Table Storage**

1. Go to Azure Portal → Storage Account → Tables.
2. Click "+ Table", enter a Table Name, and click "OK".
3. Use Azure Storage Explorer or Azure SDKs to add, update, and query data.
   
**4. Azure Queue Storage (Message Queue Service)**

Stores and processes messages asynchronously in a queue.
Helps in decoupling application components.

**How to Create Azure Queue Storage**

1. Go to Azure Portal → Storage Account → Queues.
2. Click "+ Queue", enter a Queue Name, and click "Create".
3. Use Azure SDKs or REST API to send and receive messages from the queue.

**Azure Storage Access Tiers**

**Hot Tier:** Frequently accessed data.

**Cool Tier:** Infrequently accessed data but still needs quick access.

**Archive Tier:** Rarely accessed data, stored for long-term retention.

**Best Practices for Azure Storage**

Use Azure Blob Storage Lifecycle Policies to move data between tiers.
Enable soft delete to protect against accidental deletions.
Implement encryption at rest and in transit for data security.
Optimize performance and cost by choosing the right storage type.
