---
title: "13-security"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 13
---

# 🔐 13. Security – DevSecOps, AKS, Azure & NexCart

## 1. Security Overview

Security should be implemented across the complete NexCart software supply chain:

```text
Developer
   ↓
GitLab
   ↓
Source Code
   ↓
CI/CD Security
   ↓
Docker Image
   ↓
ACR
   ↓
AKS
   ↓
Application
   ↓
Azure Services
   ↓
Monitoring / Incident Response
```

The main security principle is:

```text
Secure by Design
+
Least Privilege
+
Defense in Depth
+
Continuous Scanning
+
Identity-based Authentication
```

---

# 2. DevSecOps

DevSecOps integrates security into the development and delivery lifecycle rather than performing security only before production.

```text
Plan
 ↓
Code
 ↓
Build
 ↓
Test
 ↓
Scan
 ↓
Package
 ↓
Deploy
 ↓
Monitor
 ↓
Improve
```

Security should exist at every stage.

| Stage | Security |
|---|---|
| Code | Secure coding, secrets detection |
| GitLab | Branch protection, access control |
| Build | Dependency scanning, SAST |
| Docker | Image scanning, minimal images |
| ACR | Image security, RBAC |
| AKS | RBAC, NetworkPolicy, Pod Security |
| Azure | Managed Identity, Key Vault |
| Runtime | Monitoring, alerts, vulnerability management |

---

# 3. Security Layers

NexCart should use multiple security layers:

```text
Identity Security
       ↓
Source Code Security
       ↓
Dependency Security
       ↓
Container Security
       ↓
Kubernetes Security
       ↓
Network Security
       ↓
Cloud Security
       ↓
Runtime Security
       ↓
Monitoring & Incident Response
```

No single security control should be considered sufficient.

---

# 4. Identity and Access Management

Use Microsoft Entra ID for Azure identity management.

Important concepts:

```text
User
Service Principal
Managed Identity
Workload Identity
RBAC
Federated Identity
```

Prefer:

```text
Identity
   >
Long-lived Password / Secret
```

For Azure workloads, prefer managed or federated identity wherever supported.

---

# 5. Principle of Least Privilege

Every identity should have only the permissions it needs.

Bad:

```text
GitLab
  ↓
Owner role
```

Better:

```text
GitLab
  ↓
Specific deployment permissions
```

Example:

```text
AKS workload
  ↓
Key Vault Secrets User
```

instead of:

```text
AKS workload
  ↓
Subscription Owner
```

---

# 6. Azure RBAC

Azure RBAC controls access to Azure resources.

Example:

```bash
az role assignment list \
  --assignee <principal-id> \
  -o table
```

Typical roles:

```text
Reader
Contributor
AcrPull
Key Vault Secrets User
Network Contributor
```

Assign permissions at the smallest practical scope:

```text
Management Group
Subscription
Resource Group
Resource
```

Prefer resource-level or resource-group-level permissions where practical.

---

# 7. Service Principal vs Managed Identity

### Service Principal

Application identity created in Microsoft Entra ID.

Traditionally:

```text
Client ID
Client Secret
Tenant ID
```

Problem:

```text
Secret expiration
Secret rotation
Secret storage
Secret leakage risk
```

### Managed Identity

Azure manages the credentials.

Types:

```text
System-assigned
User-assigned
```

For Azure-hosted workloads, managed identity is generally preferred over storing service-principal secrets.

---

# 8. AKS Workload Identity

Modern AKS applications can use Microsoft Entra Workload ID.

Flow:

```text
Pod
 ↓
Kubernetes ServiceAccount
 ↓
OIDC Federation
 ↓
Microsoft Entra
 ↓
User-assigned Managed Identity
 ↓
Azure Resource
```

Example resources:

```text
Key Vault
Storage
Azure SQL
Service Bus
```

This avoids putting Azure client secrets inside Kubernetes.

---

# 9. Kubernetes ServiceAccount Security

A ServiceAccount identifies a workload inside Kubernetes.

Example:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: payment-service
  namespace: nexcart
```

Do not automatically grant broad permissions to every ServiceAccount.

Use:

```text
Dedicated ServiceAccount
+
Minimum RBAC
```

---

# 10. Kubernetes RBAC

Kubernetes RBAC controls:

```text
Who
can perform
what action
on which resource
```

Example:

```text
ServiceAccount
      ↓
Role
      ↓
RoleBinding
```

Prefer namespace-scoped `Role` over cluster-wide `ClusterRole` when possible.

---

# 11. Check Kubernetes Permissions

Useful command:

```bash
kubectl auth can-i \
  get secrets \
  -n nexcart \
  --as=system:serviceaccount:nexcart:payment-service
```

Check ServiceAccounts:

```bash
kubectl get sa -n nexcart
```

Review RBAC:

```bash
kubectl get role,rolebinding -n nexcart
kubectl get clusterrole,clusterrolebinding
```

---

# 12. Kubernetes Secrets

Kubernetes Secrets are intended for sensitive values, but they should not be treated as a complete secret-management solution.

Avoid:

```yaml
stringData:
  password: MyPassword123
```

inside Git repositories.

Prefer:

```text
Azure Key Vault
      ↓
Workload Identity
      ↓
Secrets Store CSI Driver
      ↓
Application
```

---

# 13. Azure Key Vault

Use Key Vault for:

```text
Database credentials
API keys
Certificates
Connection strings
External service secrets
Encryption keys
```

Benefits:

```text
Centralized secret management
Access control
Auditing
Rotation
Expiration
Azure integration
```

---

# 14. Key Vault Security

Prefer Azure RBAC authorization.

Example:

```bash
az role assignment create \
  --assignee <principal-id> \
  --role "Key Vault Secrets User" \
  --scope <key-vault-resource-id>
```

Do not grant:

```text
Owner
Contributor
```

when the workload only needs to read secrets.

---

# 15. Secret Rotation

Secrets should have a defined lifecycle:

```text
Create
 ↓
Store
 ↓
Use
 ↓
Rotate
 ↓
Validate
 ↓
Revoke old credential
```

Examples:

```text
Database password
API token
Certificate
Service principal secret
GitLab token
```

Prefer short-lived or federated credentials where possible.

---

# 16. Certificate Management

Certificates are operational security objects.

Monitor:

```text
Certificate expiry
Certificate chain
TLS configuration
Private key protection
Renewal status
```

Typical production flow:

```text
Certificate
    ↓
Managed Renewal
    ↓
Secret Store / Ingress
    ↓
Application
```

Do not wait for certificate expiration before discovering a renewal problem.

---

# 17. GitLab Security

Protect:

```text
main branch
production deployment
CI/CD variables
runners
deployment environments
container registry
```

Use:

```text
Protected branches
Protected tags
Merge requests
Required approvals
Protected environments
Role-based access
```

---

# 18. GitLab CI/CD Variables

Never hard-code secrets:

```yaml
variables:
  PASSWORD: "mypassword"
```

Avoid committing:

```text
AWS keys
Azure client secrets
Database passwords
API tokens
Private keys
```

Use:

```text
Protected variables
Masked variables
Environment-scoped variables
OIDC / federated authentication
Key Vault
```

---

# 19. GitLab OIDC

Prefer short-lived federated authentication over long-lived Azure client secrets where supported.

Concept:

```text
GitLab Job
    ↓
OIDC Token
    ↓
Microsoft Entra
    ↓
Federated Identity
    ↓
Azure Resource
```

Advantages:

```text
No permanent client secret
Reduced credential leakage risk
Short-lived authentication
Better auditability
```

---

# 20. Source Code Security

Security checks should include:

```text
SAST
Secret Detection
Dependency Scanning
License Scanning
IaC Scanning
Container Scanning
```

Typical pipeline:

```text
Code
 ↓
SAST
 ↓
Dependency Scan
 ↓
Secret Scan
 ↓
Build
 ↓
Container Scan
 ↓
Deploy
```

---

# 21. SAST

Static Application Security Testing analyzes source code without executing the application.

Examples of findings:

```text
SQL injection
Command injection
Insecure cryptography
Hard-coded credentials
Unsafe input handling
```

Goal:

```text
Find vulnerabilities before deployment.
```

---

# 22. Dependency Scanning

Applications depend on third-party libraries.

Examples:

```text
Node.js
npm packages
Python packages
Java Maven dependencies
```

Check for:

```text
Known CVEs
Outdated libraries
Transitive vulnerabilities
Unsupported versions
```

Example:

```text
Application
   ↓
express
   ↓
dependency A
   ↓
dependency B
```

A vulnerability may exist in a transitive dependency.

---

# 23. Secret Detection

Secret scanning searches source code and Git history for credentials.

Potential findings:

```text
AWS Access Key
Azure Secret
GitLab Token
API Key
Private Key
Database Password
```

If a secret is committed:

```text
Do not simply delete the line.
```

Assume it may already be compromised.

Recommended response:

```text
1. Revoke secret
2. Rotate credential
3. Remove from active configuration
4. Investigate usage
5. Clean repository history if required
6. Prevent future commits
```

---

# 24. Docker Security

Use minimal base images.

Prefer:

```dockerfile
FROM node:<version>-alpine
```

when compatible with the application.

Avoid unnecessary packages.

Use:

```text
Multi-stage builds
Minimal runtime image
Non-root user
Pinned dependencies
Regular rebuilds
Image scanning
```

---

# 25. Run Containers as Non-Root

Avoid:

```dockerfile
USER root
```

when unnecessary.

Example:

```dockerfile
RUN addgroup --system app \
    && adduser --system app

USER app
```

Benefits:

```text
Reduced container privilege
Reduced impact of container compromise
Better defense-in-depth
```

---

# 26. Dockerfile Security

Example secure principles:

```dockerfile
FROM node:<pinned-version>-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --omit=dev

COPY . .

USER app

EXPOSE 8082

CMD ["node", "server.js"]
```

Avoid putting:

```text
Passwords
API keys
Certificates
Tokens
```

inside the image.

---

# 27. Image Vulnerability Scanning

Use tools such as Trivy.

Example:

```bash
trivy image <image>
```

Example NexCart:

```bash
trivy image \
  <acr>.azurecr.io/product-service:<git-sha>
```

Pipeline concept:

```text
Docker Build
     ↓
Trivy Scan
     ↓
Pass
     ↓
Push to ACR
```

Critical vulnerabilities should normally block production depending on organizational policy and exploitability assessment.

---

# 28. ACR Security

Azure Container Registry should use:

```text
Azure RBAC
Managed Identity
Private networking where required
Image scanning
Retention policies
Repository permissions
```

For AKS image pulls, use appropriate identity permissions such as:

```text
AcrPull
```

Avoid sharing registry admin credentials unnecessarily.

---

# 29. Image Immutability

Avoid:

```text
latest
```

for production deployments.

Prefer:

```text
product-service:9f3a71c
```

or immutable image digest:

```text
product-service@sha256:<digest>
```

Benefits:

```text
Reproducibility
Rollback
Auditability
Supply-chain integrity
```

---

# 30. Software Supply Chain Security

The software supply chain includes:

```text
Source Code
 ↓
Dependencies
 ↓
Build Runner
 ↓
Docker Build
 ↓
Image
 ↓
Registry
 ↓
Deployment
```

Protect every stage.

Controls:

```text
Code review
SAST
Dependency scanning
SBOM
Image scanning
Signed artifacts
Protected registry
Least privilege
Immutable versions
```

---

# 31. SBOM

Software Bill of Materials lists the components inside an application or image.

Concept:

```text
NexCart Image
 |
 +-- Node.js
 +-- express
 +-- dependency A
 +-- dependency B
```

SBOM helps with:

```text
Vulnerability identification
License management
Supply-chain visibility
Incident response
```

---

# 32. Kubernetes Pod Security

Avoid unnecessary privileges.

Security settings can include:

```yaml
securityContext:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
```

Where supported and appropriate, also restrict:

```text
Linux capabilities
Host networking
Host PID
Host filesystem mounts
Privileged containers
```

---

# 33. Pod Security Context

Example:

```yaml
securityContext:
  runAsNonRoot: true
  seccompProfile:
    type: RuntimeDefault
```

Container-level restrictions can include:

```yaml
securityContext:
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: true
  capabilities:
    drop:
      - ALL
```

Validate application compatibility before enabling restrictive settings.

---

# 34. Network Security

NexCart should follow:

```text
Internet
   ↓
Ingress
   ↓
Application
   ↓
Internal Services
   ↓
Database
```

Do not expose internal services unnecessarily.

Example:

```text
Product Service → ClusterIP
Order Service   → ClusterIP
Payment Service → ClusterIP
```

Only externally required endpoints should be exposed.

---

# 35. Kubernetes NetworkPolicy

NetworkPolicy can restrict pod-to-pod traffic.

Example concept:

```text
Order Service
     ↓
Payment Service
```

but:

```text
Notification Service
      X
Payment database
```

Use network segmentation based on actual application dependencies.

---

# 36. Ingress Security

Ingress should provide:

```text
TLS
Host-based routing
Path-based routing
Security headers
Rate limiting where required
Authentication where required
```

Example:

```text
https://nexcart.example.com/api/orders
             ↓
          Ingress
             ↓
       order-service
```

---

# 37. TLS

Production traffic should use HTTPS.

Flow:

```text
Client
  ↓ HTTPS
Ingress
  ↓
Service
```

For internal traffic, determine whether encryption is required based on security/compliance requirements.

Important:

```text
TLS Certificate
Private Key
Certificate Chain
Expiration
Renewal
```

---

# 38. Azure Network Security

Important Azure controls:

```text
Virtual Network
Subnets
Network Security Groups
Private Endpoints
Azure Firewall
Route Tables
Private DNS
```

Prefer private connectivity for sensitive services where architecture and requirements justify it.

---

# 39. Public vs Private Resources

Prefer:

```text
Internet
   ↓
Public Ingress
   ↓
Private AKS workloads
   ↓
Private Azure services
```

Avoid unnecessary public exposure of:

```text
Database
Key Vault
Internal APIs
Management endpoints
```

---

# 40. Database Security

Database security should include:

```text
Encryption at rest
TLS in transit
Least-privilege users
Network restrictions
Credential rotation
Backups
Audit logging
```

Application should not connect using an administrative database account if a restricted application account is sufficient.

---

# 41. Database Credential Rotation

Recommended pattern:

```text
Key Vault
   ↓
Application Identity
   ↓
Retrieve Credential
   ↓
Database
```

Rotation should be designed so that changing the credential does not require rebuilding the Docker image.

---

# 42. API Security

NexCart APIs should consider:

```text
Authentication
Authorization
Input validation
Rate limiting
TLS
Request size limits
Timeouts
Security headers
Audit logging
```

Never trust client input.

---

# 43. Authentication vs Authorization

### Authentication

```text
Who are you?
```

### Authorization

```text
What are you allowed to do?
```

Example:

```text
User authenticated
       ↓
Is user allowed to access admin API?
       ↓
Authorization
```

Authentication alone is not sufficient.

---

# 44. API Rate Limiting

Protect APIs against:

```text
Abuse
Brute force
Traffic spikes
Denial-of-service patterns
Accidental request storms
```

Example:

```text
100 requests/minute/client
```

Actual limits should be based on business requirements and traffic patterns.

---

# 45. Timeouts and Retries

Security and reliability are connected.

Never configure unlimited retries.

Bad:

```text
Retry forever
```

Better:

```text
Timeout
 ↓
Bounded retries
 ↓
Exponential backoff
 ↓
Circuit breaker
```

Otherwise one failing dependency can create a cascading failure.

---

# 46. Kubernetes Security Checklist

```text
[ ] RBAC enabled
[ ] Least-privilege ServiceAccounts
[ ] Workload Identity
[ ] NetworkPolicies
[ ] Non-root containers
[ ] Restricted capabilities
[ ] No privileged containers
[ ] Resource limits
[ ] Readiness/liveness probes
[ ] Image scanning
[ ] Immutable image tags
[ ] Secrets outside Git
[ ] TLS
[ ] Audit logging
[ ] Regular upgrades
```

---

# 47. AKS Security

Important AKS security areas:

```text
Control plane
Node pools
RBAC
Workload Identity
Network security
Container security
Secrets
Image security
Node OS patching
Cluster upgrades
Monitoring
```

Always keep AKS and node images on supported versions.

---

# 48. Node Security

AKS nodes should be treated as critical infrastructure.

Monitor:

```text
OS patching
Node health
Disk usage
CPU
Memory
Security vulnerabilities
Suspicious processes
```

Do not run unnecessary workloads with host-level privileges.

---

# 49. CI/CD Runner Security

GitLab runners are part of the software supply chain.

Risks:

```text
Compromised runner
Credential theft
Malicious pipeline changes
Docker socket exposure
Artifact manipulation
```

Best practices:

```text
Use trusted runners
Isolate runners
Restrict privileged mode
Protect production runners
Use short-lived credentials
Do not expose unnecessary host access
```

---

# 50. Docker Socket Risk

Avoid unnecessary:

```text
/var/run/docker.sock
```

mounts.

Why?

```text
Container
   ↓
Docker socket
   ↓
Host Docker daemon
```

A compromised container may gain excessive control over the host.

Use safer build mechanisms where practical.

---

# 51. GitLab Environment Protection

Production should not be deployable by every developer.

Example:

```text
Developer
   ↓
Dev
```

Production:

```text
Merge
 ↓
Approval
 ↓
Protected Environment
 ↓
Production Deployment
```

Use environment protection and role-based access.

---

# 52. Branch Protection

Protect:

```text
main
release/*
production/*
```

Recommended:

```text
Merge Request required
Code review required
Pipeline must pass
Direct push disabled
```

This reduces accidental or malicious production changes.

---

# 53. Security Gates in CI/CD

Recommended pipeline:

```text
validate
   ↓
unit-test
   ↓
SAST
   ↓
dependency-scan
   ↓
secret-scan
   ↓
build
   ↓
Trivy image scan
   ↓
push to ACR
   ↓
Helm validation
   ↓
deploy
```

Production should only consume artifacts that passed required security gates.

---

# 54. Security Severity

A practical classification:

```text
Critical
High
Medium
Low
```

Do not automatically treat every vulnerability equally.

Consider:

```text
Severity
Exploitability
Exposure
Runtime usage
Business impact
Available mitigation
```

Example:

```text
Critical CVE
+
Internet-facing service
+
Exploit available
=
High-priority remediation
```

---

# 55. Vulnerability Management

Security is continuous.

Process:

```text
Discover
 ↓
Assess
 ↓
Prioritize
 ↓
Remediate
 ↓
Rescan
 ↓
Verify
```

Do not stop after the first successful scan.

---

# 56. Patch Management

Patch:

```text
Base images
OS packages
Node.js
Python
Java
npm dependencies
Maven dependencies
Kubernetes
AKS node images
```

Automate detection wherever possible.

---

# 57. Security Monitoring

Monitor for:

```text
Authentication failures
Authorization failures
Unexpected admin activity
Secret access anomalies
Container failures
Image changes
Network anomalies
Azure resource changes
```

Correlate security telemetry with:

```text
GitLab
Azure
AKS
Application
Identity
```

---

# 58. Incident Response

Security incident flow:

```text
Detect
 ↓
Validate
 ↓
Contain
 ↓
Investigate
 ↓
Eradicate
 ↓
Recover
 ↓
Lessons Learned
```

Example:

```text
Compromised API token
 ↓
Revoke token
 ↓
Rotate credential
 ↓
Check access logs
 ↓
Identify affected resources
 ↓
Patch vulnerability
 ↓
Restore service
 ↓
Document incident
```

---

# 59. Secret Leak Scenario

### Scenario

A developer accidentally commits an Azure secret.

### Immediate action

```text
1. Revoke secret
2. Rotate credential
3. Check access logs
4. Identify affected resources
5. Remove secret from configuration
6. Prevent future commits
```

Do not assume that deleting the file from the latest commit makes the secret safe.

Git history may still contain it.

---

# 60. Container Vulnerability Scenario

### Scenario

Trivy reports a critical vulnerability.

### What will you check?

```text
1. Which package is vulnerable?
2. Is it runtime-relevant?
3. Is a fixed version available?
4. Which base image contains it?
5. Is the image externally exposed?
6. Is there an active exploit?
```

### Fix

```text
Update dependency/base image
 ↓
Rebuild
 ↓
Rescan
 ↓
Test
 ↓
Push new immutable image
 ↓
Deploy
```

---

# 61. Kubernetes Privilege Escalation Scenario

### Scenario

A pod is running as root with privilege escalation enabled.

Check:

```bash
kubectl get pod <pod> -n nexcart -o yaml
```

Review:

```text
runAsUser
runAsNonRoot
privileged
allowPrivilegeEscalation
capabilities
hostNetwork
hostPID
```

Fix:

```yaml
securityContext:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
  seccompProfile:
    type: RuntimeDefault
```

Test application compatibility before rollout.

---

# 62. Key Vault 403 Scenario

### Scenario

Application receives:

```text
403 Forbidden
```

Check:

```text
1. Which identity is being used?
2. Is Workload Identity configured?
3. Is ServiceAccount correct?
4. Is federated identity configured?
5. Does identity have Key Vault permission?
6. Is the Key Vault network accessible?
7. Is the requested secret correct?
```

Useful commands:

```bash
kubectl get sa -n nexcart
kubectl describe pod <pod> -n nexcart

az role assignment list \
  --assignee <principal-id> \
  -o table
```

---

# 63. ACR Unauthorized Scenario

### Scenario

AKS reports:

```text
ImagePullBackOff
unauthorized
```

Check:

```text
1. Image name
2. Image tag/digest
3. ACR exists
4. AKS identity
5. AcrPull permission
6. Network connectivity
```

Commands:

```bash
az acr repository list \
  --name <acr> \
  -o table

az role assignment list \
  --assignee <principal-id> \
  -o table
```

---

# 64. Security vs Availability

Security controls can affect availability.

Example:

```text
Very aggressive NetworkPolicy
        ↓
Required traffic blocked
        ↓
Application outage
```

Therefore security changes should be:

```text
Designed
Tested
Reviewed
Monitored
Rolled out gradually
```

Security should reduce risk without introducing uncontrolled operational risk.

---

# 65. Production Security Review

Before production:

```text
Identity
[ ] Least privilege
[ ] No unnecessary secrets
[ ] Workload Identity
[ ] RBAC reviewed

Code
[ ] SAST
[ ] Dependency scan
[ ] Secret scan

Container
[ ] Minimal image
[ ] Non-root
[ ] Image scan
[ ] Immutable image

Kubernetes
[ ] RBAC
[ ] NetworkPolicy
[ ] SecurityContext
[ ] Resource limits

Azure
[ ] Key Vault
[ ] Private networking where required
[ ] RBAC
[ ] Monitoring

CI/CD
[ ] Protected branches
[ ] Protected environments
[ ] Security gates
[ ] Trusted runners
```

---

# 66. Security Architecture

```text
                         Internet
                            |
                            v
                    +---------------+
                    |    Ingress    |
                    |   TLS / WAF   |
                    +-------+-------+
                            |
                            v
                         AKS
              +-------------+-------------+
              |             |             |
              v             v             v
          Product         Order        Payment
           Service        Service       Service
              |             |             |
              +-------------+-------------+
                            |
                    Workload Identity
                            |
             +--------------+--------------+
             |                             |
             v                             v
        Azure Key Vault                  ACR
        Secrets / Certs              Container Images
             |
             v
       Azure Resources

GitLab
   |
   +-- SAST
   +-- Dependency Scan
   +-- Secret Detection
   +-- Trivy
   +-- OIDC Federation
   |
   v
 Azure / ACR / AKS
```

---

# 67. NexCart DevSecOps Flow

```text
Developer
   ↓
GitLab Merge Request
   ↓
Code Review
   ↓
SAST
   ↓
Secret Detection
   ↓
Dependency Scan
   ↓
Unit Tests
   ↓
Docker Build
   ↓
Trivy Image Scan
   ↓
SBOM
   ↓
ACR
   ↓
Helm Validation
   ↓
AKS
   ↓
Workload Identity
   ↓
Key Vault
   ↓
Runtime Monitoring
   ↓
Security Alerts
```

---

# 68. Senior Interview Questions

### Q1. How do you implement DevSecOps in a CI/CD pipeline?

> I integrate security controls throughout the pipeline rather than creating a single security stage at the end. I use SAST and secret detection during source validation, dependency scanning during build, container scanning before registry push, IaC scanning for infrastructure, and RBAC, identity and runtime controls during deployment.

---

### Q2. How do you manage secrets in AKS?

> I avoid storing long-lived secrets in Git or Docker images. For Azure resources I prefer Workload Identity with Key Vault, using a dedicated Kubernetes ServiceAccount and least-privilege Azure RBAC. The application retrieves secrets at runtime rather than embedding them into the image.

---

### Q3. How would you secure a Docker container?

> I use a minimal and trusted base image, pin versions where practical, use multi-stage builds, run as a non-root user, remove unnecessary packages, scan the image for vulnerabilities, avoid secrets in the image and deploy immutable image references.

---

### Q4. How do you secure AKS?

> I use Azure RBAC and Kubernetes RBAC, Workload Identity, least-privilege ServiceAccounts, NetworkPolicies, non-root containers, restrictive security contexts, image scanning, private connectivity where appropriate, TLS, monitoring and regular cluster and node upgrades.

---

### Q5. What would you do if a secret was committed to Git?

> I would treat it as compromised. First I would revoke or rotate the credential, then investigate its usage and access logs. After that I would remove it from the repository and prevent recurrence using secret scanning and protected CI/CD variables.

---

### Q6. Why is Workload Identity better than storing Azure service-principal secrets?

> Workload Identity provides federated, short-lived authentication without requiring a long-lived client secret inside the cluster. This reduces credential leakage and rotation problems and provides better identity-based access control.

---

### Q7. How do you secure GitLab production deployments?

> I protect production branches and environments, require successful pipelines and approvals, restrict who can deploy, use short-lived federated credentials where possible, protect CI/CD variables and runners, and make the deployment artifact immutable and traceable to a commit.

---

### Q8. How do you handle a critical CVE in production?

> I first assess whether the vulnerable component is actually reachable and exploitable in our environment. I prioritize based on severity, exposure and business impact, then upgrade the dependency or base image, rebuild and rescan the artifact, test it, deploy the fixed immutable image and verify that the vulnerability is resolved.

---

### Q9. What is defense in depth?

> Defense in depth means using multiple independent security controls so that failure of one control does not expose the entire system. For NexCart that includes identity, RBAC, network controls, container security, image scanning, Key Vault, TLS, monitoring and incident response.

---

### Q10. How do you prevent excessive Kubernetes permissions?

> I use dedicated ServiceAccounts, namespace-scoped Roles wherever possible, least-privilege RoleBindings, regular RBAC reviews and `kubectl auth can-i` to verify effective permissions.

---

### Q11. How do you secure the software supply chain?

> I secure the source repository, dependencies, CI/CD runners, build process, container registry and deployment. I use code review, SAST, dependency and secret scanning, SBOM generation, image scanning, immutable artifacts, protected environments and identity-based authentication.

---

### Q12. How would you explain security architecture in an interview?

> I would explain it as a layered model: GitLab protects source and delivery, CI/CD performs security validation, ACR stores trusted immutable images, AKS enforces workload and network security, Workload Identity removes long-lived Azure credentials, Key Vault manages secrets, and observability provides detection and incident response. The core principles are least privilege, defense in depth and continuous verification.

---

# 69. Security Command Bank

```bash
# Azure identity
az login
az account show

# Azure RBAC
az role assignment list -o table
az role assignment list --assignee <principal-id> -o table

# AKS identity
az aks show -g <resource-group> -n <aks> --query identity

# Kubernetes ServiceAccounts
kubectl get sa -n nexcart
kubectl describe sa <sa> -n nexcart

# Kubernetes RBAC
kubectl get role,rolebinding -n nexcart
kubectl get clusterrole,clusterrolebinding

# Permission testing
kubectl auth can-i get secrets \
  -n nexcart \
  --as=system:serviceaccount:nexcart:<sa>

# Pod security
kubectl get pod <pod> -n nexcart -o yaml
kubectl describe pod <pod> -n nexcart

# NetworkPolicy
kubectl get networkpolicy -n nexcart

# Secrets
kubectl get secrets -n nexcart

# ACR
az acr repository list --name <acr> -o table
az acr repository show-tags \
  --name <acr> \
  --repository <repo> \
  -o table

# Key Vault
az keyvault secret list \
  --vault-name <key-vault>

# Trivy
trivy image <image>
trivy fs .
trivy config .

# Docker
docker history <image>
docker inspect <container>
```

---

# 70. Security Ownership Model

Keep ownership clear:

| Area | Primary Responsibility |
|---|---|
| Source Code | Development + Security |
| GitLab Access | Platform/DevOps |
| CI/CD Security | DevOps + Security |
| Docker Images | DevOps + Development |
| ACR | Platform/DevOps |
| AKS | Platform/DevOps |
| Application Security | Development + Security |
| Key Vault | Platform/Security |
| Azure RBAC | Cloud/Platform |
| Network Security | Cloud/Network |
| Vulnerability Management | Security + Engineering |
| Runtime Monitoring | DevOps/SRE |
| Incident Response | Security + Operations |

---

# 71. Final NexCart Security Principles

```text
1. Never hard-code secrets.
2. Prefer identity over passwords.
3. Prefer Workload Identity over long-lived Azure credentials.
4. Use Key Vault for sensitive Azure application secrets.
5. Apply least-privilege RBAC.
6. Run containers as non-root where possible.
7. Scan dependencies and container images.
8. Use immutable image tags/digests.
9. Protect GitLab branches and production environments.
10. Secure CI/CD runners.
11. Restrict Kubernetes network communication.
12. Use TLS for sensitive traffic.
13. Monitor certificates and credentials before expiry.
14. Generate and track SBOMs where required.
15. Continuously patch dependencies and base images.
16. Treat leaked credentials as compromised.
17. Correlate security events with logs, metrics and traces.
18. Test security controls instead of assuming they work.
19. Automate security gates where practical.
20. Design security into the platform instead of adding it after deployment.
```

## Core Senior DevOps Principle

> **Security is not a separate step after deployment. In a production-grade NexCart platform, identity, least privilege, secret management, secure software supply chain, container security, Kubernetes security, network controls and continuous monitoring must work together across the entire application lifecycle.**