---
title: "03-microservices"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 3
---

# 🧩 03 — NexCart Microservices

## 3.1 Microservices Overview

NexCart is designed as a **microservices-based e-commerce application**, where business capabilities are separated into independently deployable services.

### Core Microservices

| Service | Technology | Port | Responsibility |
|---|---|---:|---|
| Product Service | Node.js / Express | `8082` | Product creation, retrieval and product APIs |
| Order Service | Application service | `8083` | Order creation and order management |
| Payment Service | Python / FastAPI | `8084` | Payment processing and payment APIs |
| Notification Service | Application service | `8085` | Notification processing |

### Basic Request Flow

```text
User
  |
  v
Ingress / Load Balancer
  |
  +----> Product Service      :8082
  |
  +----> Order Service        :8083
  |
  +----> Payment Service      :8084
  |
  +----> Notification Service :8085
```

The services communicate through APIs and are deployed independently inside Kubernetes.

---

# 3.2 Product Service

## Purpose

The Product Service manages product-related operations.

### Responsibilities

- Create products
- Retrieve products
- Update product information
- Delete products
- Product validation
- Product API availability
- Product health monitoring

### Local Port

```text
8082
```

### Important APIs

```text
GET  /health
GET  /api/products
POST /api/products
```

### Health Check

```powershell
Invoke-RestMethod http://localhost:8082/health
```

Expected response:

```json
{
  "service": "product-service",
  "status": "UP"
}
```

### Get Products

```powershell
Invoke-RestMethod http://localhost:8082/api/products
```

### Create Product

```powershell
Invoke-RestMethod `
  -Uri http://localhost:8082/api/products `
  -Method POST `
  -ContentType "application/json" `
  -Body '{"name":"Laptop","price":75000}'
```

---

# 3.3 Order Service

## Purpose

The Order Service manages customer orders.

### Responsibilities

- Create orders
- Validate order information
- Maintain order status
- Communicate with product/payment services
- Handle order lifecycle
- Provide order APIs

### Port

```text
8083
```

Typical order lifecycle:

```text
Order Created
     |
     v
Order Validated
     |
     v
Payment Requested
     |
     v
Payment Successful
     |
     v
Order Confirmed
     |
     v
Notification Triggered
```

### Production Considerations

Order processing should be designed to handle:

- Duplicate requests
- Payment failures
- Service timeouts
- Partial failures
- Retry scenarios
- Database failures
- Concurrent requests
- Transaction consistency

---

# 3.4 Payment Service

## Purpose

The Payment Service handles payment-related operations.

### Technology

```text
Python
FastAPI
```

### Port

```text
8084
```

### Example API

```text
POST /api/payments
```

The API expects:

```json
{
  "order_id": "ORD-1001"
}
```

### Important Implementation Issue

During local testing, the API rejected:

```json
{
  "orderId": "ORD-1001"
}
```

because the FastAPI model expected:

```text
order_id
```

This is a typical microservice integration issue where the producer and consumer use different API field names.

### Troubleshooting Approach

Check:

```text
1. API contract
2. Request payload
3. FastAPI/Pydantic model
4. HTTP status code
5. Application logs
6. Service-to-service communication
```

### Example Test

```powershell
Invoke-RestMethod `
  -Uri http://localhost:8084/api/payments `
  -Method POST `
  -ContentType "application/json" `
  -Body '{"order_id":"ORD-1001"}'
```

---

# 3.5 Notification Service

## Purpose

The Notification Service handles application notifications.

### Responsibilities

- Send order notifications
- Payment status notifications
- Customer notifications
- Notification retry handling
- Notification status tracking

### Port

```text
8085
```

A production implementation should avoid making the entire transaction dependent on the notification service.

For example:

```text
Order
  |
  +----> Payment
  |
  +----> Notification
```

If notification fails, the order should not necessarily fail.

A better design is:

```text
Order Service
      |
      v
Event / Queue
      |
      v
Notification Service
```

This provides loose coupling and better fault tolerance.

---

# 3.6 Service-to-Service Communication

Microservices communicate using APIs.

Example:

```text
Order Service
     |
     | HTTP/REST
     v
Payment Service
     |
     | Payment Result
     v
Order Service
```

Another example:

```text
Order Service
     |
     v
Notification Service
```

### Important Production Parameters

Every service-to-service call should consider:

- Connection timeout
- Read timeout
- Retry
- Retry count
- Backoff
- Circuit breaker
- Authentication
- Authorization
- TLS
- Correlation ID
- Logging
- Metrics
- Distributed tracing

---

# 3.7 API Contract Management

Microservices should have clearly defined API contracts.

Example:

```json
{
  "order_id": "ORD-1001",
  "amount": 2500,
  "currency": "INR"
}
```

The contract should define:

```text
Request
Response
HTTP method
HTTP status codes
Required fields
Optional fields
Validation rules
Error response
Authentication
Timeout expectations
```

### Example Error Response

```json
{
  "error": "INVALID_REQUEST",
  "message": "order_id is required"
}
```

Consistent error responses make troubleshooting much easier.

---

# 3.8 Independent Deployment

One of the major advantages of microservices is independent deployment.

For example:

```text
Product Service v1.4
Order Service   v2.1
Payment Service  v1.8
Notification     v1.3
```

A change in Product Service should not require rebuilding all services.

### Deployment Flow

```text
Developer
   |
   v
GitLab
   |
   v
CI Pipeline
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
AKS
   |
   v
Kubernetes Deployment
```

---

# 3.9 Containerization

Each microservice is packaged as an independent Docker image.

Example:

```text
product-service:commit-sha
order-service:commit-sha
payment-service:commit-sha
notification-service:commit-sha
```

Production images should preferably use immutable tags.

### Recommended

```text
product-service:8f32a91
```

### Avoid

```text
product-service:latest
```

Using Git commit SHA makes rollback and auditing easier.

---

# 3.10 Kubernetes Deployment

Each microservice runs as a Kubernetes Deployment.

Example:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: product-service

spec:
  replicas: 2

  selector:
    matchLabels:
      app: product-service

  template:
    metadata:
      labels:
        app: product-service

    spec:
      containers:
        - name: product-service
          image: <acr>.azurecr.io/product-service:<commit-sha>
          ports:
            - containerPort: 8082
```

The Deployment manages:

- Pod creation
- Replica management
- Rolling updates
- Self-healing
- Version changes

---

# 3.11 Kubernetes Service

Pods are ephemeral, so clients should not directly connect to Pod IPs.

A Kubernetes Service provides stable networking.

```text
                Kubernetes
                    |
          +---------+---------+
          |                   |
       Pod 1                Pod 2
   product-service      product-service
          \                   /
           \                 /
            +---------------+
                    |
             product-service
                 Service
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
```

---

# 3.12 Health Checks

Every production microservice should expose health endpoints.

Example:

```text
GET /health
```

Kubernetes can use:

```text
Liveness Probe
Readiness Probe
Startup Probe
```

### Liveness Probe

Determines whether the container is alive.

```text
Failure
  |
  v
Kubernetes restarts container
```

### Readiness Probe

Determines whether the application can receive traffic.

```text
Not Ready
   |
   v
Pod removed from Service endpoints
```

### Startup Probe

Useful for applications that require longer startup time.

---

# 3.13 Resource Management

Each microservice should have CPU and memory requests/limits.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

### Why Requests Matter

Kubernetes uses requests for scheduling.

### Why Limits Matter

Limits prevent one service from consuming excessive node resources.

However, incorrect limits can also cause:

```text
OOMKilled
CPU throttling
Slow application
Pod restarts
```

Therefore, limits should be based on actual workload measurements.

---

# 3.14 Horizontal Scaling

Different services may have different traffic patterns.

For example:

```text
Product Service       3 replicas
Order Service         3 replicas
Payment Service       2 replicas
Notification Service  2 replicas
```

HPA can scale replicas based on resource utilization or custom metrics.

```text
Traffic increases
       |
       v
CPU/Custom metric increases
       |
       v
HPA detects threshold
       |
       v
Replica count increases
```

Example:

```bash
kubectl get hpa -n nexcart
```

---

# 3.15 Configuration Management

Application configuration should not be hardcoded into container images.

Separate:

```text
Application Code
Configuration
Secrets
```

### ConfigMap

Used for non-sensitive configuration.

Examples:

```text
LOG_LEVEL
ENVIRONMENT
SERVICE_URL
PORT
```

### Secret

Used for sensitive information.

Examples:

```text
DATABASE_PASSWORD
API_KEY
TOKEN
CLIENT_SECRET
```

For Azure production environments, sensitive values should preferably be managed through **Azure Key Vault** and integrated with AKS using an appropriate identity-based mechanism.

---

# 3.16 ServiceAccount and Identity

Kubernetes ServiceAccount provides an identity to a Pod.

Example:

```yaml
apiVersion: v1
kind: ServiceAccount

metadata:
  name: product-service-sa
  namespace: nexcart
```

Pod:

```yaml
spec:
  serviceAccountName: product-service-sa
```

For Azure resource access, the preferred modern architecture is:

```text
Pod
 |
 v
Kubernetes ServiceAccount
 |
 v
AKS Workload Identity
 |
 v
Microsoft Entra ID
 |
 v
Azure Resource
```

For example:

```text
Pod
 |
 v
ServiceAccount
 |
 v
Workload Identity
 |
 v
Azure Key Vault
```

This avoids storing long-lived Azure credentials inside containers.

---

# 3.17 Important ServiceAccount Interview Point

A Kubernetes ServiceAccount does **not normally have a human-style password**.

Modern Kubernetes uses short-lived projected service-account tokens.

Therefore, when discussing "password rotation", distinguish between:

```text
Kubernetes ServiceAccount
Azure Service Principal credentials
Database password
Key Vault secret
TLS certificate
GitLab token
ACR credentials
```

These are different credentials with different rotation mechanisms.

---

# 3.18 Microservice Failure Scenarios

## Scenario 1 — Product Service Pod Down

Check:

```bash
kubectl get pods -n nexcart
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart
```

Possible causes:

```text
Application crash
Bad configuration
Missing secret
Image problem
Port mismatch
Memory issue
Database connectivity
```

---

## Scenario 2 — CrashLoopBackOff

Check:

```bash
kubectl logs <pod> -n nexcart --previous
kubectl describe pod <pod> -n nexcart
```

Typical causes:

```text
Application startup failure
Invalid environment variable
Database connection failure
Wrong command/entrypoint
Missing configuration
OOMKilled
```

---

## Scenario 3 — ImagePullBackOff

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

Verify:

```text
Image name
Image tag
ACR repository
ACR permissions
Network connectivity
ImagePullSecret if applicable
```

For AKS with managed identity-based ACR integration, verify the required `AcrPull` role assignment.

---

# 3.19 Service Returns 404

Check:

```bash
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get ingress -n nexcart
```

Then test directly:

```bash
kubectl port-forward svc/product-service 8082:80 -n nexcart
```

Test:

```bash
curl http://localhost:8082/health
```

Possible causes:

```text
Wrong API path
Ingress path mismatch
Wrong target service
Application route missing
Service selector mismatch
```

---

# 3.20 Service Returns 502

A `502 Bad Gateway` usually indicates a problem between the proxy/Ingress and backend service.

Check:

```bash
kubectl get ingress -n nexcart
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get pods -n nexcart -o wide
```

Then:

```bash
kubectl logs <ingress-controller-pod> -n <ingress-namespace>
```

Possible causes:

```text
Pod not ready
No service endpoints
Wrong targetPort
Application not listening on expected port
Network policy
Ingress configuration issue
Backend timeout
```

---

# 3.21 Port Mismatch

Example:

```text
Application listens on 8082
Kubernetes targetPort = 8080
```

Traffic will fail.

Verify:

```bash
kubectl get svc product-service -n nexcart -o yaml
kubectl get deploy product-service -n nexcart -o yaml
```

Inside the container:

```bash
kubectl exec -it <pod> -n nexcart -- sh
```

Then check the listening port according to the container image/tools available.

---

# 3.22 Service Selector Problem

Example:

```yaml
Service selector:
  app: product-service
```

But Pod label:

```yaml
app: product
```

The Service will have no endpoints.

Check:

```bash
kubectl get endpoints product-service -n nexcart
```

If there are no endpoints, compare:

```bash
kubectl get svc product-service -n nexcart -o yaml
kubectl get pods -n nexcart --show-labels
```

This is a common Kubernetes troubleshooting scenario.

---

# 3.23 Database Connectivity

When a microservice cannot connect to its database, check in this order:

```text
1. Pod health
2. Environment variables
3. DNS resolution
4. Network connectivity
5. Credentials
6. TLS/certificate
7. Database availability
8. Connection pool
9. Firewall/NSG
10. Private endpoint/DNS if applicable
```

Do not immediately assume the database is down.

---

# 3.24 Timeout vs Connection Refused

### Connection Refused

Usually indicates:

```text
Target reachable
But nothing is listening on the target port
```

### Timeout

Usually indicates:

```text
Network path problem
Firewall
NSG
Network Policy
Routing
DNS
Private endpoint
Service unavailable
```

This distinction is useful during production troubleshooting.

---

# 3.25 Distributed Tracing

Microservices make troubleshooting difficult because one user request can cross multiple services.

Example:

```text
Client
  |
  v
Product Service
  |
  v
Order Service
  |
  v
Payment Service
  |
  v
Notification Service
```

A single correlation/trace ID should follow the request.

Example:

```text
trace-id: 8f2a91...
```

Logs from every service should contain the same identifier.

This allows engineers to reconstruct the complete request flow.

---

# 3.26 Centralized Logging

Each service generates logs.

Instead of checking every Pod manually:

```bash
kubectl logs <pod>
```

production environments should centralize logs.

Conceptually:

```text
Microservices
     |
     v
Log Collector
     |
     v
Central Log Store
     |
     v
Grafana / Dashboard
```

For the NexCart observability design:

```text
Applications
    |
    v
Grafana Alloy
    |
    +----> Loki   → Logs
    |
    +----> Tempo  → Traces
    |
    +----> Mimir  → Metrics
    |
    v
Grafana
```

---

# 3.27 Microservice Security

Every service should follow least privilege.

Important controls:

```text
Kubernetes RBAC
ServiceAccount
Workload Identity
Azure Key Vault
Network Policies
TLS
Container image scanning
Dependency scanning
Non-root containers
Read-only filesystem where possible
Resource limits
Pod Security controls
```

Never store credentials directly in:

```text
Dockerfile
Git repository
Helm values
Application source code
GitLab YAML
```

Use secret-management mechanisms instead.

---

# 3.28 Microservice Deployment Strategy

A senior production deployment should consider:

```text
Build
  ↓
Test
  ↓
Security Scan
  ↓
Docker Image
  ↓
ACR
  ↓
Helm
  ↓
AKS
  ↓
Smoke Test
  ↓
Monitoring
  ↓
Release Validation
```

For production:

```text
Old Version
    |
    | Rolling Update
    v
New Version
```

If the new version is unhealthy:

```bash
kubectl rollout undo deployment/<deployment-name> -n nexcart
```

With Helm:

```bash
helm history <release> -n nexcart
helm rollback <release> <revision> -n nexcart
```

---

# 3.29 Microservice Versioning

Use immutable versions.

Example:

```text
product-service:1.0.0
product-service:1.0.1
product-service:git-8f32a91
```

Maintain traceability:

```text
Git Commit
    ↓
GitLab Pipeline
    ↓
Docker Image
    ↓
ACR
    ↓
Helm Release
    ↓
AKS Pod
```

An engineer should be able to answer:

> Which Git commit is currently running in production?

This is an important production-readiness capability.

---

# 3.30 Dependency Management

Microservices are not truly independent if they have uncontrolled runtime dependencies.

For example:

```text
Order Service
      |
      v
Payment Service
```

If Payment Service is unavailable, Order Service must handle the failure gracefully.

Possible approaches:

```text
Timeout
Retry
Exponential Backoff
Circuit Breaker
Fallback
Asynchronous Processing
Idempotency
```

Avoid unlimited retries because they can create a retry storm.

---

# 3.31 Idempotency

Payment and order APIs should consider duplicate requests.

Example:

```text
Client sends payment request
        |
        v
Network timeout
        |
        v
Client retries
```

Without idempotency:

```text
Payment 1
Payment 2
```

With an idempotency key:

```text
Request 1 → Payment created
Request 2 → Existing result returned
```

Example:

```text
Idempotency-Key: ORD-1001-PAYMENT
```

This is particularly important for payment operations.

---

# 3.32 Retry Strategy

Do not retry every error.

### Retryable

```text
Temporary network failure
Timeout
HTTP 503
Transient dependency failure
```

### Usually Not Retryable

```text
HTTP 400
Invalid request
Authentication failure
Authorization failure
Validation failure
```

Use exponential backoff:

```text
1 sec
2 sec
4 sec
8 sec
```

with a maximum retry limit.

---

# 3.33 Circuit Breaker

If Payment Service is continuously failing:

```text
Order Service
     |
     v
Payment Service
     X
   Failure
```

Repeated retries can overload Payment Service.

Circuit breaker pattern:

```text
Closed
  |
  | failures increase
  v
Open
  |
  | wait
  v
Half-Open
  |
  +---- Success → Closed
  |
  +---- Failure → Open
```

This prevents cascading failures.

---

# 3.34 Graceful Shutdown

Kubernetes may terminate Pods during:

```text
Deployment
Scaling
Node drain
AKS upgrade
Pod eviction
```

Applications should handle termination gracefully.

Flow:

```text
SIGTERM
   |
   v
Stop accepting new requests
   |
   v
Finish active requests
   |
   v
Close connections
   |
   v
Process exits
```

This reduces failed requests during deployments.

---

# 3.35 Production Day-2 Problems

After 2–3 years, microservices usually face operational problems beyond initial deployment.

### Common Issues

```text
1. TLS certificate expiry
2. Key Vault secret rotation
3. Azure credential expiration
4. Database password rotation
5. GitLab token expiration
6. Container image CVEs
7. Base image becoming unsupported
8. Kubernetes API deprecations
9. AKS version upgrade
10. Helm chart upgrade
11. Resource limits becoming insufficient
12. HPA not scaling correctly
13. DNS failures
14. Ingress configuration changes
15. ACR access issues
16. Storage/CSI problems
17. Network Policy changes
18. Terraform drift
19. Azure quota/capacity issues
20. Log/metric storage cost growth
```

---

# 3.36 Certificate Renewal

A production microservice platform may depend on:

```text
TLS Certificate
Ingress Certificate
Internal Certificate
API Certificate
Database Certificate
```

Certificate lifecycle should be automated where possible.

```text
Certificate
    |
    v
Monitor Expiry
    |
    v
Renew
    |
    v
Update Secret / Key Vault
    |
    v
Reload Ingress/Application
    |
    v
Validate HTTPS
```

Never wait until the certificate expires.

---

# 3.37 Secret Rotation

Secrets may include:

```text
Database password
API credentials
Third-party tokens
Azure credentials
Service integration secrets
```

A production rotation process should be:

```text
Generate New Credential
        |
        v
Store in Key Vault
        |
        v
Update Application Access
        |
        v
Validate
        |
        v
Revoke Old Credential
```

Applications should support secret refresh/restart behavior without causing unnecessary downtime.

---

# 3.38 Azure Credential Rotation

If a legacy Azure Service Principal uses a client secret:

```text
Client ID
Client Secret
Tenant ID
```

the secret must have an expiry and rotation process.

Preferred modern architecture:

```text
Workload
   |
   v
Managed Identity / Workload Identity
   |
   v
Microsoft Entra ID
```

This reduces dependency on long-lived client secrets.

---

# 3.39 Microservice Interview Explanation

### Interview Question

**"Explain the microservices architecture of your NexCart project."**

### Senior-Level Answer

> NexCart is designed as a microservices-based e-commerce application. We separated business capabilities into Product, Order, Payment and Notification services. Each service has its own deployment lifecycle and runs as a container in Kubernetes.
>
> Product Service runs on port 8082, Order Service on 8083, Payment Service on 8084 using Python FastAPI, and Notification Service on 8085. We containerize each service independently and push versioned images to Azure Container Registry.
>
> For Kubernetes deployment, we use Deployments, Services, health probes, ConfigMaps, Secrets, ServiceAccounts and Helm charts. GitLab CI/CD handles the build and deployment workflow, while AKS provides the runtime platform.
>
> For Azure resource access, we prefer identity-based access using AKS Workload Identity and Azure Key Vault rather than storing long-lived credentials inside Pods.
>
> From an operations perspective, I focus not only on deployment but also on failure handling, observability, rollback, certificate and secret rotation, dependency failures, resource scaling, security, and upgrade management.
>
> The key advantage of this architecture is independent deployment and scaling, while the major challenge is managing distributed-system complexity such as service dependencies, network failures, API contracts, retries, observability and consistency.

---

# 3.40 Senior DevOps Microservices Checklist

Before considering a microservice production-ready, verify:

```text
[ ] Dockerfile optimized
[ ] Non-root container
[ ] Immutable image tag
[ ] Image vulnerability scan
[ ] Resource requests configured
[ ] Resource limits configured
[ ] Liveness probe
[ ] Readiness probe
[ ] Startup probe where required
[ ] Kubernetes Deployment
[ ] Kubernetes Service
[ ] ConfigMap
[ ] Secret / Key Vault integration
[ ] ServiceAccount
[ ] Workload Identity where required
[ ] RBAC
[ ] Network policy
[ ] TLS
[ ] Logging
[ ] Metrics
[ ] Distributed tracing
[ ] Alerting
[ ] HPA where required
[ ] Timeout configuration
[ ] Retry strategy
[ ] Idempotency where required
[ ] Graceful shutdown
[ ] Rollback strategy
[ ] Backup/recovery plan
[ ] Certificate renewal process
[ ] Secret rotation process
[ ] Dependency failure handling
[ ] Production runbook
```

# 3.41 Key Senior-Level Takeaway

```text
Microservices are not simply multiple applications running in containers.

A production microservices platform requires:

Application Design
        +
API Contracts
        +
Containers
        +
Kubernetes
        +
Networking
        +
Identity
        +
Secrets
        +
Security
        +
Observability
        +
CI/CD
        +
Scaling
        +
Failure Handling
        +
Rollback
        +
Day-2 Operations
```

The  DevOps responsibility is not only to deploy the microservices. It is to make the platform **reliable, secure, observable, scalable, recoverable and maintainable over multiple years**.