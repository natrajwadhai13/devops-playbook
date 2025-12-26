---
title: "📌 Revision Sheet"
parent: "• AZ-900"
grand_parent: "• Azure"
grand_grand_parent: "4. Cloud Platforms"
nav_order: 6
has_children: true
---

![Image](https://learn.microsoft.com/en-us/azure/lab-services/media/classroom-labs-fundamentals/labservices-basic-architecture.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AY-6x7rFJzTRt-hE-v06Vdg.png)

![Image](https://learn.microsoft.com/en-us/azure/well-architected/design-guides/_images/regions-availability-zones/zonal-multiple-zones.png)

# ⏱️ **AZ-900 Last-Day 90-Minute Revision Sheet**

**Goal:** Fast recall of facts + exam traps. Read once, then skim again.

---

## ⏰ **How to Use (90 min)**

* **0–15 min:** Cloud concepts & cost
* **15–30 min:** Azure architecture
* **30–50 min:** Networking
* **50–70 min:** Storage
* **70–85 min:** Compute, Security, Management
* **85–90 min:** Trap checklist

---

## ☁️ **Cloud Concepts (Must-remember)**

* **IaaS:** You manage OS, runtime, apps (VMs)
* **PaaS:** Platform managed; you deploy code (App Service)
* **SaaS:** Ready apps (Microsoft 365)
* **Elasticity:** *Auto* scale out/in (trap vs scalability)
* **Scalability:** Ability to scale (often planned/manual)
* **OpEx:** Pay-as-you-go (cloud) | **CapEx:** Upfront hardware

---

## 🌍 **Azure Architecture**

* **Region:** Geographic area with datacenters
* **Availability Zones:** Separate physical DCs *within* a region
* **Availability Set:** Rack/power isolation (not full DC)
* **Resource:** *Smallest deployable unit*
* **Resource Group:** Logical container; resources can be **different regions**
* **Subscription:** Billing & access boundary
* **ARM:** Deploy/manage resources (IaC with templates)

---

## 🌐 **Networking**

* **VNet:** Private network in Azure
* **Subnet:** Segment a VNet
* **NSG:** Basic traffic filter (allow/deny)
* **Load Balancer:** L4 (TCP/UDP)
* **Application Gateway:** L7 (HTTP/HTTPS), supports **WAF**
* **WAF:** OWASP Top-10 (SQLi, XSS)
* **VPN Gateway:** Encrypted over **internet**
* **ExpressRoute:** **Private** connectivity (no internet)
* **DDoS:** Traffic flooding protection (L3/L4)
* **Bastion:** Browser-based VM access, no public IP

---

## 💾 **Storage (High-yield)**

* **Storage Account:** Parent container
* **Blob:** Unstructured data (images, videos, logs)
* **File:** Shared file system (mount on VMs)
* **Queue:** Asynchronous messaging
* **Table:** NoSQL key-value
* **Durability:** **99.999999999% (11 nines)**
* **Redundancy:**

  * **LRS:** 3 copies, 1 DC
  * **ZRS:** Across zones
  * **GRS:** Cross-region
  * **RA-GRS:** Read access to secondary
* **Encryption:** At rest (AES-256) & in transit — **ON by default**
* **Access:** Best = **Azure AD + RBAC**
* **Static Website:** Blob Storage (lowest cost)

---

## 🖥️ **Compute**

* **VM:** Full control; manage OS
* **VM Scale Set:** Auto-scale VMs
* **App Service:** PaaS for web apps
* **Functions:** **Serverless**, event-driven

---

## 🔐 **Identity, Security, Management**

* **Azure AD:** AuthN/AuthZ
* **RBAC:** Least privilege (apply at Sub/RG/Resource)
* **Key Vault:** Secrets/keys/certs
* **Azure Monitor:** Metrics & logs
* **Advisor:** Best-practice + **cost optimization**
* **Policy:** Enforce standards
* **Backup:** Data protection
* **Site Recovery:** Cross-region VM DR

---

## ⚠️ **Top Exam Traps (Read Twice)**

* Elasticity ≠ Scalability
* Availability **Zone** > Availability Set for DC failure
* Resource Group can hold **multi-region** resources
* **Resource** = smallest unit
* Blob ≠ File system
* VPN uses internet; **ExpressRoute doesn’t**
* WAF ≠ DDoS
* Storage encryption is **automatic**

---

## 🧠 **One-liners to Recall**

* L4 vs L7 → Load Balancer vs App Gateway
* Messaging → **Queue**
* Shared files → **File**
* Unstructured → **Blob**
* DR VMs → **Site Recovery**

---
============================

![Image](https://learn.microsoft.com/en-us/azure/lab-services/media/classroom-labs-fundamentals/labservices-basic-architecture.png)

![Image](https://miro.medium.com/1%2AR_Hb_aAkXZQeOgwc8r52gQ.png)

![Image](https://learn.microsoft.com/en-us/azure/reliability/media/availability-zones-overview/regions-availability-zones.svg)

# 📘 **AZ-900 Simple A-to-Z Revision Sheet (Easy & Clean)**

*Read once → You’re exam-ready*

---

## ☁️ **A – Azure Basics**

* **Azure** = Microsoft cloud platform
* **Cloud models:** IaaS, PaaS, SaaS
* **Deployment:** Public, Private, Hybrid

---

## 💰 **B – Billing & Cost**

* **OpEx** → Pay-as-you-go
* **CapEx** → Upfront hardware
* **Pricing Calculator** → Estimate cost
* **Cost Management** → Track & control spend

---

## 🌍 **C – Core Architecture**

* **Region** → Geographic location
* **Availability Zone** → Separate datacenters
* **Availability Set** → Rack/power isolation
* **Resource** → Smallest unit
* **Resource Group** → Logical container
* **Subscription** → Billing & access boundary

---

## 🧱 **D – Deployment (ARM)**

* **ARM (Azure Resource Manager)**
* Deploy, update, delete resources
* **ARM Templates** → Infrastructure as Code

---

## 🌐 **E – Networking**

* **VNet** → Private Azure network
* **Subnet** → Network segmentation
* **NSG** → Basic firewall (allow/deny)
* **Load Balancer** → Layer 4
* **Application Gateway** → Layer 7 + WAF
* **VPN Gateway** → Encrypted internet
* **ExpressRoute** → Private connection
* **DDoS Protection** → Traffic flood defense
* **Bastion** → Secure VM access (no public IP)

---

## 💾 **F – Storage**

* **Storage Account** → Parent container
* **Blob** → Unstructured data (images, videos)
* **File** → Shared file system
* **Queue** → Messaging
* **Table** → NoSQL key-value
* **Durability** → 99.999999999%
* **Redundancy** → LRS, ZRS, GRS, RA-GRS
* **Encryption** → ON by default
* **Static Website** → Blob storage

---

## 🖥️ **G – Compute**

* **VM** → Full control, manage OS
* **VM Scale Set** → Auto-scaling VMs
* **App Service** → Web apps (PaaS)
* **Functions** → Serverless, event-driven

---

## 🔐 **H – Identity & Security**

* **Azure AD** → Authentication & authorization
* **RBAC** → Role-based access control
* **Key Vault** → Secrets & keys
* **Firewall** → Centralized security
* **WAF** → Web attack protection

---

## 📊 **I – Monitoring & Management**

* **Azure Monitor** → Metrics & logs
* **Azure Advisor** → Best practices + cost tips
* **Azure Policy** → Enforce rules
* **Management Groups** → Manage multiple subscriptions

---

## 🔄 **J – Backup & DR**

* **Azure Backup** → Data protection
* **Azure Site Recovery** → VM disaster recovery

---

## ⚠️ **K – Exam Traps (Very Important)**

* Elasticity ≠ Scalability
* Availability Zone ≠ Availability Set
* Resource Group can have multi-region resources
* Blob ≠ File system
* VPN uses internet, ExpressRoute doesn’t
* WAF ≠ DDoS
* Storage encryption is automatic

---

## 🎯 **L – Last-Minute One-Liners**

* Smallest unit → **Resource**
* Serverless → **Azure Functions**
* Messaging → **Queue Storage**
* Shared files → **File Storage**
* Unstructured data → **Blob Storage**
* Cost model → **OpEx**

---
