---
title: "• Azure DevOps"
parent: "• Azure"
grand_parent: "4. Cloud Platforms"
nav_order: 6
has_children: true
---

## 🧩 **📌 Module 6: Azure DevOps (MOST IMPORTANT)**


### 6.1 Azure Repos

* Git repositories
* Branch strategy
* PR policies

### 6.2 Azure Pipelines (CI/CD)

* Build pipelines
* Release pipelines
* YAML pipelines
* Agents self-hosted & Microsoft-hosted
* Multi-stage pipelines (CI → CD → Prod)

### 6.3 Azure Artifacts

* Packages (npm, NuGet, Maven)

### 6.4 Azure Boards

* Epics, Features, User Stories
* Sprints
* Kanban

### 6.5 Azure Test Plans

* Manual + Automation test integration

---

👉 [GitHub Repo link for AZ-Cli]()


![Image](https://learn.microsoft.com/en-us/azure/devops/pipelines/architectures/media/azure-devops-ci-cd-architecture.svg?view=azure-devops)

![Image](https://media.licdn.com/dms/image/v2/D4E12AQEeJIqgIZ-fZQ/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1708237605184?e=2147483647\&t=MxyEdBN4AcIyqaspQQlACyY2c-rZM4OSyZSZPdJGp1U\&v=beta)

![Image](https://iitsnl.com/wp-content/uploads/2025/04/Azure-DevOps-Architecture.png)

# 📘 **Azure DevOps**

---

## ☁️ **What is Azure DevOps?**

**Azure DevOps** is a **cloud-based DevOps platform** by **Microsoft Azure DevOps** used to **plan, build, test, and deploy applications**.

It supports the **entire DevOps lifecycle**:

> Plan → Code → Build → Test → Release → Monitor

---

## ❓ **Why Azure DevOps?**

Azure DevOps helps teams to:

* Automate CI/CD
* Collaborate better
* Deliver software faster
* Reduce manual errors
* Maintain traceability from requirement to release

---

## ✅ **Advantages of Azure DevOps**

* End-to-end DevOps tool
* Strong CI/CD automation
* Cloud + on-prem support
* Integrates with GitHub, Docker, Kubernetes
* Secure with Azure IAM & RBAC
* Scales for small to enterprise teams

---

## 🧩 **Azure DevOps Core Services**

Azure DevOps provides **5 main services**:

---

## 1️⃣ **Azure Boards (Plan)**

### **What is Azure Boards?**

Work tracking system for:

* Planning work
* Tracking progress
* Agile project management

### **Key Concepts**

* Work Items: Epic, Feature, User Story, Bug, Task
* Boards (Kanban)
* Sprints
* Backlogs

### **Practical Use**

```
Boards → Work Items → New Item → Bug / User Story
```

📌 Used by PMs, Devs, Testers

---

## 2️⃣ **Azure Repos (Code)**

### **What is Azure Repos?**

Git-based **source code repository**.

### **Features**

* Git repositories
* Branching strategies
* Pull Requests (PR)
* Code reviews
* Access control

### **Practical Use**

```
Repos → Files → Clone Repository
```

📌 Supports **Git & TFVC**

---

## 3️⃣ **Azure Pipelines (Build & Release)**

### **What is Azure Pipelines?**

CI/CD service to **build, test, and deploy code automatically**.

---

### 🔁 **CI (Continuous Integration)**

* Triggered on code commit
* Build + test code
* Produces artifacts

### 🚀 **CD (Continuous Deployment)**

* Deploys artifacts to environments
* Dev → Test → Prod

---

### **Pipeline Types**

* **Classic pipelines** (UI-based)
* **YAML pipelines** (recommended)

### **Example YAML**

```yaml
trigger:
- main

steps:
- script: echo Hello Azure DevOps
```

---

### **Agents**

* **Microsoft-hosted agents**
* **Self-hosted agents**

---

## 4️⃣ **Azure Artifacts (Packages)**

### **What is Azure Artifacts?**

Package management service.

### **Supports**

* npm
* NuGet
* Maven
* Python

### **Use Case**

* Share libraries
* Version packages
* Dependency management

---

## 5️⃣ **Azure Test Plans (Test)**

### **What is Azure Test Plans?**

Testing management tool for:

* Manual testing
* Exploratory testing
* Test case tracking

📌 Mostly used in enterprise projects.

---

## 🔐 **Security in Azure DevOps**

* Integrated with **Microsoft Entra ID**
* RBAC at:

  * Organization
  * Project
  * Repo
* Secure pipelines
* Secrets stored in **Variable Groups / Key Vault**

---

## 🔄 **Azure DevOps Workflow (Simple)**

```
Azure Boards → Azure Repos → Azure Pipelines → Azure Artifacts → Deploy
```

---

## 🧪 **Practical Example (Simple Project)**

1. Create Azure DevOps Organization
2. Create Project
3. Push code to Azure Repos
4. Create Pipeline (YAML)
5. Build code
6. Deploy to Azure App Service / VM

---

## ⚠️ **AZ-900 Exam Traps**

* Azure DevOps ≠ Azure Portal
* Azure DevOps ≠ GitHub (but integrates with it)
* Pipelines ≠ only release (includes build)
* YAML pipelines are preferred
* Boards ≠ CI/CD tool

---

## 🧠 **One-Line Revision (Must Remember)**

* Boards → Plan
* Repos → Code
* Pipelines → Build & Deploy
* Artifacts → Packages
* Test Plans → Testing

---

## 🎯 **AZ-900 Sample Exam Q**

**Q:** Which Azure DevOps service is used for CI/CD?
**A:** **Azure Pipelines**

---
