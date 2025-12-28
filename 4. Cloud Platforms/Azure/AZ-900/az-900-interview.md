---
title: "📌 AZ-900-KeyWord"
parent: "• AZ-900"
grand_parent: "• Azure"
grand_grand_parent: "4. Cloud Platforms"
nav_order: 4
has_children: true
---




---

# 🌐 **AZ-900 Networking Interview Questions & Answers**

---

## **1️⃣ What is Azure Virtual Network (VNet)?**

**Answer:**
Azure Virtual Network is a **private network in Azure** used to securely connect Azure resources like VMs, databases, and app services.

---

## **2️⃣ What is a Subnet?**

**Answer:**
A subnet is a **logical division of a VNet** used to organize and isolate resources within the network.

---

## **3️⃣ What is the difference between VNet and Subnet?**

**Answer:**

* **VNet** → Entire private network
* **Subnet** → Smaller network inside a VNet

---

## **4️⃣ What is an IP address in Azure?**

**Answer:**
An IP address identifies a resource on the network. Azure supports:

* **Private IP** → Internal communication
* **Public IP** → Internet access

---

## **5️⃣ Difference between Public IP and Private IP?**

**Answer:**

| Public IP            | Private IP                 |
| -------------------- | -------------------------- |
| Internet accessible  | Internal only              |
| Less secure          | More secure                |
| Used for public apps | Used for backend resources |

---

## **6️⃣ What is Network Security Group (NSG)?**

**Answer:**
NSG is a **basic firewall** that controls **inbound and outbound traffic** using allow/deny rules based on IP, port, and protocol.

---

## **7️⃣ Where can you apply an NSG?**

**Answer:**

* Subnet level
* Network Interface (NIC) level

---

## **8️⃣ What is Azure Load Balancer?**

**Answer:**
Azure Load Balancer distributes incoming traffic across multiple VMs to ensure **high availability and reliability**.

---

## **9️⃣ What are the types of Azure Load Balancer?**

**Answer:**

* **Public Load Balancer** → Internet-facing
* **Internal Load Balancer** → Private traffic

---

## **🔟 What is Application Gateway?**

**Answer:**
Application Gateway is a **Layer 7 (HTTP/HTTPS) load balancer** used for web applications.

---

## **1️⃣1️⃣ Load Balancer vs Application Gateway?**

**Answer:**

| Load Balancer | Application Gateway |
| ------------- | ------------------- |
| Layer 4       | Layer 7             |
| TCP/UDP       | HTTP/HTTPS          |
| No WAF        | Supports WAF        |

---

## **1️⃣2️⃣ What is Azure VPN Gateway?**

**Answer:**
VPN Gateway provides **secure encrypted connectivity** between:

* On-premises ↔ Azure
* User ↔ Azure

---

## **1️⃣3️⃣ What is ExpressRoute?**

**Answer:**
ExpressRoute is a **private dedicated connection** between on-premises and Azure that does **not use the public internet**.

---

## **1️⃣4️⃣ VPN vs ExpressRoute?**

**Answer:**

| VPN             | ExpressRoute       |
| --------------- | ------------------ |
| Uses internet   | Private connection |
| Lower cost      | Higher cost        |
| Lower bandwidth | High bandwidth     |

---

## **1️⃣5️⃣ What is Azure DNS?**

**Answer:**
Azure DNS is a **cloud-based DNS service** used to host and manage domain names.

---

## **1️⃣6️⃣ What is VNet Peering?**

**Answer:**
VNet peering connects two VNets so resources can communicate **privately and securely** using Azure backbone network.

---

## **1️⃣7️⃣ What is Azure Firewall?**

**Answer:**
Azure Firewall is a **managed, centralized network security service** that controls traffic using rules and logging.

---

## **1️⃣8️⃣ What is Azure DDoS Protection?**

**Answer:**
Azure DDoS Protection safeguards applications from **traffic flooding attacks**.

* **Basic** → Free
* **Standard** → Advanced protection

---

## **1️⃣9️⃣ What is a Service Endpoint?**

**Answer:**
Service Endpoint allows secure access to Azure services while keeping traffic **within the Azure network**.

---

## **2️⃣0️⃣ What is a Private Endpoint?**

**Answer:**
Private Endpoint assigns a **private IP** to Azure services, completely removing internet exposure.

---

## **⭐ Most Important AZ-900 One-Liners**

* VNet = private Azure network
* Subnet = network segmentation
* NSG = basic firewall
* Load Balancer = Layer 4
* Application Gateway = Layer 7
* VPN uses internet, ExpressRoute doesn’t
* Private Endpoint is more secure than Service Endpoint

========================================


![Image](https://www.msp360.com/resources/wp-content/uploads/2017/10/1-07.png)

![Image](https://learn.microsoft.com/en-us/security/zero-trust/media/secure-storage/azure-infra-storage-network-2.svg)

![Image](https://miro.medium.com/1%2Ax9Kbo9bkJCSi12WhSRsCRA.gif)

---

# 💾 **AZ-900 – Azure Storage Interview Q&A**

---

## **1️⃣ What is Azure Storage?**

**Answer:**
Azure Storage is a **cloud storage service** that provides **secure, scalable, durable storage** for data such as files, images, videos, backups, and application data.

---

## **2️⃣ Why do we use Azure Storage?**

**Answer:**
Because it offers:

* High durability (**11 nines – 99.999999999%**)
* High availability
* Strong security (encryption + Azure AD)
* Massive scalability
* Cost-effective storage options

---

## **3️⃣ What is a Storage Account?**

**Answer:**
A Storage Account is the **top-level container** in Azure that holds all storage services like **Blob, File, Table, and Queue**.

---

## **4️⃣ What are the main Azure Storage services?**

**Answer:**

1. Blob Storage
2. File Storage
3. Table Storage
4. Queue Storage

---

## **5️⃣ What is Azure Blob Storage?**

**Answer:**
Blob Storage is used to store **unstructured data** such as:

* Images
* Videos
* Audio
* Logs
* Backups

**Example:** Website images, video streaming, application logs.

---

## **6️⃣ What is unstructured data?**

**Answer:**
Data that does not follow a fixed schema, like images, videos, PDFs, and text files.

---

## **7️⃣ What is Azure File Storage?**

**Answer:**
Azure File Storage provides a **shared file system** in the cloud that can be mounted on:

* Azure VMs
* On-prem servers
* Containers

**AWS comparison:**
Azure File ≈ EFS

---

## **8️⃣ Difference between Blob Storage and File Storage?**

**Answer:**

| Blob Storage      | File Storage   |
| ----------------- | -------------- |
| Object storage    | File system    |
| Unstructured data | Shared files   |
| HTTP/HTTPS access | SMB/NFS access |

---

## **9️⃣ What is Azure Table Storage?**

**Answer:**
A **NoSQL key-value storage** used to store structured but schema-less data.

**Example:** User profiles, configuration data.

---

## **🔟 What is Azure Queue Storage?**

**Answer:**
Queue Storage is used for **message-based communication** between application components.

**Example:**
App sends a message → background worker processes it later.

---

## **1️⃣1️⃣ What is durability in Azure Storage?**

**Answer:**
Durability means how safely data is stored.
Azure Storage provides **99.999999999% durability**, meaning extremely low chance of data loss.

---

## **1️⃣2️⃣ How does Azure achieve high durability?**

**Answer:**
By **replicating data automatically** across multiple disks, datacenters, or regions.

---

## **1️⃣3️⃣ What are replication options in Azure Storage?**

**Answer:**

* **LRS** – Locally Redundant Storage
* **ZRS** – Zone Redundant Storage
* **GRS** – Geo Redundant Storage
* **RA-GRS** – Read Access Geo Redundant Storage

---

## **1️⃣4️⃣ What is LRS?**

**Answer:**
LRS stores **3 copies of data in a single datacenter**.

---

## **1️⃣5️⃣ What is GRS?**

**Answer:**
GRS stores data in:

* Primary region
* Secondary region (geo-replication)

---

## **1️⃣6️⃣ What is performance tier in Azure Storage?**

**Answer:**
Performance tier defines speed and latency:

* **Standard** → General workloads
* **Premium** → High IOPS, low latency

---

## **1️⃣7️⃣ How is Azure Storage secured?**

**Answer:**

* Encryption at rest (AES-256)
* Encryption in transit (HTTPS)
* Azure AD integration
* RBAC
* Network security (firewall, private endpoint)

---

## **1️⃣8️⃣ What are Access Keys?**

**Answer:**
Access Keys provide **full access** to the Storage Account.
They should be used carefully and rotated regularly.

---

## **1️⃣9️⃣ What is encryption in Azure Storage?**

**Answer:**

* **At rest:** Data is encrypted automatically
* **In transit:** Data is encrypted using TLS/HTTPS

Encryption is **enabled by default**.

---

## **2️⃣0️⃣ What is Static Website hosting in Azure Storage?**

**Answer:**
Azure Blob Storage can host **static websites** using:

* HTML
* CSS
* JavaScript

**Example:** Portfolio or documentation site.

---

## **2️⃣1️⃣ What is Azure Storage Explorer?**

**Answer:**
A GUI tool used to:

* Upload/download blobs
* Manage file shares
* View tables and queues

Acts like **File Explorer for Azure Storage**.

---

## **2️⃣2️⃣ When should you use Queue Storage?**

**Answer:**
When you need **asynchronous processing** and loose coupling between application components.

---

## **2️⃣3️⃣ Which Azure Storage service is best for large media files?**

**Answer:**
**Azure Blob Storage**

---

## **2️⃣4️⃣ Which Azure Storage service supports shared access across VMs?**

**Answer:**
**Azure File Storage**

---

## **2️⃣5️⃣ One-Line Exam & Interview Facts**

* Storage Account = parent container
* Blob = unstructured data
* File = shared file system
* Table = NoSQL storage
* Queue = messaging service
* Azure Storage is encrypted by default

==========================

![Image](https://www.msp360.com/resources/wp-content/uploads/2017/10/1-07.png)

![Image](https://learn.microsoft.com/en-us/azure/storage/common/media/storage-redundancy/zone-redundant-storage.png)

![Image](https://miro.medium.com/1%2Ax9Kbo9bkJCSi12WhSRsCRA.gif)

---

# 💾 **AZ-900 Azure Storage – Exam Q&A**

---

## **Q1. What is Azure Storage used for?**

**Answer:**
To store **unstructured data, files, messages, and NoSQL data** securely and at scale.

---

## **Q2. Which Azure service stores unstructured data like images and videos?**

**Answer:**
**Azure Blob Storage**

---

## **Q3. Which Azure Storage service provides a shared file system?**

**Answer:**
**Azure File Storage**

---

## **Q4. Which Azure Storage service is best for messaging between applications?**

**Answer:**
**Azure Queue Storage**

---

## **Q5. Which Azure Storage service is a NoSQL key-value store?**

**Answer:**
**Azure Table Storage**

---

## **Q6. What is a Storage Account?**

**Answer:**
A **container that holds all Azure Storage services** like Blob, File, Table, and Queue.

---

## **Q7. Which storage account type is recommended for most workloads?**

**Answer:**
**General Purpose v2 (GPv2)**

---

## **Q8. What does durability mean in Azure Storage?**

**Answer:**
How safely data is stored and protected from loss (**99.999999999% durability**).

---

## **Q9. How does Azure achieve high durability?**

**Answer:**
By **replicating data automatically** across multiple disks/datacenters/regions.

---

## **Q10. What is LRS?**

**Answer:**
**Locally Redundant Storage** – 3 copies of data in a single datacenter.

---

## **Q11. What is ZRS?**

**Answer:**
**Zone Redundant Storage** – data replicated across multiple availability zones.

---

## **Q12. What is GRS?**

**Answer:**
**Geo Redundant Storage** – data replicated to a secondary region.

---

## **Q13. What is RA-GRS?**

**Answer:**
**Read-Access Geo Redundant Storage** – read access to secondary region.

---

## **Q14. Which redundancy option allows read access from another region?**

**Answer:**
**RA-GRS**

---

## **Q15. What are Azure Storage performance tiers?**

**Answer:**

* **Standard** – General workloads
* **Premium** – High IOPS, low latency

---

## **Q16. Is Azure Storage encrypted by default?**

**Answer:**
**Yes**, both **at rest** and **in transit**.

---

## **Q17. What encryption is used at rest in Azure Storage?**

**Answer:**
**AES-256**

---

## **Q18. How is data encrypted in transit?**

**Answer:**
Using **HTTPS (TLS)**.

---

## **Q19. What are Storage Account Access Keys?**

**Answer:**
Keys that provide **full access** to the storage account.

---

## **Q20. What is the recommended way to access Azure Storage securely?**

**Answer:**
Using **Azure AD (RBAC)** instead of access keys.

---

## **Q21. What is Azure Storage Explorer?**

**Answer:**
A GUI tool to manage blobs, files, queues, and tables.

---

## **Q22. Can Azure Blob Storage host a website?**

**Answer:**
**Yes**, it supports **static website hosting**.

---

## **Q23. Which files are supported in Azure static website hosting?**

**Answer:**
HTML, CSS, JavaScript

---

## **Q24. Which Azure Storage service is best for large media files?**

**Answer:**
**Azure Blob Storage**

---

## **Q25. Which Azure Storage service supports mounting on VMs?**

**Answer:**
**Azure File Storage**

---

## **Q26. Which Azure Storage service is best for decoupling applications?**

**Answer:**
**Azure Queue Storage**

---

## **Q27. Can Azure Storage be accessed privately without internet?**

**Answer:**
**Yes**, using **Private Endpoint**.

---

## **Q28. Which Azure feature protects data from accidental deletion?**

**Answer:**
**Soft delete**

---

## **Q29. Which Azure Storage feature allows versioning of blobs?**

**Answer:**
**Blob versioning**

---

## **Q30. One-Line Exam Facts (Very Important)**

* Blob → unstructured data
* File → shared file system
* Table → NoSQL storage
* Queue → messaging
* Storage Account → parent container
* Azure Storage is encrypted by default

---


=======================================


# 📘 **AZ-900 – Azure Resource Manager (ARM) Exam Questions & Answers**

Below are **exam-oriented ARM questions** with **clear, simple answers**.
These are **very common in AZ-900**.

---

## 🔹 **Q1. What is Azure Resource Manager (ARM)?**

**Answer:**
Azure Resource Manager (ARM) is the **management layer and API** that Azure uses to **create, update, and manage resources**.

---

## 🔹 **Q2. ARM is an API – True or False?**

**Answer:**
✅ **True**

ARM provides REST APIs used by all Azure tools.

---

## 🔹 **Q3. Which tools use Azure Resource Manager?**

**Answer:**

* Azure Portal
* Azure CLI
* Azure PowerShell
* ARM Templates
* Bicep
* SDKs
* Terraform

📌 All communicate with Azure **through ARM**.

---

## 🔹 **Q4. What is the main benefit of ARM?**

**Answer:**
Provides **standardized, consistent management** of Azure resources across all tools.

---

## 🔹 **Q5. What is an ARM template?**

**Answer:**
An ARM template is a **JSON file** used to **define and deploy Azure resources declaratively**.

---

## 🔹 **Q6. ARM templates are an example of what concept?**

**Answer:**
✅ **Infrastructure as Code (IaC)**

---

## 🔹 **Q7. What does “declarative” mean in ARM templates?**

**Answer:**
You define **what resources you want**, not **how to create them**.

---

## 🔹 **Q8. Which format is used by ARM templates?**

**Answer:**
✅ **JSON**

---

## 🔹 **Q9. What are the main components of an ARM template?**

**Answer:**

* Parameters
* Variables
* Resources
* Outputs

---

## 🔹 **Q10. What is Bicep?**

**Answer:**
Bicep is a **simpler language** that compiles into **ARM templates**.

---

## 🔹 **Q11. Bicep replaces ARM – True or False?**

**Answer:**
❌ **False**

Bicep **uses ARM internally**.

---

## 🔹 **Q12. Which is easier to write: ARM template or Bicep?**

**Answer:**
✅ **Bicep**

---

## 🔹 **Q13. Can ARM templates be version-controlled?**

**Answer:**
✅ **Yes**

They are stored in **Git repositories**.

---

## 🔹 **Q14. Which service ensures RBAC, tags, and policies during deployment?**

**Answer:**
✅ **Azure Resource Manager (ARM)**

---

## 🔹 **Q15. Which Azure service provides consistent deployment across environments?**

**Answer:**
✅ **Azure Resource Manager**

---

## 🔹 **Q16. Which editor is commonly used for ARM templates?**

**Answer:**
✅ **Visual Studio Code**

(with Azure Resource Manager Tools extension)

---

## 🔹 **Q17. Which command-line tool can deploy ARM templates?**

**Answer:**
✅ **Azure CLI**

---

## 🔹 **Q18. Which deployment method is best for automation?**

**Answer:**
✅ **ARM templates / Bicep**

---

## 🔹 **Q19. Terraform uses ARM internally – True or False?**

**Answer:**
❌ **False**

Terraform uses **providers**, not ARM templates directly.

---

## 🔹 **Q20. Which statement is TRUE?**

A. Azure Portal bypasses ARM
B. Azure CLI does not use ARM
C. ARM provides a single management layer
D. ARM is only for templates

**Correct Answer:**
✅ **C. ARM provides a single management layer**

---

## ⚠️ **Common Exam Traps (Very Important)**

* ARM ≠ only templates
* Portal, CLI, SDK → **ALL use ARM**
* ARM is **API + management layer**
* Bicep ≠ replacement of ARM
* Terraform ≠ Azure-native tool

---

## 🧠 **One-Line Exam Revision**

* ARM = Azure management API
* ARM templates = JSON IaC
* Bicep = simplified ARM
* Portal/CLI/SDK → ARM → Azure

---
======================================================