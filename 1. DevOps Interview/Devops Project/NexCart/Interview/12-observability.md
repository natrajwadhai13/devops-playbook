---
title: "12-observability"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 12
---

# 📊 12. Observability – Monitoring, Logs, Metrics & Tracing

## 1. Overview

Observability answers three production questions:

```text id="obs01"
What is happening?
Why is it happening?
Which component is causing it?
```

For NexCart, observability should cover:

```text id="obs02"
Users
  ↓
Ingress / Load Balancer
  ↓
AKS
  ↓
Microservices
  ↓
Databases / External APIs
```

Recommended observability stack:

```text id="obs03"
Applications
    |
    +---- Metrics
    |       ↓
    |     Grafana Mimir
    |
    +---- Logs
    |       ↓
    |     Grafana Loki
    |
    +---- Traces
            ↓
          Grafana Tempo

              ↓
          Grafana
              |
              ↓
       Dashboards + Alerts
```

OpenTelemetry provides a common telemetry framework, while Grafana Alloy can collect, process and forward telemetry.

---

# 2. Three Pillars of Observability

## Metrics

Numerical measurements over time.

Examples:

```text id="obs04"
CPU usage
Memory usage
Request rate
Error rate
Latency
Pod restarts
HTTP 5xx
Database connections
```

---

## Logs

Detailed event records.

Example:

```text id="obs05"
2026-10-10T10:15:23Z
ERROR
product-service
MongoDB connection timeout
```

Logs answer:

```text id="obs06"
What exactly happened?
```

---

## Traces

Traces follow a request across multiple services.

Example:

```text id="obs07"
Client
  ↓
Ingress
  ↓
Order Service
  ↓
Payment Service
  ↓
Notification Service
```

Traces answer:

```text id="obs08"
Where did the request spend time?
Which service failed?
```

---

# 3. Monitoring vs Observability

Monitoring generally tells us:

```text id="obs09"
Something is wrong.
```

Observability helps determine:

```text id="obs10"
What is wrong?
Where is it wrong?
Why is it wrong?
What changed?
```

Example:

```text id="obs11"
Monitoring:
HTTP 500 increased.

Observability:
HTTP 500 increased
→ Order Service
→ Payment API timeout
→ external dependency latency increased
→ requests exceeded timeout
```

---

# 4. NexCart Observability Architecture

```text id="obs12"
                    Users
                      |
                      v
                 Ingress
                      |
             +--------+--------+
             |        |        |
             v        v        v
          Product   Order    Payment
          Service   Service   Service
             |        |        |
             +--------+--------+
                      |
                Notification
                   Service
                      |
                      v
                External APIs
```

Telemetry:

```text id="obs13"
Microservices
     |
     +---- OpenTelemetry
     |
     +---- Prometheus Metrics
     |
     +---- Application Logs
     |
     v
Grafana Alloy
     |
 +---+---------+----------+
 |             |          |
 v             v          v
Mimir         Loki       Tempo
Metrics       Logs       Traces
 |             |          |
 +-------------+----------+
               |
               v
            Grafana
               |
        Dashboards / Alerts
```

---

# 5. Golden Signals

The four classic golden signals are:

```text id="obs14"
1. Latency
2. Traffic
3. Errors
4. Saturation
```

## Latency

How long requests take.

```text id="obs15"
p50
p95
p99
```

For production troubleshooting, p95/p99 are usually more useful than average latency.

---

## Traffic

How much traffic the service receives.

Examples:

```text id="obs16"
Requests/sec
Transactions/sec
Messages/sec
```

---

## Errors

Examples:

```text id="obs17"
HTTP 4xx
HTTP 5xx
Application exceptions
Timeouts
Failed transactions
```

---

## Saturation

How close a system is to its limits.

Examples:

```text id="obs18"
CPU
Memory
Disk
Network
Connection pool
Thread pool
Pod capacity
Node capacity
```

---

# 6. RED Method

For services:

```text id="obs19"
R = Rate
E = Errors
D = Duration
```

Example:

```text id="obs20"
Product Service
  |
  +-- Request Rate
  +-- Error Rate
  +-- Request Duration
```

Useful for application dashboards.

---

# 7. USE Method

For infrastructure:

```text id="obs21"
U = Utilization
S = Saturation
E = Errors
```

Example:

```text id="obs22"
AKS Node
  |
  +-- CPU Utilization
  +-- Memory Saturation
  +-- Disk Errors
```

RED is more application-focused.

USE is more infrastructure-focused.

---

# 8. Application Metrics

NexCart services should expose useful metrics.

Examples:

```text id="obs23"
HTTP request count
HTTP error count
Request duration
Active requests
Database connection count
External API latency
Queue/message count
Business transaction count
```

Example:

```text id="obs24"
product_requests_total
product_request_duration_seconds
product_errors_total
```

Avoid creating excessive high-cardinality labels.

---

# 9. Prometheus Metrics

Prometheus-style metrics typically look like:

```text id="obs25"
http_requests_total{
  service="product-service",
  method="GET",
  status="200"
} 15243
```

Important metric concepts:

```text id="obs26"
Counter
Gauge
Histogram
Summary
```

---

# 10. Counter

A counter increases over time.

Examples:

```text id="obs27"
requests_total
errors_total
orders_created_total
```

Concept:

```text id="obs28"
100
 ↓
101
 ↓
102
 ↓
103
```

Counters should normally not decrease except when the process restarts and the metric is recreated.

---

# 11. Gauge

A gauge represents a current value.

Examples:

```text id="obs29"
active_connections
queue_depth
memory_usage
```

It can increase or decrease.

```text id="obs30"
100 → 80 → 120 → 60
```

---

# 12. Histogram

Useful for measuring distributions such as latency.

Example:

```text id="obs31"
Request duration:

50ms
80ms
100ms
200ms
2s
5s
```

Histograms allow analysis such as:

```text id="obs32"
p50
p95
p99
```

---

# 13. High Cardinality

Avoid labels such as:

```text id="obs33"
user_id
request_id
transaction_id
```

on every metric.

Bad:

```text id="obs34"
http_requests_total{
  user_id="123456"
}
```

Millions of unique label values can create a large number of time series.

Better:

```text id="obs35"
service
method
route
status_code
```

Use traces/logs for highly unique identifiers such as request IDs.

---

# 14. Grafana Mimir

Mimir is designed for scalable long-term storage and querying of Prometheus-compatible metrics.

Concept:

```text id="obs36"
Application
    ↓
Metrics
    ↓
Alloy
    ↓
Mimir
    ↓
Grafana
```

Benefits:

- Long-term metrics storage
- Horizontal scalability
- Multi-tenant architecture
- Prometheus-compatible querying

---

# 15. Loki

Loki is used for centralized log aggregation.

Concept:

```text id="obs37"
AKS Pods
   ↓
Container Logs
   ↓
Grafana Alloy
   ↓
Loki
   ↓
Grafana
```

Loki is designed around indexed labels rather than indexing every log line like a traditional full-text logging system.

---

# 16. Tempo

Tempo stores distributed traces.

```text id="obs38"
Product Service
      |
Order Service
      |
Payment Service
      |
Notification Service
      |
      v
    Tempo
      |
      v
   Grafana
```

Tracing becomes especially useful when a request crosses multiple microservices.

---

# 17. OpenTelemetry

OpenTelemetry provides standardized telemetry instrumentation and collection.

It supports:

```text id="obs39"
Metrics
Logs
Traces
```

Typical flow:

```text id="obs40"
Application
   ↓
OpenTelemetry SDK / Agent
   ↓
OpenTelemetry data
   ↓
Grafana Alloy
   ↓
Mimir / Loki / Tempo
```

---

# 18. Grafana Alloy

Grafana Alloy is a telemetry collector/distributor.

It can:

- Collect metrics
- Collect logs
- Receive traces
- Process telemetry
- Add metadata
- Forward telemetry

Concept:

```text id="obs41"
AKS
 |
 +--> Metrics
 +--> Logs
 +--> Traces
 |
 v
Alloy
 |
 +--> Mimir
 +--> Loki
 +--> Tempo
```

---

# 19. Kubernetes Metrics

Important Kubernetes metrics:

```text id="obs42"
Node CPU
Node memory
Pod CPU
Pod memory
Pod restarts
Container OOM kills
Deployment replicas
Pending pods
Node readiness
```

Useful commands:

```bash id="obs43"
kubectl top nodes
kubectl top pods -n nexcart
```

If metrics are unavailable, check whether the cluster has the required metrics collection component.

---

# 20. Kubernetes Events

Events are extremely useful during incidents.

```bash id="obs44"
kubectl get events \
  -n nexcart \
  --sort-by=.lastTimestamp
```

Look for:

```text id="obs45"
FailedScheduling
FailedMount
Failed
BackOff
Unhealthy
Pulling
Pulled
Killing
```

Events often explain why a pod is not starting.

---

# 21. Container Logs

Basic:

```bash id="obs46"
kubectl logs <pod> -n nexcart
```

Previous container:

```bash id="obs47"
kubectl logs <pod> \
  -n nexcart \
  --previous
```

Specific container:

```bash id="obs48"
kubectl logs <pod> \
  -c product-service \
  -n nexcart
```

Follow logs:

```bash id="obs49"
kubectl logs -f <pod> -n nexcart
```

---

# 22. Structured Logging

Prefer JSON structured logs.

Example:

```json id="obs50"
{
  "timestamp": "2026-10-10T10:20:00Z",
  "level": "ERROR",
  "service": "payment-service",
  "trace_id": "abc123",
  "request_id": "req-789",
  "message": "Payment provider timeout",
  "duration_ms": 3200
}
```

Advantages:

- Easy parsing
- Searchable fields
- Better correlation
- Easier alerting
- Better machine processing

---

# 23. Log Levels

Typical levels:

```text id="obs51"
DEBUG
INFO
WARN
ERROR
```

Production should avoid excessive DEBUG logging unless temporarily enabled for troubleshooting.

---

# 24. Logging Best Practices

Never log:

```text id="obs52"
Passwords
Access tokens
Client secrets
Database credentials
Payment information
Sensitive personal data
```

Instead:

```text id="obs53"
Authentication failed
Payment request failed
Database connection failed
```

with safe diagnostic metadata.

---

# 25. Correlation ID

A correlation ID connects logs belonging to the same request.

Example:

```text id="obs54"
Request
   |
request-id = 12345
   |
   +--> Order Service
   |
   +--> Payment Service
   |
   +--> Notification Service
```

Every service logs:

```text id="obs55"
request_id=12345
```

This makes troubleshooting much easier.

---

# 26. Distributed Tracing

Example NexCart request:

```text id="obs56"
GET /api/order/1001
        |
        v
Ingress
  20ms
        |
        v
Order Service
  80ms
        |
        v
Payment Service
  1.8s
        |
        v
External Payment API
  1.6s
```

The trace immediately shows:

```text id="obs57"
Payment dependency is causing latency.
```

Without tracing, engineers may spend time investigating the wrong service.

---

# 27. Trace Context

Important concepts:

```text id="obs58"
Trace ID
Span ID
Parent Span
Trace Context
```

Example:

```text id="obs59"
Trace: abc123

Ingress
 └── Span: 001
      └── Order Service
           └── Span: 002
                └── Payment Service
                     └── Span: 003
```

---

# 28. Service-Level Dashboard

Each NexCart service should have:

```text id="obs60"
Request Rate
Error Rate
p50 Latency
p95 Latency
p99 Latency
Pod Count
CPU
Memory
Restarts
Dependency Errors
```

Example:

```text id="obs61"
Product Service

Requests/sec       420
Error Rate         0.8%
p95 Latency        180ms
p99 Latency        420ms
Pods               3/3
CPU                 42%
Memory              58%
Restarts             0
```

---

# 29. AKS Dashboard

Cluster-level dashboard:

```text id="obs62"
AKS Cluster
 |
 +-- Nodes
 |    +-- CPU
 |    +-- Memory
 |
 +-- Pods
 |    +-- Restarts
 |    +-- OOMKilled
 |
 +-- Deployments
 |    +-- Desired
 |    +-- Available
 |
 +-- Networking
 |
 +-- Storage
```

---

# 30. Node Monitoring

Check:

```bash id="obs63"
kubectl get nodes
```

Detailed:

```bash id="obs64"
kubectl describe node <node>
```

Metrics:

```bash id="obs65"
kubectl top nodes
```

Look for:

```text id="obs66"
CPU pressure
Memory pressure
Disk pressure
PID pressure
NotReady state
```

---

# 31. Pod Monitoring

```bash id="obs67"
kubectl get pods -n nexcart -o wide
```

Check restart counts:

```text id="obs68"
NAME                    READY   STATUS    RESTARTS
product-service-xxx     1/1     Running   0
payment-service-xxx     1/1     Running   4
```

A high restart count requires investigation.

---

# 32. OOMKilled

Check:

```bash id="obs69"
kubectl describe pod <pod> -n nexcart
```

Look for:

```text id="obs70"
Reason: OOMKilled
```

Possible causes:

- Memory limit too low
- Memory leak
- Traffic increase
- Large requests
- Inefficient application behavior

Check:

```bash id="obs71"
kubectl top pod <pod> -n nexcart
```

Do not simply increase memory without understanding why usage increased.

---

# 33. HPA Monitoring

Check:

```bash id="obs72"
kubectl get hpa -n nexcart
```

Detailed:

```bash id="obs73"
kubectl describe hpa <hpa-name> -n nexcart
```

Monitor:

```text id="obs74"
Current replicas
Desired replicas
CPU utilization
Memory utilization
Scaling events
```

---

# 34. Alerts

Alerts should represent actionable conditions.

Bad alert:

```text id="obs75"
CPU > 70% for 1 minute
```

This can create noise.

Better:

```text id="obs76"
CPU > 85%
AND
sustained for 15 minutes
AND
application latency is increasing
```

Alert on symptoms and impact, not every metric fluctuation.

---

# 35. Alert Categories

Recommended:

```text id="obs77"
Availability
Latency
Errors
Saturation
Capacity
Security
Certificate expiry
Infrastructure health
Deployment failures
```

---

# 36. Example Production Alerts

```text id="obs78"
HTTP 5xx > 5%
p95 latency > 1 second
Pod CrashLoopBackOff
Pod restart rate increased
Node NotReady
Disk pressure
Memory pressure
HPA at maximum replicas
AKS deployment unavailable
Certificate nearing expiry
ACR image pull failures
Key Vault access failures
```

---

# 37. SLI

Service Level Indicator is the actual measured reliability signal.

Example:

```text id="obs79"
Successful requests / Total requests
```

If:

```text id="obs80"
99,900 successful
100,000 total
```

SLI:

```text id="obs81"
99.9%
```

---

# 38. SLO

Service Level Objective is the target.

Example:

```text id="obs82"
99.9% successful requests per month
```

SLO is a target, not necessarily a contractual commitment.

---

# 39. SLA

Service Level Agreement is the contractual commitment.

Hierarchy:

```text id="obs83"
SLI = What we measure

SLO = What we target

SLA = What we contractually promise
```

---

# 40. Error Budget

If SLO is:

```text id="obs84"
99.9%
```

Allowed failure:

```text id="obs85"
0.1%
```

That allowed failure is the error budget.

Concept:

```text id="obs86"
High reliability
     ↓
Large remaining error budget
     ↓
More freedom for changes

Low reliability
     ↓
Error budget exhausted
     ↓
Focus on reliability
```

---

# 41. Availability Calculation

Example:

```text id="obs87"
Availability =
Successful Requests / Total Requests × 100
```

If:

```text id="obs88"
990,000 successful
10,000 failed
```

Availability:

```text id="obs89"
990,000 / 1,000,000
= 99%
```

---

# 42. Incident Troubleshooting

When users report:

```text id="obs90"
Application is slow
```

Do not immediately restart pods.

Start with:

```text id="obs91"
1. Is the issue global or service-specific?
2. When did it start?
3. What changed?
4. Request rate?
5. Error rate?
6. Latency?
7. Pod health?
8. Node health?
9. Database?
10. External dependency?
```

---

# 43. Production Troubleshooting Flow

```text id="obs92"
User Impact
    ↓
Check Dashboard
    ↓
Latency / Errors / Traffic
    ↓
Identify Service
    ↓
Check Trace
    ↓
Check Logs
    ↓
Check Metrics
    ↓
Check Dependency
    ↓
Check Recent Deployment
    ↓
Mitigate
    ↓
Root Cause
    ↓
Prevent Recurrence
```

---

# 44. Scenario: API 500 Errors

### What will you check?

```bash id="obs93"
kubectl get pods -n nexcart
kubectl logs <pod> -n nexcart
kubectl get events -n nexcart --sort-by=.lastTimestamp
```

Then inspect:

```text id="obs94"
5xx dashboard
Application logs
Trace
Database metrics
Recent deployment
Dependency failures
```

### Root Cause Example

```text id="obs95"
Payment service cannot connect to external payment provider.
```

### Fix

Depending on impact:

```text id="obs96"
Restore dependency
Increase timeout only if justified
Use retry/circuit breaker
Rollback if caused by deployment
```

### Interview Answer

> I would correlate the increase in 5xx errors with deployment history, traces, service logs, and dependency metrics. I would identify whether the error originates in the application, infrastructure, database, or external dependency before taking remediation action.

---

# 45. Scenario: Latency Increased

Dashboard:

```text id="obs97"
p95 = 200ms → 1.8s
```

Check:

```text id="obs98"
Traffic increase?
CPU saturation?
Memory pressure?
Database latency?
External API?
Pod throttling?
Network?
Recent deployment?
```

Use tracing to identify the slow span.

---

# 46. Scenario: Pod Restarting

Check:

```bash id="obs99"
kubectl get pods -n nexcart
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
```

Look for:

```text id="obs100"
OOMKilled
CrashLoopBackOff
Liveness probe failure
Application startup failure
Configuration error
Secret access failure
```

---

# 47. Scenario: Node NotReady

Check:

```bash id="obs101"
kubectl get nodes
kubectl describe node <node>
kubectl get events --sort-by=.lastTimestamp
```

Check:

```text id="obs102"
CPU pressure
Memory pressure
Disk pressure
Network
Kubelet
Azure VM health
Node pool capacity
```

Do not immediately delete the node without understanding workload impact.

---

# 48. Scenario: Deployment Succeeded but Application Is Down

This is a common production issue.

Deployment status:

```text id="obs103"
SUCCESS
```

Application:

```text id="obs104"
DOWN
```

Possible reasons:

```text id="obs105"
Readiness probe incorrect
Service selector wrong
Ingress routing wrong
Container port mismatch
Environment variable missing
Database unavailable
NetworkPolicy
TLS issue
```

Check:

```bash id="obs106"
kubectl get deploy,svc,ingress -n nexcart
kubectl get endpoints -n nexcart
kubectl describe ingress -n nexcart
```

---

# 49. Observability During Deployment

Before deployment:

```text id="obs107"
Baseline
```

During deployment:

```text id="obs108"
Watch
 ↓
Error rate
Latency
Pod restarts
Availability
```

After deployment:

```text id="obs109"
Compare
 ↓
Before vs After
```

This is more reliable than simply checking:

```text id="obs110"
kubectl rollout status
```

---

# 50. Deployment Correlation

Every deployment should record:

```text id="obs111"
Version
Commit SHA
Image digest
Deployment time
Environment
Change owner
```

Example:

```text id="obs112"
service=payment-service
version=9f3a71c
environment=prod
```

Then dashboards can show:

```text id="obs113"
Error increase
       ↑
Deployment at 14:32
```

This dramatically reduces incident investigation time.

---

# 51. Observability and GitLab CI/CD

Pipeline can publish deployment metadata.

```text id="obs114"
GitLab
  ↓
Commit SHA
  ↓
Docker Image
  ↓
Helm Deployment
  ↓
AKS
  ↓
Telemetry
```

The telemetry should allow engineers to correlate:

```text id="obs115"
Commit
   ↕
Deployment
   ↕
Application Metrics
   ↕
Logs
   ↕
Traces
```

---

# 52. Observability and Helm

Helm can configure:

```text id="obs116"
Pod annotations
Environment metadata
Service labels
Monitoring configuration
ServiceAccount
Probes
Resources
```

Example:

```yaml id="obs117"
metadata:
  labels:
    app: product-service
    version: "{{ .Values.image.tag }}"
    environment: "{{ .Values.environment }}"
```

Use consistent labels to simplify filtering and dashboards.

---

# 53. Observability and Terraform

Terraform can provision:

```text id="obs118"
Log Analytics
Azure Monitor
Managed Grafana
Diagnostic settings
AKS monitoring
Storage
Alerting infrastructure
```

Concept:

```text id="obs119"
Terraform
   ↓
Observability Infrastructure

Helm
   ↓
Application Monitoring Configuration

OpenTelemetry
   ↓
Application Telemetry
```

---

# 54. Azure Monitor

Azure Monitor provides monitoring capabilities for Azure resources.

Useful areas:

```text id="obs120"
AKS
VMs
Networking
Storage
Azure SQL
Key Vault
Application services
```

Use Azure Monitor alongside application-level observability.

---

# 55. Log Analytics

Log Analytics is useful for querying Azure platform logs and telemetry.

Example conceptual query:

```kusto id="obs121"
AzureActivity
| where TimeGenerated > ago(1h)
| summarize count() by OperationNameValue
```

For AKS/Azure platform troubleshooting, KQL is an important operational skill.

---

# 56. KQL Basics

Filter:

```kusto id="obs122"
AzureActivity
| where TimeGenerated > ago(30m)
```

Select fields:

```kusto id="obs123"
AzureActivity
| project TimeGenerated, OperationNameValue, ActivityStatusValue
```

Count:

```kusto id="obs124"
AzureActivity
| summarize count()
```

Group:

```kusto id="obs125"
AzureActivity
| summarize count() by ActivityStatusValue
```

---

# 57. Application vs Infrastructure Monitoring

## Application

```text id="obs126"
Requests
Errors
Latency
Business transactions
Dependencies
```

## Infrastructure

```text id="obs127"
CPU
Memory
Disk
Network
Nodes
Pods
Storage
```

Both are required.

High CPU does not automatically mean application failure.

---

# 58. Business Metrics

Technical metrics alone are not enough.

NexCart can monitor:

```text id="obs128"
Orders created
Orders failed
Payments successful
Payments failed
Cart conversions
Product API errors
Notification failures
```

Example:

```text id="obs129"
Technical:
HTTP 200 = healthy

Business:
Payment success rate = 97%
```

The API may be technically healthy while the business flow is broken.

---

# 59. Business Transaction Monitoring

Example:

```text id="obs130"
Customer
  ↓
Create Order
  ↓
Payment
  ↓
Order Confirmation
  ↓
Notification
```

Track:

```text id="obs131"
Order success rate
Payment success rate
Notification success rate
End-to-end transaction latency
```

This gives a better view of actual customer experience.

---

# 60. Observability Anti-Patterns

Avoid:

```text id="obs132"
❌ Logging everything
❌ No correlation IDs
❌ High-cardinality metrics
❌ No retention strategy
❌ Alerts for every metric
❌ No ownership for alerts
❌ Monitoring only infrastructure
❌ No deployment correlation
❌ No dashboard for critical services
❌ No runbooks
❌ No test of alerting
```

---

# 61. Alert Fatigue

If engineers receive hundreds of alerts:

```text id="obs133"
100 alerts/day
      ↓
Ignored alerts
      ↓
Real incident missed
```

Better:

```text id="obs134"
Fewer
+
Actionable
+
Prioritized
+
Correlated alerts
```

Use severity:

```text id="obs135"
P1 → Critical customer impact
P2 → Major degradation
P3 → Limited impact
P4 → Informational
```

---

# 62. Runbooks

Every important alert should have a runbook.

Example:

```text id="obs136"
Alert:
Payment Service Error Rate > 5%

Runbook:
1. Check Grafana dashboard
2. Check recent deployment
3. Check payment-service logs
4. Check external payment API
5. Check database
6. Roll back if deployment-related
7. Escalate if dependency-related
```

An alert without an action path creates noise.

---

# 63. Retention Strategy

Telemetry has a cost.

Example:

```text id="obs137"
Logs
 ↓
High volume
 ↓
Storage cost
```

Define retention based on:

```text id="obs138"
Operational need
Compliance
Incident investigation
Cost
```

Possible strategy:

```text id="obs139"
Hot data
 ↓
Recent detailed telemetry

Cold/long-term
 ↓
Aggregated or archived telemetry
```

---

# 64. Observability Cost Optimization

Optimize:

```text id="obs140"
Log volume
Metric cardinality
Trace sampling
Retention
Duplicate telemetry
Unnecessary DEBUG logs
```

For high-volume systems, trace sampling can significantly reduce cost.

Do not sample away the telemetry needed to investigate critical workflows.

---

# 65. Production Observability Checklist

```text id="obs141"
Metrics
[ ] Request rate
[ ] Error rate
[ ] p95 latency
[ ] p99 latency
[ ] CPU
[ ] Memory
[ ] Disk
[ ] Network
[ ] Pod restarts

Logs
[ ] Centralized logs
[ ] Structured JSON
[ ] Correlation ID
[ ] No secrets
[ ] Appropriate retention

Traces
[ ] Distributed tracing
[ ] Trace ID
[ ] Service-to-service visibility
[ ] External dependency visibility

Alerts
[ ] Availability
[ ] Error rate
[ ] Latency
[ ] Saturation
[ ] Node health
[ ] Certificate expiry
[ ] Deployment failure

Operations
[ ] Dashboards
[ ] Runbooks
[ ] Incident process
[ ] Deployment correlation
[ ] SLOs
[ ] Error budgets
```

---

# 66. Senior Interview Questions

### Q1. What is observability?

> Observability is the ability to understand the internal state and behavior of a system from its external telemetry. I use metrics for trends and alerting, logs for detailed events, and traces for distributed request flow.

---

### Q2. What are the three pillars?

```text id="obs142"
Metrics
Logs
Traces
```

---

### Q3. What are the four golden signals?

```text id="obs143"
Latency
Traffic
Errors
Saturation
```

---

### Q4. What is the difference between RED and USE?

> RED is primarily application/service focused: Rate, Errors and Duration. USE is infrastructure focused: Utilization, Saturation and Errors.

---

### Q5. Why are p95 and p99 more useful than average latency?

> Average latency can hide slow requests. Percentiles show the experience of the slower portion of traffic. For production services, p95 and p99 help identify tail latency and customer-impacting performance problems.

---

### Q6. How would you troubleshoot a slow microservice application?

> I would first determine whether the issue is isolated or global. Then I would correlate traffic, error rate and latency with deployment history. I would use distributed tracing to identify the slow service or dependency, inspect application logs using the trace/request ID, and check infrastructure metrics such as CPU, memory, network and database latency.

---

### Q7. Why use distributed tracing in microservices?

> A single customer request can cross multiple services and external dependencies. Tracing provides the complete request path and timing of each span, allowing us to identify exactly where latency or failure originates.

---

### Q8. What is high cardinality?

> High cardinality means a metric label has a very large number of unique values. Using user IDs or request IDs as metric labels can create millions of time series and increase memory and storage costs. I keep metrics dimensions bounded and use logs or traces for high-cardinality identifiers.

---

### Q9. How do you design production alerts?

> I alert on customer impact and sustained abnormal behavior rather than every metric fluctuation. Alerts should be actionable, have clear severity, ownership and a runbook, and should avoid creating alert fatigue.

---

### Q10. How do you monitor a Kubernetes production environment?

> I monitor nodes, pods, deployments, resource utilization, restarts, OOM kills, scheduling failures, network errors, ingress availability and application-level RED metrics. I correlate Kubernetes events with application logs, metrics and traces during incidents.

---

### Q11. What is the difference between monitoring and observability?

> Monitoring tells me that a known condition is unhealthy. Observability gives me enough telemetry to investigate unknown failure modes and understand why the system is behaving unexpectedly.

---

### Q12. How would you investigate an HTTP 500 spike?

> I would identify when the spike started and correlate it with deployments or dependency changes. Then I would inspect service-level error metrics, logs and traces, determine whether the failure originates from application code, database, network or external dependency, mitigate the impact, and then perform root-cause analysis.

---

# 67. NexCart Production Incident Example

### Scenario

Users report:

```text id="obs144"
Checkout is slow.
```

### Step 1 — Dashboard

```text id="obs145"
Order Service
p95 latency = 2.1s
```

### Step 2 — Trace

```text id="obs146"
Order Service
    |
    +-- Database = 100ms
    |
    +-- Payment Service = 1.8s
```

### Step 3 — Payment Trace

```text id="obs147"
Payment Service
      |
      +-- External Payment API = 1.6s
```

### Step 4 — Logs

```text id="obs148"
Payment provider timeout
```

### Step 5 — Mitigation

Depending on architecture:

```text id="obs149"
Retry with bounded backoff
Circuit breaker
Graceful failure
Dependency escalation
Rollback if deployment-related
```

### Root Cause

```text id="obs150"
External payment provider latency increased.
```

### Interview Answer

> I would not immediately scale the Order Service because the trace shows that most latency is downstream in the Payment Service and external payment provider. I would verify dependency health, timeout behavior, retries and circuit-breaker behavior, then mitigate the customer impact while escalating the external dependency.

---

# 68. NexCart Observability Stack

```text id="obs151"
                 +----------------+
                 |   NexCart App  |
                 +----------------+
                         |
              +----------+----------+
              |          |          |
           Metrics      Logs      Traces
              |          |          |
              +----------+----------+
                         |
                  OpenTelemetry
                         |
                  Grafana Alloy
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
        Mimir           Loki          Tempo
       Metrics          Logs          Traces
          |              |              |
          +--------------+--------------+
                         |
                       Grafana
                         |
          +--------------+--------------+
          |              |              |
      Dashboards       Alerts       Investigation
```

---

# 69. Complete NexCart Production Flow

```text id="obs152"
Developer
   |
   v
GitLab
   |
   v
CI/CD
   |
   v
Docker
   |
   v
ACR
   |
   v
Helm
   |
   v
AKS
   |
   +-----------------------------+
   |                             |
   v                             v
NexCart Services             Azure Services
   |                             |
   +-- Product                    +-- Key Vault
   +-- Order                     +-- Database
   +-- Payment                   +-- Networking
   +-- Notification
   |
   +---- Metrics
   +---- Logs
   +---- Traces
            |
            v
       Grafana Alloy
            |
     +------+------+------+
     |      |             |
     v      v             v
    Mimir  Loki          Tempo
     |      |             |
     +------+------+------+
            |
            v
         Grafana
            |
            v
     Alert / Investigate
            |
            v
       Incident Response
```

## Core Senior DevOps Principle

> **Observability is not just installing Grafana. A production-grade observability system connects customer impact, metrics, logs, traces, deployments, infrastructure and dependencies so an engineer can move from “the application is slow” to the actual root cause as quickly as possible.**