---
title: "• Identity & Security"
parent: "• Azure"
grand_parent: "4. Cloud Platforms"
nav_order: 5
has_children: true
---

👉 [GitHub Repo link for Azure Identity & Security](https://github.com/natrajwadhai13/Azure-zero-to-hero/blob/main/Day-12/01-commands.md)

8.1 Identity

AD Connect, 
Managed Identity, 
Conditional Access

8.2 Security

Defender for Cloud, 
Key Vault, 
Encryption, 
Secrets rotation

---

=====================================

![Image](https://learn.microsoft.com/en-us/azure/architecture/identity/media/identity-basic-architecture.png)

![Image](https://k21academy.com/wp-content/uploads/2020/09/93-1024x495.jpg)

![Image](https://learn.microsoft.com/en-us/azure/role-based-access-control/media/scope-overview/rbac-scope-no-label.png)

![Image](https://emilyvanputten.com/wp-content/uploads/2021/01/Azure-Managed-Identity-explained-0.png)

# 🔐 **Azure IAM (Identity & Access Management)**

---

## 1️⃣ **What is Azure IAM?**

**Azure IAM** controls **access to Azure resources** by managing:

* **Who** can sign in (Authentication)
* **What** they can do (Authorization)

Azure IAM is built using **Microsoft Entra ID**
(formerly **Azure Active Directory / AAD**).

---

## 2️⃣ **Core IAM Concepts (Foundation)**

### 🔑 Authentication vs 🛂 Authorization

| Concept            | Meaning                                 |
| ------------------ | --------------------------------------- |
| **Authentication** | Verifies identity (*Who are you?*)      |
| **Authorization**  | Grants permissions (*What can you do?*) |

📌 **Exam Trap:**
Authentication ≠ Authorization

---

## 3️⃣ **Authentication (Users, Groups, Sign-in)**

### 👤 **Users**

* Individual human identities
* Used to login to:

  * Azure Portal
  * Azure CLI (`az login`)
* Authentication methods:

  * Username + password
  * **MFA (Multi-Factor Authentication)**

**Practical (Portal):**

```
Azure Portal → Microsoft Entra ID → Users → New User
```

---

### 👥 **Groups**

* Collection of users
* Used for **easy permission management**

**Why Groups?**

* Assign role once to group
* All users inherit access

**Types**

* Security Groups (most common)
* Microsoft 365 Groups

**Best Practice**

> Assign roles to **groups**, not individual users.

**Practical:**

```
Entra ID → Groups → New Group → Add Members
```

---

## 4️⃣ **Microsoft Entra ID (Azure AD / AAD)**

### 🔐 What is Entra ID?

* Cloud-based **identity & access service**
* Manages:

  * Users
  * Groups
  * Applications
  * Authentication

### Used for:

* Azure Portal login
* Azure CLI login
* Single Sign-On (SSO)
* Application authentication

📌 **Important**

* Entra ID ≠ On-prem Active Directory
* Entra ID is **cloud-native**

---

## 5️⃣ **Authorization (RBAC – Role-Based Access Control)**

### 🧾 What is RBAC?

RBAC controls **what actions** an identity can perform on Azure resources.

---

### 🎭 RBAC Components (Very Important)

1. **Security Principal** – Who (User / Group / Service Principal / Managed Identity)
2. **Role Definition** – What actions are allowed
3. **Scope** – Where access applies

---

### 🏷️ **Built-in Roles (AZ-900 MUST-KNOW)**

| Role            | Permissions                       |
| --------------- | --------------------------------- |
| **Owner**       | Full access + assign roles        |
| **Contributor** | Full access (cannot assign roles) |
| **Reader**      | View-only                         |

📌 **Exam Line**

> Only **Owner** can assign RBAC roles.

---

### 📍 **RBAC Scope Levels**

* Management Group
* Subscription
* Resource Group
* Resource

📌 Permissions flow **top → down**.

**Practical:**

```
Resource Group → Access Control (IAM) → Add role assignment
```

---

## 6️⃣ **Service Principal (Application Identity)**

### 🤖 What is a Service Principal?

* Identity for **applications or automation**
* Used by:

  * CI/CD pipelines
  * Automation scripts
  * External apps

### Why needed?

* Applications cannot login like users
* Secure, controlled access using RBAC

**Example**

> Azure DevOps pipeline deploying resources using a Service Principal.

**Practical (CLI):**

```bash
az ad sp create-for-rbac --name myApp
```

---

## 7️⃣ **Managed Identities (Most Secure – Azure Recommended)**

### 🔄 What is a Managed Identity?

* Azure-managed identity for **Azure resources**
* No passwords, no secrets
* Automatically handled by Azure

---

### 🧩 Types of Managed Identity

| Type                | Description          |
| ------------------- | -------------------- |
| **System-assigned** | Tied to one resource |
| **User-assigned**   | Standalone, reusable |

---

### ✅ Why Managed Identity?

* No credential management
* Highly secure
* Best for Azure-to-Azure access

**Example Use Cases**

* VM accessing Azure Storage
* App Service accessing Key Vault
* Function App accessing Database

**Practical Flow**

```
Enable Managed Identity → Assign RBAC Role → Access Resource
```

📌 **Exam Line**

> Managed Identity is the **best way** for Azure services to access other Azure services.

---

## 8️⃣ **Security Features in Azure IAM**

### 🔐 Key Security Controls

* **MFA** – Extra login verification
* **Conditional Access** – Rules based on:

  * Location
  * Device
  * Risk
* **Least Privilege** – Minimum access
* **RBAC** – Controlled authorization
* **Key Vault** – Secure secrets

---

## 9️⃣ **IAM Services Summary (All Included)**

| Service            | Purpose                   |
| ------------------ | ------------------------- |
| Microsoft Entra ID | Identity & authentication |
| Users              | Human identities          |
| Groups             | Permission management     |
| RBAC               | Authorization             |
| Roles              | Define permissions        |
| Service Principal  | App identity              |
| Managed Identity   | Azure resource identity   |
| MFA                | Extra security            |
| Conditional Access | Policy-based access       |

---

## 🔟 **AZ-900 Exam Traps (VERY IMPORTANT)**

* Entra ID ≠ Active Directory
* Authentication ≠ Authorization
* RBAC = Authorization only
* Service Principal ≠ Managed Identity
* Managed Identity needs **no secrets**
* Owner ≠ Contributor

---

## 🧠 **One-Line Final Revision**

* **Authentication** → Who you are
* **Authorization** → What you can do
* **Entra ID** → Azure identity service
* **RBAC** → Permission control
* **Service Principal** → App access
* **Managed Identity** → Secure Azure access

---

## 🎯 **Real Exam Question**

**Q:** Best way for App Service to access Key Vault securely?
**A:** **Managed Identity + RBAC**

=================================



# 🔐 **Azure Security, Compliance & Governance – Complete Notes (AZ-900)**

*(Theory + Practical view | Clean, Exam-Ready | All key services covered)*

---

## 1️⃣ **What is Security, Compliance & Governance in Azure?**

Azure provides built-in services to:

* **Secure resources** (Security)
* **Control access** (Identity & RBAC)
* **Enforce rules** (Governance)
* **Meet compliance requirements** (Compliance)

👉 Core services you must know for **AZ-900**:

* **Microsoft Entra ID**
* **RBAC (Role-Based Access Control)**
* **Microsoft Defender for Cloud**
* **Azure Policy**

---

## 2️⃣ **Azure Active Directory (Microsoft Entra ID)**

### 🔑 Identity & Authentication

### What is Entra ID?

* Cloud-based **identity and access management** service
* Manages:

  * Users
  * Groups
  * Applications
  * Authentication

### Used for:

* Azure Portal login
* Azure CLI login
* Single Sign-On (SSO)
* Application authentication

📌 **Important Exam Point**

> Entra ID ≠ On-prem Active Directory

---

### Authentication Features

* Username & password
* **MFA (Multi-Factor Authentication)**
* Conditional Access (advanced)

**Practical (Portal):**

```
Azure Portal → Microsoft Entra ID → Users / Groups
```

---

## 3️⃣ **RBAC – Role Based Access Control**

### 🛂 Authorization

### What is RBAC?

RBAC controls **what actions** an identity can perform on Azure resources.

> Authentication = Who you are
> Authorization (RBAC) = What you can do

---

### RBAC Components

1. **Security Principal** – User / Group / Service Principal / Managed Identity
2. **Role Definition** – Permissions
3. **Scope** – Where access applies

---

### Built-in Roles (AZ-900 MUST KNOW)

| Role            | Permissions                      |
| --------------- | -------------------------------- |
| **Owner**       | Full access + assign roles       |
| **Contributor** | Full access (no role assignment) |
| **Reader**      | View only                        |

📌 **Exam Trap:**
Only **Owner** can assign roles.

---

### RBAC Scope Levels

* Management Group
* Subscription
* Resource Group
* Resource

**Practical:**

```
Resource → Access Control (IAM) → Add role assignment
```

---

## 4️⃣ **Microsoft Defender for Cloud**

### 🛡️ Security Management & Threat Protection

### What is Defender for Cloud?

A **security posture management** and **threat protection** service.

It helps to:

* Identify security weaknesses
* Secure cloud workloads
* Protect against threats

---

### Key Capabilities

* Secure Score
* Security recommendations
* Threat detection
* Vulnerability assessment

📌 **AZ-900 Level**

> Defender for Cloud tells you **what is insecure** and **how to fix it**.

---

### Example

* VM without disk encryption → Defender recommends encryption
* Open ports → Defender suggests closing ports

**Practical:**

```
Azure Portal → Defender for Cloud → Secure Score
```

---

## 5️⃣ **Azure Policy**

### 📜 Governance & Compliance

### What is Azure Policy?

Azure Policy is used to **enforce rules and standards** on Azure resources.

> “You can create resources, but only within rules.”

---

### What Azure Policy Can Do

* Allow or deny resource creation
* Enforce naming standards
* Restrict regions
* Require tags
* Enforce security settings

---

### Example Policies

* Only allow resources in `East US`
* Require tag `Environment=Prod`
* Deny public IP creation

---

### Policy Concepts

* **Policy Definition** – Rule
* **Initiative** – Group of policies
* **Assignment** – Apply policy to scope

**Practical:**

```
Azure Portal → Policy → Assign Policy
```

---

## 6️⃣ **Compliance in Azure**

### What is Compliance?

Compliance ensures Azure meets **industry and regulatory standards**.

Examples:

* ISO
* SOC
* GDPR
* HIPAA

Azure provides **compliance documentation & reports**, not enforcement.

📌 **Exam Trap**

> Azure is compliant → **You are still responsible** for configuring your resources correctly.

---

## 7️⃣ **Security vs Governance (Very Important)**

| Security             | Governance          |
| -------------------- | ------------------- |
| Protects resources   | Controls rules      |
| Defender for Cloud   | Azure Policy        |
| Detects threats      | Prevents violations |
| Reactive + proactive | Preventive          |

---

## 8️⃣ **How These Services Work Together**

```
User
 ↓
Microsoft Entra ID (Authentication)
 ↓
RBAC (Authorization)
 ↓
Azure Policy (Governance Rules)
 ↓
Defender for Cloud (Security Monitoring)
```

---

## 9️⃣ **AZ-900 Exam Traps (HIGH FREQUENCY)**

* Entra ID ≠ Active Directory
* RBAC ≠ Authentication
* Policy ≠ RBAC
* Defender ≠ Firewall
* Policy enforces rules, Defender recommends fixes
* Compliance ≠ automatic security

---

## 🔟 **One-Line Final Revision**

* **Entra ID** → Identity & authentication
* **RBAC** → Authorization (permissions)
* **Defender for Cloud** → Security posture & threats
* **Azure Policy** → Governance & compliance rules

---

## 🎯 **Real Exam Question**

**Q:** Which service prevents creation of non-compliant resources?
**A:** **Azure Policy**

===================================================

---

# **🌐 DDoS Attack**

**DDoS Protection का मतलब है आपके server या application को ऐसे हमलों से बचाना जहाँ attackers बहुत ज़्यादा fake traffic भेजकर system को slow या unavailable कर देते हैं। Azure DDoS Protection automatically आपके Azure resources को ऐसे attacks से बचाता है और genuine users के लिए service चालू रखता है।**

---

## 🌐 DDoS Attack क्या होता है?
- **Full form:** Distributed Denial of Service  
- **Working:** Attackers बहुत सारे computers या bots से एक साथ traffic भेजते हैं।  
- **Goal:** Application के resources (CPU, memory, bandwidth) को exhaust करना ताकि **real users access न कर पाएं**।  
- Example: किसी bank की website पर अचानक लाखों fake requests भेजना → site crash हो जाती है।  

---

## 🔒 Azure DDoS Protection क्या करता है?
- **Automatic Defense:** Azure DDoS Protection आपके virtual network resources को automatically monitor करता है।  
- **Traffic Filtering:** यह genuine traffic और malicious traffic में फर्क करता है।  
- **No App Changes Needed:** आपको application code बदलने की ज़रूरत नहीं होती।  
- **Always On:** जैसे ही attack शुरू होता है, यह तुरंत mitigation शुरू कर देता है।  
- **Integration:** Application design best practices के साथ मिलकर काम करता है।  

👉 [Refer notes Azure officil document](https://learn.microsoft.com/en-us/azure/ddos-protection/ddos-protection-overview)

---

## 🛠 Key Features
- **Adaptive tuning:** आपके workload के हिसाब से protection auto‑adjust होता है।  
- **Attack analytics:** Detailed reports और telemetry मिलती है।  
- **Cost protection:** Attack के दौरान extra traffic charges से बचाता है।  
- **Simple enablement:** किसी भी virtual network पर एक click से enable किया जा सकता है।  

---

## 📊 Types of DDoS Attacks (Azure Protects Against)
1. **Volumetric attacks** → बहुत ज़्यादा traffic भेजना (bandwidth भर देना)।  
2. **Protocol attacks** → Network protocols को misuse करना (जैसे SYN flood)।  
3. **Application layer attacks** → Specific app requests भेजकर server को overload करना।  

---

## 🎯 Simple Hindi Summary
- **DDoS Attack = Fake traffic से server को down करना।**  
- **Azure DDoS Protection = Automatic shield जो आपके Azure apps को ऐसे attacks से बचाता है।**  
- इसे enable करने पर आपका system genuine users के लिए available रहता है, चाहे attackers कितनी भी कोशिश करें।  

---

👉 आसान शब्दों में:  
**DDoS Protection आपके Azure resources के लिए “traffic filter + shield” है, जो fake requests को रोकता है और असली users को service देता है।**


=========================================

---
# 🔑 Firewall

**Firewall एक security system है जो आपके network को सुरक्षित रखता है। यह incoming और outgoing traffic को predefined rules के आधार पर allow, block या drop करता है। इसे आप ऐसे समझ सकते हैं जैसे आपके घर का दरवाज़ा – जो तय करता है कि कौन अंदर आ सकता है और कौन नहीं।**  

---

## 🔑 Firewall क्या करता है?
- **Traffic Filter करता है** → हर data packet को check करता है।  
- **Allow / Block / Drop** →  
  - Allow = सही traffic अंदर जाने देता है  
  - Block = गलत traffic को रोक देता है  
  - Drop = silently reject कर देता है (कोई response नहीं देता)  
- **Barrier बनाता है** → Trusted network (जैसे आपका office LAN) और untrusted network (Internet) के बीच दीवार की तरह काम करता है।  

---

## 🛠 Types of Firewalls
- **Network-based Firewall** → पूरे network को protect करता है।  
- **Host-based Firewall** → Individual computer या server पर चलता है।  
- **Hardware Firewall** → Physical device जो router/switch के साथ लगाया जाता है।  
- **Software Firewall** → Operating system या application में install होता है।  

---

## 📊 Importance of Firewalls
- Unauthorized access रोकता है (जैसे hacker attempts)।  
- Malware और suspicious traffic filter करता है।  
- Internal systems को external threats से बचाता है।  
- Compliance और data protection में मदद करता है।  

---

## 🎯 Simple Hindi Summary
- **Firewall = Security guard**  
- यह आपके network में आने-जाने वाले traffic को check करता है।  
- सही traffic को allow करता है, गलत को block करता है।  
- Types: Hardware, Software, Network-based, Host-based.  

---

👉 आसान शब्दों में:  
**Firewall आपके computer/network के लिए “दरवाज़ा + guard” है, जो तय करता है कि कौन अंदर आए और कौन बाहर रहे।**
