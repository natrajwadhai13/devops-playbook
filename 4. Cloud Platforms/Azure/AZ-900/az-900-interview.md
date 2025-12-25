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

