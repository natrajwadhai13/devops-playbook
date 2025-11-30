---
title: "• VPC"
nav_order: 4
parent: "• AWS"
grand_parent: "4. Cloud Platforms"
---



---

# **AWS VPC Network Architecture **

```
                           ┌────────────────────────────────────────────┐
                           │                AWS REGION                   │
                           │                                            │
                           │                                            │
                           │        ┌──────────────────────────┐        │
                           │        │     INTERNET GATEWAY     │        │
                           │        └──────────────┬───────────┘        │
                           │                       │                    │
┌───────────────────────┐  │                ┌──────┴──────┐             │  ┌────────────────────────┐
│        VPC 1           │  │                │   VPC 2     │             │  │                        │
│  CIDR: 10.0.0.0/16     │  │                │ CIDR: 20.0.0.0/16         │  │                        │
│                        │  │                │                            │  │                        │
│   ┌─────────────────┐  │  │     ┌────────────────────┐      ┌─────────────────────┐                │
│   │ PUBLIC SUBNET   │────────────►  ROUTE TABLE       ◄────────── PUBLIC SUBNET   │                │
│   │ (Route → IGW)   │  │  │     └────────────────────┘      └─────────────────────┘                │
│   └─────────────────┘  │  │               ▲                           ▲                           │
│                        │  │               │                           │                           │
│   ┌─────────────────┐  │  │     ┌────────────────────┐      ┌─────────────────────┐                │
│   │ PRIVATE SUBNET  │───────►  NAT GATEWAY (VPC1)     │      │ PRIVATE SUBNET     │                │
│   │ (Route → NAT)   │  │  │     └────────────────────┘      └─────────────────────┘                │
│   └─────────────────┘  │  │                (Outbound internet for private subnet)                  │
│                        │  │                                                                           
└───────────────────────┘  │                                                                           
                           │                                                                           
                           └──────────────────────────────────────────────────────────────────────────┘
```

---

# **Explanation (for Interviews)**

### **AWS Region**

* Your entire cloud infrastructure exists inside a specific AWS region.

---

# **VPC 1 & VPC 2**

* Two separate virtual networks isolated from each other.
* Example CIDRs:

  * **VPC 1:** 10.0.0.0/16
  * **VPC 2:** 20.0.0.0/16

---

# **Public Subnet**

* Subnets with route to the **Internet Gateway (IGW)**.
* EC2 instances here get public IPs and are internet accessible.

---

# **Private Subnet**

* No direct internet access.
* Internet access only via **NAT Gateway**.

---

# **Internet Gateway**

* Allows communication from public subnets to the internet.

---

# **NAT Gateway**

* Used by **private** subnets to access the internet **outbound only**.
* Example: software updates.

---

# **Route Gateway / Routing**

* Route tables decide:

  * What traffic stays in VPC
  * What traffic goes to IGW (public)
  * What traffic goes to NAT (private)

---
=============================================


CIDR, or Classless Inter-Domain Routing, is a method for allocating IP addresses that makes network routing more efficient by replacing the older classful system. It uses a slash followed by a number to denote the size of the network prefix, which allows for more flexible assignment of IP addresses and reduces address waste through subnetting and supernetting.  

for VPC IP calculate CIDR 

https://cidr.xyz/