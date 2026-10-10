---
title: "16-scaling-ha-dr"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 16
---

# 📈 16. Scaling, High Availability & Disaster Recovery – NexCart

## 1. Overview

Production NexCart should be designed for:

```text id="m0f0dw"
Scalability
+
High Availability
+
Fault Tolerance
+
Disaster Recovery
+
Business Continuity
```

These are related but different concepts.

| Concept | Goal |
|---|---|
| Scaling | Handle increasing workload |
| High Availability | Minimize service interruption |
| Fault Tolerance | Continue operating despite failures |
| Backup | Recover lost data |
| Disaster Recovery | Restore service after major failure |
| Business Continuity | Keep critical business operations running |

---

# 2. Scaling

Scaling means increasing system capacity to handle workload.

Two primary approaches:

```text id="xqgk2f"
Horizontal Scaling
        vs
Vertical Scaling
```

---

# 3. Vertical Scaling

Increase resources of an existing instance/node.

```text id="h4b0p6"
2 CPU / 4 GB RAM
       ↓
8 CPU / 32 GB RAM
```

Advantages:

```text id="b5c6k9"
Simple
Fewer instances
Useful for some stateful workloads
```

Limitations:

```text id="6c1lzw"
Hardware/resource limit
Larger failure domain
Potential downtime during resizing
```

---

# 4. Horizontal Scaling

Add more instances/pods.

```text id="nq7mtr"
1 Pod
 ↓
3 Pods
 ↓
10 Pods
```

For NexCart:

```text id="d7xw6a"
             Ingress
                |
       +--------+--------+
       |        |        |
       v        v        v
   Product   Product   Product
```

Benefits:

```text id="j0p6a1"
Higher capacity
Better availability
Failure isolation
Works well with Kubernetes
```

---

# 5. Kubernetes Horizontal Scaling

Scale manually:

```bash id="g7k5ne"
kubectl scale deployment product-service \
  --replicas=5 \
  -n nexcart
```

Check:

```bash id="5rq3b8"
kubectl get pods -n nexcart
kubectl get deployment product-service -n nexcart
```

For production, prefer declarative configuration through Helm/GitOps rather than persistent manual scaling.

---

# 6. HPA

Horizontal Pod Autoscaler automatically changes pod replicas based on metrics.

```text id="e1p2kj"
Traffic ↑
   ↓
CPU / Memory / Custom Metric ↑
   ↓
HPA
   ↓
More Pods
```

Example:

```yaml id="k9n3zo"
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: product-service
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: product-service
  minReplicas: 2
  maxReplicas: 10
```

---

# 7. HPA Requirements

For CPU-based HPA, resource requests should be configured.

Example:

```yaml id="4rqv3e"
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "512Mi"
```

Without meaningful resource requests, utilization-based scaling may not behave as expected.

---

# 8. HPA Troubleshooting

Check:

```bash id="1q5q34"
kubectl get hpa -n nexcart
kubectl describe hpa <hpa> -n nexcart
```

Check metrics:

```bash id="txu8u4"
kubectl top pods -n nexcart
```

Verify:

```text id="5v0m5x"
Metrics available
Resource requests configured
Target utilization
Min replicas
Max replicas
Current replicas
```

---

# 9. Cluster Autoscaler

HPA scales Pods.

Cluster Autoscaler scales nodes.

```text id="g4i0uq"
Traffic ↑
   ↓
HPA
   ↓
Pods ↑
   ↓
Node capacity insufficient
   ↓
Cluster Autoscaler
   ↓
Nodes ↑
```

This distinction is important in interviews.

---

# 10. HPA vs Cluster Autoscaler

| Component | Scales | Trigger |
|---|---|---|
| HPA | Pods | Workload metrics |
| Cluster Autoscaler | Nodes | Unschedulable pods |
| Manual scaling | Pods/Nodes | Engineer decision |

Example:

```text id="a8u4v0"
HPA:
3 Pods → 8 Pods

Cluster Autoscaler:
3 Nodes → 5 Nodes
```

---

# 11. Vertical Pod Autoscaler

VPA adjusts Pod resource requests/limits based on observed usage.

Concept:

```text id="0p9e8r"
Observed usage
      ↓
VPA recommendation
      ↓
CPU / Memory request adjustment
```

Use carefully because some VPA modes can restart Pods.

For latency-sensitive stateless services, HPA is often the primary scaling mechanism.

---

# 12. AKS Node Pool Scaling

AKS can use autoscaling node pools.

Concept:

```text id="uw2r9j"
Minimum Nodes: 2
Maximum Nodes: 8
```

When scheduling capacity becomes insufficient:

```text id="qv7kmg"
2 Nodes
 ↓
3 Nodes
 ↓
4 Nodes
```

When demand decreases:

```text id="ksg5fs"
4 Nodes
 ↓
3 Nodes
 ↓
2 Nodes
```

Do not scale below the capacity required for availability.

---

# 13. Multiple Node Pools

Use different node pools when workloads have different requirements.

Example:

```text id="s4e9wm"
AKS
 |
 +-- System Node Pool
 |
 +-- Application Node Pool
 |
 +-- High-Memory Node Pool
 |
 +-- Specialized Node Pool
```

Possible use cases:

```text id="8vupm4"
System workloads
Application workloads
Memory-intensive workloads
GPU workloads
Isolated workloads
```

Use taints/tolerations and affinity carefully.

---

# 14. Scaling the NexCart Services

Stateless services are strong candidates for horizontal scaling:

```text id="j3nyz5"
Product Service
Order Service
Payment Service
Notification Service
```

Example:

```text id="5w8r6d"
Product:      2 → 8
Order:        2 → 10
Payment:      2 → 6
Notification: 2 → 5
```

The actual numbers should come from load testing and production traffic rather than arbitrary configuration.

---

# 15. Stateless vs Stateful

### Stateless

The Pod does not require local persistent state.

```text id="6qf8fy"
Request
  ↓
Any healthy Pod
```

Easy to scale horizontally.

### Stateful

The workload depends on persistent identity or storage.

```text id="q5r0q8"
Database
Persistent storage
Stateful processing
```

Scaling requires additional design considerations.

---

# 16. Session Management

Avoid storing user sessions only in Pod memory when horizontal scaling is required.

Bad:

```text id="b6o8lc"
User
 ↓
Pod A
 ↓
Session stored in Pod A
```

Next request:

```text id="z20y5b"
User
 ↓
Pod B
 ↓
Session missing
```

Better:

```text id="e4d5ik"
User
 ↓
Load Balancer
 ↓
Any Pod
 ↓
Shared session/state store
```

Or use stateless authentication where appropriate.

---

# 17. Database Scaling

Application scaling does not automatically scale the database.

```text id="x6g3q7"
10 Pods
   ↓
Database
   ↓
Potential bottleneck
```

Monitor:

```text id="o7n1k6"
Connections
CPU
Memory
IOPS
Latency
Slow queries
Locks
Storage
```

---

# 18. Database Scaling Strategies

Depending on the database:

```text id="6g7s4c"
Vertical Scaling
Read Replicas
Connection Pooling
Caching
Index Optimization
Partitioning/Sharding
Query Optimization
```

Do not add application Pods indefinitely if the database is already saturated.

---

# 19. Caching

Caching can reduce database load.

```text id="i8g7f1"
Request
  ↓
Cache
  |
  +-- Hit → Response
  |
  +-- Miss → Database
```

Potential cache use cases:

```text id="5m0n5n"
Product catalog
Configuration
Reference data
Frequently accessed data
```

Cache invalidation must be designed carefully.

---

# 20. Scaling Based on Business Metrics

CPU is not always the best scaling signal.

Example:

```text id="n8j4vq"
Payment queue length
Order queue length
Requests/sec
Queue depth
Active sessions
Transaction latency
```

Example:

```text id="4c5n5f"
Queue depth ↑
      ↓
Scale Notification Service
```

This can be more meaningful than CPU alone.

---

# 21. Load Testing

Before defining production scaling limits, test the system.

Measure:

```text id="x2p6s0"
Requests/sec
Latency
Error rate
CPU
Memory
Database load
Pod scaling time
Node scaling time
```

Find:

```text id="4w7e8d"
Normal capacity
Peak capacity
Breaking point
Recovery behavior
```

---

# 22. Capacity Planning

Example:

```text id="d5x7hv"
Current traffic: 1,000 req/min
Peak traffic:    5,000 req/min
Growth forecast: 2x
```

Do not size only for current traffic.

Consider:

```text id="a7x4cz"
Expected growth
Peak traffic
Failure capacity
Deployment overhead
Autoscaling delay
Business events
```

---

# 23. High Availability

High Availability means designing the system to minimize downtime.

HA does not mean:

```text id="f7u0bn"
"Nothing will ever fail."
```

It means:

```text id="q1m3y6"
Failures occur
      ↓
System continues or recovers quickly
```

---

# 24. Single Point of Failure

A component is a SPOF when its failure can take down the service.

Example:

```text id="w0n5q8"
Internet
   ↓
Single Server
   ↓
Application
```

Server failure:

```text id="j3m5a8"
Application DOWN
```

HA design:

```text id="p8x2m4"
          Load Balancer
          /          \
       Pod A        Pod B
```

---

# 25. Multiple Replicas

For production services:

```yaml id="s8l2vf"
spec:
  replicas: 3
```

This provides redundancy.

But replicas alone are not enough.

Also consider:

```text id="f5b7h9"
Node distribution
Availability zones
Pod anti-affinity
PodDisruptionBudget
Readiness probes
```

---

# 26. Availability Zones

A node failure is not the only failure scenario.

Possible architecture:

```text id="t3c8mw"
                 AKS
                  |
        +---------+---------+
        |         |         |
       AZ-1      AZ-2      AZ-3
        |         |         |
       Pod       Pod       Pod
```

If one zone has an infrastructure failure:

```text id="w5v9n3"
AZ-1 DOWN
   ↓
AZ-2 + AZ-3
   ↓
Application continues
```

The exact zone architecture depends on Azure region support and workload requirements.

---

# 27. Pod Distribution

Avoid:

```text id="9h6z4c"
Node 1:
Pod A
Pod B
Pod C
```

If Node 1 fails:

```text id="8f3k2v"
A + B + C
      ↓
All unavailable
```

Prefer spreading replicas across nodes/zones.

Use:

```text id="j6p4n8"
Topology spread constraints
Pod anti-affinity
```

---

# 28. Topology Spread

Concept:

```yaml id="x0d3s8"
topologySpreadConstraints:
  - maxSkew: 1
    topologyKey: topology.kubernetes.io/zone
    whenUnsatisfiable: DoNotSchedule
```

This encourages an even distribution across zones.

Test scheduling behavior before applying strict constraints to production.

---

# 29. PodDisruptionBudget

PDB protects availability during voluntary disruptions.

Example:

```yaml id="2k7r5m"
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: product-service
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: product-service
```

Important:

```text id="n5h8q1"
PDB protects against voluntary disruptions.
It does not prevent every infrastructure failure.
```

---

# 30. Readiness Probes and HA

A Pod should receive traffic only when ready.

```text id="p5x8m2"
Pod starts
   ↓
Readiness check
   ↓
PASS
   ↓
Service sends traffic
```

If readiness fails:

```text id="k3n7q4"
Pod remains running
but
traffic is removed
```

This is critical for graceful deployments and failure isolation.

---

# 31. Liveness vs Readiness

| Probe | Purpose |
|---|---|
| Readiness | Can receive traffic? |
| Liveness | Is process healthy enough to continue? |
| Startup | Has application finished starting? |

Incorrect probes can reduce availability instead of improving it.

---

# 32. Graceful Deployment for HA

Use:

```text id="1p6y9m"
Multiple replicas
+
RollingUpdate
+
Readiness probes
+
Graceful shutdown
+
PDB
```

Deployment flow:

```text id="4s7b1m"
Old Pods
   ↓
New Pod
   ↓
Readiness PASS
   ↓
Traffic
   ↓
Old Pod removed
```

---

# 33. Rolling Update

Check strategy:

```bash id="w7n4y1"
kubectl get deployment <deployment> \
  -n nexcart \
  -o yaml
```

Typical strategy:

```yaml id="h6j2z7"
strategy:
  type: RollingUpdate
```

Tune:

```text id="p8v5s1"
maxUnavailable
maxSurge
```

based on service capacity and availability requirements.

---

# 34. Blue-Green Deployment

Architecture:

```text id="n0m8x4"
              Load Balancer
                    |
             +------+------+
             |             |
           Blue          Green
          v1.0            v2.0
```

Traffic initially:

```text id="b2f6r8"
100% → Blue
```

After validation:

```text id="z6m3q1"
100% → Green
```

Advantages:

```text id="m4n8s0"
Fast rollback
Strong isolation
Easy validation
```

Disadvantages:

```text id="x8k2q5"
Higher infrastructure cost
Database compatibility complexity
```

---

# 35. Canary Deployment

Canary gradually exposes users to a new version.

```text id="s7w5j3"
v1 → 95%
v2 → 5%
```

Monitor:

```text id="h1m6c4"
Error rate
Latency
Business metrics
Resource usage
```

Then:

```text id="q4v8n2"
5%
 ↓
25%
 ↓
50%
 ↓
100%
```

If metrics degrade:

```text id="d8s2k6"
Stop
 ↓
Rollback
```

---

# 36. High Availability vs Disaster Recovery

### High Availability

Handles failures quickly within the normal operating environment.

```text id="q6k1r8"
Node fails
 ↓
Another Pod continues
```

### Disaster Recovery

Handles major infrastructure or regional disasters.

```text id="b4n7x3"
Region unavailable
 ↓
Recover in another region
```

HA and DR solve different problems.

---

# 37. Disaster Recovery

DR is the capability to restore critical services after a major disaster.

Possible disasters:

```text id="e8j2p6"
Azure region outage
Data corruption
Security incident
Accidental deletion
Major infrastructure failure
Database failure
```

---

# 38. RPO

Recovery Point Objective.

It answers:

> How much data can we afford to lose?

Example:

```text id="k2v7s9"
RPO = 15 minutes
```

Means the recovery strategy should target no more than approximately 15 minutes of data loss.

---

# 39. RTO

Recovery Time Objective.

It answers:

> How quickly must the service be restored?

Example:

```text id="x6m4p1"
RTO = 1 hour
```

Means the service should target recovery within approximately one hour.

---

# 40. RPO vs RTO

| Metric | Question |
|---|---|
| RPO | How much data can we lose? |
| RTO | How long can we be unavailable? |

Example:

```text id="v3n8c5"
RPO = 15 min
RTO = 1 hour
```

These are business requirements, not merely Kubernetes settings.

---

# 41. Backup Strategy

Back up:

```text id="m6r4z8"
Database
Persistent data
Configuration
Critical secrets/keys according to recovery policy
Infrastructure definitions
Application artifacts
```

Do not rely on:

```text id="a8q2w7"
Docker image
Git repository
Kubernetes YAML
```

as a replacement for application data backups.

---

# 42. Backup 3-2-1 Principle

A traditional backup strategy:

```text id="q8f1m5"
3 copies
2 different media/storage types
1 offsite copy
```

For cloud systems, adapt the principle to:

```text id="y7k3c9"
Primary data
+
Independent backup
+
Offsite / cross-region recovery copy
```

The exact implementation depends on compliance and business requirements.

---

# 43. Backup Is Not DR

Important:

```text id="c1v5n7"
Backup
   ≠
Disaster Recovery
```

Backup answers:

```text id="z6m2q4"
Can I recover the data?
```

DR answers:

```text id="u9p4x1"
Can I recover the entire service?
```

DR requires:

```text id="w5r7k3"
Infrastructure
Application
Data
Networking
Identity
Secrets
DNS
Monitoring
Runbooks
```

---

# 44. Azure DR Architecture

A possible production model:

```text id="e4m8s2"
             Global DNS / Traffic
                     |
            +--------+--------+
            |                 |
            v                 v
       Primary Region    Secondary Region
            |                 |
           AKS               AKS
            |                 |
           ACR               ACR
            |                 |
        Database         Database Replica
            |                 |
            +-------+---------+
                    |
              Backup / Recovery
```

Actual architecture depends on RPO/RTO, application state and Azure service capabilities.

---

# 45. Active-Passive DR

Primary region handles traffic.

```text id="q7m3k5"
Primary
   |
 100% traffic

Secondary
   |
 Standby
```

During disaster:

```text id="r4n8v1"
Primary DOWN
    ↓
DNS / Traffic Manager
    ↓
Secondary
    ↓
Traffic
```

Advantages:

```text id="a6k2x9"
Lower cost
Simpler operations
```

Disadvantages:

```text id="m8q5z2"
Failover time
Standby capacity
Potential data lag
```

---

# 46. Active-Active DR

Both regions serve traffic.

```text id="j5v8n3"
              Global Traffic
                /        \
               /          \
          Region A      Region B
             |              |
            AKS            AKS
             |              |
             +------+-------+
                    |
             Data Architecture
```

Advantages:

```text id="w3x7k2"
Higher availability
Better regional utilization
Faster failover
```

Challenges:

```text id="n6m4p8"
Higher cost
Data consistency
Traffic management
Operational complexity
```

---

# 47. Database DR

Database recovery often becomes the most important DR component.

Consider:

```text id="b7q2m5"
Backup
Replication
Point-in-time restore
Cross-region replication
Failover
Consistency
Recovery testing
```

Application recovery is useless if critical business data cannot be recovered.

---

# 48. DNS Failover

Potential flow:

```text id="s4n8k2"
User
 ↓
Global DNS / Traffic Manager
 ↓
Healthy Region
```

During failure:

```text id="f8m3q7"
Region A unhealthy
       ↓
Health Check
       ↓
Region B
```

DNS TTL and health-check behavior affect failover time.

---

# 49. Infrastructure Recovery with Terraform

Terraform can recreate infrastructure:

```text id="z7k2p4"
Terraform Code
      ↓
New Azure Environment
      ↓
VNet
AKS
ACR
Key Vault
Monitoring
```

This is one reason infrastructure should be defined as code.

But Terraform alone does not restore application data.

---

# 50. Helm and DR

Helm charts should allow the application to be deployed into a new environment.

Example:

```text id="y3m6q8"
helm/nexcart/
       |
       +-- values-prod.yaml
       |
       +-- values-dr.yaml
```

The DR values may define:

```text id="n8p4w2"
Ingress
Replica count
Database endpoint
Region-specific settings
Secrets integration
```

Never hard-code region-specific secrets into the chart.

---

# 51. ACR and DR

If the primary Azure region is unavailable, the secondary environment must still obtain application images.

Possible strategies:

```text id="c4m7x1"
ACR geo-replication
+
Secondary registry
+
Pre-pulled critical images
```

The preferred strategy depends on availability and network requirements.

---

# 52. Key Vault and DR

A DR environment needs access to required secrets and certificates.

Design for:

```text id="k9q3v6"
Identity
 ↓
Key Vault
 ↓
Required Secrets
```

Do not manually copy secrets into the DR cluster unless the security architecture explicitly requires it.

---

# 53. Service Dependencies in DR

Map every dependency:

```text id="h5m8r2"
NexCart
 |
 +-- Database
 +-- Payment Provider
 +-- Notification Provider
 +-- Key Vault
 +-- ACR
 +-- DNS
 +-- Identity
 +-- Monitoring
```

A DR plan that restores AKS but cannot access the payment provider is incomplete.

---

# 54. DR Dependency Matrix

| Dependency | Primary | DR Strategy |
|---|---|---|
| AKS | Region A | Secondary AKS |
| ACR | Region A | Geo-replication/secondary |
| Database | Primary | Replica/backup |
| Key Vault | Primary | Recovery/secondary strategy |
| DNS | Global | Health-based failover |
| Certificates | Managed | Automated renewal/recovery |
| GitLab | External | Pipeline available |
| Monitoring | Central | Cross-region visibility |

Actual Azure service capabilities and organizational architecture must be validated before implementation.

---

# 55. Disaster Recovery Runbook

```text id="v8m2q4"
1. Declare disaster
2. Confirm primary-region impact
3. Stop conflicting changes
4. Validate secondary environment
5. Confirm database recovery point
6. Restore/activate secondary services
7. Validate identity and secrets
8. Validate application
9. Validate business transactions
10. Switch traffic
11. Monitor
12. Communicate status
13. Investigate primary failure
14. Plan failback
```

---

# 56. DR Testing

A DR strategy is not proven until tested.

Test:

```text id="k6p9x3"
Database restore
AKS redeployment
Secret retrieval
Certificate availability
DNS failover
Application startup
External dependencies
Monitoring
Rollback/failback
```

Recommended:

```text id="m3q8v1"
Tabletop Exercise
       ↓
Technical Recovery Test
       ↓
Controlled Failover
       ↓
Full DR Exercise
```

---

# 57. Backup Restore Test

A backup that cannot be restored is not a reliable backup.

Test:

```text id="f4x7n2"
Backup
 ↓
Restore
 ↓
Validate schema/data
 ↓
Application connection
 ↓
Business transaction
```

Measure:

```text id="r5k8m3"
Actual RTO
Actual RPO
Restore duration
Data completeness
Operational issues
```

---

# 58. Failover vs Failback

### Failover

```text id="n8p3v6"
Primary
 ↓
Failure
 ↓
Secondary
```

### Failback

```text id="x5m7q2"
Secondary
 ↓
Primary recovered
 ↓
Synchronize data
 ↓
Validate
 ↓
Return traffic
```

Failback should be treated as carefully as failover.

---

# 59. Chaos / Failure Testing

Controlled failure testing can validate HA.

Examples:

```text id="p2k6w8"
Terminate Pod
Drain Node
Stop dependency
Increase traffic
Simulate zone failure
Test database failover
Expire test credential
```

Goal:

```text id="z7m4q1"
Verify the system behaves as designed.
```

Do not perform destructive experiments directly in production without an approved process and controlled blast radius.

---

# 60. Autoscaling Failure Scenario

### Scenario

Traffic increases rapidly but Pods are not scaling.

### Check

```text id="v6m2x8"
1. HPA status
2. Metrics availability
3. Resource requests
4. Target utilization
5. Max replicas
6. Cluster capacity
7. Cluster Autoscaler
8. Pod scheduling
```

Commands:

```bash id="p5q7n3"
kubectl get hpa -n nexcart
kubectl describe hpa <hpa> -n nexcart
kubectl top pods -n nexcart
kubectl get nodes
```

### Root causes

```text id="w8k3m5"
Metrics unavailable
Max replicas reached
No node capacity
Incorrect resource requests
Scheduling constraints
```

---

# 61. Node Failure Scenario

### Scenario

One AKS node becomes unavailable.

Expected behavior:

```text id="a4n7x2"
Node failure
   ↓
Pods become unavailable
   ↓
Kubernetes reschedules replicas
   ↓
Healthy nodes
   ↓
Service continues
```

Check:

```bash id="g5m8q1"
kubectl get nodes
kubectl get pods -n nexcart -o wide
kubectl get events -n nexcart --sort-by=.lastTimestamp
```

If all replicas were on the failed node, HA was incorrectly designed.

---

# 62. Zone Failure Scenario

### Scenario

One availability zone becomes unavailable.

Good architecture:

```text id="q3m7v5"
AZ-1 → Pod A
AZ-2 → Pod B
AZ-3 → Pod C
```

Failure:

```text id="y8n2k4"
AZ-1 DOWN
```

Expected:

```text id="f6p9x3"
AZ-2 + AZ-3
       ↓
Continue serving traffic
```

This requires sufficient capacity in the remaining zones.

---

# 63. Database Failure Scenario

### Scenario

Database becomes unavailable.

Do not immediately scale application Pods.

First determine:

```text id="b5q8m2"
Database availability
Network
Connection pool
Credentials
DNS
Failover status
Replication
```

If the database is the bottleneck:

```text id="k7m4x1"
10 Pods
 ↓
More DB connections
 ↓
Database overload
```

Scaling application Pods can make the incident worse.

---

# 64. Cost vs Availability

Higher availability usually increases cost.

Example:

```text id="n3v8q5"
Single region
1 node
2 pods
        ↓
Low cost
Lower resilience
```

versus:

```text id="x6m2p9"
Multi-zone
Multiple nodes
Multiple replicas
Secondary region
Database replication
        ↓
Higher cost
Higher resilience
```

Choose architecture based on:

```text id="r8k4w1"
Business criticality
RTO
RPO
SLA/SLO
Risk
Budget
Compliance
```

---

# 65. Production Scaling Checklist

```text id="u5m8q2"
[ ] HPA configured
[ ] Resource requests configured
[ ] Resource limits configured
[ ] Cluster Autoscaler configured
[ ] Node pool limits reviewed
[ ] Load testing completed
[ ] Database capacity tested
[ ] Scaling alerts configured
[ ] Pod distribution reviewed
[ ] Availability zones considered
[ ] PDB configured
[ ] Graceful shutdown implemented
[ ] Readiness probes validated
```

---

# 66. Production HA Checklist

```text id="k3n7p5"
[ ] Multiple replicas
[ ] Multiple nodes
[ ] Multiple availability zones where required
[ ] Pod anti-affinity / topology spread
[ ] PDB
[ ] Readiness probes
[ ] Rolling deployment
[ ] Graceful shutdown
[ ] Health monitoring
[ ] Dependency redundancy
[ ] Database HA
[ ] Load balancer/Ingress redundancy
```

---

# 67. Production DR Checklist

```text id="m8q4x2"
[ ] Business RTO defined
[ ] Business RPO defined
[ ] Database backups
[ ] Restore tested
[ ] Secondary infrastructure strategy
[ ] Terraform recovery
[ ] Helm deployment tested
[ ] ACR image availability
[ ] Key Vault/identity recovery
[ ] DNS failover
[ ] Certificate recovery
[ ] External dependency plan
[ ] Monitoring in DR
[ ] Failover runbook
[ ] Failback runbook
[ ] Regular DR testing
```

---

# 68. Senior Interview Questions

### Q1. What is the difference between scaling and high availability?

> Scaling increases capacity to handle workload. High availability reduces service interruption by eliminating single points of failure and distributing workloads across redundant infrastructure. A system can scale without being highly available, and it can be highly available without automatically scaling.

---

### Q2. What is the difference between HPA and Cluster Autoscaler?

> HPA scales Kubernetes Pods based on workload metrics. Cluster Autoscaler scales the underlying nodes when Pods cannot be scheduled because there is insufficient capacity. In production they often work together.

---

### Q3. How would you design NexCart for high availability?

> I would run multiple replicas of stateless services, distribute them across nodes and availability zones, use readiness probes, PDBs, topology spread constraints and rolling deployments, and ensure the database and critical external dependencies also have an appropriate HA design.

---

### Q4. What happens if an AKS node fails?

> Kubernetes detects that the node is unavailable and reschedules eligible workloads onto healthy nodes. The application remains available if sufficient replicas exist and those replicas are distributed across failure domains. I would verify node status, pod placement, events and available capacity.

---

### Q5. How would you handle a sudden traffic spike?

> I would first verify whether the increase is legitimate traffic or an abnormal pattern. Then I would monitor HPA, metrics, pod resources, node capacity and database saturation. If HPA and Cluster Autoscaler are configured correctly, Pods and nodes should scale within their defined limits. I would also protect downstream dependencies from overload.

---

### Q6. What are RTO and RPO?

> RTO defines how quickly the service needs to be restored after a disaster. RPO defines how much data loss the business can tolerate. Both are business requirements that drive the technical DR architecture.

---

### Q7. Is backup enough for disaster recovery?

> No. Backup provides data recovery, while DR covers restoration of the complete service including infrastructure, networking, identity, secrets, application artifacts, DNS, monitoring and external dependencies. A backup must also be regularly restored and tested.

---

### Q8. Active-active vs active-passive?

> Active-active serves traffic from multiple regions simultaneously and provides strong availability but increases cost and data-consistency complexity. Active-passive keeps a secondary environment ready for failover and is generally simpler and cheaper, but failover takes longer.

---

### Q9. How do you prevent all replicas from being on the same node?

> I use topology spread constraints or pod anti-affinity and ensure the cluster has enough nodes across the required availability zones. I also verify actual scheduling with `kubectl get pods -o wide`.

---

### Q10. How do you test DR?

> I don't consider the DR plan valid until it has been tested. I perform controlled recovery tests covering infrastructure deployment, database restore, secrets, certificates, DNS, application health and business transactions, and I measure the actual RTO and RPO against the business targets.

---

### Q11. Can adding more Pods make an incident worse?

> Yes. If the database or another downstream dependency is saturated, adding application Pods can increase concurrent connections and amplify the failure. Scaling decisions must consider the complete dependency chain rather than only application CPU.

---

### Q12. How would you design NexCart for zero-downtime deployments?

> I would use multiple replicas, rolling updates, readiness and startup probes, graceful shutdown, PDBs and appropriate rollout parameters. For higher-risk changes, I would use canary or blue-green deployment with automated health and business validation.

---

# 69. Senior Production Scenario

## Scenario

Traffic suddenly increases 5x.

```text id="w5q9n2"
Traffic
  ↑ 5x
   ↓
Ingress
   ↓
HPA
   ↓
Pods increase
   ↓
Cluster capacity reached
   ↓
Cluster Autoscaler
   ↓
New Nodes
```

But suddenly:

```text id="g8m3v6"
Database CPU = 95%
Database latency ↑
API latency ↑
```

### Senior response

Do **not** blindly increase application replicas.

Investigate:

```text id="f4n7k2"
Database bottleneck
Connection pool
Slow queries
Cache
Read replicas
Traffic pattern
```

Possible solution:

```text id="p6x2m8"
Application
 ↓
Cache
 ↓
Read replica
 ↓
Primary database
```

The key principle is:

> **Scale the bottleneck, not just the symptom.**

---

# 70. End-to-End NexCart Scaling Architecture

```text id="q9m4x6"
                         Internet
                            |
                            v
                    Global Traffic Layer
                            |
                +-----------+-----------+
                |                       |
             Region A                Region B
              Primary                 DR
                |                       |
                v                       v
               AKS                     AKS
                |                       |
        +-------+-------+       +-------+-------+
        |       |       |       |       |       |
      Pods    Pods    Pods    Pods    Pods    Pods
        |       |       |       |       |       |
        +-------+-------+       +-------+-------+
                |                       |
                v                       v
             Services                Services
                |
                v
          Database Layer
                |
        +-------+-------+
        |               |
     Primary         Replica/DR
```

Scaling:

```text id="n7k2m5"
Traffic
 ↓
HPA
 ↓
Pods
 ↓
Cluster Autoscaler
 ↓
Nodes
```

HA:

```text id="w4p8x1"
Multiple Pods
+
Multiple Nodes
+
Multiple Zones
+
Redundant Dependencies
```

DR:

```text id="m6q3v9"
Primary Region
      ↓
Replication / Backup
      ↓
Secondary Region
      ↓
Failover
```

---

# 71. Complete NexCart Resilience Model

```text id="r8m3k6"
                    CLIENT
                       |
                       v
              Global Traffic
                       |
          +------------+------------+
          |                         |
          v                         v
      REGION A                  REGION B
      PRIMARY                     DR
          |                         |
         AKS                       AKS
          |                         |
   +------+------+           +------+------+
   |      |      |           |      |      |
 Product Order Payment     Product Order Payment
   |      |      |           |      |      |
   +------+------+           +------+------+
          |                         |
          +-----------+-------------+
                      |
                  Data Layer
                      |
              Backup / Replica
                      |
                  Key Vault
                      |
               Identity / RBAC

              Observability
        Metrics + Logs + Traces
```

---

# 72. Final Resilience Principles

```text id="t2w6q4"
1. Scale horizontally for stateless workloads.
2. Configure realistic resource requests and limits.
3. Use HPA for Pods.
4. Use Cluster Autoscaler for nodes.
5. Do not scale blindly when dependencies are saturated.
6. Distribute replicas across failure domains.
7. Use readiness probes to control traffic.
8. Use graceful shutdown.
9. Use PDBs carefully.
10. Design databases separately for HA and scaling.
11. Define business-driven RTO and RPO.
12. Backups must be independently restorable.
13. DR must include infrastructure, data, identity, secrets and networking.
14. Test failover and failback.
15. Automate infrastructure recovery with Terraform.
16. Make Helm deployments reproducible.
17. Ensure application images are available during regional recovery.
18. Monitor actual capacity and recovery performance.
19. Prefer controlled rollout strategies for high-risk changes.
20. Treat HA, scaling and DR as one resilience strategy.
```

# Core Senior DevOps Principle

> **A production system is not resilient simply because Kubernetes can create more Pods. True resilience means the application can absorb increased traffic, survive Pod/node/zone failures, protect critical dependencies from overload, recover data within the required RPO, restore service within the required RTO, and prove those capabilities through regular testing.**