---
title: "17-production-incidents"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 17
---

# 🚨 17. Production Incidents – NexCart

## 1. Production Incident Management

A production incident is an unexpected event that causes or can cause:

```text
Service Downtime
Performance Degradation
Data Loss
Security Impact
Business Transaction Failure
SLA/SLO Breach
```

The objective is not to immediately find someone responsible.

The objective is:

```text
Detect
  ↓
Assess
  ↓
Contain
  ↓
Restore
  ↓
Validate
  ↓
Communicate
  ↓
Find Root Cause
  ↓
Prevent Recurrence
```

---

# 2. Incident vs Problem vs Change

| Term | Meaning |
|---|---|
| Incident | Current service disruption |
| Problem | Underlying cause of one or more incidents |
| Change | Planned modification to the environment |
| Alert | Signal indicating abnormal behavior |
| Event | Something that happened in the system |

Example:

```text
Deployment
   ↓
Memory leak
   ↓
Pods restart
   ↓
API unavailable
```

Incident:

```text
API unavailable
```

Problem:

```text
Memory leak introduced by application change
```

Change:

```text
New application deployment
```

---

# 3. Incident Severity

Severity should be based on business impact.

Example model:

| Severity | Example |
|---|---|
| SEV-1 | Complete production outage / critical business failure |
| SEV-2 | Major functionality unavailable |
| SEV-3 | Limited functionality degradation |
| SEV-4 | Minor/non-critical issue |

The exact definitions should match the organization's incident-management policy.

---

# 4. SEV-1 Example

NexCart checkout is completely unavailable.

```text
Customer
   ↓
Checkout
   ↓
500 / Timeout
   ↓
Orders cannot be completed
```

Immediate priorities:

```text
1. Declare incident
2. Assign incident commander
3. Check recent changes
4. Stop further changes
5. Restore service
6. Communicate status
7. Preserve evidence
```

Do not spend 30 minutes trying to prove the exact root cause before restoring service.

---

# 5. Incident Roles

A mature incident response separates responsibilities.

```text
Incident Commander
        |
 +------+-------+---------+
 |              |         |
Technical     Communications
Lead             Lead
 |
Engineers
```

### Incident Commander

Responsible for:

```text
Prioritization
Decision making
Coordination
Escalation
Communication cadence
```

### Technical Lead

Responsible for:

```text
Investigation
Mitigation
Technical decisions
Validation
```

### Communications Lead

Responsible for:

```text
Stakeholder updates
Business communication
Status page updates
Customer communication
```

---

# 6. First 5 Minutes

When a production alert arrives:

```text
1. What is broken?
2. Who is impacted?
3. When did it start?
4. Is it still getting worse?
5. What changed recently?
6. Can we rollback safely?
7. Is there a security/data-loss concern?
```

Avoid immediately changing random resources.

---

# 7. Establish Incident Timeline

Create a timeline.

Example:

```text
14:00  Deployment started
14:05  Deployment completed
14:07  Error rate increased
14:09  Alert triggered
14:11  Incident declared
14:15  Rollback started
14:18  Error rate normalized
14:25  Validation completed
```

Timeline helps identify correlation between changes and impact.

---

# 8. Check Recent Changes First

One of the highest-value checks:

```text
What changed immediately before the incident?
```

Check:

```bash
kubectl rollout history deployment/product-service -n nexcart
```

Helm:

```bash
helm history nexcart -n nexcart
```

GitLab:

```text
Pipeline
Commit
Image tag
Deployment job
Configuration change
```

Azure/Terraform:

```text
Terraform apply
Infrastructure change
RBAC change
Network change
Key Vault change
```

---

# 9. Production Incident Golden Signals

Monitor:

```text
Latency
Traffic
Errors
Saturation
```

### Latency

```text
API response time ↑
```

### Traffic

```text
Requests/sec ↑
```

### Errors

```text
5xx ↑
4xx ↑
```

### Saturation

```text
CPU
Memory
Disk
Connections
Queues
```

---

# 10. Incident Investigation Layers

Use a consistent investigation order:

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
Database
   ↓
Infrastructure
```

Do not assume Kubernetes is always the root cause.

---

# 11. Production Health Snapshot

Start with:

```bash
kubectl get nodes
kubectl get pods -n nexcart -o wide
kubectl get deploy -n nexcart
kubectl get svc -n nexcart
kubectl get ingress -n nexcart
kubectl get events -n nexcart --sort-by=.lastTimestamp
```

Then:

```bash
kubectl top nodes
kubectl top pods -n nexcart
```

This gives a quick cluster-level picture.

---

# 12. Check Application Errors

```bash
kubectl logs <pod> -n nexcart --since=15m
```

Previous crashed container:

```bash
kubectl logs <pod> -n nexcart --previous
```

Follow logs:

```bash
kubectl logs -f <pod> -n nexcart
```

Multiple containers:

```bash
kubectl logs <pod> -c <container> -n nexcart
```

---

# 13. CrashLoopBackOff Incident

### Symptoms

```text
Pod
 ↓
Starts
 ↓
Crashes
 ↓
Restarts
 ↓
CrashLoopBackOff
```

Check:

```bash
kubectl get pods -n nexcart
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
```

Check:

```text
Application exception
Environment variables
Secret
ConfigMap
Database connection
Startup command
File permissions
Memory
Dependency availability
```

---

# 14. ImagePullBackOff Incident

Symptoms:

```text
Pod Pending
       ↓
ImagePullBackOff
```

Check:

```bash
kubectl describe pod <pod> -n nexcart
```

Look for:

```text
Image name
Tag
Registry authentication
ACR permissions
Network access
Image existence
```

ACR:

```bash
az acr repository list \
  --name <acr-name> \
  -o table
```

Check image:

```bash
az acr repository show-tags \
  --name <acr-name> \
  --repository product-service \
  -o table
```

---

# 15. Deployment Incident

Check:

```bash
kubectl rollout status deployment/product-service -n nexcart
```

Then:

```bash
kubectl get deployment product-service -n nexcart
kubectl describe deployment product-service -n nexcart
```

History:

```bash
kubectl rollout history deployment/product-service -n nexcart
```

If a deployment introduced the problem:

```bash
kubectl rollout undo deployment/product-service -n nexcart
```

Then verify:

```bash
kubectl rollout status deployment/product-service -n nexcart
```

---

# 16. Rollback Decision

Rollback is appropriate when:

```text
Recent deployment correlates strongly with failure
Previous version is known-good
Rollback is technically safe
Database compatibility is maintained
```

Do not blindly rollback when:

```text
Database migration is irreversible
Data schema is incompatible
Incident is unrelated to deployment
Rollback could cause greater impact
```

---

# 17. 502 Bad Gateway

Typical flow:

```text
Client
 ↓
Ingress
 ↓
Service
 ↓
Pod
```

Check:

```bash
kubectl get ingress -n nexcart
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get pods -n nexcart
```

Common causes:

```text
No healthy Pods
Wrong Service selector
Wrong targetPort
Readiness failure
Ingress backend mismatch
Application not listening
Network policy
```

---

# 18. 404 Incident

First determine:

```text
Who generated the 404?
```

Possibilities:

```text
Ingress
Application
Frontend
API Gateway
```

Check:

```bash
kubectl describe ingress <ingress> -n nexcart
```

Validate routes:

```text
/api/products
/api/orders
/api/payments
```

Do not assume every 404 is an Ingress issue.

---

# 19. Service Has No Endpoints

Check:

```bash
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
```

If:

```text
Service → Endpoints: <none>
```

Check:

```bash
kubectl get pods -n nexcart --show-labels
kubectl describe svc <service> -n nexcart
```

Common root cause:

```text
Service selector != Pod labels
```

---

# 20. DNS Incident

Test from inside the cluster:

```bash
kubectl exec -it <pod> -n nexcart -- nslookup product-service
```

Or:

```bash
kubectl exec -it <pod> -n nexcart -- getent hosts product-service
```

Check:

```bash
kubectl get svc -n nexcart
```

Common causes:

```text
Wrong service name
Wrong namespace
CoreDNS issue
Network policy
Application configuration
```

---

# 21. Service-to-Service Failure

Example:

```text
Order Service
     ↓
Product Service
```

Check:

```text
DNS
Service
Endpoints
Port
Protocol
NetworkPolicy
Application listener
```

Test:

```bash
kubectl exec -it <order-pod> -n nexcart -- \
  curl -v http://product-service:8082/health
```

The exact URL and port must match the application's Kubernetes Service configuration.

---

# 22. HTTP 500 Incident

HTTP 500 generally means the request reached the application but the application failed.

Check:

```bash
kubectl logs <pod> -n nexcart --since=15m
```

Look for:

```text
Exception
Stack trace
Database error
Null/invalid data
External API failure
Timeout
Configuration issue
```

Trace:

```text
Request
 ↓
Application
 ↓
Dependency
 ↓
Database
```

---

# 23. HTTP 401 / 403 Incident

Possible causes:

```text
Expired token
Invalid token
Wrong audience
Missing permission
RBAC failure
Key Vault authorization
Application authorization
```

Do not automatically assume Kubernetes RBAC caused an application HTTP 403.

Identify the layer producing the response.

---

# 24. Database Connection Incident

Symptoms:

```text
Connection timeout
Connection refused
Authentication failed
Connection pool exhausted
High DB latency
```

Check:

```text
Database availability
DNS
Network path
Firewall/private endpoint
Credentials
Connection string
TLS
Connection pool
Database capacity
```

Application logs are usually the first useful source.

---

# 25. Connection Pool Exhaustion

Example:

```text
Pods = 10
Pool per Pod = 100
```

Potentially:

```text
10 × 100 = 1,000 connections
```

If the database supports fewer connections, scaling the application can make the outage worse.

Senior approach:

```text
Application replicas
×
Connections per replica
≤
Safe DB capacity
```

---

# 26. High CPU Incident

Check:

```bash
kubectl top pods -n nexcart
kubectl top nodes
```

Determine:

```text
Single Pod?
Entire service?
Single node?
Entire cluster?
```

Then investigate:

```text
Traffic spike
CPU-intensive code
Infinite loop
Bad query
Logging overhead
Memory pressure
Recent deployment
```

Do not immediately increase CPU limits without understanding the workload.

---

# 27. High Memory / OOMKilled

Check:

```bash
kubectl get pods -n nexcart
kubectl describe pod <pod> -n nexcart
```

Look for:

```text
OOMKilled
```

Potential causes:

```text
Memory leak
Large payload
Unbounded cache
Too many concurrent requests
Incorrect memory limits
Application defect
```

Check historical metrics before changing limits.

---

# 28. Node NotReady

Check:

```bash
kubectl get nodes
kubectl describe node <node>
```

Investigate:

```text
CPU pressure
Memory pressure
Disk pressure
Kubelet
Container runtime
Network
Azure VM health
Node pool capacity
```

Check Pod placement:

```bash
kubectl get pods -n nexcart -o wide
```

If multiple replicas are concentrated on one node, HA may be insufficient.

---

# 29. Disk Pressure

Symptoms:

```text
DiskPressure
Pods evicted
Container failures
Logging failures
```

Check:

```bash
kubectl describe node <node>
```

Investigate:

```text
Container logs
Ephemeral storage
Unused images
Temporary files
Application-generated files
```

Long-term solution:

```text
Log retention
Storage monitoring
Ephemeral storage limits
Persistent storage design
Node sizing
```

---

# 30. Certificate Expiry Incident

Symptoms:

```text
TLS handshake failure
Certificate expired
Browser security warning
API clients reject connection
```

Check certificate:

```bash
openssl s_client \
  -connect <domain>:443 \
  -servername <domain> </dev/null 2>/dev/null \
  | openssl x509 -noout -dates
```

Check Kubernetes:

```bash
kubectl get secret -n nexcart
```

If using cert-manager:

```bash
kubectl get certificate -n nexcart
kubectl get certificaterequest -n nexcart
kubectl get challenge -n nexcart
```

Root cause could be:

```text
Renewal failure
DNS challenge failure
Ingress not updated
Wrong TLS Secret
Certificate chain issue
```

---

# 31. Secret Rotation Incident

Symptoms:

```text
Application suddenly cannot connect
Authentication failures
403
401
Database login failure
External API failure
```

Check:

```text
Was secret rotated?
Did application reload it?
Is the new secret valid?
Is the old credential revoked?
Does the Pod contain the expected value?
```

Important:

> Rotating a secret successfully does not guarantee that every running application has reloaded it.

---

# 32. Key Vault 403 Incident

Possible causes:

```text
Wrong identity
Missing role assignment
Wrong Key Vault
Wrong tenant
Workload Identity misconfiguration
Network restriction
```

Check ServiceAccount:

```bash
kubectl get sa -n nexcart
kubectl describe sa <service-account> -n nexcart
```

Check Azure role assignment:

```bash
az role assignment list \
  --assignee <principal-id> \
  -o table
```

Validate:

```text
Identity
Federated credential
ServiceAccount
Key Vault role
Network access
Secret name
```

---

# 33. ACR Authentication Incident

Symptoms:

```text
unauthorized
ImagePullBackOff
pull access denied
```

Check:

```text
AKS identity
AcrPull role
ACR name
Image repository
Image tag
Network connectivity
```

Azure:

```bash
az role assignment list \
  --assignee <principal-id> \
  -o table
```

Do not immediately create an ACR admin credential as a workaround.

Prefer identity-based access.

---

# 34. Configuration Incident

Symptoms:

```text
Application works locally
Production fails
```

Compare:

```text
Environment variables
ConfigMap
Secret
Service endpoints
Database URL
Feature flags
External API configuration
```

Check:

```bash
kubectl get configmap -n nexcart
kubectl describe configmap <name> -n nexcart
```

For sensitive values, do not print secrets into incident channels or logs.

---

# 35. GitLab Pipeline Incident

Pipeline:

```text
validate
 ↓
test
 ↓
quality
 ↓
security
 ↓
build
 ↓
push
 ↓
deploy
```

Determine exactly which stage failed.

Examples:

```text
Build failure
Test failure
Trivy failure
ACR authentication
AKS authentication
Helm validation
Deployment failure
Smoke-test failure
```

Do not rerun the entire pipeline blindly if production state may already have changed.

---

# 36. Terraform Incident

Possible scenario:

```text
Terraform plan
 ↓
Unexpected resource change
```

Stop and review.

```bash
terraform plan
terraform state list
terraform state show <resource>
```

Questions:

```text
Was infrastructure manually changed?
Was state stale?
Did provider behavior change?
Did code change?
Is the resource imported correctly?
```

Never use:

```bash
terraform apply -auto-approve
```

as an incident response shortcut without reviewing the plan.

---

# 37. Observability During Incidents

Use:

```text
Metrics
Logs
Traces
Events
Deployment history
Infrastructure telemetry
```

Example investigation:

```text
Grafana
 ↓
Latency spike
 ↓
Trace
 ↓
Order Service slow
 ↓
Application logs
 ↓
Database query slow
 ↓
Database metrics
```

Observability should reduce investigation time.

---

# 38. Correlation ID

For distributed systems, use a correlation/request ID.

Example:

```text
Request ID:
abc-123
```

Flow:

```text
Frontend
  ↓ abc-123
Order Service
  ↓ abc-123
Payment Service
  ↓ abc-123
Database/API
```

Then search logs using the same ID.

This is extremely useful for production debugging.

---

# 39. Incident Communication

A good incident update answers:

```text
What happened?
Who is impacted?
What are we doing?
What is the current status?
What is the next update?
```

Example:

```text
Production checkout is currently degraded.
Customers may experience payment failures.
The team is investigating the Payment Service and its database dependency.
Traffic remains available for other services.
Next update will be provided after the next validation step.
```

Avoid:

```text
"Everything is broken."
"Database guys caused it."
"It should be fixed soon."
```

Communicate facts, not assumptions.

---

# 40. Mitigation vs Root Cause

During an incident:

```text
Mitigation
    ↓
Restore service
```

After recovery:

```text
Root Cause Analysis
    ↓
Permanent Fix
```

Example:

```text
Memory leak
   ↓
Pods restart
   ↓
Service restored
```

Restarting Pods is mitigation.

Fixing the memory leak is the permanent solution.

---

# 41. Temporary Mitigation

Possible actions:

```text
Rollback
Scale service
Disable problematic feature
Route traffic away
Restart unhealthy workload
Fail over dependency
Increase capacity
Block abusive traffic
```

Every mitigation should consider:

```text
Blast radius
Data integrity
Security
Rollback possibility
Side effects
```

---

# 42. Blast Radius

Blast radius means how much of the system can be affected by a change or failure.

Example:

```text
One Pod
   ↓
Small blast radius
```

versus:

```text
Entire cluster
   ↓
Large blast radius
```

Reduce blast radius using:

```text
Canary
Feature flags
Namespaces
Network policies
Separate node pools
Progressive rollout
Resource limits
```

---

# 43. Production Freeze

During major incidents:

```text
Stop unrelated deployments
Stop infrastructure changes
Stop non-essential configuration changes
```

Reason:

```text
Fewer variables
Cleaner investigation
Lower risk
```

Emergency changes should be explicitly tracked.

---

# 44. Incident Evidence

Preserve:

```text
Logs
Metrics
Traces
Events
Deployment version
Image digest
Helm revision
Terraform plan
Configuration changes
Timeline
```

Do not delete logs or restart everything before collecting useful evidence unless immediate service restoration requires it.

---

# 45. Post-Incident Review

After service restoration:

```text
Incident
 ↓
Timeline
 ↓
Root Cause
 ↓
Contributing Factors
 ↓
What Worked
 ↓
What Failed
 ↓
Action Items
```

A mature postmortem should be blameless.

---

# 46. Root Cause Analysis

Use techniques such as:

```text
5 Whys
Fishbone Analysis
Timeline Analysis
Dependency Analysis
Change Correlation
```

Example:

```text
Why was checkout unavailable?
→ Payment Service failed.

Why?
→ Database connections were exhausted.

Why?
→ Connection pool was too large.

Why?
→ Replica count increased without DB capacity review.

Why?
→ Scaling policy considered CPU but not DB connection capacity.
```

Root cause:

```text
Scaling architecture ignored downstream database connection capacity.
```

---

# 47. Corrective vs Preventive Actions

### Corrective

Fix current problem.

```text
Reduce connection pool
```

### Preventive

Prevent recurrence.

```text
Add DB connection saturation alert
Add load-test scenario
Document safe replica limits
Add deployment validation
```

---

# 48. Postmortem Structure

```text
Incident Title
Date/Time
Severity
Duration
Impact
Detection
Timeline
Root Cause
Contributing Factors
Mitigation
Resolution
What Went Well
What Went Wrong
Corrective Actions
Preventive Actions
Owners
Due Dates
```

---

# 49. MTTD / MTTA / MTTR

### MTTD

Mean Time To Detect.

```text
Failure → Detection
```

### MTTA

Mean Time To Acknowledge.

```text
Detection → Engineer acknowledgement
```

### MTTR

Mean Time To Restore/Resolve.

```text
Incident → Service restoration
```

Goal:

```text
MTTD ↓
MTTA ↓
MTTR ↓
```

---

# 50. Incident Metrics

Track:

```text
Incident count
SEV-1 count
SEV-2 count
MTTD
MTTA
MTTR
Change failure rate
Rollback rate
Repeat incidents
Alert noise
```

Do not optimize metrics at the expense of actual reliability.

---

# 51. Change Failure Rate

A useful engineering metric.

Concept:

```text
Changes causing:
- Incident
- Rollback
- Hotfix
- Degradation
```

High change failure rate indicates problems in:

```text
Testing
Deployment strategy
Observability
Change review
Release process
```

---

# 52. Alert Fatigue

Too many alerts create:

```text
Ignored alerts
Delayed response
Engineer burnout
Missed critical incidents
```

Good alerts should be:

```text
Actionable
Relevant
Prioritized
Business-aware
```

Bad alert:

```text
CPU = 70%
```

Better alert:

```text
Payment Service latency > SLO
AND
error rate > threshold
FOR
5 minutes
```

---

# 53. SLO-Based Incident Detection

Instead of monitoring only infrastructure:

```text
CPU
Memory
Disk
```

monitor user experience:

```text
Availability
Latency
Error rate
Successful transactions
```

Example:

```text
Checkout success rate < 99%
```

This is more directly connected to business impact.

---

# 54. Production Incident Scenario

## Scenario: Payment Service Returns 500

### Step 1 — Confirm

```bash
kubectl get pods -n nexcart
kubectl get svc -n nexcart
```

### Step 2 — Logs

```bash
kubectl logs <payment-pod> -n nexcart --since=15m
```

### Step 3 — Dependencies

Check:

```text
Database
Payment provider
Secrets
DNS
Network
```

### Step 4 — Recent deployment

```bash
kubectl rollout history deployment/payment-service -n nexcart
helm history nexcart -n nexcart
```

### Step 5 — Mitigate

If the latest release caused the issue:

```bash
kubectl rollout undo deployment/payment-service -n nexcart
```

### Step 6 — Validate

```text
Health
Logs
Metrics
Payment test
Error rate
Latency
```

### Step 7 — RCA

Identify why the deployment passed CI/CD but failed production validation.

---

# 55. Production Incident Scenario: Pods Healthy but Application Down

This is a common senior-level scenario.

```text
kubectl get pods
        ↓
Running
        ↓
Application still unavailable
```

Do not conclude that Kubernetes is healthy therefore the application is healthy.

Check:

```text
Readiness
Service endpoints
Ingress
Application listener
NetworkPolicy
DNS
External dependencies
Database
TLS
```

The Pod being `Running` only means the container process is running.

---

# 56. Production Incident Scenario: Deployment Successful but Users Get Errors

Possible flow:

```text
GitLab
 ↓
Build
 ↓
Push
 ↓
Helm deployment
 ↓
Kubernetes rollout SUCCESS
 ↓
Users receive 500
```

Why?

```text
Deployment success
≠
Application success
```

Need:

```text
Health checks
Smoke tests
Synthetic tests
Business validation
Observability
```

---

# 57. Production Incident Scenario: Error Rate After Deployment

```text
10:00 Deployment
10:03 Error rate ↑
10:05 Alert
```

Investigation:

```text
Compare version
Compare image digest
Compare logs
Compare traces
Compare metrics
Check configuration
```

If strongly correlated:

```text
Rollback
 ↓
Validate
 ↓
Root cause
 ↓
Fix
 ↓
Test
 ↓
Progressive redeployment
```

---

# 58. Production Incident Scenario: Entire AKS Cluster Unhealthy

Check:

```bash
kubectl get nodes
kubectl get pods -A
kubectl get events -A --sort-by=.lastTimestamp
```

Then determine:

```text
Control-plane issue?
Node pool issue?
Networking?
Azure infrastructure?
Identity?
DNS?
Storage?
```

Do not start deleting Pods randomly.

---

# 59. Production Incident Scenario: Azure Region Outage

Expected DR process:

```text
Confirm regional outage
       ↓
Declare DR event
       ↓
Stop conflicting changes
       ↓
Validate secondary region
       ↓
Recover/activate database
       ↓
Deploy NexCart
       ↓
Validate Key Vault/identity
       ↓
Validate services
       ↓
Validate business transactions
       ↓
Switch traffic
       ↓
Monitor
```

Then:

```text
Failback planning
```

---

# 60. Production Incident Scenario: Data Corruption

Priority changes.

Do not immediately restart services.

First:

```text
Stop further corruption
Identify affected data
Preserve evidence
Determine recovery point
Validate backup
Coordinate database recovery
```

Then:

```text
Restore
Validate
Reconcile
Resume traffic
```

Data incidents require stronger controls than normal application outages.

---

# 61. Security Incident

Examples:

```text
Secret leaked
Compromised credential
Unexpected privileged access
Malicious container
Suspicious traffic
Dependency vulnerability exploited
```

Immediate actions may include:

```text
Contain
Revoke credentials
Block access
Preserve evidence
Rotate secrets
Investigate identity activity
Assess blast radius
```

Do not destroy evidence by blindly rebuilding everything.

Coordinate with the organization's security/incident-response process.

---

# 62. Production Incident Decision Tree

```text
                INCIDENT
                    |
             Is service impacted?
               /          \
             NO            YES
             |              |
         Monitor       Assess severity
                            |
                    +-------+-------+
                    |               |
                 Critical        Non-critical
                    |               |
              Incident Cmdr      Team response
                    |
              Recent change?
               /          \
             YES           NO
              |             |
           Rollback?     Investigate
              |             |
              +-------> Mitigate
                           |
                       Validate
                           |
                       Recover
                           |
                         RCA
                           |
                     Prevent recurrence
```

---

# 63. Production Command Bank

### Kubernetes

```bash
kubectl get nodes
kubectl get pods -A
kubectl get pods -n nexcart -o wide
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
kubectl get events -A --sort-by=.lastTimestamp
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get ingress -n nexcart
kubectl top nodes
kubectl top pods -n nexcart
```

### Deployment

```bash
kubectl rollout status deployment/<name> -n nexcart
kubectl rollout history deployment/<name> -n nexcart
kubectl rollout undo deployment/<name> -n nexcart
```

### Helm

```bash
helm list -n nexcart
helm status <release> -n nexcart
helm history <release> -n nexcart
helm rollback <release> <revision> -n nexcart
```

### Azure

```bash
az aks get-credentials -g <rg> -n <aks>
az aks show -g <rg> -n <aks>
az role assignment list --assignee <principal-id> -o table
az acr repository list --name <acr> -o table
```

---

# 64. Production Incident Golden Rules

```text
1. Stabilize first.
2. Do not guess.
3. Check recent changes.
4. Establish the blast radius.
5. Use metrics, logs, traces and events together.
6. Do not blindly scale.
7. Do not blindly restart everything.
8. Protect data before recovery actions.
9. Communicate facts, not assumptions.
10. Record the timeline.
11. Preserve useful evidence.
12. Roll back when the change is clearly responsible and rollback is safe.
13. Validate the business transaction after technical recovery.
14. Separate mitigation from root-cause analysis.
15. Perform a blameless postmortem.
16. Assign corrective actions with owners and deadlines.
17. Test the permanent fix.
18. Track repeat incidents.
19. Reduce MTTD and MTTR through automation.
20. Design systems so the same incident is harder to repeat.
```

# 65. Senior Interview Answer

### Question: How do you handle a major production incident?

> I first establish the business impact and severity, then assign or take incident ownership and create a clear timeline. I check recent deployments and infrastructure changes because change correlation is often the fastest path to mitigation. In parallel, I review metrics, logs, traces, Kubernetes events and dependency health to determine the blast radius. My first priority is restoring service safely, which may involve rollback, traffic reduction, scaling or failover. I avoid making uncontrolled changes during the incident. Once service is stable, I validate the complete business transaction, preserve evidence and perform RCA separately. Finally, I document corrective and preventive actions, assign owners, and verify that the permanent fix is tested and implemented.

---

# 66. Final NexCart Incident Response Flow

```text
                   ALERT
                     |
                     v
             Incident Declared
                     |
                     v
              Assess Impact
                     |
                     v
              Assign Severity
                     |
                     v
             Check Recent Change
                     |
                     v
       +-------------+-------------+
       |                           |
   Application                  Platform
       |                           |
   Logs/Traces              AKS/Azure/Network
       |                           |
       +-------------+-------------+
                     |
                     v
              Identify Blast Radius
                     |
                     v
                 Mitigate
                     |
          +----------+----------+
          |                     |
       Rollback               Failover
          |                     |
          +----------+----------+
                     |
                     v
               Validate
                     |
                     v
              Business Test
                     |
                     v
              Communicate
                     |
                     v
             Service Restored
                     |
                     v
                    RCA
                     |
                     v
            Corrective Actions
                     |
                     v
           Prevent Recurrence
```

# Core Senior DevOps Principle

> **During a production incident, the goal is not to prove that you know Kubernetes commands. The goal is to reduce customer impact safely, restore service quickly, preserve evidence, communicate clearly, identify the real root cause, and permanently reduce the probability of the same incident happening again.**