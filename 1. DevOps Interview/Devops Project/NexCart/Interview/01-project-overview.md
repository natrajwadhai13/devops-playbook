---
title: "01-project-overview"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 1
---

# 🛒 NexCart — Project Overview

> **Project Type:** E-Commerce Microservices Platform  
> **Architecture:** Microservices + Containerized + Kubernetes  
> **Cloud Platform:** Microsoft Azure  
> **CI/CD:** GitLab CI/CD  
> **Container Platform:** Docker  
> **Orchestration:** Kubernetes / Azure Kubernetes Service (AKS)  
> **Package Management:** Helm  
> **Infrastructure as Code:** Terraform  
> **Container Registry:** Azure Container Registry (ACR)  
> **Secrets Management:** Azure Key Vault  
> **Observability:** Prometheus + Grafana + Loki + Alloy  
> **Security:** SonarQube + Trivy + RBAC + Managed/Workload Identity

---

## 1. Project Introduction

**NexCart** is a cloud-ready, containerized e-commerce application designed using a **microservices architecture**.

The objective of the project is to implement an end-to-end DevOps platform where application source code can move from:

```text
Developer
   ↓
GitLab
   ↓
GitLab CI/CD
   ↓
Build & Test
   ↓
Security Scan
   ↓
Docker Image
   ↓
Azure Container Registry
   ↓
Helm
   ↓
Azure Kubernetes Service
   ↓
Application
   ↓
Monitoring & Logging
```

The project is designed not only for application deployment but also for **production-style operations**, including:

- Continuous Integration
- Continuous Delivery
- Containerization
- Kubernetes orchestration
- Infrastructure as Code
- Secret management
- Identity and access management
- Application monitoring
- Centralized logging
- Security scanning
- Horizontal scaling
- Rolling deployments
- Rollback
- Certificate lifecycle management
- Credential rotation
- Kubernetes upgrades
- Incident management
- Root Cause Analysis

---

# 2. Business Objective

NexCart represents an e-commerce platform where customers can:

```text
Register / Login
       ↓
Browse Products
       ↓
Search Products
       ↓
Add Products to Cart
       ↓
Create Order
       ↓
Make Payment
       ↓
Receive Notification
       ↓
Track Order
```

The application is divided into independent services so that each business capability can be developed, deployed and scaled independently.

---

# 3. Why Microservices?

A monolithic application could contain:

```text
User
Product
Order
Payment
Notification
```

inside a single application.

NexCart separates these responsibilities.

```text
                 NexCart
                    |
       +------------+------------+
       |            |            |
       v            v            v
     User        Product       Order
    Service      Service      Service
       |            |            |
       v            v            v
   Database      Database      Database

                    |
          +---------+---------+
          |                   |
          v                   v
      Payment            Notification
      Service               Service
          |                   |
          v                   v
       Database             Redis
```

### Benefits

- Independent deployment
- Independent scaling
- Fault isolation
- Technology flexibility
- Smaller codebases
- Faster development
- Easier troubleshooting
- Better ownership boundaries

---

# 4. NexCart Microservices

The platform contains the following major components.

| Component | Technology | Responsibility |
|---|---|---|
| Frontend | React + Nginx | Customer UI |
| API Gateway | API Gateway | Central API routing |
| User Service | Java / Spring Boot | User management |
| Product Service | Node.js / Express | Product management |
| Order Service | Java / Spring Boot | Order processing |
| Payment Service | Python / FastAPI | Payment processing |
| Notification Service | Python | Notifications |
| PostgreSQL | Database | User / Order data |
| MySQL | Database | Product data |
| MongoDB | Database | Payment-related data |
| Redis | Cache / Messaging | Notification / temporary data |

---

# 5. High-Level Architecture

```text
                         INTERNET
                            |
                            v
                       Azure DNS
                            |
                            v
                  Azure Load Balancer
                            |
                            v
                    AKS Ingress
                            |
                            v
                      API Gateway
                            |
        +-------------------+-------------------+
        |                   |                   |
        v                   v                   v
   User Service       Product Service      Order Service
        |                   |                   |
        v                   v                   v
   PostgreSQL              MySQL             PostgreSQL

                            |
                  +---------+---------+
                  |                   |
                  v                   v
            Payment Service     Notification Service
                  |                   |
                  v                   v
               MongoDB              Redis


                    DEVOPS PLATFORM
                         |
       +-----------------+------------------+
       |                 |                  |
       v                 v                  v
     GitLab          GitLab Runner       Terraform
       |                 |                  |
       +-----------------+------------------+
                         |
                         v
                    Docker Build
                         |
                         v
                Azure Container Registry
                         |
                         v
                       Helm
                         |
                         v
                  Azure Kubernetes
                      Service
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
       Pods           Services       Ingress


                   SECURITY
                       |
          +------------+------------+
          |                         |
          v                         v
      SonarQube                   Trivy
          |
          v
     Code Quality


                   SECRETS
                       |
                       v
                 Azure Key Vault
                       |
                       v
            Managed / Workload Identity


                 OBSERVABILITY
                       |
             +---------+---------+
             |         |         |
             v         v         v
          Alloy      Metrics     Logs
             |         |         |
             v         v         v
           Loki   Prometheus     |
             |         |         |
             +---------+---------+
                       |
                       v
                    Grafana
```

---

# 6. End-to-End DevOps Flow

The complete deployment lifecycle is:

```text
Developer
    |
    | Git Push
    v
GitLab Repository
    |
    v
GitLab CI/CD
    |
    +---- Validate
    |
    +---- Unit Test
    |
    +---- Build
    |
    +---- SonarQube
    |
    +---- Dependency Scan
    |
    +---- Trivy Scan
    |
    +---- Docker Build
    |
    +---- Docker Image
    |
    v
Azure Container Registry
    |
    v
Helm
    |
    v
AKS
    |
    v
Kubernetes Deployment
    |
    +---- Pods
    +---- Services
    +---- ConfigMaps
    +---- Secrets
    +---- HPA
    +---- Ingress
    |
    v
Application
    |
    v
Monitoring / Logging
```

---

# 7. Source Code Management

GitLab is used as the source code management platform.

Typical repository structure:

```text
NexCart/
│
├── services/
│   ├── user-service/
│   ├── product-service/
│   ├── order-service/
│   ├── payment-service/
│   ├── notification-service/
│   ├── api-gateway/
│   └── frontend/
│
├── docker/
│
├── helm/
│   └── nexcart/
│
├── k8s/
│
├── terraform/
│
├── scripts/
│
├── docs/
│
└── .gitlab-ci.yml
```

The Git repository becomes the **single source of truth** for:

- Application code
- Dockerfiles
- Kubernetes manifests
- Helm charts
- Terraform configuration
- CI/CD configuration
- Documentation

---

# 8. CI/CD Architecture

GitLab CI/CD automates the application delivery process.

```text
Git Push
   |
   v
Pipeline Trigger
   |
   v
Validation
   |
   v
Unit Tests
   |
   v
Application Build
   |
   v
Code Quality
   |
   v
Security Scan
   |
   v
Docker Build
   |
   v
Container Scan
   |
   v
Push Image to ACR
   |
   v
Helm Validation
   |
   v
Deploy to AKS
   |
   v
Smoke Test
```

---

# 9. Containerization

Each application component is packaged as a Docker image.

Example:

```text
product-service
      |
      v
Dockerfile
      |
      v
Docker Build
      |
      v
product-service:1.0.25
```

The image is then pushed to ACR:

```text
Azure Container Registry
        |
        +-- user-service
        +-- product-service
        +-- order-service
        +-- payment-service
        +-- notification-service
        +-- api-gateway
        +-- frontend
```

---

# 10. Image Versioning

Production deployments should avoid relying on:

```text
latest
```

Instead, images should use immutable versions.

Examples:

```text
product-service:1.0.25
```

or:

```text
product-service:<Git-Commit-SHA>
```

Example:

```text
nexcartaacr.azurecr.io/product-service:8f91c2a
```

This allows us to trace:

```text
Git Commit
     ↓
Pipeline
     ↓
Docker Image
     ↓
Helm Release
     ↓
Kubernetes Deployment
```

This is important for production troubleshooting and rollback.

---

# 11. Kubernetes / AKS

Azure Kubernetes Service is used to run the containerized applications.

High-level structure:

```text
AKS Cluster
│
├── Node Pool
│
├── Namespace: nexcart
│
├── Frontend Deployment
│
├── API Gateway Deployment
│
├── User Service Deployment
│
├── Product Service Deployment
│
├── Order Service Deployment
│
├── Payment Service Deployment
│
├── Notification Service Deployment
│
├── Services
│
├── ConfigMaps
│
├── Secrets
│
├── HPA
│
└── Ingress
```

---

# 12. Kubernetes Namespace

NexCart workloads are logically isolated using a namespace.

```bash
kubectl create namespace nexcart
```

Check:

```bash
kubectl get namespaces
```

Check workloads:

```bash
kubectl get all -n nexcart
```

---

# 13. Kubernetes Deployment Strategy

Applications use Kubernetes Deployments.

Example:

```text
Deployment
    |
    +---- Pod
    +---- Pod
    +---- Pod
```

For a production service:

```yaml
replicas: 3
```

If one Pod fails:

```text
Pod 1 ❌
Pod 2 ✅
Pod 3 ✅
```

Kubernetes creates a replacement Pod.

This provides better:

- Availability
- Fault tolerance
- Rolling updates
- Scalability

---

# 14. Kubernetes Services

Pods are temporary and their IP addresses can change.

Therefore applications communicate through Kubernetes Services.

```text
Client
   |
   v
Kubernetes Service
   |
   +---- Pod
   +---- Pod
   +---- Pod
```

Example:

```text
product-service.nexcart.svc.cluster.local
```

This provides stable service discovery.

---

# 15. Ingress

External HTTP/HTTPS traffic enters the cluster through the ingress layer.

```text
Internet
   |
   v
Load Balancer
   |
   v
Ingress Controller
   |
   +---- /api/users
   +---- /api/products
   +---- /api/orders
   +---- /api/payments
```

Ingress is responsible for:

- HTTP routing
- HTTPS/TLS termination
- Host-based routing
- Path-based routing
- External application exposure

---

# 16. Helm

Helm is used to package Kubernetes resources.

Instead of manually maintaining multiple YAML files, Helm provides reusable templates.

Example:

```text
helm/
└── nexcart/
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
        ├── deployment.yaml
        ├── service.yaml
        ├── ingress.yaml
        ├── configmap.yaml
        ├── secret.yaml
        └── hpa.yaml
```

Deployment:

```bash
helm upgrade --install nexcart ./helm/nexcart \
  --namespace nexcart \
  --create-namespace
```

---

# 17. Environment Management

The same Helm chart can be used for multiple environments.

Example:

```text
values-dev.yaml
values-test.yaml
values-prod.yaml
```

Development:

```text
replicas: 1
```

Production:

```text
replicas: 3
```

This prevents duplication of Kubernetes manifests.

---

# 18. Azure Container Registry

ACR stores the container images used by AKS.

Flow:

```text
GitLab CI/CD
      |
      v
Docker Build
      |
      v
ACR
      |
      v
AKS
```

Example:

```bash
az acr repository list \
  --name nexcartacr \
  --output table
```

---

# 19. Azure Key Vault

Sensitive information should not be stored directly in source code.

Examples:

```text
Database credentials
API keys
Certificates
Tokens
Connection strings
Service credentials
```

These should be managed through Azure Key Vault.

Architecture:

```text
AKS Workload
      |
      v
Managed / Workload Identity
      |
      v
Azure RBAC
      |
      v
Azure Key Vault
```

---

# 20. Identity and Service Accounts

There are two concepts that should not be confused.

### Kubernetes ServiceAccount

Used primarily for Kubernetes workload identity and Kubernetes API authorization.

```yaml
serviceAccountName: nexcart-sa
```

### Azure Managed / Workload Identity

Used for secure access from workloads to Azure resources.

Example:

```text
Pod
 ↓
Workload Identity
 ↓
Azure RBAC
 ↓
Key Vault
```

This reduces dependency on long-lived credentials.

---

# 21. Infrastructure as Code

Terraform is used to define Azure infrastructure.

Typical components:

```text
Resource Group
      |
      +---- VNet
      |
      +---- Subnets
      |
      +---- ACR
      |
      +---- AKS
      |
      +---- Key Vault
      |
      +---- Monitoring
```

Terraform commands:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

---

# 22. Security

Security is integrated into the delivery lifecycle.

```text
Developer
   |
   v
GitLab
   |
   v
SAST / Code Quality
   |
   v
Dependency Scan
   |
   v
Container Scan
   |
   v
Infrastructure Scan
   |
   v
Deployment
```

Typical tools:

```text
SonarQube
Trivy
Azure RBAC
Key Vault
Managed Identity
Kubernetes RBAC
TLS
```

---

# 23. Observability

The platform requires visibility into:

```text
Application
Infrastructure
Kubernetes
Network
Database
```

Observability architecture:

```text
Applications
     |
     v
Kubernetes
     |
     v
Alloy
   /   \
  /     \
 v       v
Logs   Metrics
 |        |
 v        v
Loki   Prometheus
  \       /
   \     /
    v   v
    Grafana
```

---

# 24. Monitoring Areas

Important metrics include:

### Application

```text
Request rate
Error rate
Latency
HTTP 4xx
HTTP 5xx
```

### Kubernetes

```text
Pod restarts
CPU
Memory
Node health
Pod availability
Deployment status
```

### Infrastructure

```text
CPU
Memory
Disk
Network
Node capacity
```

### Business

```text
Orders
Payments
Failed transactions
Successful transactions
```

---

# 25. Production Deployment

Production deployments should be controlled.

Typical flow:

```text
Developer
   ↓
Git Commit
   ↓
Pull/Merge Request
   ↓
CI Validation
   ↓
Security Checks
   ↓
Build Image
   ↓
Push to ACR
   ↓
Deploy through Helm
   ↓
Smoke Test
   ↓
Monitor
```

For critical releases, approval gates can be added before production deployment.

---

# 26. Rollback Strategy

If the new release introduces a problem:

```text
Version 1.0.24
      ↓
Working
      ↓
Version 1.0.25
      ↓
Failure
```

Kubernetes rollout history can be checked:

```bash
kubectl rollout history deployment/product-service \
  -n nexcart
```

Rollback:

```bash
kubectl rollout undo deployment/product-service \
  -n nexcart
```

Helm rollback:

```bash
helm history nexcart -n nexcart
```

```bash
helm rollback nexcart <REVISION> \
  -n nexcart
```

The objective is:

> Restore service first, then perform detailed RCA.

---

# 27. Production Troubleshooting Methodology

I use a structured troubleshooting approach rather than randomly restarting components.

```text
1. Detect
2. Identify affected service
3. Determine blast radius
4. Check recent changes
5. Check application health
6. Check Pods
7. Check Services
8. Check Ingress
9. Check networking
10. Check dependencies
11. Check database
12. Mitigate
13. Restore service
14. Perform RCA
15. Implement preventive action
```

---

# 28. Common Production Problems

Over the lifecycle of a platform like NexCart, common issues include:

### Kubernetes

```text
CrashLoopBackOff
ImagePullBackOff
OOMKilled
Pending Pods
Node NotReady
Readiness failures
Liveness failures
```

### Application

```text
HTTP 500
HTTP 502
HTTP 503
Slow response
Database connection failure
Configuration mismatch
```

### Networking

```text
DNS failure
Ingress routing issue
Wrong targetPort
Network policy
NSG
Load balancer
```

### Security

```text
Expired credentials
Certificate expiry
Container CVE
RBAC issue
Secret access failure
```

### Infrastructure

```text
Node capacity
Disk pressure
Memory pressure
Scaling
Azure resource availability
```

---

# 29. Certificate Lifecycle Management

Certificates are not a one-time deployment task.

Lifecycle:

```text
Certificate Issued
       ↓
Configured
       ↓
Monitored
       ↓
Renewed
       ↓
Validated
       ↓
Old Certificate Retired
```

Certificate expiry should be proactively monitored.

A production platform should not wait for users to report:

```text
"HTTPS is not working."
```

---

# 30. Credential Rotation

Credentials should have a defined lifecycle.

```text
Credential Created
       ↓
Stored Securely
       ↓
Used by Application
       ↓
Rotation Due
       ↓
New Credential Created
       ↓
Application Updated
       ↓
Validation
       ↓
Old Credential Revoked
```

Where possible, use:

```text
Managed Identity
Workload Identity
Short-lived credentials
```

instead of long-lived passwords.

---

# 31. Kubernetes Upgrade Management

Kubernetes is a continuously evolving platform.

Before upgrading AKS:

```text
Application compatibility
        ↓
Kubernetes API compatibility
        ↓
Ingress compatibility
        ↓
CSI/Storage compatibility
        ↓
Monitoring agent compatibility
        ↓
Security agent compatibility
        ↓
Non-production upgrade
        ↓
Testing
        ↓
Production upgrade
```

Never treat a cluster upgrade as simply:

```bash
az aks upgrade
```

The surrounding ecosystem must also be validated.

---

# 32. Container Vulnerability Management

Suppose Trivy detects:

```text
Critical CVE
```

Process:

```text
CVE Detected
     ↓
Identify Package
     ↓
Upgrade Dependency/Base Image
     ↓
Build New Image
     ↓
Run Tests
     ↓
Trivy Scan
     ↓
Push New Image
     ↓
Deploy
```

---

# 33. Day-2 Operations

The DevOps responsibility does not end after deployment.

Day-2 responsibilities include:

```text
Monitoring
Incident response
Capacity planning
Certificate renewal
Credential rotation
Kubernetes upgrades
Node maintenance
Image updates
Security patching
Backup validation
Cost optimization
Performance tuning
RCA
Disaster recovery
```

This is an important distinction between:

```text
Deployment Engineer
```

and:

```text
DevOps / Platform Engineer
```

---

# 34. Disaster Recovery

A production architecture should consider:

```text
Application failure
Pod failure
Node failure
Availability zone failure
Database failure
Region failure
```

Recovery planning includes:

```text
Backups
Replication
Infrastructure as Code
Database recovery
Container registry availability
Configuration recovery
Secrets recovery
DNS recovery
```

Important metrics:

### RPO

**Recovery Point Objective**

How much data can potentially be lost.

### RTO

**Recovery Time Objective**

How quickly the service must be restored.

---

# 35. High Availability

NexCart should avoid single points of failure.

Example:

```text
Ingress
   |
   +---- Pod
   +---- Pod
   +---- Pod
```

Applications should use:

```text
Multiple replicas
Health probes
Autoscaling
Rolling deployment
Multiple nodes
```

For critical production infrastructure, Azure availability-zone-aware architecture should be considered where supported and justified.

---

# 36. Scalability

There are two primary scaling approaches.

### Horizontal Scaling

```text
3 Pods
 ↓
5 Pods
 ↓
10 Pods
```

Using:

```text
HPA
```

### Vertical Scaling

```text
500Mi Memory
 ↓
1Gi Memory
```

Both approaches should be based on actual workload characteristics.

---

# 37. Performance Troubleshooting

When application response time increases:

```text
User
 ↓
Ingress
 ↓
API Gateway
 ↓
Microservice
 ↓
Database
```

Measure latency at each layer.

Check:

```text
CPU
Memory
GC
Thread pools
Connection pools
Database queries
Network latency
External APIs
Pod restarts
```

Do not automatically increase Kubernetes resources without understanding the bottleneck.

---

# 38. Cost Optimization

Azure cost should also be considered.

Potential optimization areas:

```text
AKS node sizing
Node autoscaling
Unused resources
ACR storage
Log retention
Database sizing
Non-production shutdown
Reserved capacity
Right-sizing
```

For example:

```text
Development environment
       ↓
Schedule shutdown outside working hours
       ↓
Reduce Azure cost
```

---

# 39. Project Success Criteria

The project is considered successful when:

- Application is containerized
- Code is managed through GitLab
- CI/CD is automated
- Docker images are versioned
- Images are stored in ACR
- Applications run on AKS
- Helm manages deployments
- Secrets are securely managed
- Infrastructure is reproducible
- Security scanning is integrated
- Monitoring is available
- Logs are centralized
- Deployments are traceable
- Rollbacks are possible
- Production incidents can be diagnosed
- Certificates and credentials have lifecycle processes
- Platform can be scaled and maintained

---

# 40. Key DevOps Principles Used

```text
Infrastructure as Code
        +
Automation
        +
Immutable Artifacts
        +
Version Control
        +
Least Privilege
        +
Observability
        +
Continuous Delivery
        +
Fault Isolation
        +
Automated Recovery
        +
Production Readiness
```

---

# 41. Interview Summary

When asked:

> **"Explain your NexCart project."**

The answer should follow this structure:

```text
1. Business purpose
        ↓
2. Microservices architecture
        ↓
3. Source control
        ↓
4. CI/CD
        ↓
5. Docker
        ↓
6. ACR
        ↓
7. AKS
        ↓
8. Kubernetes
        ↓
9. Helm
        ↓
10. Key Vault / Identity
        ↓
11. Terraform
        ↓
12. Security
        ↓
13. Monitoring
        ↓
14. Troubleshooting
        ↓
15. Production operations
```

The interviewer should come away understanding that you know **why the architecture exists, how it is implemented, how it fails, how you recover it, and how you operate it over several years**.

---

# 42. Senior-Level Interview Positioning

The most important mindset for this project is:

> **I am not just deploying an application. I am designing and operating a reliable application delivery platform.**

Therefore, every technology should have a reason.

```text
GitLab
→ Source control + CI/CD

Docker
→ Consistent application packaging

ACR
→ Private container image registry

AKS
→ Container orchestration

Kubernetes
→ Scheduling, service discovery, scaling and self-healing

Helm
→ Reusable and versioned Kubernetes deployments

Terraform
→ Reproducible Azure infrastructure

Key Vault
→ Secure secret management

Managed/Workload Identity
→ Reduce long-lived credentials

SonarQube
→ Code quality

Trivy
→ Container/dependency security

Prometheus
→ Metrics

Loki
→ Logs

Grafana
→ Visualization and alerting

Alloy
→ Telemetry collection
```

---

# 43. One-Line Architecture

For quick interview revision:

```text
GitLab → GitLab CI/CD → Docker → ACR → Helm → AKS → Ingress → Microservices → Databases, with Terraform for Azure infrastructure, Key Vault/Identity for security, and Prometheus/Grafana/Loki/Alloy for observability.
```

