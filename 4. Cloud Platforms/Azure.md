---
title: "• Azure"
parent: "4. Cloud Platforms"
nav_order: 2
has_children: true
---

# 🚀 Azure DevOps Roadmap

**Focus:** Azure + DevOps + Production + Interview Preparation

- [1. Azure Fundamentals](#azure-fundamentals)
- [2. Azure Compute](#azure-compute)
- [3. Azure Networking](#azure-networking)
- [4. Azure Storage & Database](#azure-storage-database)
- [5. Azure Identity & Security](#azure-identity-security)
- [6. Azure DevOps & CI/CD](#azure-devops)
- [7. Infrastructure as Code](#infrastructure-as-code)
- [8. Azure Kubernetes Service (AKS)](#azure-kubernetes)
- [9. Monitoring & Observability](#azure-monitoring)
- [10. HA, DR, Backup & Scaling](#azure-ha-dr)
- [11. Production Troubleshooting](#azure-troubleshooting)
- [12. Serverless](#azure-functions)
- [13. Cost & Governance](#azure-governance)
- [14. Real-World DevOps Projects](#real-world-projects)
- [15. Optional Azure Services](#azure-optional)

---

<a id="azure-fundamentals"></a>

## 🧩 1. Azure Fundamentals

**Cloud:** IaaS, PaaS, SaaS, Public/Private/Hybrid, Scalability, Elasticity, HA, Regions, Availability Zones, Resource Groups

**Core:** Subscription, Management Groups, Azure Portal, CLI, PowerShell, Azure Resource Manager

**Services:** VM, VMSS, App Service, Functions, VNet, Storage, Azure SQL, ACR, AKS

**Governance:** RBAC, Azure Policy, Tags, Locks, Cost Management

---

<a id="azure-compute"></a>

## 🧩 2. Azure Compute

**VM:** VM Types, Images, Sizes, Disks, SSH/RDP, Extensions, Custom Script, Managed Disks

**Availability:** Availability Sets, Availability Zones, VMSS, Autoscaling

**App Service:** Web App, App Service Plan, Deployment Slots, Scaling, Custom Domain, SSL, CI/CD

**Containers:** ACI, ACR, Container Images, Registry Authentication, Image Scanning

---

<a id="azure-networking"></a>

## 🧩 3. Azure Networking ⭐

**VNet:** CIDR, Public/Private IP, Subnets, NSG, Route Tables, DNS

**Connectivity:** VNet Peering, Private Endpoint, Service Endpoint, VPN Gateway, ExpressRoute

**Traffic:** Load Balancer, Application Gateway, WAF, Traffic Manager, Front Door

**Security:** Azure Firewall, Bastion, DDoS Protection

**Troubleshooting:** NSG Rules, Routes, DNS, Ports, `curl`, `nslookup`, `traceroute`

---

<a id="azure-storage-database"></a>

## 🧩 4. Azure Storage & Database

**Storage:** Blob, File, Queue, Table, Storage Account, Access Tiers, Lifecycle Management

**Redundancy:** LRS, ZRS, GRS, GZRS

**Security:** Encryption, SAS, Access Keys, RBAC, Private Endpoint

**Database:** Azure SQL, PostgreSQL, MySQL, Cosmos DB, Azure Cache for Redis

**DevOps:** Backup, Restore, Migration, Connection Strings, Secret Management

---

<a id="azure-identity-security"></a>

## 🧩 5. Azure Identity & Security ⭐

**Identity:** Microsoft Entra ID, Users, Groups, RBAC, Service Principal, Managed Identity

**Security:** Key Vault, Secrets, Certificates, Keys, Rotation, Conditional Access

**DevSecOps:** Defender for Cloud, Vulnerability Scanning, SAST, Dependency Scan, Container Scan, Trivy, SonarQube

**Best Practice:** Least Privilege, No Hardcoded Secrets, Managed Identity, Private Access

---

<a id="azure-devops"></a>

## 🧩 6. Azure DevOps & CI/CD ⭐⭐⭐⭐⭐

**Repos:** Git, Branching Strategy, PR, Branch Policies, Merge, Rebase, Tags

**Pipelines:** YAML, Stages, Jobs, Steps, Variables, Parameters, Conditions, Templates

**CI:** Build, Unit Test, SonarQube, Docker Build, ACR Push

**CD:** Dev → QA → UAT → Prod, Approvals, Environments, Gates, Rollback

**Agents:** Microsoft-hosted, Self-hosted, Agent Pools, Permissions

**Artifacts:** Azure Artifacts, Maven, npm, NuGet

**Deployment:** VM, App Service, ACR, AKS, Helm

**Strategies:** Rolling, Blue/Green, Canary

**Troubleshooting:** Pipeline Failure, Agent Issue, Authentication, Deployment Failure

---

<a id="infrastructure-as-code"></a>

## 🧩 7. Infrastructure as Code ⭐⭐⭐⭐⭐

**Terraform:** Provider, Resources, Variables, Outputs, Locals, Modules, tfvars

**State:** State File, Remote Backend, Azure Storage Backend, State Locking

**Workflow:** `init`, `validate`, `plan`, `apply`, `destroy`

**Azure:** VNet, VM, NSG, ACR, AKS, Key Vault, Storage

**Best Practices:** Modules, Remote State, Secrets, Git, Environment Separation

**Other:** ARM Templates, Bicep — Basic Awareness

---

<a id="azure-kubernetes"></a>

## 🧩 8. Azure Kubernetes Service (AKS) ⭐⭐⭐⭐⭐

**Architecture:** Control Plane, Node Pools, System/User Pools, VM Sizes

**Networking:** Azure CNI, Kubenet, Service, Ingress, DNS, Network Policies

**Workloads:** Pods, Deployments, ReplicaSets, StatefulSets, ConfigMaps, Secrets

**Storage:** PV, PVC, StorageClass, Azure Disk, Azure Files

**Scaling:** HPA, Cluster Autoscaler, Node Scaling

**Deployment:** ACR → AKS, Helm, Azure Pipelines, GitHub Actions, GitLab CI/CD

**Ingress:** NGINX, Application Gateway, TLS/SSL, Certificates

**Security:** RBAC, Workload Identity, Key Vault CSI Driver, Network Policies

**Production:** Rolling Update, Rollback, PDB, Affinity, Taints/Tolerations

**Troubleshooting:** Pending, CrashLoopBackOff, OOMKilled, ImagePullBackOff, 404/502/503, Node Issues

---

<a id="azure-monitoring"></a>

## 🧩 9. Monitoring & Observability ⭐⭐⭐⭐⭐

**Azure Monitor:** Metrics, Alerts, Action Groups, Dashboards

**Log Analytics:** Workspace, KQL, Queries, Logs, Workbooks

**Application Insights:** Application Logs, Requests, Dependencies, Tracing, Availability

**Infrastructure:** VM Insights, AKS Container Insights, Network Monitoring

**Cloud-Native:** Azure Managed Prometheus, Grafana, OpenTelemetry

**Alerting:** CPU, Memory, Disk, Pod Restart, HTTP 5xx, Application Errors

---

<a id="azure-ha-dr"></a>

## 🧩 10. HA, DR, Backup & Scaling ⭐⭐⭐⭐⭐

**HA:** Availability Zones, VMSS, Load Balancer, Application Gateway

**Backup:** Azure Backup, Recovery Services Vault, VM Backup, Storage Backup

**DR:** Azure Site Recovery, Region Failover, Replication

**AKS:** Cluster Backup, Persistent Data Backup, ACR Image Retention

**Concepts:** RPO, RTO, Backup Policy, Restore Testing, DR Runbook

**Scaling:** VMSS Autoscale, App Service Autoscale, AKS HPA, Cluster Autoscaler

---

<a id="azure-troubleshooting"></a>

## 🧩 11. Production Troubleshooting ⭐⭐⭐⭐⭐

**VM:** CPU, Memory, Disk, Process, Service, SSH/RDP, Boot Issues

**Network:** DNS, NSG, Routes, Ports, Load Balancer, App Gateway, Private Endpoint

**Application:** 4xx, 5xx, Timeout, SSL, Connection Refused, Dependency Failure

**AKS:** Pod, Node, Deployment, Service, Ingress, DNS, Storage, Scaling

**Pipeline:** Build Failure, Agent, Credentials, ACR Login, Deployment, Rollback

**Method:** Monitor → Logs → Metrics → Network → Application → Root Cause → Fix → Prevent

---

<a id="azure-functions"></a>

## 🧩 12. Serverless

**Azure Functions:** HTTP, Timer, Blob, Queue, Event Triggers, Bindings, Durable Functions

**DevOps:** CI/CD, Configuration, Managed Identity, Key Vault, Monitoring

**Use Cases:** Automation, Scheduled Jobs, Notifications, Event Processing

---

<a id="azure-governance"></a>

## 🧩 13. Cost & Governance

**Governance:** Management Groups, Subscriptions, Resource Groups, Azure Policy, RBAC, Locks, Tags

**Cost:** Cost Management, Budgets, Alerts, Right-Sizing, Autoscaling, Reserved Instances, Savings Plans

**Best Practices:** Remove Unused Resources, Storage Lifecycle, VM Optimization, AKS Cost Optimization

---

<a id="real-world-projects"></a>

## 🧩 14. Real-World DevOps Projects ⭐⭐⭐⭐⭐

### 🔥 NexCart — End-to-End Azure DevOps

**GitLab → CI/CD → Docker → ACR → AKS → Helm → Key Vault → Managed Identity → Terraform → Monitoring → Backup → HA/DR**

**Project 1:** Azure DevOps CI/CD → App Service  
**Project 2:** Docker → ACR → AKS → Helm  
**Project 3:** Terraform → Complete Azure Infrastructure  
**Project 4:** Key Vault → Managed Identity → Secret Rotation  
**Project 5:** Azure Monitor → Log Analytics → Prometheus/Grafana  
**Project 6:** AKS → Backup → HA → DR  
**Project 7:** Production Incident → Troubleshooting → RCA

---

<a id="azure-optional"></a>

## 🧩 15. Optional Azure Services

**Data:** Data Factory, Databricks, Synapse, Data Lake

**Messaging:** Service Bus, Event Grid, Event Hubs

**Advanced:** Front Door, API Management, Logic Apps, Azure Automation

**Focus:** Understand Architecture + Use Cases, Deep Hands-on only when required.

---