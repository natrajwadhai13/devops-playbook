---
title: "📌 VPC"
nav_order: 1
parent: "• VPC & Networking"
grand_parent: "• AWS"
grand_grand_parent: "4. Cloud Platforms"
---

# **VPC**

**What is it?** Your own isolated network in the AWS cloud.

 **Why care?** Full control over subnets, IPs, routes, firewalls
 (security groups), NATs.

 **Pro Tip:** Separate public & private subnets for
 security + efficiency.

 **Bonus Insight:** Use VPC Peering or Transit Gateway to
 connect multiple VPCs across regions/accounts.
 Enable Flow Logs to monitor network traffic
 for compliance or debugging

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



=============================================


---

# **VPC Peering**

### **What is VPC Peering?**

VPC Peering is a **network connection between two VPCs** that enables you to route traffic between them using **private IP addresses**.

It allows VPCs to communicate **as if they are part of the same network**.

---

# **Key Points**

### ✅ **Private communication**

* Traffic stays **inside AWS network**
* No public internet involvement
* Low latency & secure

---

### ✅ **1:1 Relationship**

* Peering is created **between exactly two VPCs**
* No group or multi-VPC peering

---

### ✅ **Non-transitive**

* **Very important exam point**
* If VPC-A is peered with VPC-B
  and VPC-B is peered with VPC-C
  → **A cannot talk to C**
* You must create **direct peering** between A ↔ C

---

### ✅ **Works across**

* Same region
* Cross-region (Inter-Region VPC Peering)

---

### ✅ **CIDR Must Not Overlap**

Peering is allowed **only when both VPCs have non-overlapping CIDR blocks**.

Example:

* VPC1: 10.0.0.0/16
* VPC2: 172.31.0.0/16
  → Valid

---

### ✅ **Resources that can communicate**

* EC2 Instances
* RDS
* Lambda (inside VPC)
* ECS tasks
* Anytime the resource has a **private IP**

---

### ✅ **Routing Required**

After peering creation, you must:

1. **Add route entries** in route tables
2. Point to **the peering connection**

Otherwise, instances won’t communicate.

---

### ✅ **Security Groups**

* Security Groups must allow traffic from the other VPC
* Example: allow 10.0.0.0/16 from peered VPC

---

# **When to Use VPC Peering?**

Use it when:

* You need **direct, private, low-latency** communication
* You want to share **internal applications** across VPCs
* Two teams/Accounts need private connectivity
* Simple point-to-point architecture

---

# **When NOT to Use VPC Peering**

Avoid it when:

* You need **many-to-many** connectivity
* You want **hub-and-spoke** architecture
* You want **centralized security/firewall**

Better options:

* **Transit Gateway**
* **AWS PrivateLink**
* **VPN** or **Direct Connect**

---

# **VPC Peering Limitations**

* No transitive routing
* No edge-to-edge routing
* No full-mesh automatically
* CIDR cannot overlap
* Cannot use with VPN/DirectConnect as a path
* No support for **security group referencing across VPCs (cross-region)**

---

# **Steps to Create VPC Peering (High-Level)**

1. Go to **VPC Console → Peering Connections**
2. Click **Create Peering Connection**
3. Select:

   * Requester VPC
   * Accepter VPC (Same or Another Account)
4. Accepter must **Accept** the peering request
5. Update **Route Tables** in both VPCs
6. Update **Security Groups** to allow traffic

---

# **Diagram (Text Version)**

```
VPC A (10.0.0.0/16) ------------------- Peering ------------------- VPC B (172.31.0.0/16)
          |                                                                       |
      EC2 Instances                                                       EC2 Instances
```

---

# **One-Line Summary**

**VPC Peering is a secure, private, point-to-point connection between two VPCs that requires route table updates and doesn't support transitive routing.**

---

=============================================


# ✅ **VPC – Complete Practical Notes (With EC2 Example)**

---

# **1. Create Your Own VPC**

### **Step 1: Create a VPC**

* Go to **VPC Console → Your VPCs → Create VPC**
* Choose **VPC only**
* Enter:

  * **Name:** my-vpc
  * **IPv4 CIDR:** `10.0.0.0/16`
* Click **Create VPC**

✔ Your VPC is ready.

---

# **2. Create Subnets**

We will create:

| Subnet Type    | Name             | CIDR          |
| -------------- | ---------------- | ------------- |
| Public Subnet  | public-subnet-1  | `10.0.1.0/24` |
| Private Subnet | private-subnet-1 | `10.0.2.0/24` |

### **Step: Create Public Subnet**

* Go to **Subnets → Create Subnet**
* Select VPC: **my-vpc**
* Name: **public-subnet-1**
* CIDR: `10.0.1.0/24`

### **Step: Create Private Subnet**

* Name: **private-subnet-1**
* CIDR: `10.0.2.0/24`

---

# **3. Create Internet Gateway (IGW)**

### **Step**

* Go to **Internet Gateway → Create IGW**
* Name: **my-igw**
* Attach to:

  * VPC → **my-vpc**

---

# **4. Route Table for Public Subnet**

### **Step**

* Go to **Route Tables → Create Route Table**
* Name: **public-rt**
* Select VPC: **my-vpc**

### **Edit Routes**

* Destination: `0.0.0.0/0`
* Target: **my-igw**

### **Associate Subnet**

* public-subnet → **Associate with public-rt**

✔ Now public subnet has internet access.

---

# **5. NAT Gateway (For Private Subnet Internet Outbound)**

### **Step: Create NAT Gateway**

* Go to **NAT gateway → Create**
* Subnet → **public-subnet-1**
* Elastic IP → Allocate New → Attach

### **Update Private Route Table**

* Create route table → **private-rt**
* Add route:

  * Destination: `0.0.0.0/0`
  * Target: **NAT Gateway**

### **Associate Subnet**

* Connect **private-subnet-1** to **private-rt**

✔ Private subnet can reach the internet through NAT.

---

# **6. Launch EC2 Instance in Public Subnet**

### Steps:

* Go to **EC2 → Launch Instance**
* Name: **public-ec2**
* AMI: Amazon Linux 2023
* Instance type: t2.micro
* Network:

  * VPC: **my-vpc**
  * Subnet: **public-subnet-1**
  * Auto-assign Public IP: **Enable**
* Security Group:

  * Allow SSH: 22 from My IP
  * Allow HTTP: 80 from Anywhere

Launch instance.

### Test:

SSH into instance:

```sh
ssh -i mykey.pem ec2-user@<Public-IP>
```

---

# **7. Launch EC2 Instance in Private Subnet**

### Steps:

* Name: **private-ec2**
* VPC: **my-vpc**
* Subnet: **private-subnet-1**
* Auto-assign Public IP: **Disable**
* SG: Allow SSH only from public-ec2 SG

✔ No public internet → needs Bastion EC2 to connect.

### Connect using Bastion:

From Public-EC2:

```sh
ssh ec2-user@10.0.2.10   # private-ec2 private IP
```

---

# ⭐ **Your VPC Architecture Now**

```
                 INTERNET
                     |
                [ IGW ]
                     |
        ---------------------------------
        |          PUBLIC SUBNET        |
        |   EC2 (public-ec2)            |
        ---------------------------------
                     |
                [ NAT GW ]
                     |
        ---------------------------------
        |         PRIVATE SUBNET        |
        |   EC2 (private-ec2)           |
        ---------------------------------
```

---

# 🟦 **VPC Peering – Complete Practical with Example**

We will create 2 VPCs:

| VPC   | CIDR           |
| ----- | -------------- |
| VPC-A | 10.0.0.0/16    |
| VPC-B | 192.168.0.0/16 |

✔ No overlapping → Peering possible.

---

# **1. Create VPC-A and EC2**

(Already created above)

Add another instance to use for testing:

* Name: **VpcA-EC2**
* Subnet: public-subnet-1 (10.0.1.0/24)
* Private IP example: `10.0.1.50`

---

# **2. Create VPC-B**

* VPC name: **my-vpc-b**
* CIDR: `192.168.0.0/16`

### Create subnet

* Name: **public-subnet-b**
* CIDR: `192.168.1.0/24`

### Create route table

* Name: **rtb-b**
* Route:

  * `0.0.0.0/0 → IGW-B`

### Create IGW-B

Attach to VPC-B

### Launch EC2

* Name: **VpcB-EC2**
* Private IP example: `192.168.1.50`

---

# ⭐ **3. Create VPC Peering Connection**

### Step:

* Go to **VPC → Peering Connections**
* Create Peering

  * Requester VPC: **my-vpc**
  * Accepter VPC: **my-vpc-b**

Click **Create**

### Accept Request

* Select peering request → **Accept**

✔ Peering is active.

---

# ⭐ **4. Update Route Tables (VERY IMPORTANT)**

### In VPC-A Route Table:

Add:

* Destination: `192.168.0.0/16`
* Target: **pcx-xxxx** (VPC Peering ID)

### In VPC-B Route Table:

Add:

* Destination: `10.0.0.0/16`
* Target: **pcx-xxxx**

✔ Now both VPCs can reach each other through private IPs.

---

# ⭐ **5. Security Groups**

Allow ICMP (ping) and SSH from other VPC CIDR.

### On VPC-A EC2 SG:

Allow:

```
Source: 192.168.0.0/16
Type: All ICMP (ping)
```

### On VPC-B EC2 SG:

Allow:

```
Source: 10.0.0.0/16
Type: All ICMP
```

---

# ⭐ **6. Test Connectivity**

### From VPC-A EC2:

```
ping 192.168.1.50
```

### From VPC-B EC2:

```
ping 10.0.1.50
```

✔ Ping success = VPC Peering working!

---

# ⭐ **Practical Verification Commands**

### On EC2 (Linux):

Check private IP:

```sh
hostname -I
```

Check route tables:

```sh
ip route
```

Ping peer VPC:

```sh
ping <peer-private-ip>
```

---

# ⭐ VPC Peering Architecture (Text Diagram)

```
                 VPC-A (10.0.0.0/16)
           --------------------------------
           | EC2: 10.0.1.50               |
           --------------------------------
                     ||
                     ||  VPC PEERING (PCX)
                     ||
           --------------------------------
           | EC2: 192.168.1.50            |
           --------------------------------
                VPC-B (192.168.0.0/16)
```

---

# ⭐ **Key VPC Peering Limitations**

* 🚫 No transitive routing
* 🚫 CIDR overlap not allowed
* 🚫 Cannot use as NAT or VPN path
* 🚫 SG referencing cross-region not supported

---

# ⭐ **When to Use VPC Peering**

* Simple point-to-point connection
* < 10 VPCs
* Direct connection between teams/apps

# ⭐ **Better Replace With**

* AWS Transit Gateway (hub & spoke)
* AWS PrivateLink (when connecting to services)

---
