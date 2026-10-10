---
title: "19-interview-questions"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 19
---

# 🎯 19. Senior DevOps Interview Questions – NexCart

## 1. Project & Architecture

### Q1. Explain the NexCart project architecture.

> NexCart is an e-commerce microservices application running as containerized workloads on Kubernetes/AKS. The confirmed application services are Product, Order, Payment and Notification. The source code is maintained in GitLab, CI/CD builds and scans container images, images are pushed to Azure Container Registry, and Helm is used to package and deploy Kubernetes workloads. Azure services provide the infrastructure, identity, secrets and supporting platform capabilities. Observability is handled through metrics, logs and traces.

```text
GitLab
   |
   v
GitLab CI/CD
   |
   +--> Test
   +--> Quality
   +--> Security
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
   +--> Product Service
   +--> Order Service
   +--> Payment Service
   +--> Notification Service
```

---

### Q2. Why did you choose microservices for NexCart?

> The main reason is independent service ownership and deployment. Product, Order, Payment and Notification have different responsibilities and can scale or release independently. The trade-off is additional operational complexity around networking, observability, deployments, security and distributed failure handling.

---

### Q3. What are the major challenges with microservices?

> The biggest challenges are distributed debugging, network failures, service discovery, data consistency, dependency management, observability, security and deployment coordination. I address these through Kubernetes Services, centralized observability, correlation IDs, health probes, timeouts, controlled retries, Helm and automated CI/CD.

---

### Q4. How do services communicate inside AKS?

```text
Order Service
     |
     v
Kubernetes Service
     |
     v
Product Service Pods
```

> I use Kubernetes Services and DNS rather than directly addressing Pod IPs. For example, an application can communicate with a service using its Kubernetes DNS name and service port.

Example:

```bash
curl http://product-service:8082/health
```

---

### Q5. Why shouldn't applications use Pod IPs directly?

> Pod IPs are ephemeral. Pods can be recreated or moved to another node, which changes their IP. Kubernetes Services provide a stable virtual endpoint and service discovery mechanism.

---

# 2. Git & GitLab

### Q6. Explain your Git branching strategy.

> I prefer a controlled branching model where developers work on feature branches, changes go through merge requests and validation pipelines, and protected branches are used for controlled releases. Production deployment permissions should be restricted.

Typical flow:

```text
feature/*
   |
Merge Request
   |
CI Validation
   |
main/release
   |
Production
```

---

### Q7. What controls would you configure in GitLab for production?

```text
Protected branches
Protected environments
Required approvals
CI/CD variables
Runner restrictions
Security scanning
Deployment permissions
Audit logs
```

---

### Q8. How do you handle a failed merge or conflict?

```bash
git status
git fetch origin
git pull --rebase origin main
```

Resolve conflicts:

```bash
git add .
git rebase --continue
```

Then validate:

```bash
git status
git log --oneline
```

> I don't blindly force-push shared branches.

---

### Q9. How do you prevent secrets from being committed?

> I use secret detection, protected CI/CD variables, Key Vault or another dedicated secret manager, and `.gitignore` where appropriate. Secrets should never be embedded in Dockerfiles, source code or Helm values committed to Git.

---

# 3. GitLab CI/CD

### Q10. Explain the NexCart CI/CD pipeline.

```text
Validate
   ↓
Test
   ↓
Quality
   ↓
Security
   ↓
Build
   ↓
Push to ACR
   ↓
Deploy
   ↓
Smoke Test
   ↓
Approval
   ↓
Production
```

> I separate build, security and deployment concerns so a vulnerable or untested artifact does not reach production.

---

### Q11. Why build once and deploy many?

> The artifact promoted to production should be the same artifact that passed validation in lower environments. Rebuilding during production deployment can introduce differences. Therefore I prefer immutable image tags or digests.

```text
Git Commit
   ↓
Docker Image
   ↓
ACR
   ↓
Dev
   ↓
Test
   ↓
Production
```

---

### Q12. Why avoid `latest`?

> `latest` is mutable and does not uniquely identify the artifact. It makes rollback and auditing difficult. I prefer Git SHA tags and, for stronger immutability, image digests.

Example:

```text
product-service:8f31a2c
```

---

### Q13. How do you secure GitLab-to-Azure authentication?

> I prefer OIDC/federated identity because it avoids storing long-lived Azure client secrets in GitLab. If a service principal secret is still used, it should be protected, rotated and treated as a temporary legacy pattern.

---

### Q14. What happens if the deployment stage succeeds but the application is broken?

> A Kubernetes deployment can succeed while the application is functionally broken. Therefore I use readiness checks, smoke tests, application metrics and business-level validation after deployment. Deployment success is not equivalent to application success.

---

# 4. Docker

### Q15. Explain your Docker strategy.

> Each service gets its own image. I prefer multi-stage builds where appropriate, small trusted base images, non-root execution, pinned dependencies and immutable image tags. Images are scanned before being pushed or promoted.

---

### Q16. How do you troubleshoot a container that exits immediately?

```bash
docker ps -a
docker logs <container>
docker inspect <container>
```

Check:

```text
Entrypoint
Command
Environment variables
Application startup
Port
Permissions
Dependencies
Exit code
```

---

### Q17. How do you reduce Docker image size?

```text
Multi-stage build
Minimal base image
Remove build tools from runtime
Use .dockerignore
Clean package caches
Avoid unnecessary files
```

---

### Q18. How do you secure a Docker image?

```text
Minimal trusted base image
Non-root user
Pinned dependencies
Trivy/image scanning
No secrets in image
Regular base-image updates
SBOM
```

---

# 5. Azure Container Registry

### Q19. How does AKS pull images from ACR?

```text
AKS
 |
Kubelet / managed identity
 |
AcrPull
 |
ACR
```

> I prefer identity-based access instead of storing static registry credentials.

---

### Q20. What is the difference between ACR authentication and Workload Identity?

> ACR image pulling is generally handled by the AKS/kubelet identity with appropriate ACR permissions. Workload Identity is primarily used when an application Pod needs to authenticate to Azure resources such as Key Vault, Storage or other Azure APIs.

---

### Q21. How do you troubleshoot `ImagePullBackOff`?

```bash
kubectl describe pod <pod> -n nexcart
```

Check:

```text
Image name
Image tag
ACR repository
ACR permissions
AKS identity
Network connectivity
Image existence
```

---

# 6. Kubernetes / AKS

### Q22. Explain the difference between Pod, Deployment and Service.

> A Pod runs containers. A Deployment manages the desired number and lifecycle of Pods. A Service provides a stable network endpoint for accessing those Pods.

```text
Deployment
    |
    v
Pods
    |
    v
Service
```

---

### Q23. What happens when a Pod crashes?

> Kubernetes detects that the actual state differs from the desired state and attempts to restore the desired state. Depending on the failure, the container may restart or a new Pod may be created.

---

### Q24. What is the difference between `Running` and `Ready`?

> `Running` generally means the Pod's containers have started. `Ready` indicates that the Pod is considered ready to receive traffic according to its readiness conditions. A Pod can be Running but not Ready.

---

### Q25. How do you troubleshoot `CrashLoopBackOff`?

```bash
kubectl get pods -n nexcart
kubectl describe pod <pod> -n nexcart
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
```

Check:

```text
Application exception
Environment variables
Secrets
ConfigMaps
Database
Startup command
Probe configuration
Memory/OOM
Permissions
```

---

### Q26. How do you troubleshoot a Pod stuck in Pending?

> I check events, resource requests, node capacity, taints/tolerations, affinity rules, topology constraints and PVC availability.

```bash
kubectl describe pod <pod> -n nexcart
kubectl get nodes
kubectl get events -n nexcart --sort-by=.lastTimestamp
```

---

### Q27. What causes `OOMKilled`?

> The container exceeded its memory limit or the node experienced memory pressure. I check historical memory usage, application behavior, limits and possible memory leaks before simply increasing the limit.

---

# 7. Kubernetes Networking

### Q28. Explain Kubernetes Service types.

| Type | Purpose |
|---|---|
| ClusterIP | Internal service |
| NodePort | Exposes service through node port |
| LoadBalancer | Cloud load balancer |
| ExternalName | DNS-based external mapping |

For internal NexCart service-to-service communication, ClusterIP is usually sufficient.

---

### Q29. How do you troubleshoot Service with no endpoints?

```bash
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get pods -n nexcart --show-labels
kubectl describe svc <service> -n nexcart
```

Most common issue:

```text
Service selector
        ≠
Pod labels
```

---

### Q30. How do you troubleshoot 502 from Ingress?

Check:

```text
Ingress
Service
Endpoints
Pod readiness
Service port
TargetPort
Application listener
NetworkPolicy
```

Commands:

```bash
kubectl get ingress -n nexcart
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get pods -n nexcart
```

---

### Q31. How do you troubleshoot service-to-service DNS?

```bash
kubectl exec -it <pod> -n nexcart -- nslookup product-service
```

Then test:

```bash
kubectl exec -it <pod> -n nexcart -- \
  curl -v http://product-service:8082/health
```

Check:

```text
DNS
Service
Endpoints
Port
NetworkPolicy
Application
```

---

# 8. Kubernetes ServiceAccount & Identity

### Q32. What is a Kubernetes ServiceAccount?

> A ServiceAccount provides an identity for workloads running inside Kubernetes. It is different from a human user account and can be associated with RBAC permissions and, in AKS, workload identity for Azure resource access.

---

### Q33. Does a Kubernetes ServiceAccount have a password?

> No. Modern Kubernetes uses short-lived projected service-account tokens where appropriate. For AKS Workload Identity, the ServiceAccount participates in federated authentication to Azure rather than using a permanent password.

---

### Q34. Explain AKS Workload Identity.

```text
Pod
 ↓
Kubernetes ServiceAccount
 ↓
Federated Identity
 ↓
Microsoft Entra / Azure Identity
 ↓
Azure RBAC
 ↓
Key Vault / Azure Resource
```

> This allows applications to access Azure resources without storing long-lived Azure credentials inside the Pod.

---

### Q35. How do you troubleshoot Key Vault 403?

Check:

```text
ServiceAccount
Workload Identity configuration
Federated credential
Azure identity
RBAC role
Key Vault
Network restrictions
Secret name
```

---

# 9. Helm

### Q36. Why use Helm?

> Helm packages Kubernetes manifests into reusable charts and allows environment-specific configuration through values. It also provides release history and rollback capabilities.

```text
Chart
 |
 +-- templates
 +-- values.yaml
 +-- values-prod.yaml
```

---

### Q37. What is the difference between `Chart.yaml` and `values.yaml`?

> `Chart.yaml` contains chart metadata such as chart name and version. `values.yaml` contains configurable application values consumed by templates.

---

### Q38. How do you validate Helm before deployment?

```bash
helm lint ./helm/nexcart

helm template nexcart ./helm/nexcart \
  -f values-prod.yaml

helm upgrade --install nexcart ./helm/nexcart \
  -n nexcart \
  --create-namespace \
  -f values-prod.yaml \
  --dry-run
```

---

### Q39. How do you rollback a Helm deployment?

```bash
helm history nexcart -n nexcart
helm rollback nexcart <revision> -n nexcart
```

Then:

```bash
kubectl rollout status deployment/<deployment> -n nexcart
```

---

# 10. Azure / AKS Security

### Q40. Service Principal vs Managed Identity?

> A service principal can authenticate applications to Azure, but long-lived client secrets create credential-management overhead. Managed identity removes the need to manage application credentials. For AKS workloads, Workload Identity is the preferred modern pattern when applications need Azure access.

---

### Q41. How do you implement least privilege?

```text
Developer
   ↓
Required access only

Application
   ↓
Specific Azure role

ServiceAccount
   ↓
Specific Kubernetes RBAC
```

Avoid broad permissions such as:

```text
Owner
Cluster-admin
Key Vault Administrator
```

unless genuinely required.

---

# 11. Terraform

### Q42. What does Terraform manage in NexCart?

> Terraform should primarily manage the Azure infrastructure layer: resource groups, networking, AKS, ACR, Key Vault, identities, RBAC, monitoring and other platform resources. Helm manages Kubernetes application packaging and deployment.

---

### Q43. Explain Terraform state.

> Terraform state maps configuration resources to real infrastructure and allows Terraform to determine what needs to change. For team environments I use remote state with appropriate locking and access control.

---

### Q44. Why use remote Terraform state?

```text
Centralized state
Team collaboration
Locking
Access control
Recovery
Auditability
```

Azure Blob Storage is a common backend for Azure environments.

---

### Q45. How do you troubleshoot Terraform drift?

```bash
terraform plan
```

Then compare:

```text
Terraform configuration
Terraform state
Actual Azure resource
```

Do not automatically overwrite manual production changes.

---

### Q46. What happens if Terraform wants to destroy a production resource unexpectedly?

> I stop the deployment and investigate the plan. I check configuration changes, provider behavior, state, imports and dependencies. I never blindly apply a destructive plan to production.

---

# 12. Observability

### Q47. Explain your observability architecture.

```text
Applications
    |
OpenTelemetry
    |
Grafana Alloy
    |
+---+---+---+
|   |   |
Mimir Loki Tempo
|   |   |
+---+---+---+
      |
    Grafana
```

> Metrics, logs and traces provide different perspectives of the same system and together reduce troubleshooting time.

---

### Q48. Why are distributed traces important?

> In a microservices architecture, one user request can cross multiple services. Traces show the complete request path and identify which service or dependency introduced latency or failure.

---

### Q49. How do you correlate logs across services?

> I propagate a correlation or request ID across service boundaries and include it in structured logs and traces.

```text
Frontend
  ↓ request-id=abc
Order
  ↓ request-id=abc
Payment
  ↓ request-id=abc
Database/API
```

---

# 13. Production Troubleshooting

### Q50. A Pod is healthy but users cannot access the application. What do you check?

```text
Ingress
 ↓
Service
 ↓
Endpoints
 ↓
Readiness
 ↓
Pod
 ↓
Application
 ↓
Dependency
```

Commands:

```bash
kubectl get ingress -n nexcart
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl get pods -n nexcart
```

> `Running` does not prove that the application is reachable or functionally healthy.

---

### Q51. Application latency suddenly increases. How do you troubleshoot?

```text
1. Check traffic
2. Check error rate
3. Check CPU/memory
4. Check traces
5. Check application logs
6. Check database latency
7. Check external dependencies
8. Check recent deployment
9. Check network
10. Compare with historical baseline
```

---

### Q52. How would you troubleshoot a 500 error?

> First identify whether the response originated from the application or an upstream component. Then inspect application logs and traces, check dependency failures, database connectivity, configuration, recent changes and error patterns.

---

### Q53. What if adding more Pods makes the problem worse?

> I check downstream capacity. For example, if the database has a limited connection capacity, increasing application replicas may increase database connections and make the outage worse. Scaling must consider the entire dependency chain.

---

# 14. Scaling & HA

### Q54. HPA vs Cluster Autoscaler?

> HPA scales Pods based on workload metrics. Cluster Autoscaler scales nodes when additional scheduling capacity is required.

```text
HPA
Pods ↑
   ↓
Node capacity insufficient
   ↓
Cluster Autoscaler
Nodes ↑
```

---

### Q55. How do you design NexCart for high availability?

```text
Multiple replicas
+
Multiple nodes
+
Multiple zones
+
Pod distribution
+
Readiness probes
+
PDB
+
Database HA
+
Redundant dependencies
```

> I also validate that replicas are actually distributed across failure domains.

---

### Q56. What is RTO and RPO?

> RTO is the target time to restore service after a disaster. RPO is the acceptable amount of data loss measured in time.

Example:

```text
RTO = 1 hour
RPO = 15 minutes
```

---

### Q57. How do you test DR?

```text
Backup
 ↓
Restore
 ↓
Infrastructure recovery
 ↓
Secrets
 ↓
Certificates
 ↓
Application deployment
 ↓
DNS
 ↓
Business validation
```

> I measure the actual recovery time and recovered data against the defined RTO and RPO.

---

# 15. Security & DevSecOps

### Q58. Where would you add security scanning?

```text
Source
 ↓
SAST
 ↓
Dependency Scan
 ↓
Docker Build
 ↓
Container Scan
 ↓
Infrastructure Scan
 ↓
Deploy
```

Possible tools:

```text
SAST
Dependency scanning
Trivy
Checkov
Secret detection
```

---

### Q59. What would you do if Trivy detects a critical vulnerability?

> First determine whether the vulnerable component is actually present in the runtime image and whether it is exploitable. Then identify the remediation version, update the dependency or base image, rebuild, rescan and promote the new immutable image. If exploitation risk is high, I would follow the organization's emergency patching process.

---

### Q60. What if a secret is accidentally committed to Git?

> I treat the credential as compromised immediately. I revoke or rotate it, identify where it was used, check audit logs, remove it from the source history where appropriate, move secret management to the approved secret store, and investigate the blast radius. Removing the secret from Git alone does not make the credential safe.

---

# 16. Certificate & Secret Rotation

### Q61. How do you manage TLS certificate renewal?

> I monitor expiry, automate renewal where possible, validate the renewed certificate at the actual serving endpoint and alert before expiration. I also verify the certificate chain and ensure the application or Ingress is using the renewed certificate.

---

### Q62. What happens if a secret is rotated but the application still uses the old value?

> It depends on how the secret is consumed. Environment variables normally require a Pod restart to load the new value, while mounted secret mechanisms can behave differently. I verify the application's secret reload mechanism and perform a controlled rollout if required.

---

### Q63. How do you rotate a database password without downtime?

> If the database supports it, I use a dual-credential or overlap strategy. I introduce the new credential, deploy consumers, validate connectivity, then revoke the old credential. The exact approach depends on the database and application connection behavior.

---

# 17. Day-2 Operations

### Q64. What does Day-2 Operations mean?

> Day-2 operations covers everything required after deployment: monitoring, incident response, scaling, upgrades, security, certificate and secret rotation, backups, DR, capacity planning, cost optimization and continuous improvement.

---

### Q65. What do you check regularly in production?

```text
Cluster health
Pod health
Application SLO
Error rate
Latency
Database
Backups
Certificates
Secrets
Security findings
Capacity
Cost
Recent changes
```

---

### Q66. How do you handle configuration drift?

> I identify the difference between Git, Terraform state and the actual environment. I determine whether the manual change was intentional. If it is required, I update the source of truth and redeploy through the standard process.

---

### Q67. How do you optimize cloud cost?

> I use utilization data rather than simply reducing resource sizes. I look for idle resources, over-provisioned Pods, unnecessary storage, excessive log retention, unused IPs, oversized nodes and inefficient monitoring. Availability and SLO requirements always remain the constraint.

---

# 18. Production Incident Management

### Q68. How do you handle a SEV-1 production incident?

> I first establish impact and assign incident ownership. I stop unrelated changes, check recent deployments, determine the blast radius and use metrics, logs, traces and events to identify the fastest safe mitigation. If a recent release is clearly responsible and rollback is safe, I roll back. Once service is restored, I validate the business transaction, communicate recovery and perform RCA separately.

---

### Q69. What is your first action during a production outage?

> Establish impact and stabilize the situation. I want to know what is broken, who is affected, when it started and whether the impact is increasing. Then I check recent changes and begin controlled mitigation.

---

### Q70. What should you avoid during a production incident?

```text
Blind restarts
Random configuration changes
Unreviewed production changes
Deleting evidence
Changing multiple variables simultaneously
Blaming individuals
Blindly scaling
Uncontrolled rollback
```

---

### Q71. How do you perform RCA?

```text
Timeline
 ↓
Change correlation
 ↓
Technical evidence
 ↓
Root cause
 ↓
Contributing factors
 ↓
Corrective action
 ↓
Preventive action
```

Use:

```text
5 Whys
Timeline analysis
Dependency analysis
Change correlation
```

---

# 19. Advanced Senior Scenarios

### Q72. Production deployment succeeded, but users receive 500 errors. What do you do?

```text
1. Confirm business impact
2. Check error rate
3. Check application logs
4. Check traces
5. Compare release versions
6. Check configuration
7. Check dependencies
8. Check database
9. Roll back if release is responsible
10. Validate business flow
```

Key principle:

> Deployment success is not application success.

---

### Q73. HPA is at maximum replicas but latency is still increasing. What do you check?

```text
Traffic
CPU
Memory
Node capacity
Database
External dependencies
Connection pool
Network
Application locks
HPA maxReplicas
Cluster Autoscaler
```

> I determine the bottleneck before increasing the maximum replica count.

---

### Q74. Pods are Pending even though nodes show low CPU. Why?

Possible causes:

```text
Memory shortage
Pod resource request
Taints
Tolerations
Affinity
Topology constraints
PVC
Node selectors
Pod limits
```

> Scheduler decisions are based on requested resources and scheduling constraints, not simply current CPU utilization.

---

### Q75. One availability zone fails. What should happen?

> If the architecture is designed correctly, replicas distributed across other zones continue serving traffic. Kubernetes should reschedule workloads where capacity exists. I would validate remaining capacity, Pod distribution, PDB behavior and application dependencies.

---

### Q76. Database is at 95% CPU and application latency is increasing. What do you do?

> I would not blindly scale application Pods. I first confirm whether database CPU is the bottleneck, inspect slow queries, connections, locks and traffic, and check whether caching or read replicas can reduce pressure. Application scaling can actually worsen database saturation.

---

### Q77. How would you recover NexCart after a complete Azure region failure?

```text
Confirm outage
 ↓
Declare DR
 ↓
Validate secondary region
 ↓
Recover database
 ↓
Provision infrastructure with Terraform
 ↓
Deploy application with Helm
 ↓
Restore identity/secrets
 ↓
Validate certificates
 ↓
Validate dependencies
 ↓
Run business smoke tests
 ↓
Switch traffic
 ↓
Monitor
```

---

### Q78. How would you design zero-downtime deployment?

```text
Multiple replicas
+
Readiness probes
+
Startup probes
+
Graceful shutdown
+
RollingUpdate
+
PDB
+
Progressive rollout
+
Smoke tests
```

For high-risk releases:

```text
Canary
or
Blue-Green
```

---

### Q79. What is the difference between HA and DR?

> HA protects against normal infrastructure failures such as Pod, node or zone failure within the operating environment. DR addresses major disasters such as regional failure or severe data/infrastructure loss and requires a recovery environment and data recovery strategy.

---

### Q80. What is the most important production principle you follow?

> **Never optimize one component in isolation.** I look at the complete dependency chain. Increasing application replicas, CPU or retries may improve one metric while damaging the database or another dependency. Production engineering is about maintaining the reliability of the complete system.

---

# 20. Rapid-Fire Senior Questions

### Q81. Why use readiness probes?

> To prevent traffic from reaching a Pod that is running but not ready to serve requests.

### Q82. Why use startup probes?

> To protect slow-starting applications from being incorrectly killed by liveness checks during initialization.

### Q83. Why use PDB?

> To maintain minimum application availability during voluntary disruptions.

### Q84. Why use topology spread?

> To distribute replicas across failure domains and reduce correlated failures.

### Q85. Why use immutable image tags?

> To make artifacts traceable and deployments reproducible.

### Q86. Why use image digests?

> A digest identifies the exact image content and cannot silently point to another image.

### Q87. Why use Key Vault?

> To centrally manage sensitive credentials, keys and certificates instead of embedding secrets in application configuration.

### Q88. Why use Workload Identity?

> To give workloads Azure access without storing long-lived Azure credentials in Pods.

### Q89. Why use Terraform?

> To manage Azure infrastructure declaratively and reproducibly.

### Q90. Why use Helm?

> To package and consistently deploy Kubernetes applications across environments.

### Q91. Why use OpenTelemetry?

> To standardize telemetry collection across applications and services.

### Q92. Why use correlation IDs?

> To follow one request across multiple microservices during troubleshooting.

### Q93. What is configuration drift?

> The actual environment differs from the intended configuration stored in the source of truth.

### Q94. What is MTTR?

> Mean Time To Restore/Resolve an incident.

### Q95. What is error budget?

> The amount of unreliability allowed while still meeting an SLO.

### Q96. Why is `kubectl get pods` not enough?

> It only provides a basic Pod view. Production troubleshooting also requires events, logs, endpoints, resource metrics, application telemetry and dependency health.

### Q97. Why can `Running` still mean unhealthy?

> The process can be running while the application is unable to serve traffic, connect to dependencies or process requests correctly.

### Q98. What is blast radius?

> The scope of systems, users or business functionality that can be affected by a failure or change.

### Q99. What is graceful degradation?

> Continuing critical business functionality when a non-critical dependency is unavailable.

### Q100. What makes someone senior in DevOps?

> Not the number of tools they know. A senior engineer understands trade-offs, failure modes, security, reliability, automation, cost, business impact and operational ownership. They can design the system, troubleshoot it under pressure and improve it after the incident.

---

# 21. Project Explanation – 2 Minute Interview Answer

> **NexCart is an e-commerce microservices application that I use to demonstrate an end-to-end DevOps implementation. The application consists of Product, Order, Payment and Notification services. The source code is maintained in GitLab.**
>
> **For CI/CD, I follow a pipeline that validates the code, runs tests and quality checks, performs security scanning, builds Docker images and pushes immutable images to Azure Container Registry. The application is deployed to AKS using Helm with environment-specific values.**
>
> **On the Kubernetes side, I use Deployments, Services, Ingress, ConfigMaps, Secrets or external secret integration, ServiceAccounts, resource requests and limits, readiness/liveness/startup probes and HPA. For Azure resource access, I prefer managed identity and Workload Identity rather than long-lived credentials.**
>
> **Infrastructure such as AKS, networking, ACR, Key Vault, identities and RBAC can be managed through Terraform.**
>
> **For observability, I use metrics, logs and traces with OpenTelemetry/Grafana-based tooling and correlate requests across services using request IDs.**
>
> **From a production perspective, I focus heavily on rollback, incident response, certificate and secret rotation, backup, DR, scaling, HA, security, cost optimization and Day-2 operations.**
>
> **The main principle is that deployment is only the beginning. A production DevOps solution must be observable, secure, scalable, recoverable and operationally maintainable.**

---

# 22. Interview Troubleshooting Framework

When an interviewer gives any production scenario, use this structure:

```text
Scenario
   ↓
Impact
   ↓
What changed?
   ↓
What will I check?
   ↓
Commands / Evidence
   ↓
Possible root causes
   ↓
Safe mitigation
   ↓
Validation
   ↓
Permanent fix
   ↓
Prevention
```

Example:

```text
Question:
Application is returning 502.

Answer:

1. Impact
2. Check recent changes
3. Check Ingress
4. Check Service
5. Check endpoints
6. Check Pod readiness
7. Check application logs
8. Check targetPort
9. Identify root cause
10. Mitigate
11. Validate
12. Add preventive monitoring
```

---

# 23. Senior-Level Command Sheet

```bash
# AKS
az aks get-credentials -g <rg> -n <aks>

# Cluster
kubectl get nodes
kubectl get pods -A
kubectl get events -A --sort-by=.lastTimestamp

# Application
kubectl get deploy,svc,ingress -n nexcart
kubectl get pods -n nexcart -o wide
kubectl logs <pod> -n nexcart
kubectl logs <pod> -n nexcart --previous
kubectl describe pod <pod> -n nexcart

# Resources
kubectl top nodes
kubectl top pods -n nexcart

# Networking
kubectl get svc -n nexcart
kubectl get endpoints -n nexcart
kubectl describe ingress <ingress> -n nexcart

# Deployment
kubectl rollout status deployment/<name> -n nexcart
kubectl rollout history deployment/<name> -n nexcart
kubectl rollout undo deployment/<name> -n nexcart

# Helm
helm list -n nexcart
helm status <release> -n nexcart
helm history <release> -n nexcart
helm rollback <release> <revision> -n nexcart

# ACR
az acr repository list --name <acr> -o table
az acr repository show-tags --name <acr> --repository <repo> -o table

# Terraform
terraform fmt
terraform validate
terraform plan
terraform state list
terraform state show <resource>
terraform apply
```

---

# 24. Final Interview Checklist

```text
Project
[ ] Explain architecture
[ ] Explain business flow
[ ] Explain microservices
[ ] Explain dependencies

GitLab
[ ] Branching
[ ] Merge Requests
[ ] CI/CD
[ ] Protected environments
[ ] OIDC

Docker
[ ] Dockerfile
[ ] Multi-stage builds
[ ] Security
[ ] Image tagging
[ ] Trivy

ACR
[ ] Push
[ ] Pull
[ ] AcrPull
[ ] Image digest
[ ] Retention

AKS
[ ] Pods
[ ] Deployments
[ ] Services
[ ] Ingress
[ ] ConfigMaps
[ ] Secrets
[ ] ServiceAccounts
[ ] Workload Identity
[ ] HPA
[ ] PDB
[ ] Node pools

Helm
[ ] Chart
[ ] Values
[ ] Templates
[ ] Release
[ ] Rollback
[ ] Environment configuration

Terraform
[ ] State
[ ] Backend
[ ] Modules
[ ] Drift
[ ] Plan
[ ] Apply

Security
[ ] RBAC
[ ] Key Vault
[ ] Workload Identity
[ ] SAST
[ ] Trivy
[ ] Secret rotation

Observability
[ ] Metrics
[ ] Logs
[ ] Traces
[ ] Correlation ID
[ ] SLO
[ ] Alerting

Production
[ ] Incident response
[ ] Rollback
[ ] Scaling
[ ] HA
[ ] DR
[ ] Backup
[ ] Certificates
[ ] Secrets

Day-2
[ ] Upgrades
[ ] Capacity
[ ] Cost
[ ] Configuration drift
[ ] Runbooks
[ ] Automation
```

# Core Senior Interview Principle

> **Do not answer senior DevOps interview questions as a list of commands. Explain the reasoning: impact → evidence → hypothesis → validation → mitigation → recovery → root cause → prevention. The interviewer should see that you can operate the system under real production pressure, not just deploy it.**

