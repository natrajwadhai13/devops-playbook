---
title: "15-certificate-secret-rotation"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 15
---
# 🔐 15. Certificate & Secret Rotation – NexCart

## 1. Why Rotation Matters

Certificates, passwords, API keys and tokens have a limited lifecycle.

Production systems should not depend on credentials that remain valid indefinitely.

```text
Credential Created
       ↓
Stored Securely
       ↓
Used by Application
       ↓
Monitored
       ↓
Rotated
       ↓
Validated
       ↓
Old Credential Revoked
```

Main objectives:

```text
Security
+
Availability
+
Automation
+
Auditability
```

A rotation process is successful only when the application continues working without an outage.

---

# 2. What Needs Rotation?

Typical NexCart credentials include:

| Credential | Example | Rotation Concern |
|---|---|---|
| TLS Certificate | HTTPS certificate | Expiry |
| Database Password | MongoDB/DB credential | Password expiration |
| API Key | Payment provider | Provider-specific |
| Azure Credential | Service Principal secret | Expiration |
| GitLab Token | CI/CD access | Expiration/revocation |
| Registry Credential | ACR credential | Expiration/security |
| Application Secret | JWT/signing secret | Controlled rotation |
| Private Key | TLS/signing key | Certificate lifecycle |

Prefer short-lived or identity-based authentication whenever possible.

---

# 3. Certificate vs Secret

These are related but different.

### Certificate

Used primarily for identity and encryption.

```text
Certificate
+
Private Key
+
Certificate Chain
```

Example:

```text
https://nexcart.example.com
```

### Secret

Sensitive value used for authentication or configuration.

Examples:

```text
Database Password
API Token
Encryption Key
Client Secret
```

A certificate can contain a public key, while the associated private key must remain protected.

---

# 4. Certificate Lifecycle

```text
Generate
   ↓
Issue
   ↓
Deploy
   ↓
Monitor
   ↓
Renew
   ↓
Deploy New Certificate
   ↓
Validate
   ↓
Revoke Old Certificate
```

Do not wait until the final day before expiry.

---

# 5. Certificate Expiry Risk

Example:

```text
Certificate expires:
10 October

Monitoring detects:
01 October

Renewal:
02 October

Deployment:
03 October

Validation:
03 October
```

This provides recovery time if renewal fails.

---

# 6. Certificate Monitoring

Monitor:

```text
Expiry date
Days remaining
Issuer
Subject
SANs
Certificate chain
TLS handshake
Renewal status
```

A useful alert policy could be:

```text
30 days → Warning
14 days → High priority
7 days  → Critical
```

Actual thresholds should match organizational risk and renewal duration.

---

# 7. TLS Certificate Architecture

Typical NexCart flow:

```text
Client
   |
 HTTPS/TLS
   |
   v
Ingress / Load Balancer
   |
   v
AKS Services
   |
   v
Internal Services
```

The TLS certificate may be managed by:

```text
cert-manager
Azure Application Gateway
Azure Key Vault
External certificate provider
```

The exact implementation depends on the platform architecture.

---

# 8. Key Vault as Certificate Store

Azure Key Vault can centrally manage:

```text
Certificates
Secrets
Keys
```

Benefits:

```text
Centralized management
Access control
Expiration tracking
Auditing
Integration with Azure
```

Applications should not store certificate private keys inside Docker images or Git repositories.

---

# 9. Certificate Renewal Models

## Model 1 — Manual

```text
Certificate expires
      ↓
Engineer downloads certificate
      ↓
Updates secret
      ↓
Deploys
```

Problem:

```text
Human dependency
Higher outage risk
Difficult to scale
```

---

## Model 2 — Automated

```text
Certificate Authority
       ↓
Automatic Renewal
       ↓
Secret Store
       ↓
Ingress / Application
       ↓
Validation
```

This is preferred for production wherever supported.

---

# 10. Let's Encrypt / cert-manager Pattern

A common Kubernetes pattern:

```text
cert-manager
      ↓
Let's Encrypt
      ↓
Certificate
      ↓
Kubernetes Secret
      ↓
Ingress
```

Example concept:

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: nexcart-tls
  namespace: nexcart
spec:
  secretName: nexcart-tls
  dnsNames:
    - nexcart.example.com
```

The actual issuer and DNS/HTTP challenge configuration depends on the environment.

---

# 11. Certificate Renewal Validation

After renewal verify:

```text
Certificate exists
Certificate is not expired
Correct hostname
Correct issuer
Correct certificate chain
Ingress is using new certificate
TLS handshake succeeds
Application is reachable
```

Do not consider renewal successful merely because the certificate was generated.

---

# 12. Check Certificate from Linux

Example:

```bash
openssl s_client \
  -connect nexcart.example.com:443 \
  -servername nexcart.example.com
```

Extract certificate dates:

```bash
openssl s_client \
  -connect nexcart.example.com:443 \
  -servername nexcart.example.com \
  </dev/null 2>/dev/null \
  | openssl x509 -noout -dates
```

Check subject:

```bash
openssl s_client \
  -connect nexcart.example.com:443 \
  -servername nexcart.example.com \
  </dev/null 2>/dev/null \
  | openssl x509 -noout -subject -issuer
```

---

# 13. Kubernetes TLS Secret

Check secret metadata:

```bash
kubectl get secret nexcart-tls -n nexcart
```

Check type:

```bash
kubectl get secret nexcart-tls \
  -n nexcart \
  -o jsonpath='{.type}'
```

Expected:

```text
kubernetes.io/tls
```

Do not print private-key contents into terminals, tickets or chat channels unnecessarily.

---

# 14. Certificate Secret Rotation

Typical flow:

```text
New Certificate
      ↓
Secret Updated
      ↓
Ingress/Application Detects Change
      ↓
New TLS Certificate Served
      ↓
Validation
      ↓
Old Certificate Retired
```

The key requirement is avoiding a gap where no valid certificate exists.

---

# 15. Secret Rotation Strategy

Use:

```text
Create New
    ↓
Deploy/Make Available
    ↓
Switch Consumer
    ↓
Validate
    ↓
Revoke Old
```

Avoid:

```text
Delete Old
    ↓
Create New
```

because this can create downtime.

---

# 16. Database Password Rotation

Example:

```text
Old DB Password
      ↓
Create New DB Password
      ↓
Update Key Vault
      ↓
Application picks up New Credential
      ↓
Test DB Connection
      ↓
Revoke Old Password
```

The exact mechanism depends on whether the application can reload credentials without restart.

---

# 17. Zero-Downtime Database Rotation

A safer strategy is dual credential support where the database/application architecture permits it:

```text
Old Credential + New Credential
            ↓
Application supports both
            ↓
Switch traffic/configuration
            ↓
Verify
            ↓
Remove Old Credential
```

This avoids an authentication gap.

---

# 18. API Key Rotation

Example payment provider:

```text
Old API Key
      +
New API Key
      ↓
Store New Key
      ↓
Update Application
      ↓
Test Transactions
      ↓
Monitor
      ↓
Revoke Old Key
```

Never revoke the old key before confirming the application has successfully switched.

---

# 19. Azure Service Principal Secret Rotation

Legacy pattern:

```text
Client ID
Client Secret
Tenant ID
```

Problem:

```text
Secret expires
       ↓
Pipeline fails
```

Preferred direction:

```text
GitLab
  ↓
OIDC
  ↓
Federated Identity
  ↓
Azure
```

This removes the need for a long-lived Azure client secret.

---

# 20. GitLab Token Rotation

If a token is required:

```text
Create new token
      ↓
Update protected variable
      ↓
Run pipeline
      ↓
Validate
      ↓
Revoke old token
```

Check:

```text
Scope
Expiration
Owner
Environment
Usage
```

Avoid giving a token broader permissions than required.

---

# 21. ACR Authentication Rotation

Prefer:

```text
AKS Managed Identity
       ↓
AcrPull
       ↓
ACR
```

instead of:

```text
AKS
 ↓
Long-lived Registry Password
 ↓
ACR
```

For CI/CD, use identity-based authentication such as federated identity where supported.

---

# 22. JWT Signing Secret Rotation

JWT signing keys require special care.

Naive approach:

```text
Replace signing key
      ↓
All existing tokens invalid
```

This may log out users or break service-to-service communication.

Safer pattern:

```text
New Signing Key
      ↓
Start signing new tokens
      ↓
Continue validating old key temporarily
      ↓
Token lifetime passes
      ↓
Remove old key
```

Use key IDs (`kid`) when implementing multiple signing keys.

---

# 23. Kubernetes Secret Rotation Problem

Important question:

> If a Kubernetes Secret changes, does my application automatically use the new value?

The answer depends on how the secret is consumed.

### Environment Variable

```text
Secret
 ↓
Environment variable
 ↓
Container
```

Existing processes normally continue using the old environment value.

A pod restart is typically required.

### Mounted Secret Volume

Kubernetes can update the mounted files, subject to the application consuming/reloading them.

The application must support dynamic reload if zero-downtime rotation is required.

---

# 24. Secret Rotation with Helm

Helm should reference secret configuration, but sensitive secret values should not be committed into Git.

Example:

```yaml
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: database-secret
        key: password
```

Better production architecture:

```text
Azure Key Vault
      ↓
CSI / Secret Integration
      ↓
Kubernetes Workload
```

---

# 25. Helm Secret Anti-Pattern

Avoid:

```yaml
apiVersion: v1
kind: Secret
data:
  password: <base64-password>
```

inside a public or broadly accessible Git repository.

Important:

```text
Base64 ≠ Encryption
```

Base64 only encodes data.

---

# 26. Key Vault + Workload Identity

Recommended NexCart pattern:

```text
                    AKS
                     |
             Kubernetes SA
                     |
             Workload Identity
                     |
             Microsoft Entra ID
                     |
                     v
                Key Vault
               /    |    \
              /     |     \
             v      v      v
           DB     API Key  TLS
         Secret    Secret Certificate
```

Benefits:

```text
No Azure password in Pod
No client secret in Git
Centralized secret lifecycle
Azure RBAC
Auditing
```

---

# 27. Secret Rotation Automation

A production-grade design should automate:

```text
Expiry Detection
      ↓
Rotation
      ↓
Application Update
      ↓
Health Check
      ↓
Validation
      ↓
Old Credential Revocation
```

Automation should also have:

```text
Failure detection
Rollback strategy
Alerting
Audit logs
```

---

# 28. Secret Expiry Monitoring

Monitor:

```text
Secret expiration
Certificate expiration
Token expiration
Service principal credential expiration
API credential expiration
```

Alert before expiration.

Example:

```text
30 days → Warning
14 days → Action required
7 days  → Critical
```

---

# 29. What If a Secret Expires in Production?

Example:

```text
Payment API
     ↓
Authentication failures
     ↓
HTTP 401
```

### Immediate response

```text
1. Confirm credential expiration
2. Generate/obtain replacement
3. Store securely
4. Update application
5. Validate connectivity
6. Monitor transactions
7. Revoke old credential if necessary
8. Document incident
```

Do not expose the replacement credential during troubleshooting.

---

# 30. Certificate Expired in Production

### Scenario

Users receive TLS certificate errors.

### Check

```text
DNS
 ↓
Ingress / Load Balancer
 ↓
Certificate
 ↓
Certificate expiry
 ↓
Renewal mechanism
```

Commands:

```bash
openssl s_client \
  -connect nexcart.example.com:443 \
  -servername nexcart.example.com \
  </dev/null 2>/dev/null \
  | openssl x509 -noout -dates
```

Then verify the actual certificate presented by the production endpoint.

---

# 31. Certificate Renewal Failed

### What will you check?

```text
1. Certificate status
2. Issuer status
3. DNS
4. HTTP/DNS challenge
5. Ingress
6. Key Vault/secret store
7. Permissions
8. Renewal controller logs
9. Certificate chain
10. Expiry date
```

For cert-manager:

```bash
kubectl get certificate -n nexcart
kubectl describe certificate <certificate> -n nexcart

kubectl get certificaterequest -n nexcart
kubectl get order -n nexcart
kubectl get challenge -n nexcart
```

---

# 32. cert-manager Troubleshooting

Check:

```bash
kubectl get pods \
  -n cert-manager
```

Logs:

```bash
kubectl logs \
  -n cert-manager \
  deploy/cert-manager
```

Check certificate:

```bash
kubectl describe certificate \
  <certificate> \
  -n nexcart
```

Look for:

```text
DNS challenge failure
HTTP challenge failure
Issuer problem
ACME rate limit
DNS propagation
Secret permissions
Ingress configuration
```

---

# 33. Certificate Chain Problem

Symptoms:

```text
Works in one browser
Fails in another client
TLS handshake errors
```

Possible cause:

```text
Incomplete certificate chain
```

Check:

```bash
openssl s_client \
  -connect nexcart.example.com:443 \
  -servername nexcart.example.com \
  -showcerts
```

Review:

```text
Server certificate
Intermediate certificate
Root trust
```

---

# 34. Private Key Protection

Private keys must be treated as highly sensitive.

Avoid:

```text
Git
Docker image
Application source code
Public CI logs
Plain-text chat
```

Prefer:

```text
Azure Key Vault
Managed certificate service
Secure Kubernetes integration
Restricted filesystem permissions
```

---

# 35. Secret Exposure in Logs

Bad:

```text
DB_PASSWORD=SuperSecret123
```

in application logs.

Also avoid logging:

```text
Authorization headers
JWT tokens
API keys
Connection strings
Private keys
```

Use structured logging with sensitive-field redaction.

---

# 36. Secret Exposure in CI/CD

Bad:

```bash
echo $AZURE_CLIENT_SECRET
```

Even masked variables can be exposed accidentally through unsafe commands or artifacts.

Avoid:

```text
Debug dumps
Environment dumps
Verbose credential commands
Uploading secret-containing files
```

Use the minimum required variables.

---

# 37. Rotation Failure Due to Caching

A common issue:

```text
Key Vault
   ↓
New Secret
   ↓
Application still uses old value
```

Possible reasons:

```text
Application cache
Environment variable
Secret volume update delay
Connection pool
Long-running process
External proxy/cache
```

Always verify what credential the running process is actually using without exposing the secret itself.

---

# 38. Secret Rotation and Pods

If the application consumes the secret through environment variables:

```text
Secret Updated
      ↓
Existing Pod
      ↓
Old Environment
```

A controlled restart may be required.

Safer approach:

```text
Update Secret
      ↓
Rolling Restart
      ↓
Readiness Validation
      ↓
Old Pods Terminated
```

Use:

```bash
kubectl rollout restart deployment/<deployment> -n nexcart
```

only after confirming that restart behavior is safe for the workload.

---

# 39. Rolling Rotation

Preferred production pattern:

```text
Old Pods
  ↓
New Credential Available
  ↓
New Pods Start
  ↓
Readiness Pass
  ↓
Traffic Moves
  ↓
Old Pods Terminate
```

This provides a controlled transition.

---

# 40. Blue-Green Rotation

For high-risk credential changes:

```text
Blue
Old Credential
    |
    | Production Traffic
    v

Green
New Credential
```

Test Green first.

Then:

```text
Traffic
   ↓
Green
```

After validation:

```text
Blue
 ↓
Retire
```

---

# 41. Canary Rotation

For large production systems:

```text
New Credential
     ↓
5% Pods
     ↓
Monitor
     ↓
25%
     ↓
50%
     ↓
100%
```

Monitor:

```text
Error rate
Latency
Authentication failures
Business transactions
```

If failures increase:

```text
Stop rollout
 ↓
Rollback
```

---

# 42. Rotation Runbook

```text
Rotation Item:
Credential Type:
Owner:
Environment:
Current Expiry:
New Expiry:

Current Consumer:
Rotation Method:

Pre-checks:
[ ] Backup/recovery available
[ ] New credential generated
[ ] Permissions validated
[ ] Application compatibility verified
[ ] Rollback plan ready

Rotation:
[ ] New credential stored
[ ] Application updated
[ ] Health checks passed
[ ] Business transaction tested

Post-check:
[ ] Monitoring normal
[ ] Old credential revoked
[ ] Audit record updated
[ ] Expiry monitoring confirmed
```

---

# 43. Pre-Rotation Checklist

Before rotation:

```text
[ ] Identify every consumer
[ ] Confirm current credential
[ ] Generate replacement
[ ] Verify permissions
[ ] Verify application compatibility
[ ] Check rollback process
[ ] Confirm monitoring
[ ] Schedule change if required
[ ] Notify stakeholders if production impact is possible
```

The most important question is:

> **Which systems consume this credential?**

---

# 44. Post-Rotation Checklist

After rotation:

```text
[ ] Application healthy
[ ] Pods Ready
[ ] No restart loops
[ ] No authentication errors
[ ] No TLS errors
[ ] Database connectivity healthy
[ ] API transactions successful
[ ] Monitoring healthy
[ ] Old credential revoked
[ ] Documentation updated
```

---

# 45. Rotation Failure Scenario

### Scenario

Database password was rotated, but the application started returning `401/500`.

### Troubleshooting

```text
Check application logs
        ↓
Check database authentication errors
        ↓
Check Key Vault secret version
        ↓
Check application configuration
        ↓
Check Pod restart/reload
        ↓
Test database connectivity
```

Possible root cause:

```text
New credential stored successfully
but existing pods still had the old environment variable.
```

Fix:

```text
Controlled rolling restart
 ↓
Readiness validation
 ↓
Database connectivity test
```

---

# 46. Rotation Caused Outage

### What should you do?

```text
1. Stop further rotation
2. Confirm scope of impact
3. Restore known-good credential if safe
4. Recover application
5. Validate all consumers
6. Identify why rotation failed
7. Correct automation/process
8. Retry with controlled rollout
```

Do not immediately rotate multiple related credentials at the same time.

---

# 47. Certificate + Secret Rotation Together

Sometimes certificates and credentials expire around the same period.

Avoid:

```text
Certificate rotation
+
Database password rotation
+
API key rotation
+
Deployment
```

all in one uncontrolled change.

Prefer:

```text
Change 1
 ↓
Validate

Change 2
 ↓
Validate

Change 3
 ↓
Validate
```

This makes failure isolation much easier.

---

# 48. Terraform and Secret Rotation

Terraform can manage infrastructure and references to secret resources, but avoid putting sensitive secret values unnecessarily into Terraform configuration/state.

Remember:

```text
Terraform State
      ≠
Secret Vault
```

Sensitive values can still appear in Terraform state depending on how resources are modeled.

Prefer:

```text
Terraform
   ↓
Create Azure infrastructure
   ↓
Create identity / access
   ↓
Key Vault
   ↓
Application retrieves secret
```

---

# 49. Helm and Secret Rotation

Helm should manage deployment configuration, not become a long-term plaintext secret store.

Preferred:

```text
Helm
 ↓
ServiceAccount
 ↓
Workload Identity
 ↓
Key Vault
 ↓
Application
```

rather than:

```text
Helm values.yaml
 ↓
Database password
```

---

# 50. GitLab CI/CD and Rotation

Pipeline should validate:

```text
Certificate status
Secret availability
Identity permissions
Deployment health
```

Example flow:

```text
Validate
 ↓
Deploy
 ↓
Smoke Test
 ↓
Authentication Test
 ↓
TLS Test
 ↓
Business Transaction
```

Do not mark rotation successful only because Kubernetes deployment succeeded.

---

# 51. Automated Certificate Monitoring

A production monitoring system should expose:

```text
certificate_expiry_days
certificate_renewal_status
tls_handshake_failures
```

Alert based on expiry windows.

Example:

```text
certificate < 30 days
        ↓
Warning

certificate < 7 days
        ↓
Critical
```

---

# 52. Automated Secret Monitoring

Monitor metadata such as:

```text
Expiration
Last rotation
Owner
Resource
Usage
```

Avoid monitoring by retrieving and exposing the actual secret value.

The monitoring system should know:

```text
"Credential expires in 14 days"
```

not:

```text
"Credential = abc123..."
```

---

# 53. Auditability

Every sensitive rotation should answer:

```text
Who rotated it?
What was rotated?
When?
Why?
Which application consumes it?
Was the new credential validated?
Was the old credential revoked?
```

Use:

```text
Azure Activity Logs
Key Vault logs
GitLab audit logs
Kubernetes audit logs
Change management records
```

---

# 54. Common Rotation Mistakes

```text
❌ Waiting until expiry day
❌ Hard-coding secrets
❌ Storing secrets in Git
❌ Putting secrets in Docker images
❌ Using long-lived credentials unnecessarily
❌ Rotating without identifying consumers
❌ Revoking old credential too early
❌ Updating secret but not application
❌ Not testing the new certificate
❌ Restarting all workloads blindly
❌ Rotating multiple credentials simultaneously
❌ Forgetting monitoring after rotation
```

---

# 55. Production Certificate/Secret Architecture

```text
                         GitLab
                           |
                     OIDC / Federation
                           |
                           v
                      Azure Identity
                           |
                           v
                     Azure Key Vault
                    /       |       \
                   /        |        \
                  v         v         v
             Database     API Key   Certificate
                  |         |         |
                  +---------+---------+
                            |
                            v
                           AKS
                            |
                    Workload Identity
                            |
                            v
                     NexCart Pods
                            |
                  +---------+---------+
                  |                   |
               Ingress            Services
                  |
               TLS Cert
                  |
               Internet
```

---

# 56. Senior Interview Scenario – Certificate Expiry

### Scenario

Production API certificate expires in 3 days.

### What will you check?

```text
1. Current certificate
2. Expiry date
3. Certificate issuer
4. Renewal mechanism
5. DNS
6. Ingress/load balancer
7. Key Vault/certificate store
8. Application dependency
9. Monitoring
```

### Commands

```bash
openssl s_client \
  -connect nexcart.example.com:443 \
  -servername nexcart.example.com \
  </dev/null 2>/dev/null \
  | openssl x509 -noout -dates

kubectl get ingress -n nexcart
kubectl get secret -n nexcart
```

### Root Cause Examples

```text
Renewal job failed
DNS challenge failed
Certificate controller unavailable
Permission issue
Certificate not attached to ingress
```

### Fix

```text
Correct renewal problem
 ↓
Issue new certificate
 ↓
Deploy/attach
 ↓
Validate TLS
 ↓
Monitor
```

### Interview Answer

> I would first verify the certificate actually being served by the production endpoint and determine whether the problem is issuance, renewal, storage or deployment. I would restore a valid certificate with the lowest-risk change, validate the TLS chain and hostname, and then fix the automation so the same expiry condition cannot recur.

---

# 57. Senior Interview Scenario – Secret Expiry

### Scenario

Payment transactions suddenly fail with authentication errors.

### What will you check?

```text
Application logs
 ↓
HTTP status
 ↓
Credential expiry
 ↓
Key Vault version
 ↓
Application configuration
 ↓
External provider
```

### Root Cause

```text
Payment API credential expired.
```

### Fix

```text
Generate new credential
 ↓
Store securely
 ↓
Update application
 ↓
Smoke test
 ↓
Business transaction
 ↓
Revoke old credential
```

### Interview Answer

> I would confirm whether the failures are authentication-related before rotating anything. Once the expired credential is confirmed, I would introduce the replacement credential, switch the workload safely, validate a real payment flow, monitor the error rate and only then revoke the old credential.

---

# 58. Senior Interview Scenario – Key Vault Secret Changed but Application Still Fails

### Scenario

The secret was successfully rotated in Key Vault, but the application still uses the old value.

### Investigation

```text
Key Vault
   ↓
Secret version updated?
   ↓
Pod configuration
   ↓
Environment variable vs mounted volume
   ↓
Application reload support
```

### Likely Root Cause

```text
Application consumed the secret as an environment variable,
so existing processes still had the old value.
```

### Fix

```text
Update secret
 ↓
Controlled rolling restart
 ↓
Readiness checks
 ↓
Validate application
```

### Prevention

```text
Document reload behavior
Automate rotation
Use appropriate secret delivery mechanism
Add post-rotation smoke tests
```

---

# 59. Senior Interview Scenario – Certificate Renewed but Users Still See Old Certificate

### Check

```text
Certificate authority
       ↓
New certificate exists?
       ↓
Key Vault / Kubernetes Secret
       ↓
Ingress
       ↓
Load Balancer
       ↓
DNS endpoint
```

Possible root causes:

```text
Old certificate still attached
Ingress not reloaded
Multiple ingress/load balancers
DNS pointing to another endpoint
CDN/proxy caching
```

Validate from the actual client-facing endpoint using:

```bash
openssl s_client \
  -connect nexcart.example.com:443 \
  -servername nexcart.example.com
```

Never assume that updating the certificate store means the client is receiving the new certificate.

---

# 60. Certificate and Secret Rotation Strategy

For NexCart, the preferred long-term model is:

```text
Identity-Based Authentication
          +
Centralized Secret Management
          +
Automated Certificate Renewal
          +
Short Credential Lifetime
          +
Expiry Monitoring
          +
Automated Validation
```

Architecture:

```text
GitLab
  ↓
OIDC
  ↓
Azure Identity
  ↓
Key Vault
  ↓
AKS Workload Identity
  ↓
NexCart
```

For TLS:

```text
Certificate Authority
       ↓
Automated Renewal
       ↓
Ingress / Key Vault
       ↓
TLS Validation
```

---

# 61. Final Rotation Checklist

```text
IDENTITY
[ ] Prefer Workload Identity
[ ] Avoid long-lived secrets
[ ] Least-privilege RBAC
[ ] Monitor credential expiry

SECRETS
[ ] Store in Key Vault
[ ] Never commit to Git
[ ] Never put in Docker images
[ ] Rotate before expiry
[ ] Validate consumers
[ ] Revoke old credentials

CERTIFICATES
[ ] Monitor expiry
[ ] Automate renewal
[ ] Validate hostname/SAN
[ ] Validate certificate chain
[ ] Validate actual production endpoint
[ ] Monitor renewal failures

KUBERNETES
[ ] Dedicated ServiceAccounts
[ ] Controlled secret delivery
[ ] Understand reload behavior
[ ] Rolling restart when required
[ ] Readiness checks

CI/CD
[ ] OIDC where possible
[ ] Protected variables
[ ] Protected environments
[ ] No credential logging
[ ] Post-rotation smoke tests

OPERATIONS
[ ] Rotation runbook
[ ] Rollback plan
[ ] Audit trail
[ ] Alerts
[ ] Owner assigned
```

---

# 62. Core Senior DevOps Principle

> **A secure credential that expires unexpectedly is still a production reliability problem. Certificate and secret rotation must therefore be designed as an automated lifecycle: detect expiry early, create the replacement, switch consumers safely, validate the new credential, revoke the old credential and continuously monitor the result.**
