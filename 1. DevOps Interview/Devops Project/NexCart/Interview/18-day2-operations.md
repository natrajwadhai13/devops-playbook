---
title: "18-day2-operations"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 18
---

# ⚙️ 18. Day-2 Operations – NexCart

## 1. What Is Day-2 Operations?

Day-2 operations means everything required **after the application has been successfully deployed to production**.

```text
Day 0
Design + Infrastructure

Day 1
Build + Deploy

Day 2
Operate + Monitor + Maintain + Improve
```

For NexCart:

```text
Deploy
  ↓
Monitor
  ↓
Troubleshoot
  ↓
Scale
  ↓
Patch
  ↓
Rotate
  ↓
Upgrade
  ↓
Backup
  ↓
Optimize
  ↓
Improve
```

A production environment is never "finished."

---

# 2. Day-2 Operations Areas

```text
┌─────────────────────────────────────────┐
│           NexCart Day-2 Operations      │
├─────────────────────────────────────────┤
│ Monitoring & Alerting                   │
│ Incident Management                     │
│ Scaling & Capacity                      │
│ Kubernetes Maintenance                  │
│ Application Releases                    │
│ Certificate Rotation                    │
│ Secret Rotation                         │
│ Backup & DR                             │
│ Security & Vulnerability Management     │
│ Cost Optimization                       │
│ Performance Optimization                │
│ Infrastructure Changes                  │
│ Access & RBAC                            │
│ Documentation & Runbooks                │
│ Capacity Planning                        │
└─────────────────────────────────────────┘
```

---

# 3. Daily Production Health Check

Start with a quick health snapshot.

```bash
kubectl get nodes
kubectl get pods -A
kubectl get pods -n nexcart -o wide
kubectl get deploy,svc,ingress -n nexcart
```

Check events:

```bash
kubectl get events -A --sort-by=.lastTimestamp
```

Check resource usage:

```bash
kubectl top nodes
kubectl top pods -n nexcart
```

Check Helm:

```bash
helm list -n nexcart
```

Look for:

```text
CrashLoopBackOff
ImagePullBackOff
Pending
OOMKilled
Evicted
NotReady
High CPU
High memory
Failed deployments
Recent warning events
```

---

# 4. Production Health Dashboard

A good operations dashboard should show:

```text
Cluster
 ├── Node health
 ├── CPU
 ├── Memory
 └── Disk

Application
 ├── Request rate
 ├── Error rate
 ├── Latency
 └── Availability

Pods
 ├── Running
 ├── Restarting
 └── Pending

Dependencies
 ├── Database
 ├── External APIs
 └── Queues

Business
 ├── Orders
 ├── Payments
 └── Failed transactions
```

---

# 5. Golden Signals

Monitor:

```text
Latency
Traffic
Errors
Saturation
```

Example:

```text
Product API
Latency     → 120 ms
Traffic     → 800 req/s
Error rate  → 0.3%
CPU         → 55%
Memory      → 62%
```

The goal is to understand both infrastructure health and user experience.

---

# 6. Application Health

Each NexCart service should expose a health endpoint where appropriate.

Example:

```text
GET /health
```

Expected:

```json
{
  "service": "product-service",
  "status": "UP"
}
```

Use health checks for:

```text
Kubernetes probes
Smoke tests
Monitoring
Synthetic checks
Deployment validation
```

---

# 7. Readiness and Liveness

Production Pods should use appropriate probes.

```text
Startup
   ↓
Startup Probe
   ↓
Application Ready
   ↓
Readiness Probe
   ↓
Traffic
```

Liveness determines whether Kubernetes should restart the container.

Readiness determines whether the Pod should receive traffic.

Incorrect probes can create unnecessary restarts and outages.

---

# 8. Pod Restart Monitoring

Check:

```bash
kubectl get pods -n nexcart
```

Detailed:

```bash
kubectl get pods -n nexcart \
  -o custom-columns=NAME:.metadata.name,RESTARTS:.status.containerStatuses[*].restartCount
```

A restart is not automatically an incident.

Investigate:

```text
OOMKilled
Application crash
Probe failure
Node failure
Deployment
Manual restart
Container runtime issue
```

---

# 9. Resource Management

Every production workload should have resource requests and limits.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "512Mi"
```

Requests influence scheduling.

Limits constrain maximum resource usage.

Poor resource settings can cause:

```text
Pending Pods
CPU throttling
OOMKilled
Poor performance
Unnecessary scaling
```

---

# 10. Resource Right-Sizing

Do not choose resources randomly.

Use historical metrics.

Example:

```text
Current request:
CPU    1 CPU
Memory 2 GB

Observed 30-day usage:
CPU    150-300m
Memory 300-500 MB
```

Potentially:

```text
New request:
CPU    300-500m
Memory 512-768 MB
```

But validate under peak traffic before changing production values.

---

# 11. HPA Operations

Check:

```bash
kubectl get hpa -n nexcart
```

Detailed:

```bash
kubectl describe hpa <hpa-name> -n nexcart
```

Review:

```text
Current replicas
Desired replicas
CPU utilization
Memory utilization
Min replicas
Max replicas
Scaling events
```

If HPA continuously stays at maximum:

```text
Investigate capacity.
```

Do not simply increase `maxReplicas`.

---

# 12. Cluster Autoscaler Operations

Monitor:

```text
Node count
Pending Pods
Unschedulable Pods
Node utilization
Scale-up events
Scale-down events
```

Typical flow:

```text
Traffic ↑
 ↓
HPA creates Pods
 ↓
No node capacity
 ↓
Cluster Autoscaler adds nodes
 ↓
Pods scheduled
```

If scaling stops at the maximum node count, investigate capacity configuration.

---

# 13. Node Maintenance

Regularly review:

```bash
kubectl get nodes -o wide
```

Check:

```text
Version
Ready status
CPU
Memory
Disk
Conditions
Pod count
```

Before maintenance:

```bash
kubectl cordon <node>
kubectl drain <node> --ignore-daemonsets --delete-emptydir-data
```

After maintenance:

```bash
kubectl uncordon <node>
```

Drain operations must account for PDBs, workload replicas and critical workloads.

---

# 14. AKS Upgrade Operations

Kubernetes and AKS versions require planned lifecycle management.

Before upgrade:

```text
Check supported versions
Check deprecated APIs
Check workload compatibility
Check Helm charts
Check admission policies
Check CSI drivers
Check ingress controller
Check monitoring agents
```

Production upgrade flow:

```text
Dev
 ↓
Test
 ↓
Staging
 ↓
Production
```

Do not treat a Kubernetes upgrade as a simple version change.

---

# 15. Application Release Operations

NexCart release flow:

```text
Developer
   ↓
GitLab
   ↓
CI
   ↓
Test
   ↓
Quality
   ↓
Security
   ↓
Docker Build
   ↓
ACR
   ↓
Helm
   ↓
AKS
   ↓
Smoke Test
   ↓
Production
```

Day-2 responsibility includes verifying that this process remains reliable.

---

# 16. Release Verification

After deployment:

```bash
kubectl rollout status deployment/product-service -n nexcart
```

Check:

```bash
kubectl get pods -n nexcart
kubectl get endpoints -n nexcart
```

Then validate:

```text
Health endpoint
API functionality
Logs
Error rate
Latency
Database connectivity
External dependencies
Business transaction
```

Deployment success does not automatically mean application success.

---

# 17. Helm Release Management

Check releases:

```bash
helm list -n nexcart
```

Check release:

```bash
helm status nexcart -n nexcart
```

History:

```bash
helm history nexcart -n nexcart
```

Rollback:

```bash
helm rollback nexcart <revision> -n nexcart
```

Maintain a clear relationship between:

```text
Git commit
Image digest
Helm revision
Deployment
Production release
```

---

# 18. Immutable Deployment Tracking

A production deployment should be traceable.

Example:

```text
Git Commit
   ↓
Image SHA
   ↓
ACR Image
   ↓
Helm Release
   ↓
AKS Deployment
```

Prefer:

```text
product-service:8f31a2c
```

or an immutable image digest rather than:

```text
product-service:latest
```

This makes rollback and audit much easier.

---

# 19. Configuration Management

Day-2 operations often involve configuration changes.

Examples:

```text
API URL
Database endpoint
Feature flag
Timeout
Replica count
Resource limits
External provider configuration
```

Use:

```text
Git
Helm values
ConfigMaps
Key Vault
Environment-specific configuration
```

Avoid manual `kubectl edit` as the permanent configuration mechanism.

---

# 20. Secret Management

Review:

```text
Database credentials
API keys
OAuth credentials
TLS certificates
Azure credentials
GitLab tokens
Application secrets
```

Preferred architecture:

```text
Application
    ↓
Workload Identity
    ↓
Azure Key Vault
    ↓
Secret
```

Avoid storing plaintext secrets in Git.

---

# 21. Secret Rotation

Typical process:

```text
Create new credential
       ↓
Update secret store
       ↓
Deploy/reload application
       ↓
Validate
       ↓
Revoke old credential
```

For critical systems, use dual-credential support when possible.

Never revoke the old credential before confirming that consumers have successfully moved to the new one.

---

# 22. Certificate Operations

Monitor:

```text
Certificate expiry
TLS configuration
Certificate chain
Ingress TLS secret
Renewal status
```

Example:

```bash
openssl s_client \
  -connect <domain>:443 \
  -servername <domain> </dev/null 2>/dev/null \
  | openssl x509 -noout -dates
```

Production principle:

```text
Detect expiry early
+
Automate renewal
+
Validate renewal
+
Monitor served certificate
```

---

# 23. Database Day-2 Operations

Monitor:

```text
CPU
Memory
Storage
Connections
Connection pool
Latency
Slow queries
Locks
Errors
Replication
Backup status
```

Common operational issues:

```text
Connection exhaustion
Slow queries
Storage growth
Index problems
Replication lag
Failed backups
Credential expiry
```

---

# 24. Database Connection Pool

Suppose:

```text
10 Pods
×
50 connections
=
500 potential connections
```

If the database supports only 300 safe connections:

```text
Application scaling
        ↓
Connection pressure
        ↓
Database failure
```

Day-2 operations must consider the entire dependency chain.

---

# 25. Backup Monitoring

Do not just configure backups.

Monitor:

```text
Last successful backup
Backup size
Backup duration
Backup failures
Retention
Storage
Restore capability
```

Example operational question:

> When was the last successful backup, and have we tested restoring it?

---

# 26. Disaster Recovery Operations

Regularly verify:

```text
RPO
RTO
Backup
Restore
Secondary environment
Infrastructure recreation
Secrets
Certificates
DNS
Application deployment
Database recovery
```

A DR document that has never been tested should not be treated as production-ready.

---

# 27. Observability Operations

NexCart observability can use:

```text
OpenTelemetry
Grafana Alloy
Mimir
Loki
Tempo
Grafana
```

Operational responsibilities include:

```text
Dashboard maintenance
Alert tuning
Log retention
Metric retention
Trace sampling
Cardinality management
Storage management
```

---

# 28. Log Management

Structured logs are preferred.

Example:

```json
{
  "timestamp": "2026-10-10T10:30:00Z",
  "service": "order-service",
  "level": "ERROR",
  "request_id": "abc-123",
  "message": "Payment request failed"
}
```

Do not log:

```text
Passwords
Tokens
Private keys
Full sensitive customer data
```

---

# 29. Log Retention

More logs are not always better.

Define:

```text
Hot retention
Archive retention
Compliance retention
Deletion policy
```

Control:

```text
Volume
Storage cost
Query performance
Sensitive-data exposure
```

---

# 30. Metrics Cardinality

Avoid labels such as:

```text
user_id
request_id
transaction_id
```

on high-volume metrics.

This can create extremely high cardinality.

Better labels:

```text
service
method
status_code
endpoint
environment
```

Use traces/logs for highly unique identifiers.

---

# 31. Alert Management

Review alerts regularly.

Every alert should answer:

```text
What is wrong?
How serious is it?
Who should respond?
What action is expected?
```

Avoid alerts that simply generate noise.

---

# 32. Alert Example

Weak:

```text
CPU > 70%
```

Better:

```text
Payment Service:
5xx error rate > 5%
for 5 minutes
```

Even better:

```text
Payment availability SLO breached
AND
customer transaction failures increasing
```

Alerts should reflect service impact where possible.

---

# 33. Security Day-2 Operations

Regularly review:

```text
RBAC
ServiceAccounts
Azure roles
Key Vault access
Network policies
Container vulnerabilities
OS vulnerabilities
Kubernetes vulnerabilities
GitLab permissions
Registry access
Audit logs
```

Use least privilege.

---

# 34. Vulnerability Management

Typical lifecycle:

```text
Scan
 ↓
Identify CVE
 ↓
Assess severity
 ↓
Determine exploitability
 ↓
Patch
 ↓
Test
 ↓
Deploy
 ↓
Rescan
```

Tools may include:

```text
Trivy
SAST
Dependency scanning
Container scanning
Infrastructure scanning
```

Not every CVE requires an emergency production deployment; prioritize based on severity, exploitability and exposure.

---

# 35. Container Maintenance

Regularly update:

```text
Base image
OS packages
Application dependencies
Runtime
Security libraries
CA certificates
```

Avoid:

```dockerfile
FROM ubuntu:latest
```

Prefer controlled and regularly updated versions.

---

# 36. Image Lifecycle

ACR should not grow indefinitely.

Review:

```text
Old images
Unused tags
Development images
Failed build images
Production versions
Retention policies
```

Use immutable versioning.

Example:

```text
product-service:
  a1b2c3d
  d4e5f6g
  93af812
```

Keep enough versions for operational rollback without retaining unlimited artifacts.

---

# 37. Node Image / OS Maintenance

AKS node pools require lifecycle management.

Review:

```text
OS image
Security patches
Kubernetes compatibility
Node health
Capacity
```

Use controlled rollout rather than changing every production node simultaneously.

---

# 38. Access Management

Regularly review:

```text
Azure RBAC
Kubernetes RBAC
GitLab permissions
ACR access
Key Vault access
Production access
ServiceAccounts
```

Remove:

```text
Unused users
Unused service principals
Expired credentials
Excess privileges
Old access paths
```

---

# 39. ServiceAccount Operations

Kubernetes ServiceAccounts are identities for workloads.

Check:

```bash
kubectl get sa -n nexcart
```

For Workload Identity, verify:

```text
ServiceAccount
 ↓
Azure identity
 ↓
Federated credential
 ↓
Azure RBAC
 ↓
Azure resource
```

A ServiceAccount should not be treated like a human account with a permanent password.

---

# 40. Network Operations

Regularly review:

```text
Ingress
Services
DNS
NetworkPolicy
NSG
Load Balancer
Private Endpoints
Firewall
Routes
```

Production connectivity should be documented.

Example:

```text
Internet
 ↓
Ingress
 ↓
Service
 ↓
Pod
 ↓
Database
```

---

# 41. Capacity Planning

Capacity planning answers:

> Will the current platform handle expected future demand?

Track:

```text
Traffic growth
CPU growth
Memory growth
Storage growth
Database growth
Node growth
Cost growth
```

Example:

```text
Current traffic: 10K requests/hour
Forecast: 25K requests/hour
```

Validate whether:

```text
AKS
Database
Ingress
External APIs
ACR
Monitoring
```

can support the expected load.

---

# 42. Cost Optimization

Day-2 operations must also control cloud cost.

Review:

```text
AKS nodes
Idle resources
Over-provisioned Pods
Storage
Log retention
Monitoring ingestion
ACR storage
Public IPs
Load Balancers
Database sizing
Unused resources
```

Do not optimize cost by removing redundancy required by the business SLA.

---

# 43. Cost vs Reliability

Example:

```text
Single node
   ↓
Cheap
   ↓
Low resilience
```

versus:

```text
Multiple zones
Multiple replicas
DR region
   ↓
Higher cost
   ↓
Higher resilience
```

Optimization should be based on:

```text
Business criticality
RTO
RPO
SLO
Traffic
Risk
```

---

# 44. Production Change Management

Every production change should have:

```text
Reason
Scope
Risk
Implementation plan
Validation plan
Rollback plan
Owner
Change window
```

High-risk changes should use:

```text
Canary
Blue-green
Feature flags
Progressive rollout
```

---

# 45. Standard Change Workflow

```text
Request
  ↓
Impact Assessment
  ↓
Testing
  ↓
Approval
  ↓
Deployment
  ↓
Validation
  ↓
Monitoring
  ↓
Close
```

Emergency changes should still be documented retrospectively.

---

# 46. Runbooks

A runbook is an operational procedure for a known task or incident.

Examples:

```text
Production rollback
Certificate renewal
Secret rotation
AKS node maintenance
Database restore
DR failover
ACR authentication failure
Key Vault access failure
```

A good runbook contains:

```text
Prerequisites
Commands
Expected output
Decision points
Rollback
Validation
Escalation
```

---

# 47. Example Rollback Runbook

```text
1. Confirm incident
2. Identify affected service
3. Identify current release
4. Identify last known-good revision
5. Check database compatibility
6. Roll back
7. Monitor rollout
8. Validate health
9. Validate business transaction
10. Communicate recovery
11. Create RCA
```

Commands:

```bash
helm history nexcart -n nexcart

helm rollback nexcart <revision> -n nexcart

kubectl rollout status deployment/<service> -n nexcart
```

---

# 48. Operational Documentation

Keep updated:

```text
Architecture
Service ownership
Ports
Dependencies
Runbooks
Deployment process
Rollback process
DR plan
Certificate inventory
Secret inventory
Monitoring dashboards
Alerts
Escalation matrix
```

Documentation should reflect the actual production environment.

---

# 49. Service Ownership

Every service should have a clear owner.

Example:

| Service | Primary Responsibility |
|---|---|
| Product | Product catalog |
| Order | Order processing |
| Payment | Payment processing |
| Notification | Notifications |

Operational ownership should include:

```text
Application
Deployment
Monitoring
Alerts
Runbooks
Dependencies
Escalation
```

---

# 50. Dependency Mapping

For every service, document:

```text
Service
 ↓
Database
 ↓
External API
 ↓
Secret
 ↓
Azure resource
```

Example:

```text
Payment Service
   |
   +-- Database
   |
   +-- Payment Provider
   |
   +-- Key Vault
   |
   +-- DNS
```

This dramatically reduces troubleshooting time.

---

# 51. Production Access

Use least privilege.

Avoid:

```text
Everyone → Cluster Admin
Everyone → Production SSH
Everyone → Key Vault Administrator
```

Prefer:

```text
Developer
   ↓
Read-only production visibility

DevOps
   ↓
Operational access

Application team
   ↓
Application-specific access

Security
   ↓
Audit/security access
```

Exact roles depend on organizational policy.

---

# 52. Break-Glass Access

For critical emergencies, organizations may maintain emergency access.

Characteristics:

```text
Restricted
Audited
Time-bound
Approved
Used only when normal access is unavailable
```

Every use should be reviewed.

---

# 53. Operational SLO Review

Review:

```text
Availability
Latency
Error rate
Successful transactions
MTTR
Incident frequency
```

Example:

```text
SLO:
99.9% availability
```

If the service repeatedly consumes its error budget:

```text
Investigate reliability
before increasing release velocity.
```

---

# 54. Error Budget

Concept:

```text
SLO
 ↓
Allowed unreliability
 ↓
Error Budget
```

If error budget is healthy:

```text
More release velocity may be acceptable.
```

If error budget is exhausted:

```text
Focus on reliability improvements.
```

This creates a balance between:

```text
Innovation
vs
Reliability
```

---

# 55. Production Maintenance Window

Use maintenance windows for planned changes such as:

```text
Database maintenance
AKS upgrades
Node maintenance
Network changes
Certificate changes
Major application releases
```

Before maintenance:

```text
Backup
Check health
Confirm rollback
Notify stakeholders
Validate monitoring
```

After:

```text
Health check
Business validation
Monitoring
Close change
```

---

# 56. Day-2 Automation

Automate repetitive operations.

Examples:

```text
Health checks
Certificate expiry checks
Backup validation
Image cleanup
Security scanning
Scaling
Deployment verification
Smoke tests
DR validation
```

The goal:

```text
Manual Operations ↓
Automation ↑
Human Error ↓
MTTR ↓
```

---

# 57. Automated Smoke Test

After deployment:

```text
GET /health
GET /api/products
Create test order
Validate payment flow
Validate notification
```

Do not use real customer transactions for smoke tests unless specifically designed and isolated.

---

# 58. Production Synthetic Monitoring

A synthetic transaction can continuously test the customer journey.

Example:

```text
Homepage
   ↓
Product API
   ↓
Create Order
   ↓
Payment
   ↓
Notification
```

This detects problems that infrastructure metrics may miss.

---

# 59. Day-2 Problem: Configuration Drift

Example:

```text
Git:
replicas = 3

Cluster:
replicas = 5
```

Possible causes:

```text
Manual kubectl change
Emergency scaling
Old Helm release
Different deployment source
```

Preferred solution:

```text
Git
 ↓
Approved configuration
 ↓
Deployment
 ↓
Cluster
```

Git should remain the source of truth where GitOps/declarative management is used.

---

# 60. Day-2 Problem: Manual Changes

Manual changes can be useful during incidents.

But:

```text
Manual fix
   ↓
Incident resolved
   ↓
Configuration not documented
   ↓
Next deployment overwrites it
```

After emergency changes:

```text
Document
Review
Convert to code
Test
Commit
Deploy through standard process
```

---

# 61. Day-2 Problem: Resource Leak

Examples:

```text
Unused Load Balancer
Unused Public IP
Unused Disk
Old ACR images
Old snapshots
Unused node pools
Unused test environments
```

Regular inventory prevents cloud waste.

---

# 62. Day-2 Problem: Storage Growth

Monitor:

```text
Database storage
Persistent volumes
Logs
Metrics
Traces
ACR storage
Backups
```

Set alerts before storage reaches critical levels.

Example:

```text
Warning: 70%
Critical: 85%
```

Actual thresholds should be based on workload and recovery time.

---

# 63. Day-2 Problem: Dependency Failure

NexCart may depend on:

```text
Database
Payment provider
Notification provider
Azure services
Key Vault
ACR
DNS
Identity
```

For every critical dependency define:

```text
Timeout
Retry
Backoff
Circuit breaker where appropriate
Fallback
Alert
Runbook
```

---

# 64. Retry Storm

Bad design:

```text
Service A
 ↓
Service B fails
 ↓
A retries immediately 10 times
 ↓
100 Pods
 ↓
1000 requests
 ↓
B becomes even more overloaded
```

Better:

```text
Exponential backoff
+
Jitter
+
Retry limits
+
Circuit breaker
```

Retries must be designed carefully.

---

# 65. Graceful Degradation

Not every dependency failure should take down the entire application.

Example:

```text
Notification service unavailable
```

Potential behavior:

```text
Order
 ↓
Order saved
 ↓
Notification queued
 ↓
Notification processed later
```

instead of:

```text
Notification unavailable
 ↓
Order completely fails
```

Design degradation based on business requirements.

---

# 66. Operational Readiness Review

Before calling NexCart production-ready:

```text
[ ] Monitoring
[ ] Alerting
[ ] Logging
[ ] Tracing
[ ] Health probes
[ ] Autoscaling
[ ] HA
[ ] Backup
[ ] DR
[ ] Security
[ ] Certificate monitoring
[ ] Secret rotation
[ ] Rollback
[ ] Runbooks
[ ] Ownership
[ ] SLO
[ ] Cost monitoring
[ ] Capacity planning
```

---

# 67. Weekly Day-2 Review

Review:

```text
Incidents
Alerts
Deployments
Rollbacks
Capacity
Security findings
Certificate expiry
Secret rotation
Backup status
Cost
Performance
SLO
```

Questions:

```text
What failed?
What almost failed?
What changed?
What is growing?
What is expensive?
What needs automation?
```

---

# 68. Monthly Operations Review

Review:

```text
Infrastructure versions
AKS version
Node images
Container base images
Application dependencies
Security vulnerabilities
Database growth
Storage growth
Cloud cost
DR readiness
Access permissions
Unused resources
```

---

# 69. Day-2 Operational Maturity

### Level 1 — Reactive

```text
Alert
 ↓
Engineer investigates
 ↓
Manual fix
```

### Level 2 — Documented

```text
Alert
 ↓
Runbook
 ↓
Engineer executes
```

### Level 3 — Automated

```text
Alert
 ↓
Automation
 ↓
Mitigation
 ↓
Engineer validates
```

### Level 4 — Preventive

```text
Metrics
 ↓
Prediction
 ↓
Automated action
 ↓
Problem prevented
```

The goal is to move repetitive operations toward automation without removing necessary human controls.

---

# 70. Senior Production Scenario

## Scenario

NexCart has been running successfully for six months.

Suddenly:

```text
CPU ↑
Memory ↑
Database connections ↑
Cloud cost ↑
```

A junior approach:

```text
Increase node size.
```

A senior approach:

```text
1. Establish when the trend started.
2. Correlate with releases and traffic.
3. Compare resource usage historically.
4. Check application and database metrics.
5. Check connection pool behavior.
6. Check traffic growth.
7. Identify inefficient queries.
8. Check memory leaks.
9. Review HPA behavior.
10. Right-size resources.
11. Load-test the fix.
12. Monitor after deployment.
```

The objective is to solve the underlying capacity problem rather than continuously adding infrastructure.

---

# 71. Senior Interview Questions

### Q1. What is Day-2 Operations?

> Day-2 operations covers the ongoing lifecycle of a production system after initial deployment: monitoring, incident response, scaling, upgrades, security, backups, DR, certificate and secret rotation, cost optimization, capacity planning and continuous reliability improvement.

---

### Q2. What do you check every morning for a production AKS environment?

> I start with node and Pod health, recent events, deployment status, resource utilization, application error rates, latency, critical alerts, database health, backup status and any recent production changes. I also check for recurring warnings rather than only looking for active outages.

---

### Q3. How do you manage Kubernetes upgrades?

> I first check AKS-supported versions and deprecated APIs, then test the upgrade in lower environments. I validate workloads, Helm charts, ingress, CSI drivers, monitoring and admission policies. Production is upgraded through a controlled change window with rollback or recovery procedures and post-upgrade validation.

---

### Q4. How do you prevent configuration drift?

> I keep infrastructure and application configuration declarative and version-controlled. Terraform manages infrastructure and Helm manages Kubernetes application configuration. Emergency manual changes may be required, but they should be documented and converted back into code afterward.

---

### Q5. How do you manage production secrets?

> I avoid storing secrets in Git and prefer Azure Key Vault with workload identity. I monitor expiration, rotate credentials through a controlled process and verify that applications have successfully reloaded the new credentials before revoking the old ones.

---

### Q6. How do you optimize Azure cost without reducing availability?

> I start with utilization data and identify idle or over-provisioned resources. I right-size workloads, use autoscaling, clean unused resources, control log and metric retention and optimize storage. I do not remove redundancy or lower capacity requirements without checking the SLO, RTO and RPO.

---

### Q7. What happens after an emergency production fix?

> I document the change, preserve the incident timeline, review its risk, convert the required configuration into code, test it, commit it and redeploy through the standard process. Otherwise the emergency fix can become configuration drift.

---

### Q8. How do you know that a backup strategy actually works?

> I perform restore tests. I measure the actual recovery time, verify the recovered data and run application-level validation. A successful backup job alone does not prove recoverability.

---

### Q9. How do you manage certificate expiry?

> I maintain certificate inventory and expiry monitoring, automate renewal where possible, validate the renewed certificate at the actual serving endpoint and alert before expiration. I also test the complete renewal path rather than assuming that the certificate store being updated means production is updated.

---

### Q10. What is your approach to production operations?

> I operate from measurable SLOs and business impact rather than individual infrastructure metrics. I use observability to detect issues, runbooks to standardize response, automation for repetitive tasks and post-incident analysis to continuously reduce operational risk.

---

# 72. NexCart Day-2 Operating Model

```text
                         PRODUCTION
                              |
        +---------------------+---------------------+
        |                     |                     |
    Monitoring            Operations            Security
        |                     |                     |
 Metrics/Logs/Traces      Incidents              RBAC
 Alerts                   Scaling                Secrets
 Dashboards               Upgrades               Vulnerabilities
        |                     |                     |
        +---------------------+---------------------+
                              |
                         Reliability
                              |
                 +------------+------------+
                 |            |            |
                HA           DR          Backup
                 |            |            |
                 +------------+------------+
                              |
                       Continuous Improvement
                              |
              +---------------+---------------+
              |               |               |
          Automation        Cost          Performance
```

---

# 73. Complete Day-2 Lifecycle

```text
Deploy
  ↓
Observe
  ↓
Validate
  ↓
Operate
  ↓
Monitor
  ↓
Scale
  ↓
Patch
  ↓
Rotate
  ↓
Backup
  ↓
Test DR
  ↓
Optimize
  ↓
Upgrade
  ↓
Automate
  ↓
Improve
```

---

# 74. Final Day-2 Checklist

```text
Production Operations
[ ] Daily health check
[ ] Node monitoring
[ ] Pod monitoring
[ ] Deployment monitoring
[ ] Application monitoring
[ ] Database monitoring

Scaling
[ ] HPA
[ ] Cluster Autoscaler
[ ] Capacity planning
[ ] Resource right-sizing

Reliability
[ ] HA
[ ] PDB
[ ] Multi-zone strategy
[ ] Graceful shutdown
[ ] SLO

Security
[ ] RBAC review
[ ] Secret rotation
[ ] Certificate rotation
[ ] Vulnerability scanning
[ ] Access review

Data
[ ] Backup
[ ] Restore testing
[ ] RPO
[ ] RTO
[ ] DR testing

Operations
[ ] Runbooks
[ ] Incident management
[ ] Change management
[ ] Rollback
[ ] Documentation

Optimization
[ ] Cost review
[ ] Storage review
[ ] ACR cleanup
[ ] Log retention
[ ] Resource optimization

Automation
[ ] Smoke tests
[ ] Health checks
[ ] Certificate monitoring
[ ] Backup validation
[ ] Security scanning
[ ] Deployment validation
```

# Core Senior DevOps Principle

> **Day-2 operations is where DevOps maturity is actually proven. Deploying an application to AKS is only the beginning. A senior DevOps engineer ensures the platform remains secure, observable, scalable, recoverable, cost-efficient and maintainable months and years after the original deployment.**