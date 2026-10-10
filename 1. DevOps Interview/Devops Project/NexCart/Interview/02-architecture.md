---
title: "02-architecture"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 2
---

# 🏗️ NexCart — Architecture

> **Document:** 02-architecture.md  
> **Project:** NexCart E-Commerce Platform  
> **Architecture:** Microservices + Containerized + Kubernetes  
> **Cloud:** Microsoft Azure  
> **CI/CD:** GitLab CI/CD  
> **Orchestration:** Azure Kubernetes Service (AKS)

---

# 1. Architecture Overview

NexCart is designed as a **cloud-native microservices application** deployed on Azure Kubernetes Service (AKS).

The architecture separates the platform into multiple layers:

```text
┌───────────────────────────────────────────────────────────────┐
│                         USERS / CLIENTS                       │
└──────────────────────────────┬────────────────────────────────┘
                               │
                               ▼
                         Azure DNS
                               │
                               ▼
                    Azure Load Balancer
                               │
                               ▼
                       AKS Ingress
                               │
                               ▼
                        API Gateway
                               │
             ┌─────────────────┼─────────────────┐
             │                 │                 │
             ▼                 ▼                 ▼
       User Service      Product Service     Order Service
             │                 │                 │
             ▼                 ▼                 ▼
        PostgreSQL            MySQL            PostgreSQL

                               │
                    ┌──────────┴──────────┐
                    │                     │
                    ▼                     ▼
             Payment Service       Notification Service
                    │                     │
                    ▼                     ▼
                 MongoDB                Redis
```

The DevOps platform surrounds the application:

```text
Developer
    │
    ▼
 GitLab
    │
    ▼
GitLab CI/CD
    │
    ├── Test
    ├── Build
    ├── SonarQube
    ├── Security Scan
    ├── Docker Build
    └── Trivy
    │
    ▼
Azure Container Registry
    │
    ▼
   Helm
    │
    ▼
   AKS
```

Infrastructure and security:

```text
Terraform
    │
    ├── Resource Group
    ├── VNet
    ├── Subnets
    ├── ACR
    ├── AKS
    ├── Key Vault
    └── Monitoring
```

Security:

```text
AKS Workload
      │
      ▼
Managed / Workload Identity
      │
      ▼
Azure RBAC
      │
      ▼
Azure Key Vault
```

Observability:

```text
AKS Workloads
      │
      ▼
    Alloy
    /   \
   /     \
Logs    Metrics
 |         |
 ▼         ▼
Loki    Prometheus
  \        /
   \      /
    ▼    ▼
    Grafana
```

---

# 2. Complete End-to-End Architecture

```text
                              INTERNET
                                  │
                                  ▼
                            ┌───────────┐
                            │ Azure DNS │
                            └─────┬─────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │ Azure Load Balancer │
                       └──────────┬──────────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │  AKS Ingress        │
                       │  HTTP / HTTPS       │
                       └──────────┬──────────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │    API Gateway      │
                       └──────────┬──────────┘
                                  │
             ┌────────────────────┼────────────────────┐
             │                    │                    │
             ▼                    ▼                    ▼
      ┌────────────┐       ┌────────────┐       ┌────────────┐
      │User Service│       │  Product   │       │   Order    │
      │            │       │  Service   │       │  Service   │
      └─────┬──────┘       └─────┬──────┘       └─────┬──────┘
            │                    │                    │
            ▼                    ▼                    ▼
       PostgreSQL              MySQL              PostgreSQL


                            API Gateway
                                 │
                    ┌────────────┴────────────┐
                    │                         │
                    ▼                         ▼
             Payment Service          Notification Service
                    │                         │
                    ▼                         ▼
                 MongoDB                   Redis
```

---

# 3. Application Layer

The application layer contains the business services.

```text
Application
│
├── Frontend
│
├── API Gateway
│
├── User Service
│
├── Product Service
│
├── Order Service
│
├── Payment Service
│
└── Notification Service
```

Each service is independently:

```text
Developed
   ↓
Tested
   ↓
Built
   ↓
Containerized
   ↓
Versioned
   ↓
Deployed
   ↓
Monitored
```

---

# 4. Frontend Architecture

The frontend is implemented using React.

Production flow:

```text
React Source
     │
     ▼
npm build
     │
     ▼
Static Files
     │
     ▼
Nginx
     │
     ▼
Docker Image
     │
     ▼
ACR
     │
     ▼
AKS
```

The browser does not directly communicate with every backend microservice.

Instead:

```text
Browser
   │
   ▼
Ingress
   │
   ▼
API Gateway
   │
   ▼
Backend Services
```

This provides a cleaner security and routing model.

---

# 5. API Gateway

The API Gateway provides a central entry point for backend APIs.

```text
Client
  │
  ▼
API Gateway
  │
  ├──── /users ───────► User Service
  │
  ├──── /products ────► Product Service
  │
  ├──── /orders ──────► Order Service
  │
  ├──── /payments ────► Payment Service
  │
  └──── /notifications ► Notification Service
```

Potential responsibilities:

- Request routing
- Authentication
- Authorization
- Rate limiting
- Request logging
- Header management
- API policies
- Centralized error handling

---

# 6. Microservice Communication

Internal service communication uses Kubernetes Service discovery.

Example:

```text
Order Service
     │
     ▼
payment-service
     │
     ▼
Payment Service
```

Instead of using Pod IP addresses:

```text
10.x.x.x
```

the application uses the Kubernetes service DNS name:

```text
payment-service.nexcart.svc.cluster.local
```

This is important because Pod IPs are ephemeral.

---

# 7. Service-to-Service Communication

Example order flow:

```text
User
 │
 ▼
Frontend
 │
 ▼
Ingress
 │
 ▼
API Gateway
 │
 ▼
Order Service
 │
 ├────► Product Service
 │
 └────► Payment Service
              │
              ▼
           MongoDB
```

After successful payment:

```text
Payment Service
      │
      ▼
Notification Service
      │
      ▼
Redis
      │
      ▼
Notification Processing
```

---

# 8. Port Mapping

The locally implemented NexCart services used the following ports.

| Service | Technology | Port |
|---|---|---:|
| Product Service | Node.js / Express | `8082` |
| Order Service | Java | `8083` |
| Payment Service | Python / FastAPI | `8084` |
| Notification Service | Python | `8085` |

The frontend and other components can use their respective container/service ports based on the deployment configuration.

> **Important:** Kubernetes Service ports and container/application ports do not necessarily have to be identical. The Service `port` and `targetPort` must be mapped correctly.

Example:

```text
Service
port: 80
   │
   ▼
targetPort: 8082
   │
   ▼
Product Service
```

---

# 9. Product Service

Product Service was implemented using:

```text
Node.js
Express
MySQL
Docker
Kubernetes
```

Local application port:

```text
8082
```

Example endpoints:

```text
GET  /health
GET  /api/products
POST /api/products
```

Example:

```text
GET /health
```

Response:

```json
{
  "service": "product-service",
  "status": "UP"
}
```

---

# 10. Order Service

Order Service manages order-related operations.

```text
Frontend
   │
   ▼
API Gateway
   │
   ▼
Order Service
   │
   ▼
PostgreSQL
```

Local application port:

```text
8083
```

Responsibilities include:

- Order creation
- Order status
- Order lookup
- Order history

---

# 11. Payment Service

Payment Service was implemented using:

```text
Python
FastAPI
MongoDB
Docker
Kubernetes
```

Local port:

```text
8084
```

Example request:

```json
{
  "order_id": "ORD123"
}
```

A real implementation should maintain a consistent API contract between:

```text
Client
   ↓
API Gateway
   ↓
Payment Service
```

---

# 12. Notification Service

Notification Service is responsible for asynchronous notification processing.

```text
Order / Payment Event
        │
        ▼
Notification Service
        │
        ▼
Redis
        │
        ▼
Notification Processing
```

Local port:

```text
8085
```

Redis can be used for:

- Queue-like processing
- Temporary state
- Caching
- Event coordination

---

# 13. Database Architecture

NexCart follows a service-oriented database approach.

```text
User Service
     │
     ▼
PostgreSQL

Product Service
     │
     ▼
MySQL

Order Service
     │
     ▼
PostgreSQL

Payment Service
     │
     ▼
MongoDB

Notification Service
     │
     ▼
Redis
```

The key principle is:

> A microservice should own its data and expose access through its API rather than allowing other services to directly manipulate its database.

This reduces coupling.

---

# 14. Why Separate Databases?

Consider:

```text
Product Service
```

should not directly modify:

```text
Order Service Database
```

Instead:

```text
Product Service
       │
       ▼
Product API
       │
       ▼
Order Service
```

Benefits:

- Loose coupling
- Independent scaling
- Independent schema evolution
- Better ownership
- Fault isolation

---

# 15. Kubernetes Architecture

The NexCart application runs inside an AKS cluster.

Conceptually:

```text
AKS Cluster
│
├── Control Plane
│
└── Node Pool
     │
     ├── Node 1
     │    ├── Frontend Pod
     │    ├── Product Pod
     │    └── Order Pod
     │
     ├── Node 2
     │    ├── User Pod
     │    ├── Payment Pod
     │    └── Notification Pod
     │
     └── Node 3
          ├── API Gateway Pod
          └── Other workloads
```

Actual scheduling is controlled by Kubernetes.

Pods should not be manually assigned to nodes unless there is a specific scheduling requirement.

---

# 16. Kubernetes Namespace

NexCart workloads are logically grouped under:

```text
nexcart
```

Example:

```bash
kubectl get pods -n nexcart
```

```bash
kubectl get svc -n nexcart
```

```bash
kubectl get ingress -n nexcart
```

```bash
kubectl get deployments -n nexcart
```

---

# 17. Kubernetes Objects

Major Kubernetes resources:

```text
Namespace
Deployment
ReplicaSet
Pod
Service
ConfigMap
Secret
Ingress
HorizontalPodAutoscaler
ServiceAccount
```

Relationship:

```text
Deployment
    │
    ▼
ReplicaSet
    │
    ▼
Pods
    │
    ▼
Service
    │
    ▼
Ingress
```

---

# 18. Deployment and ReplicaSet

Example:

```text
Deployment
replicas: 3
       │
       ▼
ReplicaSet
       │
       ├── Pod 1
       ├── Pod 2
       └── Pod 3
```

The Deployment manages the desired state.

If one Pod disappears:

```text
Pod 1 ❌
Pod 2 ✅
Pod 3 ✅
```

Kubernetes attempts to create:

```text
Pod 1-new
```

to restore:

```text
3 replicas
```

---

# 19. Kubernetes Service

A Service provides stable networking.

```text
                    Service
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
           Pod 1     Pod 2     Pod 3
```

Example:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: product-service

spec:
  selector:
    app: product-service

  ports:
    - port: 80
      targetPort: 8082

  type: ClusterIP
```

Traffic:

```text
product-service:80
        │
        ▼
      :8082
        │
        ▼
Product Pods
```

---

# 20. Ingress Architecture

External traffic:

```text
Internet
   │
   ▼
Azure Load Balancer
   │
   ▼
Ingress Controller
   │
   ├── /api/products
   │        ↓
   │   Product Service
   │
   ├── /api/orders
   │        ↓
   │   Order Service
   │
   └── /api/payments
            ↓
       Payment Service
```

---

# 21. TLS / HTTPS

Production traffic should use:

```text
HTTPS
```

Flow:

```text
Client
   │
   │ HTTPS
   ▼
Ingress
   │
   │ TLS termination
   ▼
Backend Service
```

Certificate lifecycle must be monitored.

Typical production risks:

```text
Certificate expiry
Incorrect certificate
Wrong secret
Incorrect hostname
TLS configuration mismatch
```

---

# 22. Kubernetes ConfigMap

Non-sensitive configuration can be stored in ConfigMaps.

Examples:

```text
Application environment
Service URLs
Feature flags
Non-sensitive configuration
```

Example:

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: product-config

data:
  APP_ENV: "production"
  PRODUCT_PORT: "8082"
```

---

# 23. Kubernetes Secrets

Secrets should be used for sensitive Kubernetes configuration where appropriate.

Examples:

```text
Passwords
Tokens
Credentials
```

However, Kubernetes Secret objects alone should not be treated as a complete enterprise secret-management solution.

For Azure production workloads:

```text
Azure Key Vault
       +
Managed / Workload Identity
```

is preferred where applicable.

---

# 24. Azure Key Vault Integration

Architecture:

```text
                     Azure Key Vault
                           ▲
                           │
                     Azure RBAC
                           ▲
                           │
                  Workload Identity
                           ▲
                           │
                          Pod
```

The Pod does not need a hardcoded Azure client secret.

This reduces:

- Credential leakage
- Manual secret management
- Long-lived credentials
- Rotation complexity

---

# 25. Kubernetes ServiceAccount

Example:

```yaml
apiVersion: v1
kind: ServiceAccount

metadata:
  name: nexcart-sa
  namespace: nexcart
```

Pod:

```yaml
spec:
  serviceAccountName: nexcart-sa
```

The ServiceAccount should have only the permissions required by the workload.

This follows:

```text
Least Privilege
```

---

# 26. Azure Workload Identity

For Azure resources:

```text
Pod
 │
 ▼
Kubernetes ServiceAccount
 │
 ▼
Workload Identity
 │
 ▼
Azure Identity
 │
 ▼
Azure RBAC
 │
 ▼
Key Vault / Azure Resource
```

This is preferable to putting:

```text
AZURE_CLIENT_ID
AZURE_CLIENT_SECRET
AZURE_TENANT_ID
```

as long-lived credentials inside the application configuration.

---

# 27. Azure Resource Architecture

The Azure environment can be logically represented as:

```text
Azure Subscription
│
└── Resource Group
     │
     ├── VNet
     │    ├── AKS Subnet
     │    └── Private Endpoint Subnet
     │
     ├── AKS
     │
     ├── Azure Container Registry
     │
     ├── Azure Key Vault
     │
     ├── Monitoring
     │
     └── Other Required Resources
```

---

# 28. Network Architecture

Conceptually:

```text
                         Azure VNet
                              │
             ┌────────────────┴────────────────┐
             │                                 │
             ▼                                 ▼
        AKS Subnet                       Private Resources
             │                                 │
             ▼                                 ▼
        AKS Cluster                       Key Vault
             │                            Database
             │                            Other Services
             ▼
       Ingress / Services
```

The production design should minimize unnecessary public exposure.

Preferred principle:

```text
Public Internet
      │
      ▼
Only required entry points
      │
      ▼
Private application communication
```

---

# 29. Azure Container Registry Flow

The CI/CD pipeline publishes images to ACR.

```text
GitLab
   │
   ▼
GitLab Runner
   │
   ▼
Docker Build
   │
   ▼
Security Scan
   │
   ▼
Azure Container Registry
   │
   ▼
AKS
```

Example image:

```text
nexcartaacr.azurecr.io/product-service:1.0.25
```

---

# 30. AKS Pulling Images from ACR

AKS requires authorization to pull private images.

Conceptually:

```text
AKS
 │
 ▼
Azure Identity / RBAC
 │
 ▼
ACR
 │
 ▼
Docker Image
```

The preferred approach is Azure identity-based access rather than distributing registry passwords to every workload.

---

# 31. Helm Architecture

Helm acts as the deployment packaging layer.

```text
GitLab
   │
   ▼
Helm Chart
   │
   ├── Deployment
   ├── Service
   ├── Ingress
   ├── ConfigMap
   ├── HPA
   └── ServiceAccount
   │
   ▼
AKS
```

Helm allows environment-specific values:

```text
values-dev.yaml
values-test.yaml
values-prod.yaml
```

---

# 32. Helm Release Flow

```bash
helm lint ./helm/nexcart
```

```bash
helm template nexcart ./helm/nexcart
```

```bash
helm upgrade --install nexcart ./helm/nexcart \
  --namespace nexcart \
  --create-namespace
```

Verify:

```bash
helm list -n nexcart
```

Check history:

```bash
helm history nexcart -n nexcart
```

Rollback:

```bash
helm rollback nexcart <REVISION> \
  -n nexcart
```

---

# 33. CI/CD Architecture

The pipeline can be represented as:

```text
                    GitLab Repository
                           │
                           ▼
                       Pipeline
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
        Application                  Infrastructure
             │                           │
             ▼                           ▼
          Build                       Terraform
             │
             ▼
          Testing
             │
             ▼
        SonarQube
             │
             ▼
       Dependency Scan
             │
             ▼
       Docker Build
             │
             ▼
        Trivy Scan
             │
             ▼
           ACR
             │
             ▼
           Helm
             │
             ▼
           AKS
```

---

# 34. Git Commit to Production Traceability

A major production requirement is traceability.

Example:

```text
Git Commit
8f91c2a
     │
     ▼
GitLab Pipeline #125
     │
     ▼
Docker Image
product-service:8f91c2a
     │
     ▼
ACR
     │
     ▼
Helm Release
revision 12
     │
     ▼
AKS Deployment
     │
     ▼
Production Pods
```

If an incident occurs, we can determine:

```text
Which code?
Which pipeline?
Which image?
Which deployment?
Which Helm revision?
```

This greatly improves RCA.

---

# 35. Deployment Strategy

The default deployment strategy is rolling deployment.

```text
Current:

Pod A
Pod B
Pod C


New Version:

New A
Old B
Old C

       ↓

New A
New B
Old C

       ↓

New A
New B
New C
```

The application should use appropriate:

```text
Readiness Probe
Liveness Probe
Startup Probe
```

to ensure Kubernetes sends traffic only to healthy Pods.

---

# 36. Health Checks

Example:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 8082

livenessProbe:
  httpGet:
    path: /health
    port: 8082
```

### Readiness

Determines whether the Pod should receive traffic.

### Liveness

Determines whether the application is alive.

### Startup

Useful when an application takes a long time to initialize.

---

# 37. Scaling Architecture

NexCart should support horizontal scaling.

```text
Normal Traffic
     │
     ▼
3 Pods
```

During high traffic:

```text
Traffic ↑
    │
    ▼
HPA
    │
    ▼
5 Pods
    │
    ▼
8 Pods
```

Example:

```bash
kubectl get hpa -n nexcart
```

Scaling should be based on measurable metrics rather than arbitrary replica counts.

---

# 38. Observability Architecture

```text
                         AKS
                          │
            ┌─────────────┼─────────────┐
            │             │             │
            ▼             ▼             ▼
        Application     Kubernetes    Infrastructure
            │
            ▼
          Alloy
          /   \
         /     \
        ▼       ▼
      Logs    Metrics
        │        │
        ▼        ▼
      Loki    Prometheus
        \        /
         \      /
          ▼    ▼
          Grafana
```

---

# 39. Logging Flow

```text
Application
     │
     ▼
Container stdout/stderr
     │
     ▼
Alloy
     │
     ▼
Loki
     │
     ▼
Grafana
```

Troubleshooting command:

```bash
kubectl logs <pod> -n nexcart
```

Previous container:

```bash
kubectl logs <pod> \
  -n nexcart \
  --previous
```

---

# 40. Monitoring Flow

Important metrics:

```text
CPU
Memory
Pod Restarts
Request Rate
Request Latency
HTTP Errors
Node Health
Database Health
```

Flow:

```text
Metrics
   ↓
Prometheus
   ↓
Grafana
   ↓
Dashboard
   ↓
Alert
   ↓
Incident Response
```

---

# 41. Failure Domains

A senior architecture should consider failures at multiple levels.

```text
Application
     ↓
Pod
     ↓
Node
     ↓
Availability Zone
     ↓
Azure Region
```

Example:

```text
One Pod fails
     ↓
Kubernetes recreates Pod
```

If:

```text
One Node fails
```

Kubernetes can reschedule workloads onto healthy nodes if sufficient capacity exists.

For larger production environments, zone-aware architecture should be considered.

---

# 42. Common Failure Scenarios

## Pod Failure

```text
Pod
 ↓
Crash
 ↓
Kubernetes restart
```

---

## Node Failure

```text
Node
 ↓
NotReady
 ↓
Pods affected
 ↓
Scheduler reschedules where possible
```

---

## Image Failure

```text
Deployment
 ↓
ImagePullBackOff
```

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

---

## Application Failure

```text
Pod Running
       ↓
Application Crash
       ↓
CrashLoopBackOff
```

Check:

```bash
kubectl logs <pod> -n nexcart
```

---

## Service Failure

```text
Service
   ↓
No Endpoints
```

Check:

```bash
kubectl get endpoints -n nexcart
```

Potential issue:

```text
Service selector ≠ Pod labels
```

---

# 43. 404 Troubleshooting

If users receive:

```text
HTTP 404
```

check:

```text
Ingress
 ↓
Host
 ↓
Path
 ↓
Backend Service
 ↓
Application Route
```

Commands:

```bash
kubectl get ingress -n nexcart
```

```bash
kubectl describe ingress <name> -n nexcart
```

---

# 44. 502 Troubleshooting

If users receive:

```text
HTTP 502
```

check:

```text
Ingress
   ↓
Service
   ↓
Endpoints
   ↓
Pod
   ↓
Application Port
```

Common causes:

```text
Wrong targetPort
Application not listening
No healthy endpoints
Readiness failure
Ingress configuration issue
```

---

# 45. Database Connectivity

If a microservice cannot connect to its database:

```text
Application
    ↓
DNS
    ↓
Network
    ↓
Firewall / NSG
    ↓
Database
    ↓
Credentials
    ↓
Connection Pool
```

Don't immediately assume the database is down.

Check:

```text
DNS resolution
Connectivity
Port
Credentials
TLS
Database availability
Connection limits
```

---

# 46. Production Incident Flow

The operational flow is:

```text
Alert
  ↓
Acknowledge
  ↓
Assess Impact
  ↓
Determine Blast Radius
  ↓
Check Recent Changes
  ↓
Troubleshoot
  ↓
Mitigate
  ↓
Restore Service
  ↓
Root Cause Analysis
  ↓
Permanent Fix
  ↓
Preventive Action
```

Important principle:

> **Mitigation restores service; RCA prevents recurrence.**

---

# 47. Example Production Incident

### Problem

Product API starts returning HTTP 500.

### Investigation

```bash
kubectl get pods -n nexcart
```

```bash
kubectl logs <pod> -n nexcart
```

```bash
kubectl describe pod <pod> -n nexcart
```

Check recent deployment:

```bash
kubectl rollout history deployment/product-service \
  -n nexcart
```

Suppose the issue started immediately after release `1.0.25`.

### Mitigation

Rollback:

```bash
kubectl rollout undo deployment/product-service \
  -n nexcart
```

### RCA

Root cause might be:

```text
Incorrect environment variable
```

Permanent actions:

```text
Fix Helm configuration
Add configuration validation
Add deployment smoke test
Improve CI validation
Deploy corrected version
```

---

# 48. Certificate Architecture

HTTPS certificate:

```text
Client
   │
   ▼
Ingress
   │
   ▼
TLS Certificate
   │
   ▼
Backend
```

Certificate lifecycle:

```text
Issue
 ↓
Deploy
 ↓
Monitor
 ↓
Renew
 ↓
Validate
 ↓
Retire Old Certificate
```

Production monitoring should alert before expiry.

---

# 49. Credential Rotation Architecture

```text
Existing Credential
        │
        ▼
Rotation Due
        │
        ▼
Create New Credential
        │
        ▼
Key Vault
        │
        ▼
Application Refresh
        │
        ▼
Validation
        │
        ▼
Revoke Old Credential
```

Where possible:

```text
Managed Identity
```

should be preferred over manually rotated passwords.

---

# 50. Kubernetes Upgrade Architecture

Before an AKS upgrade:

```text
Application Compatibility
          ↓
Kubernetes API Compatibility
          ↓
Helm Compatibility
          ↓
Ingress Compatibility
          ↓
CSI / Storage Compatibility
          ↓
Monitoring Compatibility
          ↓
Security Agent Compatibility
          ↓
Non-Production Upgrade
          ↓
Testing
          ↓
Production Upgrade
```

The upgrade should have:

```text
Change plan
Validation plan
Rollback/recovery plan
Communication plan
```

---

# 51. Security Boundaries

Security should be applied at multiple layers.

```text
Internet
   │
   ▼
Ingress / TLS
   │
   ▼
API Gateway
   │
   ▼
Kubernetes RBAC
   │
   ▼
Service Accounts
   │
   ▼
Workload Identity
   │
   ▼
Azure RBAC
   │
   ▼
Azure Resources
```

Security principles:

```text
Least Privilege
Defense in Depth
No Hardcoded Secrets
Private Communication
Identity-Based Access
Auditability
```

---

# 52. Infrastructure as Code Architecture

Terraform manages Azure infrastructure.

```text
GitLab
   │
   ▼
Terraform Code
   │
   ▼
terraform plan
   │
   ▼
Review / Approval
   │
   ▼
terraform apply
   │
   ▼
Azure
```

Terraform should be the source of truth for infrastructure rather than making uncontrolled manual changes through the Azure Portal.

---

# 53. Terraform Drift

Example:

Terraform:

```text
AKS node count = 3
```

Someone manually changes Azure:

```text
AKS node count = 5
```

Then:

```bash
terraform plan
```

may identify the difference.

Process:

```text
Detect Drift
     ↓
Understand Change
     ↓
Determine Desired State
     ↓
Update Terraform if required
     ↓
Apply Controlled Change
```

---

# 54. Environment Architecture

The platform should maintain separate environments.

```text
                    GitLab
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
         DEV          TEST         PROD
          │            │            │
          ▼            ▼            ▼
        AKS          AKS          AKS
```

Typical differences:

```text
DEV
replicas: 1

TEST
replicas: 2

PROD
replicas: 3+
```

Production should have stronger:

```text
Approval
Security
Monitoring
Availability
Access Control
```

---

# 55. Deployment Promotion

A controlled promotion model:

```text
Developer
   ↓
DEV
   ↓
Automated Tests
   ↓
TEST
   ↓
Integration Testing
   ↓
Approval
   ↓
PRODUCTION
```

The same tested artifact should ideally be promoted rather than rebuilding different artifacts for each environment.

---

# 56. Artifact Promotion

Preferred model:

```text
Build Once
     ↓
Image: 8f91c2a
     ↓
DEV
     ↓
TEST
     ↓
PROD
```

Avoid:

```text
DEV → rebuild
TEST → rebuild
PROD → rebuild
```

because each rebuild can introduce differences.

---

# 57. Architecture Principles

NexCart follows these principles:

### 1. Automation

Automate repeatable tasks.

### 2. Immutable Artifacts

Build versioned images and promote them.

### 3. Least Privilege

Grant only required permissions.

### 4. Observability

Every production component should be observable.

### 5. Traceability

Every deployment should map to source code.

### 6. Fault Isolation

A single service failure should have limited blast radius.

### 7. Horizontal Scalability

Scale workloads based on demand.

### 8. Infrastructure as Code

Azure infrastructure should be reproducible.

### 9. Secure by Design

Secrets and identities should be managed securely.

### 10. Recovery First

During incidents, restore service quickly and perform RCA afterward.

---

# 58. Architecture Decision Summary

| Requirement | Solution |
|---|---|
| Source Control | GitLab |
| CI/CD | GitLab CI/CD |
| Application Packaging | Docker |
| Container Registry | Azure Container Registry |
| Orchestration | Kubernetes / AKS |
| Deployment Packaging | Helm |
| Infrastructure | Terraform |
| Secrets | Azure Key Vault |
| Identity | Managed / Workload Identity |
| Code Quality | SonarQube |
| Container Security | Trivy |
| Metrics | Prometheus |
| Visualization | Grafana |
| Logs | Loki |
| Telemetry Collection | Alloy |
| External Routing | Ingress |
| Scaling | HPA |
| Service Discovery | Kubernetes Service/DNS |
| Rollback | Helm / Kubernetes |
| TLS | Ingress + Certificate Management |

---

# 59. Senior-Level Architecture Questions

For interviews, be ready for:

### Architecture

1. Why did you choose microservices?
2. Why Kubernetes?
3. Why AKS instead of VMs?
4. Why Helm?
5. Why ACR?
6. Why GitLab CI/CD?
7. Why Terraform?

### Networking

8. How does internet traffic reach a Pod?
9. How does one microservice communicate with another?
10. What happens if the Service has no endpoints?
11. How would you troubleshoot a 502?
12. How would you troubleshoot DNS?

### Kubernetes

13. What happens when a Pod crashes?
14. What happens when a node fails?
15. Difference between Deployment and ReplicaSet?
16. Difference between Service and Ingress?
17. ClusterIP vs LoadBalancer?
18. Readiness vs liveness?
19. How does HPA work?

### Azure

20. How does AKS authenticate to ACR?
21. How does a Pod access Key Vault?
22. Managed Identity vs Service Principal?
23. How would you secure the AKS cluster?
24. How would you design the VNet?

### CI/CD

25. How does code move from GitLab to AKS?
26. How do you version images?
27. How do you prevent vulnerable images from reaching production?
28. How do you rollback?
29. How do you promote the same artifact across environments?

### Production

30. What happens if a certificate expires?
31. How do you rotate credentials?
32. How do you upgrade AKS?
33. How do you troubleshoot CrashLoopBackOff?
34. How do you troubleshoot ImagePullBackOff?
35. How do you handle a production deployment failure?
36. How do you perform RCA?

---

# 60. Interview Architecture Explanation

When an interviewer asks:

> **"Explain your architecture end-to-end."**

Use this sequence:

```text
First, explain the business application.

        ↓

Then explain the microservices.

        ↓

Then explain GitLab and CI/CD.

        ↓

Then explain Docker and ACR.

        ↓

Then explain AKS and Kubernetes.

        ↓

Then explain Helm.

        ↓

Then explain Azure networking.

        ↓

Then explain Key Vault and identity.

        ↓

Then explain Terraform.

        ↓

Then explain monitoring and logging.

        ↓

Finally explain production troubleshooting,
rollback, certificate renewal,
credential rotation and day-2 operations.
```

A strong answer should sound like:

> "The application is a microservices-based e-commerce platform. Source code is maintained in GitLab, and GitLab CI/CD handles validation, testing, security scanning, container image creation and publishing to ACR. The versioned images are deployed to AKS using Helm. Kubernetes provides service discovery, self-healing, rolling deployments and horizontal scaling. External traffic enters through the ingress layer, while internal services communicate through Kubernetes Services and DNS. Sensitive configuration is managed through Key Vault with identity-based access. Terraform manages the Azure infrastructure. Prometheus, Grafana, Loki and Alloy provide observability. Operationally, we handle deployment failures, rollbacks, certificate renewal, credential rotation, scaling, upgrades and incident RCA as part of the platform lifecycle."

---

# 61. Architecture Mental Model

For interview preparation, remember this single flow:

```text
                    ┌──────────────┐
                    │   USER       │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ Azure DNS    │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ LoadBalancer │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │   Ingress    │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ API Gateway  │
                    └──────┬───────┘
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
          User          Product        Order
          Service       Service        Service
             │             │             │
             ▼             ▼             ▼
          PostgreSQL      MySQL       PostgreSQL

                           │
                    ┌──────┴──────┐
                    ▼             ▼
                 Payment      Notification
                 Service        Service
                    │             │
                    ▼             ▼
                 MongoDB         Redis


              DEVOPS / PLATFORM FLOW

 Developer
     │
     ▼
   GitLab
     │
     ▼
 GitLab CI/CD
     │
     ├── Test
     ├── SonarQube
     ├── Trivy
     └── Docker
           │
           ▼
          ACR
           │
           ▼
         Helm
           │
           ▼
          AKS
           │
           ▼
      Production


              SECURITY FLOW

 Pod
  │
  ▼
ServiceAccount
  │
  ▼
Workload Identity
  │
  ▼
Azure RBAC
  │
  ▼
Key Vault


             OBSERVABILITY FLOW

 Pod
  │
  ▼
Alloy
 ├────────► Loki
 │
 └────────► Prometheus
                 │
                 ▼
              Grafana
```

---

# 62. Final Architecture Statement

The NexCart architecture is designed around the principle:

```text
Application
     +
Automation
     +
Security
     +
Scalability
     +
Observability
     +
Reliability
     +
Operational Readiness
```

The goal is not simply to deploy containers into AKS.

The goal is to build a platform where:

```text
Code
 ↓
Can be tested
 ↓
Can be secured
 ↓
Can be packaged
 ↓
Can be deployed
 ↓
Can be observed
 ↓
Can be scaled
 ↓
Can be rolled back
 ↓
Can be recovered
 ↓
Can be maintained for years
```
