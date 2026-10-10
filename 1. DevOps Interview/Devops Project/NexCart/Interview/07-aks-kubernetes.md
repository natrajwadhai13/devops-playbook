---
title: "07-aks-kubernetes"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 7
---
# AKS & Kubernetes

## 1. What is AKS?

**Azure Kubernetes Service (AKS)** is Azure's managed Kubernetes platform.

Azure manages the Kubernetes control-plane components, while the DevOps team manages workloads, node pools, networking, identities, security, scaling, deployments, and day-2 operations.

For NexCart:

```text
GitLab CI/CD
     ↓
ACR
     ↓
AKS
     ↓
Kubernetes Namespace
     ↓
Deployments
     ↓
Pods
     ↓
Services
     ↓
Ingress
     ↓
NexCart Microservices
```

---

# 2. NexCart AKS Architecture

```text
                         Internet
                            |
                            v
                    Azure Load Balancer
                            |
                            v
                       Ingress
                            |
              +-------------+-------------+
              |             |             |
              v             v             v
         Product         Order         Payment
         Service         Service       Service
              |             |             |
              v             v             v
             Pods          Pods          Pods
                            |
                            v
                    Notification
                       Service
```

Supporting components:

```text
AKS
├── Namespace
├── Deployments
├── Pods
├── Services
├── Ingress
├── ConfigMaps
├── Secrets / Key Vault integration
├── ServiceAccounts
├── HPA
├── RBAC
└── Network Policies
```

---

# 3. AKS Cluster Components

A production AKS environment normally contains:

```text
AKS Cluster
│
├── Control Plane
│   ├── API Server
│   ├── Scheduler
│   ├── Controller Manager
│   └── etcd
│
└── Node Pools
    ├── Node 1
    ├── Node 2
    └── Node 3
         |
         └── Pods
```

Azure manages the AKS control plane.

The application team primarily manages:

- Node pools
- Workloads
- Kubernetes objects
- Networking
- RBAC
- Identity
- Scaling
- Monitoring
- Deployment strategy
- Security
- Day-2 operations

---

# 4. Create AKS Cluster

Example:

```bash
az login

az account set --subscription "<subscription-id>"

az aks create \
  --resource-group nexcart-rg \
  --name nexcart-aks \
  --location centralindia \
  --node-count 3 \
  --node-vm-size Standard_D4s_v5 \
  --enable-managed-identity \
  --generate-ssh-keys
```

Get credentials:

```bash
az aks get-credentials \
  --resource-group nexcart-rg \
  --name nexcart-aks
```

Verify:

```bash
kubectl get nodes
```

---

# 5. AKS Node Pools

A node pool is a group of Kubernetes worker nodes with the same configuration.

Example:

```text
AKS Cluster
│
├── system pool
│   ├── node-1
│   └── node-2
│
└── user pool
    ├── node-3
    ├── node-4
    └── node-5
```

Production architecture commonly separates system and application workloads.

Check node pools:

```bash
az aks nodepool list \
  --resource-group nexcart-rg \
  --cluster-name nexcart-aks \
  -o table
```

Check nodes:

```bash
kubectl get nodes -o wide
```

---

# 6. Kubernetes Namespace

Use a dedicated namespace for NexCart.

```bash
kubectl create namespace nexcart
```

Verify:

```bash
kubectl get namespaces
```

Example:

```text
nexcart
```

Benefits:

- Logical isolation
- RBAC boundary
- Resource quotas
- Easier troubleshooting
- Cleaner deployments
- Environment separation

For example:

```text
AKS Cluster
├── nexcart-dev
├── nexcart-test
└── nexcart-prod
```

Alternatively, production environments can use separate AKS clusters when stronger isolation is required.

---

# 7. Kubernetes Deployment

A Deployment manages replicated Pods.

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
  namespace: nexcart
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
          image: nexcartacr.azurecr.io/product-service:a81f92c
          ports:
            - containerPort: 8082
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

Check:

```bash
kubectl get deployment -n nexcart
```

---

# 8. Pod

A Pod is the smallest deployable unit in Kubernetes.

Example:

```text
Deployment
    |
    +---- ReplicaSet
             |
       +-----+-----+
       |           |
     Pod-1       Pod-2
       |           |
 Product App   Product App
```

Check:

```bash
kubectl get pods -n nexcart
```

Detailed:

```bash
kubectl get pods -n nexcart -o wide
```

---

# 9. Kubernetes Service

Pods are ephemeral.

Their IP addresses can change.

A Kubernetes Service provides a stable network endpoint.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: product-service
  namespace: nexcart
spec:
  selector:
    app: product-service
  ports:
    - port: 8082
      targetPort: 8082
  type: ClusterIP
```

Apply:

```bash
kubectl apply -f service.yaml
```

Check:

```bash
kubectl get svc -n nexcart
```

---

# 10. Service Types

| Type | Use |
|---|---|
| ClusterIP | Internal communication |
| NodePort | Basic external access; generally not preferred for production |
| LoadBalancer | Azure Load Balancer integration |
| ExternalName | DNS-based external service mapping |

For NexCart:

```text
Product → ClusterIP
Order → ClusterIP
Payment → ClusterIP
Notification → ClusterIP
Ingress → External entry point
```

---

# 11. Internal Microservice Communication

Example:

```text
order-service
      |
      v
http://payment-service:8084
```

Kubernetes DNS resolves:

```text
payment-service
```

For cross-namespace communication:

```text
payment-service.nexcart.svc.cluster.local
```

Full DNS:

```text
<service>.<namespace>.svc.cluster.local
```

---

# 12. Ingress

Ingress provides HTTP/HTTPS routing into the cluster.

Example:

```text
Internet
   |
   v
Azure Load Balancer
   |
   v
Ingress Controller
   |
   +---- /api/products → product-service
   |
   +---- /api/orders   → order-service
   |
   +---- /api/payment  → payment-service
```

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nexcart-ingress
  namespace: nexcart
spec:
  rules:
    - host: api.nexcart.example.com
      http:
        paths:
          - path: /api/products
            pathType: Prefix
            backend:
              service:
                name: product-service
                port:
                  number: 8082
```

The exact ingress controller depends on the production architecture.

---

# 13. Kubernetes ConfigMap

ConfigMaps store non-sensitive configuration.

Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: product-config
  namespace: nexcart
data:
  NODE_ENV: production
  LOG_LEVEL: info
```

Check:

```bash
kubectl get configmap -n nexcart
```

Do not store passwords or secrets in ConfigMaps.

---

# 14. Kubernetes Secrets

Secrets are intended for sensitive configuration.

Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: payment-secret
  namespace: nexcart
type: Opaque
stringData:
  DB_PASSWORD: "<secret>"
```

Check:

```bash
kubectl get secrets -n nexcart
```

For Azure production environments, prefer integrating workloads with Azure Key Vault rather than treating native Kubernetes Secrets as the ultimate secret-management solution.

---

# 15. AKS + Azure Key Vault

Recommended architecture:

```text
Pod
 |
Kubernetes ServiceAccount
 |
Azure Workload Identity
 |
Managed Identity
 |
Azure Key Vault
 |
Secrets
```

This avoids putting long-lived Azure credentials inside Pods.

Typical components:

```text
ServiceAccount
Federated Identity Credential
Azure Managed Identity
Key Vault
Secrets Store CSI Driver
```

---

# 16. Kubernetes ServiceAccount

A ServiceAccount provides an identity for a workload inside Kubernetes.

Example:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: product-service-sa
  namespace: nexcart
```

Apply:

```bash
kubectl apply -f serviceaccount.yaml
```

Check:

```bash
kubectl get serviceaccount -n nexcart
```

Important:

> A Kubernetes ServiceAccount is not a human user account and does not have a normal username/password.

---

# 17. Azure Workload Identity

Recommended modern pattern:

```text
Pod
 ↓
Kubernetes ServiceAccount
 ↓
OIDC Federation
 ↓
Azure Managed Identity
 ↓
Azure Resource
```

Example Azure resources:

- Key Vault
- Storage
- Azure SQL
- Service Bus
- Azure APIs

Advantages:

- No long-lived secrets
- Short-lived tokens
- Workload-level identity
- Better least privilege
- Better auditability

---

# 18. Kubernetes RBAC

RBAC controls what identities can do inside Kubernetes.

Objects:

```text
Role
RoleBinding
ClusterRole
ClusterRoleBinding
```

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: product-reader
  namespace: nexcart
rules:
  - apiGroups: [""]
    resources:
      - configmaps
    verbs:
      - get
      - list
```

Bind it to a ServiceAccount using `RoleBinding`.

Verify permission:

```bash
kubectl auth can-i \
  get configmaps \
  -n nexcart \
  --as=system:serviceaccount:nexcart:product-service-sa
```

---

# 19. Resource Requests and Limits

Every production workload should have appropriate resource requests and limits.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "1000m"
    memory: "512Mi"
```

Meaning:

```text
Request = resources required for scheduling
Limit   = maximum resource usage allowed
```

Without proper requests:

- Scheduling becomes unpredictable
- HPA behavior becomes unreliable
- Capacity planning becomes difficult

---

# 20. Liveness Probe

Liveness determines whether the container should be restarted.

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8082
  initialDelaySeconds: 20
  periodSeconds: 10
```

If the application becomes permanently unhealthy, Kubernetes can restart the container.

---

# 21. Readiness Probe

Readiness determines whether the Pod should receive traffic.

Example:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 8082
  initialDelaySeconds: 10
  periodSeconds: 5
```

Important distinction:

```text
Liveness  → Should Kubernetes restart the container?
Readiness → Should the Service send traffic to this Pod?
```

---

# 22. Startup Probe

Useful for applications with long startup times.

```yaml
startupProbe:
  httpGet:
    path: /health
    port: 8082
  failureThreshold: 30
  periodSeconds: 10
```

Startup probe prevents liveness checks from killing an application while it is still starting.

---

# 23. HPA

Horizontal Pod Autoscaler automatically adjusts Pod replicas based on resource or custom metrics.

Example:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: product-service
  namespace: nexcart
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: product-service
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

Check:

```bash
kubectl get hpa -n nexcart
```

---

# 24. HPA vs Cluster Autoscaler

These solve different problems.

### HPA

```text
High application load
       ↓
More Pods
```

### Cluster Autoscaler

```text
Insufficient node capacity
       ↓
More Nodes
```

Production scaling:

```text
Traffic increases
      ↓
HPA increases Pods
      ↓
Nodes become insufficient
      ↓
Cluster Autoscaler adds nodes
```

---

# 25. AKS Cluster Autoscaler

Example:

```bash
az aks nodepool update \
  --resource-group nexcart-rg \
  --cluster-name nexcart-aks \
  --name agentpool \
  --enable-cluster-autoscaler \
  --min-count 2 \
  --max-count 6
```

Check:

```bash
az aks nodepool show \
  --resource-group nexcart-rg \
  --cluster-name nexcart-aks \
  --name agentpool \
  -o table
```

---

# 26. Rolling Deployment

Default Kubernetes Deployment strategy is normally RollingUpdate.

```text
Version 1
Pod-1
Pod-2
Pod-3

       ↓

Version 2
Pod-1 v2
Pod-2 v1
Pod-3 v1

       ↓

Pod-1 v2
Pod-2 v2
Pod-3 v1

       ↓

Pod-1 v2
Pod-2 v2
Pod-3 v2
```

Advantages:

- Minimal downtime
- Gradual replacement
- Easy rollback

---

# 27. Rolling Update Configuration

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

For critical services:

```text
maxUnavailable = 0
```

can help maintain availability, but it increases temporary resource usage.

The correct setting depends on capacity and availability requirements.

---

# 28. Deployment Commands

```bash
kubectl get deployments -n nexcart
```

```bash
kubectl rollout status \
  deployment/product-service \
  -n nexcart
```

History:

```bash
kubectl rollout history \
  deployment/product-service \
  -n nexcart
```

Restart:

```bash
kubectl rollout restart \
  deployment/product-service \
  -n nexcart
```

Rollback:

```bash
kubectl rollout undo \
  deployment/product-service \
  -n nexcart
```

---

# 29. AKS Deployment Using Helm

NexCart should use Helm for repeatable deployments.

Example:

```text
helm/nexcart/
├── Chart.yaml
├── values.yaml
├── values-dev.yaml
├── values-test.yaml
├── values-prod.yaml
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    ├── serviceaccount.yaml
    ├── configmap.yaml
    ├── hpa.yaml
    └── secretproviderclass.yaml
```

Install:

```bash
helm upgrade --install nexcart \
  ./helm/nexcart \
  -n nexcart \
  --create-namespace \
  -f values-prod.yaml
```

---

# 30. Verify Helm Deployment

```bash
helm list -n nexcart
```

```bash
helm status nexcart -n nexcart
```

```bash
helm history nexcart -n nexcart
```

Rollback:

```bash
helm rollback nexcart <revision> -n nexcart
```

---

# 31. AKS Production Deployment Flow

```text
Git Push
   ↓
GitLab Pipeline
   ↓
Tests
   ↓
Security Scan
   ↓
Docker Build
   ↓
Push to ACR
   ↓
Helm
   ↓
AKS
   ↓
Deployment
   ↓
ReplicaSet
   ↓
Pods
   ↓
Readiness Probe
   ↓
Service
   ↓
Ingress
   ↓
Users
```

---

# 32. Troubleshooting Pods

Start:

```bash
kubectl get pods -n nexcart
```

Example:

```text
NAME                               READY   STATUS
product-service-6f8c9d7c8-xk2p1   1/1     Running
payment-service-7d9f4b9c5-q1w2e   0/1     CrashLoopBackOff
```

Describe:

```bash
kubectl describe pod \
  payment-service-7d9f4b9c5-q1w2e \
  -n nexcart
```

Logs:

```bash
kubectl logs \
  payment-service-7d9f4b9c5-q1w2e \
  -n nexcart
```

Previous container logs:

```bash
kubectl logs \
  payment-service-7d9f4b9c5-q1w2e \
  -n nexcart \
  --previous
```

---

# 33. CrashLoopBackOff

Common causes:

```text
Application crash
Wrong environment variable
Database connection failure
Port/configuration mismatch
Missing secret
Dependency unavailable
Incorrect startup command
Insufficient memory
```

Troubleshooting:

```bash
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
kubectl describe pod <pod> -n nexcart
kubectl get events -n nexcart --sort-by=.lastTimestamp
```

Senior approach:

```text
Pod status
 ↓
Events
 ↓
Current logs
 ↓
Previous logs
 ↓
Application configuration
 ↓
Dependencies
 ↓
Resources
 ↓
Root cause
```

---

# 34. ImagePullBackOff

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

Typical causes:

```text
Wrong image name
Wrong tag
Image does not exist
ACR authentication
Missing AcrPull
Network/DNS issue
Private endpoint issue
```

Check ACR:

```bash
az acr repository show-tags \
  --name nexcartacr \
  --repository product-service \
  -o table
```

---

# 35. Pending Pod

If:

```text
STATUS = Pending
```

check:

```bash
kubectl describe pod <pod> -n nexcart
```

Common causes:

```text
Insufficient CPU
Insufficient memory
Node selector mismatch
Taints/tolerations
PVC pending
Affinity rules
Resource quota
```

Check nodes:

```bash
kubectl describe nodes
```

Check resources:

```bash
kubectl top nodes
kubectl top pods -n nexcart
```

---

# 36. OOMKilled

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

Look for:

```text
Reason: OOMKilled
```

Possible causes:

- Memory limit too low
- Application memory leak
- Large request/response
- JVM heap configuration
- Node.js memory usage
- Python process memory growth

Fix:

```text
Measure actual usage
       ↓
Tune application
       ↓
Tune memory requests/limits
       ↓
Load test
       ↓
Monitor
```

Do not blindly increase memory limits.

---

# 37. Service Troubleshooting

Check:

```bash
kubectl get svc -n nexcart
```

Check endpoints:

```bash
kubectl get endpoints -n nexcart
```

If Service has no endpoints:

```text
Service
   ↓
Selector
   ↓
Pod Labels
```

Verify:

```bash
kubectl get pods \
  -n nexcart \
  --show-labels
```

Example mismatch:

```yaml
Service:
  selector:
    app: product

Pod:
  labels:
    app: product-service
```

Result:

```text
Service → No endpoints
```

Fix selector/labels.

---

# 38. 502 Bad Gateway

Typical flow:

```text
Client
 ↓
Load Balancer
 ↓
Ingress
 ↓
Service
 ↓
Pod
 ↓
Application
```

Troubleshoot from outside inward:

```text
Ingress
 ↓
Service
 ↓
Endpoints
 ↓
Pod
 ↓
Application
```

Commands:

```bash
kubectl get ingress -n nexcart
kubectl describe ingress <ingress> -n nexcart
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get pods -n nexcart
kubectl logs <pod> -n nexcart
```

---

# 39. DNS Troubleshooting Inside Cluster

Test service resolution:

```bash
kubectl exec -it <pod> \
  -n nexcart \
  -- nslookup payment-service
```

Test HTTP:

```bash
kubectl exec -it <pod> \
  -n nexcart \
  -- curl http://payment-service:8084/health
```

If DNS works but HTTP fails:

```text
Check Service
Check targetPort
Check Pod port
Check application listener
Check NetworkPolicy
```

---

# 40. Port Mismatch

Example:

```text
Container listens: 8082
Service targetPort: 8080
```

Traffic fails.

Verify:

```bash
kubectl get svc product-service \
  -n nexcart \
  -o yaml
```

Verify Deployment:

```bash
kubectl get deployment product-service \
  -n nexcart \
  -o yaml
```

The chain must be correct:

```text
Ingress
 ↓
Service port
 ↓
targetPort
 ↓
Container listening port
```

---

# 41. Kubernetes Events

One of the fastest troubleshooting commands:

```bash
kubectl get events \
  -n nexcart \
  --sort-by=.lastTimestamp
```

Look for:

```text
FailedScheduling
FailedMount
Failed
BackOff
Unhealthy
FailedCreate
FailedAttachVolume
```

Events often provide the first indication of the actual failure.

---

# 42. Node Troubleshooting

Check:

```bash
kubectl get nodes
```

Detailed:

```bash
kubectl describe node <node-name>
```

Check resource usage:

```bash
kubectl top nodes
```

Check conditions:

```bash
kubectl get nodes \
  -o custom-columns=NAME:.metadata.name,STATUS:.status.conditions[-1].type
```

Common node problems:

```text
MemoryPressure
DiskPressure
PIDPressure
NetworkUnavailable
NotReady
```

---

# 43. Node NotReady Troubleshooting

```bash
kubectl get nodes
kubectl describe node <node-name>
kubectl get events --sort-by=.lastTimestamp
```

Check:

```text
Node health
Kubelet
Disk
Memory
Networking
Azure VM status
Node pool health
```

Azure:

```bash
az aks nodepool list \
  --resource-group nexcart-rg \
  --cluster-name nexcart-aks \
  -o table
```

---

# 44. NetworkPolicy

NetworkPolicy can restrict Pod-to-Pod traffic.

Example concept:

```text
Product Service
      |
      X
Payment Service

Order Service
      |
      ↓
Payment Service
```

Only required communication should be allowed.

Production approach:

```text
Default deny
       ↓
Allow required application flows
       ↓
Monitor rejected traffic
```

Do not introduce restrictive NetworkPolicies without understanding DNS, ingress, monitoring, and application dependencies.

---

# 45. Pod Disruption Budget

PDB protects application availability during voluntary disruptions.

Example:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: product-service-pdb
  namespace: nexcart
spec:
  minAvailable: 1
  selector:
    matchLabels:
      app: product-service
```

Useful during:

- Node maintenance
- AKS upgrades
- Node pool changes
- Cluster maintenance

PDB does not protect against every failure, especially involuntary failures.

---

# 46. AKS Upgrade Strategy

AKS must be upgraded periodically.

Before upgrade:

```text
Check Kubernetes version
Check supported versions
Check deprecated APIs
Check workloads
Check PodDisruptionBudgets
Check capacity
Check ingress controller
Check CSI drivers
Check Helm charts
Check application compatibility
```

Get versions:

```bash
az aks show \
  --resource-group nexcart-rg \
  --name nexcart-aks \
  --query kubernetesVersion
```

Available versions:

```bash
az aks get-upgrades \
  --resource-group nexcart-rg \
  --name nexcart-aks
```

---

# 47. AKS Security Baseline

Production AKS should consider:

```text
Managed Identity
RBAC
Workload Identity
Private networking
Network Policies
Pod Security
Resource Limits
Image Scanning
ACR RBAC
Key Vault
Audit Logging
Defender/Security Monitoring
Secrets Rotation
Least Privilege
```

Never give every workload cluster-admin access.

---

# 48. AKS Observability

Production monitoring should cover:

### Infrastructure

```text
CPU
Memory
Disk
Network
Node health
Node pool capacity
```

### Kubernetes

```text
Pod restarts
Pending pods
CrashLoopBackOff
ImagePullBackOff
HPA
Replica availability
Deployment failures
```

### Application

```text
Request rate
Latency
Error rate
Health checks
Dependency failures
```

### Logs

```text
Application logs
Ingress logs
Kubernetes events
Audit logs
```

### Traces

```text
Request
 ↓
Ingress
 ↓
Product
 ↓
Order
 ↓
Payment
 ↓
Notification
```

---

# 49. NexCart Observability Stack

A possible production architecture:

```text
Applications
     |
OpenTelemetry
     |
Grafana Alloy
     |
 +---+---------+---------+
 |             |         |
Logs         Metrics    Traces
 |             |         |
Loki          Mimir     Tempo
 |             |         |
 +-------------+---------+
               |
            Grafana
```

The exact telemetry implementation should match the actual deployment.

---

# 50. AKS Production Troubleshooting Framework

When a production application is down:

```text
1. Confirm impact
        ↓
2. Check ingress/load balancer
        ↓
3. Check Service
        ↓
4. Check endpoints
        ↓
5. Check Pods
        ↓
6. Check application logs
        ↓
7. Check dependencies
        ↓
8. Check node health
        ↓
9. Check Azure resources
        ↓
10. Mitigate
        ↓
11. Root cause analysis
        ↓
12. Prevent recurrence
```

Do not immediately restart Pods without understanding the failure.

---

# 51. Rollback Strategy

If the new version is unhealthy:

```bash
kubectl rollout history \
  deployment/product-service \
  -n nexcart
```

Rollback:

```bash
kubectl rollout undo \
  deployment/product-service \
  -n nexcart
```

Verify:

```bash
kubectl rollout status \
  deployment/product-service \
  -n nexcart
```

With Helm:

```bash
helm history nexcart -n nexcart
```

```bash
helm rollback nexcart <revision> -n nexcart
```

Senior approach:

```text
Detect
 ↓
Stop further rollout
 ↓
Assess impact
 ↓
Rollback if appropriate
 ↓
Validate health
 ↓
Investigate root cause
```

---

# 52. AKS Day-2 Problems

Production AKS management includes:

| Problem | Typical Action |
|---|---|
| Pod CrashLoopBackOff | Logs + events + config |
| ImagePullBackOff | ACR + RBAC + network |
| Pending Pods | Capacity + scheduling |
| OOMKilled | Memory analysis |
| 502/503 | Ingress → Service → Pod |
| DNS failure | CoreDNS + network |
| Certificate expiry | Certificate/issuer validation |
| Secret rotation | Key Vault/secret integration |
| Node NotReady | Node + Azure health |
| HPA not scaling | Metrics + requests |
| AKS upgrade | Compatibility + capacity |
| High cost | Rightsizing + autoscaling |
| CVE in image | Rebuild + scan + redeploy |
| DNS/Ingress issue | Controller + DNS + LB |
| Terraform drift | Plan + reconcile |
| RBAC failure | `kubectl auth can-i` |
| Storage failure | PVC/CSI/Azure disk |
| Network failure | NSG/route/NetworkPolicy |

---

# 53. Useful AKS Commands

```bash
# Cluster
az aks show -g nexcart-rg -n nexcart-aks

# Credentials
az aks get-credentials -g nexcart-rg -n nexcart-aks

# Nodes
kubectl get nodes
kubectl get nodes -o wide
kubectl describe node <node>

# Namespace
kubectl get ns

# Pods
kubectl get pods -n nexcart
kubectl get pods -n nexcart -o wide

# Deployment
kubectl get deploy -n nexcart
kubectl describe deploy <name> -n nexcart

# Services
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart

# Ingress
kubectl get ingress -n nexcart
kubectl describe ingress <name> -n nexcart

# Logs
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous

# Events
kubectl get events -n nexcart --sort-by=.lastTimestamp

# Execute
kubectl exec -it <pod> -n nexcart -- sh

# Resources
kubectl top nodes
kubectl top pods -n nexcart

# Permissions
kubectl auth can-i get secrets -n nexcart \
  --as=system:serviceaccount:nexcart:<sa>

# Rollout
kubectl rollout status deploy/<name> -n nexcart
kubectl rollout history deploy/<name> -n nexcart
kubectl rollout undo deploy/<name> -n nexcart
```

---

# 54. Senior Interview Questions

## Q1. Explain your NexCart AKS architecture.

**Answer:**

> NexCart runs as containerized microservices on AKS. GitLab CI builds and scans the Docker images and pushes immutable artifacts to ACR. Helm manages Kubernetes deployments. Each microservice runs through a Deployment and internal ClusterIP Service. External HTTP traffic enters through the ingress layer. Configuration is externalized, sensitive Azure resources are accessed using managed identity and Workload Identity where applicable, and HPA provides workload scaling. Monitoring covers infrastructure, Kubernetes, application metrics, logs, and traces.

---

## Q2. Pod is Running but application is unavailable. What do you check?

> I would not assume that Running means healthy. I would check readiness status, Service endpoints, Service selectors, targetPort, application listener, ingress routing, and application logs.

Commands:

```bash
kubectl get pods -n nexcart
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart
```

---

## Q3. How do you troubleshoot 502?

> I trace the request path from ingress to Service to endpoints to Pod to application. I verify ingress rules, backend service, endpoints, targetPort, Pod readiness and application logs. If all Kubernetes components are healthy, I move to the application dependency layer.

---

## Q4. HPA is not scaling. What do you check?

```text
HPA status
 ↓
Metrics Server / metrics source
 ↓
CPU/memory requests
 ↓
Current utilization
 ↓
Min/max replicas
 ↓
Deployment target
 ↓
Resource availability
```

Commands:

```bash
kubectl get hpa -n nexcart
kubectl describe hpa <name> -n nexcart
kubectl top pods -n nexcart
kubectl get deployment <name> -n nexcart -o yaml
```

---

## Q5. What is the difference between Service and Ingress?

> A Service provides stable network access to Pods. Ingress provides HTTP/HTTPS routing from outside the cluster to Services.

```text
Ingress
   ↓
Service
   ↓
Pods
```

---

## Q6. What is the difference between liveness and readiness?

> Liveness answers whether the container should be restarted. Readiness answers whether the Pod should receive traffic.

---

## Q7. How do you perform a zero/minimal-downtime deployment?

> I use multiple replicas, readiness probes, RollingUpdate, appropriate PodDisruptionBudgets, graceful application shutdown, and sufficient cluster capacity. I validate the new version before allowing full traffic and keep rollback available.

---

## Q8. How do you secure AKS?

> I use Azure RBAC and Kubernetes RBAC, managed identities, Workload Identity, private networking where required, least-privilege ServiceAccounts, image scanning, NetworkPolicies, Key Vault integration, resource controls, audit logging, and regular AKS/node upgrades.

---

## Q9. How do you handle a production deployment failure?

```text
Detect
 ↓
Assess blast radius
 ↓
Stop rollout
 ↓
Check logs/metrics/events
 ↓
Rollback if customer impact exists
 ↓
Validate service health
 ↓
Root cause analysis
 ↓
Permanent corrective action
```

---

## Q10. How would you explain AKS in an interview?

> AKS gives us managed Kubernetes on Azure. For NexCart, GitLab handles CI/CD, ACR stores immutable container images, and AKS runs the workloads. Helm provides repeatable deployment configuration. Kubernetes Deployments manage replicas, Services provide stable internal networking, ingress handles external routing, HPA handles application scaling, and Azure identity integration provides secure access to Azure resources without embedding long-lived credentials in containers. My focus is not just deploying workloads but operating the cluster safely through upgrades, scaling, observability, security, rollback, and day-2 troubleshooting.

---

# 55. Senior AKS Checklist

```text
Architecture
✓ AKS cluster
✓ System/user node pools
✓ Dedicated namespaces
✓ Multi-replica workloads

Deployment
✓ GitLab CI/CD
✓ ACR
✓ Helm
✓ Immutable image tags/digests
✓ Rolling deployments
✓ Rollback

Networking
✓ Services
✓ Ingress
✓ DNS
✓ Load Balancer
✓ NetworkPolicy where required

Security
✓ Azure RBAC
✓ Kubernetes RBAC
✓ Managed Identity
✓ Workload Identity
✓ Key Vault
✓ Image scanning
✓ Least privilege

Reliability
✓ Readiness probe
✓ Liveness probe
✓ Startup probe
✓ HPA
✓ Cluster Autoscaler
✓ PDB
✓ Resource requests/limits

Observability
✓ Logs
✓ Metrics
✓ Traces
✓ Events
✓ Alerts
✓ Application health

Operations
✓ AKS upgrades
✓ Node pool management
✓ Certificate rotation
✓ Secret rotation
✓ Image cleanup
✓ Cost optimization
✓ Disaster recovery
✓ Incident response
```

# 56. Final NexCart AKS Flow

```text
                         USERS
                           |
                           v
                  Azure Load Balancer
                           |
                           v
                     Ingress Layer
                           |
            +--------------+--------------+
            |              |              |
            v              v              v
       Product         Order          Payment
       Service         Service        Service
            |              |              |
            +--------------+--------------+
                           |
                           v
                    Notification
                       Service

                           |
                    Kubernetes Cluster
                           |
       +-------------------+-------------------+
       |                   |                   |
   Deployments           Services            HPA
       |                   |                   |
      Pods             ClusterIP          Autoscaling
       |
       +---- ConfigMap
       |
       +---- ServiceAccount
       |
       +---- Workload Identity
       |
       +---- Key Vault
       |
       +---- Observability

CI/CD:
GitLab
  ↓
Test
  ↓
Security Scan
  ↓
Docker Build
  ↓
ACR
  ↓
Helm
  ↓
AKS
  ↓
Production
```

## Core Senior Principle

> **AKS is not just a place to run containers. Production Kubernetes engineering means controlling the complete lifecycle: secure image supply chain, identity, networking, deployment, scaling, observability, upgrades, rollback, reliability, and day-2 operations.**