---
title: "14-production-troubleshooting"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 14
---

# 🚨 14. Production Troubleshooting – NexCart

## 1. Production Troubleshooting Philosophy

Production troubleshooting should be systematic, evidence-based and risk-controlled.

The objective is not:

```text
"Find something and restart it."
```

The objective is:

```text
Detect
 ↓
Assess Impact
 ↓
Stabilize
 ↓
Collect Evidence
 ↓
Identify Root Cause
 ↓
Fix
 ↓
Validate
 ↓
Prevent Recurrence
```

Senior engineers should avoid random changes during an incident.

---

# 2. First 5 Questions During an Incident

Before changing anything, answer:

```text
1. What is failing?
2. Who is impacted?
3. When did it start?
4. What changed immediately before the issue?
5. Is the issue application, Kubernetes, network, Azure, database or dependency related?
```

Example:

```text
Order API
   ↓
HTTP 500
   ↓
Started 10 minutes ago
   ↓
Deployment happened 15 minutes ago
   ↓
Check latest deployment first
```

---

# 3. Production Troubleshooting Framework

Use this sequence:

```text
User
 ↓
DNS
 ↓
Ingress / Load Balancer
 ↓
Service
 ↓
Pod
 ↓
Application
 ↓
Dependency
 ↓
Database / External Service
 ↓
Infrastructure
```

For every layer check:

```text
Availability
Connectivity
Latency
Errors
Capacity
Recent Changes
```

---

# 4. Incident Severity

A practical classification:

| Severity | Example |
|---|---|
| P1 | Complete production outage |
| P2 | Major functionality unavailable |
| P3 | Limited functionality or degraded performance |
| P4 | Minor issue / non-urgent defect |

Example:

```text
All checkout requests failing
        ↓
P1/P2 depending on business impact
```

---

# 5. Incident Roles

For a major incident, separate responsibilities.

```text
Incident Commander
       |
       +--- Application
       +--- Kubernetes
       +--- Network
       +--- Database
       +--- Cloud
       +--- Communications
```

The Incident Commander should coordinate the incident rather than personally perform every technical action.

---

# 6. Check Recent Changes First

One of the highest-value production checks is:

```text
"What changed?"
```

Check:

```text
Application deployment
Docker image
Helm values
ConfigMap
Secret
Ingress
NetworkPolicy
Terraform
Azure configuration
Database migration
Certificate
Dependency
```

Useful commands:

```bash
kubectl rollout history deploy/<deployment> -n nexcart
kubectl get events -n nexcart --sort-by=.lastTimestamp
helm history <release> -n nexcart
```

---

# 7. Production Health Overview

Start broad:

```bash
kubectl get nodes
kubectl get ns
kubectl get pods -n nexcart -o wide
kubectl get deploy -n nexcart
kubectl get svc -n nexcart
kubectl get ingress -n nexcart
```

Then narrow down to the failing component.

---

# 8. Check Cluster Health

```bash
kubectl get nodes
```

Expected:

```text
STATUS
Ready
```

Check detailed node information:

```bash
kubectl describe node <node>
```

Check resource usage:

```bash
kubectl top nodes
```

Look for:

```text
CPU saturation
Memory pressure
Disk pressure
PID pressure
NotReady nodes
```

---

# 9. Node NotReady

### Symptoms

```text
Node status = NotReady
Pods affected
Scheduling problems
Application degradation
```

Check:

```bash
kubectl get nodes
kubectl describe node <node>
kubectl get events --sort-by=.lastTimestamp
```

Look for:

```text
MemoryPressure
DiskPressure
PIDPressure
NetworkUnavailable
Kubelet problems
```

### Root causes

```text
OS issue
Disk full
Memory pressure
Network issue
Node failure
Container runtime problem
Azure infrastructure issue
```

---

# 10. Pod Status Check

```bash
kubectl get pods -n nexcart -o wide
```

Common states:

```text
Running
Pending
CrashLoopBackOff
ImagePullBackOff
ErrImagePull
OOMKilled
Completed
ContainerCreating
Terminating
```

Pod status is only the starting point.

Always inspect the details for unexpected states.

---

# 11. CrashLoopBackOff

### Symptoms

```text
Pod repeatedly starts and exits
```

Check:

```bash
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
```

Important:

```text
--previous
```

is extremely useful because the current container may already have restarted.

### Possible root causes

```text
Application startup failure
Invalid configuration
Missing environment variable
Database connection failure
Port conflict
Dependency unavailable
Incorrect command
Certificate problem
```

---

# 12. ImagePullBackOff

### Symptoms

```text
Pod cannot pull Docker image
```

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

Look at Events.

Check image:

```bash
kubectl get pod <pod> \
  -n nexcart \
  -o jsonpath='{.spec.containers[*].image}'
```

Check ACR:

```bash
az acr repository list \
  --name <acr> \
  -o table

az acr repository show-tags \
  --name <acr> \
  --repository <repo> \
  -o table
```

### Common causes

```text
Wrong image name
Wrong tag
Image does not exist
ACR permission missing
Network restriction
Registry authentication issue
```

---

# 13. OOMKilled

### Meaning

The container exceeded its memory limit.

Check:

```bash
kubectl describe pod <pod> -n nexcart
kubectl top pod <pod> -n nexcart
```

Review:

```yaml
resources:
  requests:
    memory: "256Mi"
  limits:
    memory: "512Mi"
```

### Root causes

```text
Memory leak
Insufficient memory limit
Traffic increase
Large request
Large cache
Incorrect JVM/Node/Python memory configuration
```

Do not simply increase the limit without understanding the memory behavior.

---

# 14. Pod Pending

### Symptoms

```text
Pod remains Pending
```

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

Look at scheduling events.

Common causes:

```text
Insufficient CPU
Insufficient memory
Node selector mismatch
Taints/tolerations
Affinity rules
PVC unavailable
Resource quota
No suitable node
```

Check:

```bash
kubectl get nodes
kubectl describe nodes
kubectl get pvc -n nexcart
```

---

# 15. Deployment Rollout Failure

Check:

```bash
kubectl rollout status \
  deploy/<deployment> \
  -n nexcart
```

Then:

```bash
kubectl rollout history \
  deploy/<deployment> \
  -n nexcart
```

Check:

```bash
kubectl get rs -n nexcart
kubectl get pods -n nexcart
```

Common causes:

```text
Bad image
Failed readiness probe
Invalid configuration
Insufficient resources
Secret missing
ConfigMap incorrect
Application startup failure
```

---

# 16. Rollback a Deployment

If the new release is clearly causing an outage:

```bash
kubectl rollout undo \
  deployment/<deployment> \
  -n nexcart
```

Verify:

```bash
kubectl rollout status \
  deployment/<deployment> \
  -n nexcart
```

Then validate:

```bash
kubectl get pods -n nexcart
```

Rollback is a stabilization mechanism, not the final root-cause fix.

---

# 17. Helm Production Rollback

Check history:

```bash
helm history <release> -n nexcart
```

Rollback:

```bash
helm rollback \
  <release> \
  <revision> \
  -n nexcart
```

Check:

```bash
helm status <release> -n nexcart
```

For safer production deployments:

```bash
helm upgrade --install \
  <release> \
  ./helm/nexcart \
  -n nexcart \
  -f values-prod.yaml \
  --wait \
  --atomic
```

---

# 18. Service Not Working

Check Service:

```bash
kubectl get svc -n nexcart
kubectl describe svc <service> -n nexcart
```

Check endpoints:

```bash
kubectl get endpoints <service> -n nexcart
```

or:

```bash
kubectl get endpointslice \
  -n nexcart
```

If the Service has no endpoints, investigate:

```text
Selector
 ↓
Pod labels
 ↓
Pod readiness
```

---

# 19. Service Selector Problem

Example:

Service:

```yaml
selector:
  app: product-service
```

Pod:

```yaml
labels:
  app: product
```

Result:

```text
Service
   ↓
No matching Pods
   ↓
No endpoints
   ↓
Traffic fails
```

Check:

```bash
kubectl get pods \
  -n nexcart \
  --show-labels
```

---

# 20. Port Mismatch

Common issue:

```text
Service port
      ≠
targetPort
      ≠
containerPort
```

Check:

```bash
kubectl get svc <service> -n nexcart -o yaml
kubectl get deploy <deployment> -n nexcart -o yaml
```

For NexCart, verify each service's actual application port before changing the Kubernetes configuration.

---

# 21. Ingress 404

Possible causes:

```text
Wrong host
Wrong path
Ingress rule mismatch
Backend Service incorrect
Backend port incorrect
Ingress controller issue
```

Check:

```bash
kubectl get ingress -n nexcart
kubectl describe ingress <ingress> -n nexcart
kubectl get svc -n nexcart
```

Test the backend Service independently before troubleshooting the external route.

---

# 22. Ingress 502

A `502 Bad Gateway` commonly means the proxy/ingress cannot successfully reach the backend.

Check:

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
kubectl describe ingress <ingress> -n nexcart
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get pods -n nexcart
```

Then test the Service from inside the cluster.

---

# 23. DNS Troubleshooting

Kubernetes Services normally use DNS.

Example:

```text
product-service.nexcart.svc.cluster.local
```

Check DNS from a pod:

```bash
kubectl exec -it <pod> \
  -n nexcart \
  -- nslookup product-service
```

If `nslookup` is unavailable, use an appropriate diagnostic container.

Check:

```text
Service name
Namespace
CoreDNS
NetworkPolicy
Service existence
```

---

# 24. Service-to-Service Connectivity

Example:

```text
order-service
      ↓
payment-service
```

Test from inside the cluster:

```bash
kubectl exec -it <pod> \
  -n nexcart \
  -- curl http://payment-service:8084/health
```

Check:

```text
DNS
Service
Endpoints
Port
NetworkPolicy
Application listener
```

---

# 25. NetworkPolicy Issue

Symptoms:

```text
Service exists
Pods are healthy
Endpoints exist
But traffic is blocked
```

Check:

```bash
kubectl get networkpolicy -n nexcart
kubectl describe networkpolicy <policy> -n nexcart
```

Temporarily changing security controls should follow the incident process and be carefully assessed.

---

# 26. Application HTTP 500

A `500` means the application encountered an internal failure.

Start with:

```bash
kubectl logs <pod> -n nexcart
```

Then correlate:

```text
Request
 ↓
Application log
 ↓
Trace ID
 ↓
Dependency
 ↓
Database
```

Look for:

```text
Exception
Timeout
Connection failure
Authentication failure
Invalid input
Database error
```

---

# 27. HTTP 401 vs 403

### 401 Unauthorized

Usually indicates:

```text
Authentication missing/invalid
```

### 403 Forbidden

Usually indicates:

```text
Authentication succeeded
but authorization failed
```

For Azure/Key Vault:

```text
Identity
 ↓
Authentication
 ↓
RBAC authorization
```

Check both layers.

---

# 28. Database Connectivity Issue

Symptoms:

```text
500 errors
Timeouts
Connection refused
Application startup failure
```

Check application logs first.

Then verify:

```text
DNS
Network connectivity
Firewall
Private Endpoint
Credentials
TLS
Database availability
Connection pool
```

Do not immediately rotate credentials unless evidence indicates an authentication problem.

---

# 29. Database Connection Pool Exhaustion

Symptoms:

```text
Requests waiting
High latency
Timeouts
Database appears healthy
```

Check:

```text
Active connections
Connection pool size
Application concurrency
Slow queries
Long-running transactions
Connection leaks
```

Increasing the pool blindly can overload the database.

---

# 30. External Dependency Failure

Example:

```text
Payment Service
      ↓
External Payment API
      ↓
Timeout
```

Check:

```text
DNS
Network
TLS
Authentication
HTTP status
Latency
Provider status
Timeouts
Retries
```

Use bounded retries and circuit-breaking behavior where appropriate.

---

# 31. High Latency

Start with:

```text
Is traffic increased?
Did deployment change?
Are pods CPU/memory constrained?
Is dependency latency high?
Is database slow?
Is network latency high?
```

Check:

```bash
kubectl top pods -n nexcart
kubectl top nodes
```

Then use traces to identify where time is being spent.

---

# 32. CPU Saturation

Check:

```bash
kubectl top pods -n nexcart
kubectl top nodes
```

Look for:

```text
CPU near limits
CPU throttling
High request volume
Inefficient code
Expensive database queries
```

Possible actions:

```text
Scale horizontally
Tune resource requests/limits
Optimize application
Optimize database
Investigate traffic pattern
```

---

# 33. Memory Pressure

Check:

```bash
kubectl top nodes
kubectl top pods -n nexcart
```

Look for:

```text
OOMKilled
MemoryPressure
Container restarts
Increasing memory usage
```

Possible causes:

```text
Memory leak
Large payload
Cache growth
Traffic increase
Incorrect runtime configuration
```

---

# 34. HPA Troubleshooting

Check:

```bash
kubectl get hpa -n nexcart
kubectl describe hpa <hpa> -n nexcart
```

Verify:

```text
Metrics available
CPU/memory requests configured
Target utilization
Min replicas
Max replicas
Current replicas
```

HPA cannot make a correct scaling decision if the required metrics are unavailable or resource configuration is incorrect.

---

# 35. Cluster Autoscaler Troubleshooting

If pods remain Pending even though HPA increased replicas:

```text
HPA
 ↓
More Pods
 ↓
Insufficient node capacity
 ↓
Cluster Autoscaler
 ↓
New Node
```

Check:

```text
Node pool limits
VM capacity
Quota
Pod scheduling constraints
Taints
Affinity
Subnet/IP capacity
```

---

# 36. Certificate Expiry Incident

Symptoms:

```text
TLS handshake failure
Certificate expired
Browser security warning
API clients failing
```

Check:

```text
Certificate expiry
Certificate chain
Secret
Ingress
Renewal mechanism
DNS
```

If renewal is automated, investigate why renewal failed instead of manually replacing the certificate without understanding the automation.

---

# 37. Key Vault Access Failure

Symptoms:

```text
Application cannot retrieve secret
403 Forbidden
Authentication error
```

Check:

```text
1. ServiceAccount
2. Workload Identity configuration
3. Federated identity
4. Azure identity
5. Key Vault RBAC
6. Key Vault network access
7. Secret name/version
```

Commands:

```bash
kubectl get sa -n nexcart
kubectl describe pod <pod> -n nexcart

az role assignment list \
  --assignee <principal-id> \
  -o table
```

---

# 38. ACR Access Failure

Symptoms:

```text
ImagePullBackOff
unauthorized
manifest unknown
```

Check:

```text
Image repository
Image tag
Digest
AKS identity
AcrPull permission
Registry network access
```

Do not immediately create an `imagePullSecret` if AKS is already designed to use managed identity for ACR access.

---

# 39. Configuration Problem

Production configuration may come from:

```text
Helm values
ConfigMap
Secret
Key Vault
Environment variables
Azure configuration
```

Compare:

```text
Expected configuration
        vs
Actual configuration
```

Useful:

```bash
kubectl describe pod <pod> -n nexcart
kubectl get configmap -n nexcart
kubectl get secret -n nexcart
helm get values <release> -n nexcart
```

Never print secret values into incident channels or logs.

---

# 40. ConfigMap Change Incident

A ConfigMap change can cause application behavior to change without a new image.

Check:

```bash
kubectl get configmap -n nexcart
kubectl describe configmap <configmap> -n nexcart
```

Then correlate:

```text
ConfigMap change
 ↓
Pod restart
 ↓
Application behavior
```

Use version-controlled configuration and controlled deployment processes.

---

# 41. Secret Rotation Incident

Symptoms:

```text
Application suddenly receives authentication failures
```

Possible cause:

```text
Secret rotated
      ↓
Application still using old credential
```

Check:

```text
Secret version
Application configuration
Pod restart/reload behavior
Database credential
Key Vault version
```

Design secret rotation so applications can transition safely.

---

# 42. Deployment Succeeded but Application Is Down

This is a common senior-level troubleshooting scenario.

Do not assume:

```text
Deployment succeeded = Application healthy
```

Check:

```text
Deployment
 ↓
Pods
 ↓
Readiness
 ↓
Service
 ↓
Endpoints
 ↓
Ingress
 ↓
Application
 ↓
Dependencies
```

A Kubernetes deployment can succeed while the application remains functionally broken.

---

# 43. Readiness Probe Failure

Symptoms:

```text
Pod Running
but
Pod not Ready
```

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

Possible causes:

```text
Wrong endpoint
Wrong port
Slow startup
Dependency unavailable
Application unhealthy
Probe timeout too low
```

Do not simply disable the probe.

---

# 44. Liveness Probe Failure

Symptoms:

```text
Container repeatedly restarts
```

Check:

```bash
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
```

Possible causes:

```text
Incorrect health endpoint
Deadlock
Application hung
Probe too aggressive
Temporary dependency issue
```

A poorly designed liveness probe can itself cause an outage.

---

# 45. Startup Probe

Use a startup probe when an application needs significant initialization time.

Flow:

```text
Container starts
       ↓
Startup Probe
       ↓
Application initializes
       ↓
Readiness Probe
       ↓
Traffic allowed
```

This prevents Kubernetes from killing slow-starting applications prematurely.

---

# 46. Log Troubleshooting

Basic:

```bash
kubectl logs <pod> -n nexcart
```

Previous container:

```bash
kubectl logs <pod> \
  -n nexcart \
  --previous
```

Multiple containers:

```bash
kubectl logs <pod> \
  -n nexcart \
  -c <container>
```

Follow logs:

```bash
kubectl logs -f <pod> -n nexcart
```

Avoid relying only on logs during distributed incidents.

Correlate logs with metrics and traces.

---

# 47. Events Are Extremely Important

Check:

```bash
kubectl get events \
  -n nexcart \
  --sort-by=.lastTimestamp
```

Events can reveal:

```text
Scheduling failures
Image pull errors
Mount failures
Probe failures
Node problems
Admission errors
```

For Kubernetes troubleshooting, Events are often one of the fastest sources of evidence.

---

# 48. Observability Troubleshooting

Use the three pillars:

```text
Metrics
Logs
Traces
```

Example:

```text
Metrics
  ↓
Latency increased
  ↓
Logs
  ↓
Database timeout
  ↓
Trace
  ↓
Payment API waiting 4 seconds on DB call
```

This reduces guesswork.

---

# 49. Golden Signals

Monitor:

```text
Latency
Traffic
Errors
Saturation
```

Example:

```text
Latency ↑
Errors ↑
CPU normal
Traffic normal
        ↓
Investigate dependency/database
```

---

# 50. Deployment Correlation

Always correlate incidents with:

```text
Commit
Pipeline
Docker image
Helm release
Kubernetes rollout
Configuration change
```

Example:

```text
14:00 Deployment
14:05 Error rate increases
14:06 Rollback
14:07 Error rate normal
```

This is strong evidence that the deployment caused the issue.

---

# 51. Production Rollback Decision

Rollback when:

```text
New release is strongly correlated with outage
AND
Previous version is known-good
AND
Rollback is lower risk than debugging live
```

Flow:

```text
Incident
 ↓
Confirm recent release
 ↓
Rollback
 ↓
Restore service
 ↓
Investigate root cause
 ↓
Fix
 ↓
Test
 ↓
Redeploy
```

---

# 52. Terraform Production Incident

Never blindly run:

```bash
terraform apply
```

during an incident.

First:

```bash
terraform plan
```

Review:

```text
What will change?
Will resources be replaced?
Will production connectivity be affected?
Will state drift be reconciled?
```

If infrastructure caused the incident, restore service safely before performing broad infrastructure changes.

---

# 53. Terraform Drift

Check:

```bash
terraform plan
```

If unexpected changes appear:

```text
1. Identify manual change
2. Identify intended state
3. Review Terraform state
4. Decide source of truth
5. Reconcile safely
```

Do not blindly overwrite a legitimate emergency production change.

---

# 54. Database Migration Failure

Scenario:

```text
Deployment
 ↓
Database migration
 ↓
Migration fails
 ↓
Application incompatible
```

Check:

```text
Migration logs
Database state
Application version
Schema compatibility
Rollback capability
```

Prefer backward-compatible migrations:

```text
Expand
 ↓
Deploy compatible application
 ↓
Migrate data
 ↓
Contract/remove old schema
```

---

# 55. Zero-Downtime Deployment

A production deployment should ideally follow:

```text
Old Version
    ↓
New Version
    ↓
Readiness
    ↓
Traffic
    ↓
Old Version Removed
```

Use:

```text
RollingUpdate
Readiness probes
PodDisruptionBudget
Multiple replicas
Graceful shutdown
```

---

# 56. Graceful Shutdown

Applications should handle termination correctly.

Flow:

```text
SIGTERM
 ↓
Stop accepting new requests
 ↓
Finish active requests
 ↓
Close connections
 ↓
Exit
```

Without graceful shutdown:

```text
Deployment
 ↓
Pod terminated
 ↓
Active request interrupted
 ↓
5xx / failed transaction
```

---

# 57. PodDisruptionBudget

PDB helps maintain application availability during voluntary disruptions.

Example:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
spec:
  minAvailable: 2
```

Use it carefully.

A PDB should not prevent necessary cluster maintenance indefinitely.

---

# 58. Production Debugging Do Not's

Avoid:

```text
❌ Restart everything
❌ Delete random pods without evidence
❌ Change multiple things simultaneously
❌ Disable security controls permanently
❌ Increase resources blindly
❌ Delete Kubernetes objects without backup/understanding
❌ Run Terraform apply without reviewing plan
❌ Expose secrets during debugging
❌ Modify production manually without recording the change
❌ Assume Kubernetes Running means application healthy
```

---

# 59. Safe Production Debugging

Prefer:

```text
Evidence
 ↓
Smallest Safe Change
 ↓
Observe
 ↓
Validate
 ↓
Next Action
```

Every production change should have:

```text
Reason
Owner
Timestamp
Expected outcome
Rollback plan
```

---

# 60. Common Incident Matrix

| Symptom | First Check | Likely Areas |
|---|---|---|
| 500 | Application logs | App/DB/dependency |
| 502 | Ingress → Service → endpoints | Service/network |
| 503 | Service/readiness | Pods/service |
| 404 | Ingress path/route | Ingress |
| ImagePullBackOff | Pod events | ACR/image |
| CrashLoopBackOff | Previous logs | Application/config |
| OOMKilled | Pod resources | Memory/app |
| Pending | Pod events | Scheduler/resources |
| Node NotReady | Node conditions | AKS/node |
| High latency | Metrics/traces | App/DB/network |
| DNS failure | Service/CoreDNS | Kubernetes DNS |
| 401 | Authentication | Token/identity |
| 403 | Authorization | RBAC/permissions |
| TLS error | Certificate | Ingress/cert |
| Key Vault 403 | Identity/RBAC | Workload Identity |
| ACR unauthorized | Identity/AcrPull | AKS/ACR |
| Pods Ready, API down | Service/Ingress | Networking/routing |

---

# 61. End-to-End Troubleshooting Example

## Scenario

Users report:

```text
Checkout API is returning 500.
```

### Step 1 — Confirm impact

```text
Is all checkout traffic failing?
Only one region?
Only one API?
Only one customer?
```

### Step 2 — Check metrics

```text
Error rate
Latency
Request rate
Pod restarts
CPU
Memory
```

### Step 3 — Check recent deployment

```bash
kubectl rollout history \
  deploy/order-service \
  -n nexcart
```

### Step 4 — Check pods

```bash
kubectl get pods -n nexcart -o wide
```

### Step 5 — Check logs

```bash
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
```

### Step 6 — Check dependency

```text
order-service
      ↓
payment-service
      ↓
database/external API
```

### Step 7 — Stabilize

If the latest release is confirmed as the cause:

```bash
kubectl rollout undo \
  deployment/order-service \
  -n nexcart
```

### Step 8 — Validate

```bash
kubectl rollout status \
  deployment/order-service \
  -n nexcart
```

Then verify:

```text
API
Logs
Metrics
Traces
Business transaction
```

### Step 9 — Root cause

Example:

```text
New deployment introduced an incompatible
payment-service API contract.
```

### Step 10 — Prevent recurrence

```text
Contract tests
Integration tests
API versioning
Deployment validation
Canary/blue-green strategy
Better observability
```

---

# 62. Production Troubleshooting Decision Tree

```text
                    Incident
                       |
                       v
                 Is service reachable?
                  /              \
                NO                YES
                |                  |
             Ingress             5xx?
             Service             /   \
             Network           YES    NO
                               |       |
                           App/DB    Latency?
                           logs      /     \
                                   YES      NO
                                   |         |
                              Metrics/     Business
                              traces       logic
```

Then investigate from:

```text
External
 ↓
Ingress
 ↓
Service
 ↓
Pod
 ↓
Application
 ↓
Dependency
 ↓
Infrastructure
```

---

# 63. Production Incident Runbook

```text
Incident:
Start Time:
Detected By:
Severity:
Affected Service:
Affected Environment:

Customer Impact:
Business Impact:

Recent Changes:
Deployment:
Image:
Helm Release:
Configuration:
Infrastructure:

Current Symptoms:

Initial Hypothesis:

Actions Taken:

Rollback Required:
Yes / No

Root Cause:

Resolution:

Validation:

Follow-up Actions:

Owner:

Deadline:
```

---

# 64. Post-Incident Review

After recovery:

```text
What happened?
Why did it happen?
Why was it not detected earlier?
Why did existing controls not prevent it?
How quickly was it detected?
How quickly was it mitigated?
What should be automated?
```

Track:

```text
MTTD
MTTR
Error budget impact
Customer impact
Preventive actions
```

---

# 65. MTTD and MTTR

### MTTD

Mean Time To Detect.

```text
Failure
 ↓
Detection
```

### MTTR

Mean Time To Recovery/Repair.

```text
Failure
 ↓
Recovery
```

Senior DevOps teams try to improve both through:

```text
Automation
Observability
Runbooks
Alerting
Safe rollback
Testing
```

---

# 66. Production Troubleshooting Command Bank

```bash
# Cluster
kubectl get nodes
kubectl describe node <node>
kubectl top nodes

# Pods
kubectl get pods -n nexcart -o wide
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
kubectl logs -f <pod> -n nexcart

# Events
kubectl get events \
  -n nexcart \
  --sort-by=.lastTimestamp

# Deployment
kubectl get deploy -n nexcart
kubectl rollout status deploy/<deployment> -n nexcart
kubectl rollout history deploy/<deployment> -n nexcart
kubectl rollout undo deploy/<deployment> -n nexcart

# Service
kubectl get svc -n nexcart
kubectl describe svc <service> -n nexcart
kubectl get endpoints -n nexcart
kubectl get endpointslice -n nexcart

# Ingress
kubectl get ingress -n nexcart
kubectl describe ingress <ingress> -n nexcart

# Resources
kubectl top pods -n nexcart
kubectl top nodes

# HPA
kubectl get hpa -n nexcart
kubectl describe hpa <hpa> -n nexcart

# Config
kubectl get configmap -n nexcart
kubectl get secret -n nexcart
helm get values <release> -n nexcart

# Helm
helm list -n nexcart
helm status <release> -n nexcart
helm history <release> -n nexcart
helm rollback <release> <revision> -n nexcart

# Permissions
kubectl auth can-i get secrets \
  -n nexcart \
  --as=system:serviceaccount:nexcart:<sa>
```

---

# 67. Senior Interview Questions

### Q1. How do you troubleshoot a production outage?

> I first establish customer impact and severity, then check recent changes and the complete request path from ingress to application dependencies. I use metrics, logs, traces and Kubernetes events to form an evidence-based hypothesis. If a recent deployment is clearly responsible and the previous version is healthy, I stabilize the service through rollback, then perform root-cause analysis and implement preventive actions.

---

### Q2. A pod is Running but the application is unavailable. What do you check?

> I don't consider Running as healthy. I check readiness, Service selectors, endpoints, application listening port, ingress routing, network policies and application logs. I also test the Service from inside the cluster to isolate whether the issue is application-level or networking-level.

---

### Q3. How do you troubleshoot CrashLoopBackOff?

> I start with `kubectl describe pod`, then check current and previous container logs. I inspect exit codes, environment variables, ConfigMaps, Secrets, probes, resource limits and dependency connectivity. The previous logs are particularly important because the container may have already restarted.

---

### Q4. How do you troubleshoot HTTP 502?

> I trace the request path from ingress to Service, endpoints and Pod. I verify the ingress backend, Service selector, targetPort, endpoint availability, pod readiness and application listener. If those are healthy, I investigate network policy, TLS or ingress-controller behavior.

---

### Q5. How do you decide whether to rollback?

> I look for strong correlation between the release and the incident, confirm that the previous version is known-good and evaluate whether rollback is lower risk than debugging the new release in production. Rollback restores service, but I still perform root-cause analysis afterward.

---

### Q6. How do you troubleshoot high latency?

> I start with latency, traffic, errors and saturation metrics. Then I use distributed traces to identify which service or dependency consumes the latency budget. I check CPU, memory, database performance, external API latency, network behavior and recent deployments before deciding on scaling or code/configuration changes.

---

### Q7. What is your approach when several teams are involved?

> I establish an Incident Commander, define the customer impact, assign focused investigation areas and maintain a timeline. I avoid multiple people changing the same environment simultaneously. Evidence and decisions are recorded so the team can work in parallel without losing control of the incident.

---

### Q8. How do you troubleshoot a Key Vault 403?

> I separate authentication from authorization. First I identify which workload identity the pod is using, then validate the ServiceAccount and federated identity configuration. After that I verify Azure RBAC on Key Vault and finally check network access and the requested secret.

---

### Q9. How do you troubleshoot ImagePullBackOff?

> I inspect pod events first. I verify the exact image repository and tag, confirm that the image exists in ACR, check the AKS identity and AcrPull permission, and then investigate registry network connectivity. I avoid changing authentication mechanisms before confirming the actual failure.

---

### Q10. What is the difference between fixing an incident and finding the root cause?

> Incident mitigation restores service as quickly and safely as possible. Root-cause analysis explains why the failure happened and why our existing controls didn't prevent or detect it earlier. A rollback may mitigate the incident, but it doesn't necessarily fix the underlying defect.

---

# 68. Production Troubleshooting Golden Rules

```text
1. Stabilize first, investigate second when customer impact is severe.
2. Check recent changes early.
3. Follow the request path layer by layer.
4. Use evidence instead of assumptions.
5. Check Events, Logs, Metrics and Traces together.
6. Never expose secrets while debugging.
7. Make the smallest safe production change.
8. Record every significant production action.
9. Roll back when rollback is safer than live debugging.
10. Do not confuse Pod Running with application health.
11. Do not restart everything without understanding the failure.
12. Validate after every corrective action.
13. Separate mitigation from root-cause analysis.
14. Automate repeated troubleshooting where practical.
15. Every major incident should produce preventive actions.
```

---

# 69. Final NexCart Production Troubleshooting Flow

```text
                   Customer Issue
                         |
                         v
                   Check Impact
                         |
                         v
                 Check Recent Changes
                         |
                         v
                Metrics / Alerts
                         |
                         v
                     Ingress
                         |
                         v
                      Service
                         |
                         v
                     Endpoints
                         |
                         v
                       Pods
                         |
             +-----------+-----------+
             |                       |
          Logs                     Events
             |                       |
             +-----------+-----------+
                         |
                         v
                    Application
                         |
                         v
                   Dependencies
                         |
              +----------+----------+
              |                     |
           Database            External API
              |                     |
              +----------+----------+
                         |
                         v
                  Infrastructure
                         |
                         v
                    Root Cause
                         |
                         v
                  Fix / Rollback
                         |
                         v
                     Validate
                         |
                         v
                Post-Incident Review
                         |
                         v
                 Prevent Recurrence
```

## Core Senior DevOps Principle

> **Production troubleshooting is not about knowing the most commands. It is about isolating the failure domain quickly, minimizing customer impact, making controlled changes, validating the recovery, and converting every major incident into an engineering improvement.**