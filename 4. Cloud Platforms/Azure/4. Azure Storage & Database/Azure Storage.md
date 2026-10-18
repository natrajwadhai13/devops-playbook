---
title: "• Azure Storage"
parent: "• Azure"
grand_parent: "4. Cloud Platforms"
nav_order: 4
has_children: true
---


**4.1 Storage**

Blob static hosting, 
Lifecycle policies, 
Encryption & backup

**4.2 Databases**

Azure SQL, 
MySQL/PostgreSQL, Flexible Server, 
Cosmos DB (NoSQL), 
Redis Cache, 

**AZure Database migration service (DMS)**

=================================

---

👉 [GitHub Repo link for Azure Storage](https://github.com/natrajwadhai13/Azure-zero-to-hero/tree/main/Day-09)

![Image](https://learn.microsoft.com/en-us/security/zero-trust/media/secure-storage/azure-infra-storage-network-2.svg)

![Image](https://cloud.361way.com/azure/images/azure-blob-storage.jpg)

![Image](https://www.c-sharpcorner.com/article/azure-storage-components-blob-part-one/Images/image001.jpg)

---

# 💾 **Azure Storage – Complete Notes**

---

## **1️⃣ What is Azure Storage? (What?)**

Azure Storage is a **cloud-based storage service** provided by Microsoft Azure to store **data securely, durably, and at massive scale**.

It is used to store:

* Files
* Images & videos
* Application data
* Logs
* Backups
* Messages for applications

---

## **2️⃣ Why Azure Storage? (Why?)**

Azure Storage is used because it provides:

### ✅ **High Durability**

* **99.999999999% (11 nines)** durability
* Very low data loss probability
  *(~0.0000001% chance)*

### ✅ **High Availability**

* Data replicated automatically
* Access even during failures

### ✅ **High Performance**

* Low latency access
* Supports massive parallel reads/writes

### ✅ **Security**

* Integrated with **Microsoft Entra ID (Azure AD)**
* Encryption at rest & in transit
* Role-based access (RBAC)

### ✅ **Scalability**

* Handles **petabytes of data**
* Scales automatically

---

## **3️⃣ Azure Storage Account**

A **Storage Account** is the **top-level container** in Azure Storage.

All storage services (Blob, File, Table, Queue) live **inside a Storage Account**.

### **Storage Account Contains**

* Azure Blobs: A massively scalable object store for text and binary data. Also includes support for big data analytics through Data Lake Storage Gen2.
* Azure Files: Managed file shares for cloud or on-premises deployments.
* Azure Queues: A messaging store for reliable messaging between application components.
* Azure Disks: Block-level storage volumes for Azure VMs.
* Azure Tables: NoSQL table option for structured, non-relational data.

### **Important Properties**

* Account name (globally unique)
* Region
* Performance tier
* Replication type
* Security settings

---

## **4️⃣ Storage Account Types**

| Type                      | Use                       |
| ------------------------- | ------------------------- |
| General Purpose v2 (GPv2) | Most common (recommended) |
| Blob Storage              | Optimized for blobs       |
| FileStorage               | Premium file shares       |

---

## **5️⃣ Durability & Replication**

Azure ensures durability using **replication**.

### **Replication Options**

| Type   | Description                            |
| ------ | -------------------------------------- |
| LRS    | Data copied 3 times in same datacenter |
| ZRS    | Replicated across availability zones   |
| GRS    | Geo-replicated to another region       |
| RA-GRS | Read access to secondary region        |

📌 **Example:**
If one datacenter fails → data is still available.

---

## **6️⃣ Performance Tiers**

| Tier     | Use                                          |
| -------- | -------------------------------------------- |
| Standard | General purpose workloads                    |
| Premium  | High IOPS, low latency (VM disks, databases) |

---

## **7️⃣ Azure Storage Services**

---

## **7.1 Azure Blob Storage**

Used to store **unstructured data**.

### **What is Unstructured Data?**

* Images, Videos, Audio. PDFs, Logs, Source code backups

### **Use Cases**

* Website images, Video streaming, Backup & restore, Application logs

### **Example**

* Store application images in Blob
* Store CI/CD build artifacts

### **Blob Components**

* Container (like folder)
* Blob (file)

---

## **7.2 Azure File Storage**

Provides **shared file system** in the cloud.

### **Key Points**

* Acts like a **network file share**
* Can be mounted on:

  * Azure VMs
  * On-prem servers
  * Containers & Pods

### **AWS Comparison**

* Azure File ≈ **EFS**
* Azure Disk ≈ **EBS**

### **Use Cases**

* Shared config files
* Lift-and-shift applications
* VM-to-VM file sharing

---

## **7.3 Azure Table Storage**

A **NoSQL key-value storage**.

### **Key Characteristics**

* Schema-less
* Fast access
* Highly scalable

### **Use Cases**

* User metadata
* Application configuration
* Logs (structured)

📌 Example:

```
PartitionKey | RowKey | Value
UserID       | 101    | Active
```

---

## **7.4 Azure Queue Storage**

Used for **message-based communication**.

### **Why Queue Storage?**

* Decouples application components
* Enables asynchronous processing

### **Use Cases**

* Background jobs
* Order processing
* Email notifications

### **Example**

1. App sends message to Queue
2. Worker picks message and processes task

---

## **8️⃣ Storage Explorer / Storage Browser**

A GUI tool to:

* Upload/download blobs
* Manage file shares
* View queues & tables

Works like **File Explorer for Azure Storage**.

---

## **9️⃣ Static Website Hosting**

Azure Blob Storage can host **static websites**.

### **Supports**

* HTML
* CSS
* JavaScript

### **Example**

* Portfolio website
* Documentation site

📌 URL provided automatically after enabling static website.

---

## **🔟 Security in Azure Storage**

---

### **10.1 Authentication & Access**

* **Access Keys** (Full access – use carefully)
* **Shared Access Signature (SAS)** (Time-limited access)
* **Azure AD (Recommended)**

---

### **10.2 Encryption**

* **At Rest** → AES-256 encryption
* **In Transit** → HTTPS (TLS)

Enabled by default.

---

### **10.3 Network Security**

* Firewall rules
* Private Endpoint
* Service Endpoint

---

## **1️⃣1️⃣ Data Protection**

* Soft delete
* Blob versioning
* Snapshots
* Backup & restore

---

## **1️⃣2️⃣ Real-World Example**

**Scenario:**
Web app stores images and logs securely.

**Solution:**

* Blob Storage for images
* Queue Storage for background tasks
* File Storage for shared configs
* Private Endpoint for security
* Azure AD for access control

---

## **1️⃣3️⃣ Interview One-Liners**

* Blob = unstructured data
* File = shared file system
* Table = NoSQL key-value
* Queue = messaging
* Storage Account = parent container
* Azure Storage is encrypted by default

==========================


![Image](https://learn.microsoft.com/en-us/azure/storage/common/media/storage-account-create/menu-expand-lrg.png)

![Image](https://learn.microsoft.com/en-us/azure/developer/javascript/tutorial/media/browser-file-upload-azure-storage-blob/file-upload-request-flow.png)

![Image](https://azure.microsoft.com/en-us/blog/wp-content/uploads/2018/12/AzurePortalStaticWebsite-1.webp)

![Image](https://learn.microsoft.com/en-us/azure/storage/storage-explorer/media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-overview.png)

---

# 💾 **Azure Storage Hands-On Labs (AZ-900)**

(Using **Microsoft Azure Portal)

---

## 🔬 **LAB 1: Create a Storage Account**

### **Objective**

Create a basic Azure Storage Account.

### **Steps**

1. Login to **Azure Portal**
2. Search → **Storage accounts**
3. Click **Create**
4. Fill details:

   * Subscription → Select
   * Resource Group → Create new (ex: `rg-storage-lab`)
   * Storage account name → unique (ex: `azstoragelab01`)
   * Region → Select nearest
   * Performance → **Standard**
   * Redundancy → **LRS**
5. Click **Review + Create**
6. Click **Create**

✅ Storage account created successfully

---

## 🔬 **LAB 2: Create Blob Container & Upload File**

### **Objective**

Store unstructured data using Blob Storage.

### **Steps**

1. Open your **Storage Account**
2. Go to **Containers**
3. Click **+ Container**
4. Enter:

   * Name → `images`
   * Public access level → **Private**
5. Click **Create**
6. Open container → **Upload**
7. Upload any image or text file

✅ File stored as a **Blob**

---

## 🔬 **LAB 3: Access Blob via URL**

### **Objective**

Understand blob access.

### **Steps**

1. Click uploaded blob
2. Copy **Blob URL**
3. Open URL in browser

🔒 If container is **private**, access will be denied
(public container → file opens)

---

## 🔬 **LAB 4: Create File Share (Azure File Storage)**

### **Objective**

Create shared file storage.

### **Steps**

1. Storage Account → **File shares**
2. Click **+ File share**
3. Name → `sharedfiles`
4. Tier → **Transaction optimized**
5. Click **Create**

✅ File share ready (can be mounted on VM)

---

## 🔬 **LAB 5: Create Queue Storage**

### **Objective**

Understand messaging service.

### **Steps**

1. Storage Account → **Queues**
2. Click **+ Queue**
3. Name → `appqueue`
4. Click **Create**
5. Open queue → **Add message**
6. Enter message → `Process order 101`

✅ Message stored for background processing

---

## 🔬 **LAB 6: Create Table Storage**

### **Objective**

Use NoSQL key-value storage.

### **Steps**

1. Storage Account → **Tables**
2. Click **+ Table**
3. Name → `usertable`
4. Click **Create**

📌 Used for structured, schema-less data

---

## 🔬 **LAB 7: Enable Static Website Hosting**

### **Objective**

Host a static website using Blob Storage.

### **Steps**

1. Storage Account → **Static website**
2. Click **Enable**
3. Index document name → `index.html`
4. Error document → `404.html`
5. Click **Save**
6. Open **$web** container
7. Upload `index.html`

🌍 Website URL is generated automatically

---

## 🔬 **LAB 8: Access Keys & Security**

### **Objective**

Understand authentication.

### **Steps**

1. Storage Account → **Access keys**
2. View:

   * Key1
   * Key2
3. Copy key (⚠ full access)

📌 Best practice: Use **Azure AD / RBAC**, not keys

---

## 🔬 **LAB 9: Verify Encryption**

### **Objective**

Confirm data security.

### **Steps**

1. Storage Account → **Encryption**
2. Verify:

   * Encryption at rest → **Enabled**
   * Microsoft-managed keys

🔐 Encryption is **ON by default**

---

## 🔬 **LAB 10: Use Azure Storage Explorer (Optional)**

### **Objective**

Manage storage visually.

### **Steps**

1. Download **Azure Storage Explorer**
2. Login with Azure account
3. Browse:

   * Blob containers
   * File shares
   * Queues
   * Tables
4. Upload/download files easily

---

## 🧠 **What You Learned (Exam Mapping)**

* Storage Account creation
* Blob, File, Queue, Table usage
* Static website hosting
* Access keys & encryption
* Azure Storage Explorer

---

============================

**Azure Blob Storage has three main blob types: Block Blob, Append Blob, and Page Blob. Each is optimized for different use cases.**

---

## 📦 Types of Azure Blob Storage

### 1. **Block Blob**
- **Purpose**: Stores text and binary data.  
- **Best For**: Images, documents, media files, backups.  
- **How It Works**: Data is divided into blocks; each block can be managed individually.  
- **Use Case Example**: Hosting website images or storing large log files.

---

### 2. **Append Blob**
- **Purpose**: Optimized for append operations.  
- **Best For**: Log files, audit trails, telemetry data.  
- **How It Works**: Data can only be appended to the end of the blob, not modified in place.  
- **Use Case Example**: Continuous logging from applications.

---

### 3. **Page Blob**
- **Purpose**: Stores random-access files up to 8 TB.  
- **Best For**: Virtual hard drives (VHDs) for Azure Virtual Machines.  
- **How It Works**: Data is organized in 512-byte pages, enabling fast read/write operations.  
- **Use Case Example**: Running Azure VMs with attached disks.

---

## 📊 Comparison Table

| Blob Type     | Optimized For         | Typical Use Case                  | Size Limit |
|---------------|-----------------------|-----------------------------------|------------|
| Block Blob    | Large files, streaming| Media, backups, documents         | Up to 190.7 TiB |
| Append Blob   | Sequential writes     | Logs, telemetry, audit trails     | Up to 195 GiB |
| Page Blob     | Random read/write     | VM disks, databases               | Up to 8 TiB |

---

## ⚠️ Key Notes
- **Storage Tiers**: Azure Blob Storage also offers **Hot, Cool, and Archive tiers** for cost optimization depending on access frequency.  
- **Access**: Blobs can be accessed via HTTP/HTTPS globally.  
- **Integration**: Works seamlessly with Azure Data Lake Storage Gen2 for analytics.  

=======================

# 🧠 **AZ-900 Shorts Notes – Azure Storage Accounts**


### 📦 What is an Azure Storage Account?

An **Azure Storage Account** provides a **unique namespace** to store data in **Microsoft Azure**.
Data is accessible **globally over HTTP/HTTPS** and is:

* 🔐 Secure
* 🔁 Highly available
* 💾 Durable
* 📈 Massively scalable

---

## 🧩 Storage Account Types (Exam-Focused)

### 🔹 Standard General-Purpose v2 (Most Common)

* **Services**: Blob, Data Lake Gen2, Queue, Table, Azure Files
* **Redundancy**: LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS
* **Use**: Default choice for most workloads

---

### 🔹 Premium Block Blobs

* **Services**: Block blobs, Append blobs
* **Redundancy**: LRS, ZRS
* **Use**: High transaction rate, low latency workloads

---

### 🔹 Premium File Shares

* **Services**: Azure Files only
* **Redundancy**: LRS, ZRS
* **Use**: Enterprise apps, high-performance file shares
* **Note**: Supports **SMB + NFS**

---

### 🔹 Premium Page Blobs

* **Services**: Page blobs only
* **Redundancy**: LRS
* **Use**: VM disks, random read/write scenarios

---

## 🔁 Redundancy Options (Very Important for AZ-900)

| Option                  | Meaning                                |
| --------------------------------- | -------------------------------------- |
| **Locally redundant storage (LRS)**     | Copies data within a single datacenter |
| **Zone-redundant storage (ZRS)**     | Copies data across availability zones  |
| **Geo-redundant storage (GRS)**     | Replicates data to another region      |
| **Read-access geo-redundant storage (RA-GRS)**  | Read access to secondary region        |
| **Geo-zone-redundant storage (GZRS)**    | Zone + region replication              |
| **Read-access geo-zone-redundant storage (RA-GZRS)** | Read access to geo-zone replica        |

---

## 🌐 Storage Account Endpoints

Each storage account name must be **globally unique**.

### 📝 Naming Rules

* Length: **3–24 characters**
* Only **lowercase letters & numbers**
* Must be **unique across Azure**

---

### 🔗 Endpoint Formats (Must Remember)

| Storage Service | Endpoint Format                         |
| --------------- | --------------------------------------- |
| Blob Storage    | `https://<name>.blob.core.windows.net`  |
| Data Lake Gen2  | `https://<name>.dfs.core.windows.net`   |
| Azure Files     | `https://<name>.file.core.windows.net`  |
| Queue Storage   | `https://<name>.queue.core.windows.net` |
| Table Storage   | `https://<name>.table.core.windows.net` |

---

## ✅ AZ-900 Exam Tips

* ✔ **GPv2** = default & recommended
* ✔ **Premium** = performance + low latency
* ✔ Redundancy = **availability + durability**
* ✔ Endpoint questions = **URL format based MCQs**

=================================

## 🔁 **AZ-900 Rearranged Notes – Azure Storage Redundancy**

![Image](https://learn.microsoft.com/en-us/azure/storage/common/media/storage-redundancy/geo-redundant-storage.png)

![Image](https://www.graber.cloud/wp-content/uploads/2020/05/LRS-vs-ZRS-vs-GRS-vs-GZRS.png)

### 📦 What is Azure Storage Redundancy?

**Azure Storage** always keeps **multiple copies** of your data to protect against:

* Hardware failures
* Network / power outages
* Natural disasters

Redundancy helps meet **availability** and **durability** targets in **Microsoft Azure**.

---

## 🧠 How to Choose the Right Redundancy

Consider these trade-offs:

* 💰 **Cost vs Availability**
* 🔁 How data is replicated in the **primary region**
* 🌍 Whether data is replicated to a **secondary (paired) region**
* 👀 Whether **read access** is required in the secondary region

---

## 1️⃣ Redundancy in the Primary Region

Data is **always replicated 3 times** in the primary region.

### 🔹 Locally Redundant Storage (LRS)

* Replication: **3 copies in a single datacenter**
* Durability: **11 nines (99.999999999%)**
* 💰 Lowest cost
* ❌ No datacenter disaster protection
  👉 **Use**: Dev/Test, non-critical data

---

### 🔹 Zone-Redundant Storage (ZRS)

* Replication: **3 copies across Availability Zones**
* Durability: **12 nines (99.9999999999%)**
* ✅ Survives zone failure
* 🔁 No remount needed for Azure Files
  👉 **Use**: High availability apps, data residency needs

---

## 2️⃣ Redundancy in a Secondary Region

For **disaster recovery**, data can be replicated to a **paired region** (fixed, cannot be changed).

⚠ Replication to secondary region is **asynchronous**

* **RPO** (Recovery Point Objective): typically **< 15 minutes**
* Possible **data loss** during regional failure

---

### 🔹 Geo-Redundant Storage (GRS)

* Primary: **LRS**
* Secondary: **LRS (paired region)**
* Durability: **16 nines**
* ❌ No read access unless failover occurs
  👉 **Use**: Regional disaster recovery

---

### 🔹 Geo-Zone-Redundant Storage (GZRS)

* Primary: **ZRS**
* Secondary: **LRS**
* Durability: **16 nines**
* ✅ Zone + regional protection
  👉 **Use**: Mission-critical, maximum resilience

---

## 3️⃣ Read Access to Secondary Region

By default, secondary data is **not readable**.

### 🔹 RA-GRS

* GRS + **Read access** to secondary
* Secondary data may be slightly **out of sync (RPO)**

### 🔹 RA-GZRS

* GZRS + **Read access** to secondary
* Highest availability + disaster recovery

---

## 📊 Quick Comparison (Exam Ready)

| Option      | Zone Protection | Region Protection | Read Access |
| ----------- | --------------- | ----------------- | ----------- |
| **LRS**     | ❌               | ❌                 | ❌           |
| **ZRS**     | ✅               | ❌                 | ❌           |
| **GRS**     | ❌               | ✅                 | ❌           |
| **RA-GRS**  | ❌               | ✅                 | ✅           |
| **GZRS**    | ✅               | ✅                 | ❌           |
| **RA-GZRS** | ✅               | ✅                 | ✅           |

---

## ✅ AZ-900 Key Takeaways

* **LRS** = cheapest, least protection
* **ZRS** = high availability within region
* **GRS** = regional disaster recovery
* **RA-*** = read access to secondary
* **GZRS / RA-GZRS** = maximum durability & availability

=============================

---

## 🧠 ** Azure Blob Storage Access Tiers**


### 📦 Why Blob Storage Tiers?

Cloud data grows **very fast**, so Azure lets you **optimize storage cost** based on:

* 📊 How often data is accessed
* ⏳ How long data is stored (retention period)

Using the right tier helps **balance cost vs performance** in **Microsoft Azure**.

---

## 🔹 Azure Blob Storage Access Tiers

### 🔥 Hot Access Tier

* 📌 **Frequently accessed data**
* 💰 Highest storage cost, **lowest access cost**
* ⚡ Lowest latency
  👉 **Example**: Website images, active app data

---

### ❄ Cool Access Tier

* 📌 **Infrequently accessed**
* ⏳ Stored for **at least 30 days**
* 💰 Lower storage cost than Hot
* 💸 Higher access cost than Hot
  👉 **Example**: Customer invoices, reports

---

### 🧊 Cold Access Tier

* 📌 **Rarely accessed**
* ⏳ Stored for **at least 90 days**
* 💰 Cheaper than Cool
* 💸 Higher retrieval cost
  👉 **Example**: Old project files, audit data

---

### 🗄 Archive Access Tier

* 📌 **Very rarely accessed**
* ⏳ Stored for **at least 180 days**
* 💰 **Lowest storage cost**
* 🕒 **Highest access latency** (rehydration needed)
  👉 **Example**: Long-term backups, compliance data

---

## ⚙ Important Rules (Exam-Focused)

* ✅ **Hot / Cool / Cold**

  * Can be set at **account level**
  * Can also be set at **blob level**
* ❌ **Archive**

  * ❌ Not available at account level
  * ✅ Only at **blob level**
* 🔁 Tier can be changed **during or after upload**

---

## 📊 Availability & Cost Trade-offs

| Tier    | Storage Cost | Access Cost | Availability   | Latency |
| ------- | ------------ | ----------- | -------------- | ------- |
| Hot     | High         | Low         | Highest        | Lowest  |
| Cool    | Lower        | Higher      | Slightly lower | Low     |
| Cold    | Very low     | Higher      | Slightly lower | Low     |
| Archive | Lowest       | Highest     | Offline        | High    |

---

## ✅ AZ-900 Key Takeaways

* **Hot** → frequent access
* **Cool (30 days)** → infrequent
* **Cold (90 days)** → rare access
* **Archive (180 days)** → long-term, offline
* Archive = **cheapest storage, slowest access**

==========================

---

## 🧠 **AZ-900 Shorts Notes – Azure Files**

### 📁 What is Azure Files?

**Azure Files** provides **fully managed file shares in the cloud** using standard file-sharing protocols in **Microsoft Azure**.

You can access Azure file shares using:

* **SMB (Server Message Block)** → Windows, Linux, macOS
* **NFS (Network File System)** → Linux, macOS

Azure Files can be mounted **simultaneously** by:

* Cloud VMs
* On-premises servers

---

## 🔗 Protocol Support

* **SMB Azure Files**

  * Windows, Linux, macOS
  * Supports **Azure File Sync** (Windows Server caching)
* **NFS Azure Files**

  * Linux & macOS only
  * No Windows support

---

## ⭐ Key Benefits of Azure Files

### 🔹 Shared Access

* Uses **industry-standard SMB & NFS**
* Easy replacement for **on-premises file servers**
* No application changes required

---

### 🔹 Fully Managed

* No hardware or OS management
* No patching, no disk failures to handle
* Microsoft manages availability & durability

---

### 🔹 Scripting & Tooling

* Manage using:

  * Azure Portal
  * Azure Storage Explorer
  * Azure CLI
  * PowerShell
* Easy automation & DevOps integration

---

### 🔹 High Resiliency

* Built for **high availability**
* No worries about:

  * Power outages
  * Network failures
  * Hardware crashes

---

### 🔹 Familiar Programmability

* Access data using **standard file system I/O**
* Existing applications work without rewrite
* Also supports:

  * Azure Storage Client Libraries
  * Azure Storage REST API

---

## ⚡ Azure File Sync (Exam Favorite)

* Cache Azure Files on **Windows Server**
* Fast local access + cloud backup
* Ideal for **hybrid environments**

---

## ✅ AZ-900 Key Takeaways

* Azure Files = **cloud file shares**
* Supports **SMB & NFS**
* Works with **cloud + on-prem**
* Fully managed & highly available
* Azure File Sync = **local caching**

========================================

---

## 🧠 **AZ-900 Shorts Notes – Azure Queue, Disk & Table Storage**

## 📬 Azure Queue Storage

### 🔹 What is Azure Queue Storage?

**Azure Queue Storage** is a service used to store **large numbers of messages** that can be accessed securely via **HTTP/HTTPS** from anywhere in **Microsoft Azure**.

### 📌 Key Points

* Stores **millions of messages**
* Max message size: **64 KB**
* Used for **asynchronous processing**
* Helps **decouple application components**

### ⚙ Common Use Case

* Create a **backlog of work**
* Process tasks independently and reliably

### 🔁 Integration (Exam Favorite)

* Works with **Azure Functions**
* Example:

  * User submits form → Message added to Queue
  * Azure Function triggers → Processes task

✅ **Keyword**: *Asynchronous communication*

---

## 💽 Azure Disk Storage (Managed Disks)

### 🔹 What is Azure Disk Storage?

**Azure Managed Disks** are **block-level storage volumes** used with **Azure Virtual Machines**.

### 📌 Key Points

* Similar to a **physical hard disk**
* Fully **managed by Azure**
* High availability & resiliency
* No OS or hardware management

### 🧠 Use Case

* OS disks and data disks for VMs
* Databases running on VMs

✅ **Keyword**: *Block storage for VMs*

---

## 📊 Azure Table Storage

### 🔹 What is Azure Table Storage?

**Azure Table Storage** is a **NoSQL key-value datastore** for storing **large amounts of structured, non-relational data**.

### 📌 Key Points

* Schema-less (No fixed schema)
* Accessible from **inside & outside Azure**
* Supports **authenticated access**
* Ideal for **hybrid & multi-cloud** apps

### 🧠 Use Case

* Metadata storage
* User profiles
* IoT data

✅ **Keyword**: *Structured NoSQL storage*

---

## 📌 Quick Comparison (AZ-900 Ready)

| Service           | Type          | Best For                       |
| ----------------- | ------------- | ------------------------------ |
| **Queue Storage** | Messaging     | Async task processing          |
| **Disk Storage**  | Block storage | VM OS & data disks             |
| **Table Storage** | NoSQL         | Structured non-relational data |

---

## ✅ AZ-900 Exam Tips

* Queue = **message-based async communication**
* Disk = **VM storage**
* Table = **NoSQL structured data**
* Blob ≠ Queue ≠ Table (very common trap)


=========================================

---

# **Identify Azure data migration options**


### 📦 Why Data Migration Matters?

To move **infrastructure, applications, and data** into **Microsoft Azure**, Azure provides:

* **Online (real-time) migration** → *Azure Migrate*
* **Offline (asynchronous) migration** → *Azure Data Box*

---

## 🔁 Azure Migrate (Online Migration)

### 🔹 What is Azure Migrate?

A **central migration hub** that helps you **assess, plan, and migrate** on-premises environments to Azure.

### ⭐ Key Capabilities

* **Unified migration platform**

  * Single portal to track entire migration
* **Assessment + Migration**

  * Identify readiness, sizing, and costs
* **Tool integration**

  * Works with Azure tools + ISV tools

---

### 🧰 Integrated Tools (Exam Important)

* **Discovery & Assessment**

  * VMware, Hyper-V, physical servers
* **Server Migration**

  * Migrate VMs & physical servers
* **Data Migration Assistant**

  * SQL Server assessment
* **Azure Database Migration Service**

  * Databases → Azure SQL / SQL MI / SQL on VM
* **App Service Migration Assistant**

  * .NET & PHP apps → Azure App Service
* **Azure Data Box**

  * Large offline data transfer

✅ **Best for**: Connected environments, live migrations

---

## 📦 Azure Data Box (Offline Migration)

### 🔹 What is Azure Data Box?

A **physical migration device** used to move **very large datasets** when network transfer is slow or unavailable.

### 📌 Key Features

* Up to **80 TB usable storage**
* Secure, rugged, tamper-resistant device
* Ordered & tracked via **Azure Portal**
* Data auto-uploaded after device return

### 🔐 Security

* Disk wiping follows **NIST 800-88r1** standards
* Full end-to-end tracking

---

## 🎯 When to Use Azure Data Box?

### 📥 Import Scenarios

* One-time bulk migration (>40 TB)
* Media libraries (tapes → cloud)
* VM farms, SQL, app data
* Historical data for analytics (HDInsight)
* Initial seeding + incremental network sync

### 📤 Export Scenarios

* Disaster recovery (restore Azure data on-prem)
* Government / security requirements
* Move back to on-prem or another cloud

✅ **Best for**: Limited bandwidth, massive data volumes

---

## ⚖ Azure Migrate vs Azure Data Box

| Feature        | Azure Migrate      | Azure Data Box      |
| -------------- | ------------------ | ------------------- |
| Migration type | Online / Real-time | Offline / Physical  |
| Network needed | Yes                | No / Limited        |
| Data size      | Small–Large        | Very Large (>40 TB) |
| Use case       | Live workloads     | Bulk data transfer  |

---

## ✅ AZ-900 Exam Takeaways

* **Azure Migrate** = assess + migrate infra & apps
* **Azure Data Box** = physical device for huge data
* Online vs Offline migration is a **common exam trap**
* Data Box supports **import & export**


============================================

## 🧠 **AZ-900 Shorts Notes – Azure File Movement Options**



To move **individual files or small file groups** (not full migrations) in **Microsoft Azure**, Azure provides **AzCopy**, **Azure Storage Explorer**, and **Azure File Sync**.

---

## 🔹 AzCopy

### 📌 What is AzCopy?

A **command-line tool** to copy data **to, from, or between** Azure storage accounts.

### ✅ Key Features

* Upload & download files
* Copy data between storage accounts
* Synchronize files/blobs (**one-direction only**)
* Can work with **other cloud providers**

⚠ **Important (Exam Trap)**

* AzCopy sync is **one-way**
* No bi-directional sync or timestamp-based reconciliation

👉 **Best for**: Automation, scripts, bulk file copy

---

## 🔹 Azure Storage Explorer

### 📌 What is Azure Storage Explorer?

A **GUI-based desktop application** to manage Azure Storage.

### ✅ Key Features

* Works on **Windows, macOS, Linux**
* Upload, download, move files
* Manage blobs, files, queues, tables
* Uses **AzCopy in the backend**

👉 **Best for**: Manual operations, beginners, admins

---

## 🔹 Azure File Sync

### 📌 What is Azure File Sync?

A service that keeps **on-prem Windows file servers** and **Azure Files** **bi-directionally synchronized**.

### ⭐ Key Capabilities

* Centralize file shares in Azure Files
* Local access using:

  * SMB
  * NFS
  * FTPS
* Multiple caches across locations
* Replace failed servers easily

### ⚡ Cloud Tiering

* Frequently used files → cached locally
* Infrequently used files → stored in cloud

👉 **Best for**: Hybrid environments, file server modernization

---

## 📊 Quick Comparison (AZ-900 Ready)

| Tool                 | Interface | Sync Type      | Best Use Case           |
| -------------------- | --------- | -------------- | ----------------------- |
| **AzCopy**           | CLI       | One-direction  | Automated file transfer |
| **Storage Explorer** | GUI       | Manual         | Easy file management    |
| **Azure File Sync**  | Service   | Bi-directional | Hybrid file servers     |

---

## ✅ AZ-900 Exam Key Takeaways

* **AzCopy** = CLI + one-way sync
* **Storage Explorer** = GUI + AzCopy backend
* **Azure File Sync** = bi-directional + cloud tiering
* File movement ≠ full migration (Azure Migrate/Data Box)


---

