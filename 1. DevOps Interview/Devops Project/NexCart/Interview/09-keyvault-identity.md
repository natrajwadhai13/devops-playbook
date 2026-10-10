---
title: "09-keyvault-identity"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 9
---
# 🔐 09. Azure Key Vault + Identity

## 1. Overview

Azure Key Vault is used to securely store and access sensitive information such as:

- Database passwords
- API keys
- TLS certificates
- Application secrets
- Connection strings
- Encryption keys

For NexCart, Kubernetes workloads should **not store long-lived credentials directly inside Git, Docker images, Helm values, or application configuration files**.

Recommended architecture:

```text
GitLab CI/CD
     |
     | Deploy
     v
   AKS
     |
     | Workload Identity
     v
Microsoft Entra ID
     |
     | Federated Identity
     v
Azure Key Vault
     |
     +---- DB credentials
     +---- API secrets
     +---- Certificates
     +---- Other application secrets
```

---

# 2. Identity Concepts

## Microsoft Entra ID

Microsoft Entra ID is Azure's identity and access-management service.

It provides:

- Authentication
- Authorization
- Managed identities
- Service principals
- Federated identity
- RBAC
- Application identities

---

## Managed Identity

Managed Identity allows Azure resources to authenticate to Azure services without storing passwords or client secrets.

Example:

```text
AKS workload
    |
    | Identity
    v
Microsoft Entra ID
    |
    v
Key Vault
```

Main advantage:

```text
No client secret
No password
No credential stored in Git
```

---

# 3. Types of Azure Managed Identity

## System-Assigned Managed Identity

Identity lifecycle is tied to the Azure resource.

```text
AKS created
   ↓
Identity created

AKS deleted
   ↓
Identity deleted
```

Good when the identity belongs exclusively to one resource.

---

## User-Assigned Managed Identity

Identity is created separately and can be reused.

```text
User Assigned Identity
        |
   +----+----+
   |         |
 AKS App   Another workload
```

For production AKS workloads, a **User-Assigned Managed Identity + Workload Identity** is commonly preferred because identity lifecycle is independent from the workload.

---

# 4. Workload Identity

Azure Workload Identity allows a Kubernetes workload to authenticate to Azure resources using a Kubernetes ServiceAccount and Microsoft Entra federated identity.

This avoids storing Azure client secrets inside Kubernetes.

```text
Pod
 |
 | Kubernetes ServiceAccount token
 v
Microsoft Entra ID
 |
 | Federated Identity Credential
 v
User-Assigned Managed Identity
 |
 v
Azure Key Vault
```

This is the preferred modern pattern for AKS workloads.

> Azure AD Pod Identity is a legacy/deprecated approach. For new implementations, use Microsoft Entra Workload ID.

---

# 5. Kubernetes ServiceAccount

A ServiceAccount provides an identity for a Kubernetes workload.

Example:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: product-service-sa
  namespace: nexcart
  annotations:
    azure.workload.identity/client-id: "<MANAGED-IDENTITY-CLIENT-ID>"
```

Deployment:

```yaml
spec:
  template:
    metadata:
      labels:
        azure.workload.identity/use: "true"
    spec:
      serviceAccountName: product-service-sa
      containers:
        - name: product-service
          image: <acr>.azurecr.io/product-service:<git-sha>
```

Important:

```text
ServiceAccount
      +
Workload Identity label
      +
Federated Identity Credential
      +
Azure Managed Identity
      =
Passwordless Azure authentication
```

---

# 6. Federated Identity Credential

The Federated Identity Credential establishes trust between:

```text
AKS ServiceAccount
        ↓
Microsoft Entra ID
        ↓
Managed Identity
```

It typically matches:

- Issuer
- Subject
- Audience

Example subject:

```text
system:serviceaccount:nexcart:product-service-sa
```

This prevents one Kubernetes workload from automatically impersonating another workload.

---

# 7. Key Vault Access Models

Azure Key Vault supports two major authorization approaches.

## Azure RBAC

Recommended for modern Azure implementations.

Example role:

```text
Key Vault Secrets User
```

This allows a workload to read secrets without giving unnecessary administrative permissions.

---

## Key Vault Access Policies

Older authorization model.

Example:

```text
Get
List
Set
Delete
```

For new production environments, prefer **Azure RBAC** unless there is a specific compatibility requirement.

---

# 8. Least Privilege

Never give an application:

```text
Owner
Contributor
Key Vault Administrator
```

just because it needs one secret.

Example:

```text
Product Service
      |
      v
Key Vault Secrets User
      |
      v
Read secrets
```

The application should only receive the permissions it actually requires.

---

# 9. Create Key Vault

Example:

```bash
az keyvault create \
  --name <keyvault-name> \
  --resource-group <resource-group> \
  --location <region> \
  --enable-rbac-authorization true
```

Verify:

```bash
az keyvault show \
  --name <keyvault-name> \
  --resource-group <resource-group>
```

---

# 10. Store Secrets

Example:

```bash
az keyvault secret set \
  --vault-name <keyvault-name> \
  --name mongodb-connection-string \
  --value "<connection-string>"
```

Another example:

```bash
az keyvault secret set \
  --vault-name <keyvault-name> \
  --name payment-api-key \
  --value "<secret-value>"
```

List secrets:

```bash
az keyvault secret list \
  --vault-name <keyvault-name> \
  -o table
```

Get a secret:

```bash
az keyvault secret show \
  --vault-name <keyvault-name> \
  --name mongodb-connection-string
```

Do not print secret values unnecessarily in CI/CD logs.

---

# 11. Create User-Assigned Managed Identity

```bash
az identity create \
  --name nexcart-workload-identity \
  --resource-group <resource-group> \
  --location <region>
```

Get client ID:

```bash
az identity show \
  --name nexcart-workload-identity \
  --resource-group <resource-group> \
  --query clientId \
  -o tsv
```

Get principal ID:

```bash
az identity show \
  --name nexcart-workload-identity \
  --resource-group <resource-group> \
  --query principalId \
  -o tsv
```

---

# 12. Grant Key Vault Permission

Example:

```bash
az role assignment create \
  --assignee-object-id <principal-id> \
  --assignee-principal-type ServicePrincipal \
  --role "Key Vault Secrets User" \
  --scope "/subscriptions/<subscription-id>/resourceGroups/<rg>/providers/Microsoft.KeyVault/vaults/<keyvault>"
```

Verify:

```bash
az role assignment list \
  --assignee <principal-id> \
  -o table
```

Senior principle:

```text
Identity → Role → Scope

not

Application → Administrator access
```

---

# 13. Configure Federated Identity

Conceptually:

```text
AKS OIDC issuer
       |
       v
Federated Identity Credential
       |
       v
Managed Identity
```

Example:

```bash
az identity federated-credential create \
  --name product-service-fic \
  --identity-name nexcart-workload-identity \
  --resource-group <resource-group> \
  --issuer <AKS-OIDC-ISSUER> \
  --subject system:serviceaccount:nexcart:product-service-sa \
  --audiences api://AzureADTokenExchange
```

The exact issuer must come from the AKS cluster.

Check:

```bash
az aks show \
  --resource-group <resource-group> \
  --name <aks-name> \
  --query oidcIssuerProfile.issuer \
  -o tsv
```

---

# 14. Enable AKS Workload Identity

For an existing cluster, verify:

```bash
az aks show \
  --resource-group <resource-group> \
  --name <aks-name> \
  --query "{oidc:oidcIssuerProfile.enabled, workloadIdentity:securityProfile.workloadIdentity.enabled}"
```

The cluster should have:

```text
OIDC issuer = enabled
Workload Identity = enabled
```

---

# 15. Key Vault + AKS Integration

A common production pattern is:

```text
AKS
 |
 +-- ServiceAccount
 |
 +-- Workload Identity
 |
 +-- Microsoft Entra ID
 |
 +-- Managed Identity
 |
 +-- Key Vault
 |
 +-- Secret
```

There are two common application integration patterns.

### Pattern 1 — Azure SDK

Application directly authenticates using Azure Identity libraries.

```text
Application
   |
Azure SDK
   |
Workload Identity
   |
Key Vault
```

### Pattern 2 — Secrets Store CSI Driver

Kubernetes retrieves secrets from Key Vault and exposes them to the workload.

```text
Key Vault
    |
Secrets Store CSI Driver
    |
Kubernetes Pod
```

If using CSI, a `SecretProviderClass` defines which Key Vault objects should be accessed.

---

# 16. SecretProviderClass Example

Example:

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: nexcart-keyvault
  namespace: nexcart
spec:
  provider: azure

  parameters:
    usePodIdentity: "false"
    clientID: "<MANAGED-IDENTITY-CLIENT-ID>"

    keyvaultName: "<KEYVAULT-NAME>"

    tenantId: "<TENANT-ID>"

    objects: |
      array:
        - |
          objectName: mongodb-connection-string
          objectType: secret
        - |
          objectName: payment-api-key
          objectType: secret
```

The exact configuration depends on the installed Secrets Store CSI Driver and Azure provider version.

---

# 17. Mount Secrets into Pod

Example:

```yaml
spec:
  containers:
    - name: product-service
      image: <acr>.azurecr.io/product-service:<git-sha>

      volumeMounts:
        - name: secrets-store
          mountPath: "/mnt/secrets-store"
          readOnly: true

  volumes:
    - name: secrets-store
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: nexcart-keyvault
```

The application can then read the mounted secret depending on the selected integration pattern.

---

# 18. Secret as Environment Variable

Environment variables are convenient but require careful handling.

Example:

```yaml
env:
  - name: MONGODB_CONNECTION_STRING
    valueFrom:
      secretKeyRef:
        name: mongodb-secret
        key: connection-string
```

Avoid putting the actual value directly into:

```yaml
env:
  - name: PASSWORD
    value: "mypassword"
```

---

# 19. Kubernetes Secret vs Azure Key Vault

| Kubernetes Secret | Azure Key Vault |
|---|---|
| Stored in Kubernetes | Managed Azure service |
| Base64 encoded by default | Centralized secret management |
| Namespace scoped | Azure RBAC |
| Easy for Kubernetes-native apps | Better for centralized enterprise secrets |
| Can be exposed through manifests | Better audit/control |
| Secret rotation must be designed | Supports centralized rotation |

Important:

```text
Base64 != Encryption
```

A Kubernetes Secret containing:

```text
password123
```

may simply be:

```text
cGFzc3dvcmQxMjM=
```

It is not automatically secure just because it is Base64 encoded.

---

# 20. Secret Rotation

Production credentials should not be permanent.

Example:

```text
Day 0
  ↓
Secret created
  ↓
Application uses Secret
  ↓
Secret rotation
  ↓
New version created
  ↓
Application refresh/restart
  ↓
Old credential revoked
```

Typical credentials requiring rotation:

- Database passwords
- API keys
- Client secrets
- TLS certificates
- GitLab tokens
- Service credentials

Modern Workload Identity reduces the need for Azure client-secret rotation because the application does not store a long-lived Azure secret.

---

# 21. Certificate Management

Certificates are another Key Vault use case.

Example:

```text
Key Vault
   |
TLS Certificate
   |
Application Gateway / Ingress / Application
```

Production concerns:

- Expiry monitoring
- Renewal
- New certificate version
- Application reload
- Validation
- Rollback
- Alerting

Never wait until the certificate expires before discovering the problem.

---

# 22. GitLab CI/CD + Key Vault

Do not put secrets directly in:

```yaml
variables:
  DB_PASSWORD: "mypassword"
```

Better:

```text
GitLab
   |
OIDC / Federated Identity
   |
Azure
   |
Key Vault
```

The pipeline retrieves only the required secret when needed.

Preferred authentication:

```text
GitLab OIDC
      ↓
Microsoft Entra Federated Credential
      ↓
Azure Identity
```

This avoids long-lived Azure client secrets in GitLab CI variables.

---

# 23. Key Vault + Terraform

Terraform can provision:

- Key Vault
- Managed Identity
- RBAC assignments
- Federated credentials
- Key Vault secrets where appropriate
- Private endpoints
- Networking

Example:

```hcl
resource "azurerm_key_vault" "nexcart" {
  name                = var.key_vault_name
  location            = var.location
  resource_group_name = var.resource_group_name
  tenant_id            = data.azurerm_client_config.current.tenant_id

  sku_name = "standard"

  rbac_authorization_enabled = true
}
```

Avoid storing sensitive values unnecessarily in Terraform state.

Example risk:

```hcl
resource "azurerm_key_vault_secret" "db_password" {
  name         = "db-password"
  value        = var.db_password
  key_vault_id = azurerm_key_vault.nexcart.id
}
```

The secret can become part of Terraform state.

Senior approach:

```text
Infrastructure provisioning
        +
Secret lifecycle management
        =
separate concerns where possible
```

---

# 24. Key Vault Network Security

For production environments, consider:

```text
Public Network
      |
      X
      |
Private Endpoint
      |
Azure Key Vault
```

Controls can include:

- Private Endpoint
- Private DNS
- Firewall rules
- Network restrictions
- RBAC
- Diagnostic logs

A private Key Vault requires correct DNS and network routing from AKS.

---

# 25. Key Vault Troubleshooting

## Problem: 403 Forbidden

Check:

```bash
az role assignment list \
  --assignee <principal-id> \
  -o table
```

Verify:

```text
Correct identity
Correct role
Correct scope
Correct tenant
```

---

## Problem: Pod Cannot Read Secret

Check ServiceAccount:

```bash
kubectl get sa -n nexcart
kubectl describe sa product-service-sa -n nexcart
```

Check deployment:

```bash
kubectl get deploy product-service -n nexcart -o yaml
```

Verify:

```text
serviceAccountName
azure.workload.identity/client-id
azure.workload.identity/use=true
```

---

## Problem: Workload Identity Not Working

Check:

```bash
az aks show \
  -g <rg> \
  -n <aks> \
  --query oidcIssuerProfile
```

Check ServiceAccount:

```bash
kubectl get sa product-service-sa -n nexcart -o yaml
```

Check pod:

```bash
kubectl describe pod <pod> -n nexcart
```

Check events:

```bash
kubectl get events \
  -n nexcart \
  --sort-by=.lastTimestamp
```

---

# 26. Common Key Vault Errors

| Error | Possible Cause |
|---|---|
| `403 Forbidden` | Missing RBAC permission |
| `SecretNotFound` | Wrong secret name/version |
| Authentication failed | Identity configuration issue |
| Token exchange failure | Federated identity mismatch |
| Timeout | Network/private endpoint/DNS |
| DNS resolution failure | Private DNS issue |
| CSI mount failure | CSI/provider configuration |
| Wrong tenant | Tenant configuration mismatch |

---

# 27. Troubleshooting Flow

When a pod cannot access Key Vault:

```text
1. Is Pod running?
       ↓
2. Is correct ServiceAccount attached?
       ↓
3. Is Workload Identity enabled?
       ↓
4. Is OIDC issuer configured?
       ↓
5. Does federated credential match?
       ↓
6. Is correct Managed Identity used?
       ↓
7. Does identity have Key Vault role?
       ↓
8. Is secret name correct?
       ↓
9. Is network/DNS reachable?
       ↓
10. Check application/CSI logs
```

Commands:

```bash
kubectl get pod -n nexcart
kubectl describe pod <pod> -n nexcart
kubectl get sa -n nexcart
kubectl get events -n nexcart --sort-by=.lastTimestamp
```

Azure:

```bash
az identity show \
  -g <rg> \
  -n <identity>
```

```bash
az role assignment list \
  --assignee <principal-id> \
  -o table
```

---

# 28. Identity Security Checklist

```text
[ ] No Azure client secrets in Git
[ ] No database passwords in Git
[ ] No secrets inside Docker images
[ ] No plaintext secrets in Helm values
[ ] Workload Identity enabled
[ ] OIDC enabled
[ ] Federated identity configured
[ ] Least-privilege RBAC
[ ] Key Vault RBAC enabled
[ ] Private networking where required
[ ] Secret rotation process defined
[ ] Certificate expiry monitoring
[ ] Key Vault diagnostic logging enabled
[ ] Access reviewed periodically
```

---

# 29. Production Day-2 Problems

## Problem 1 — Secret rotation breaks application

Possible reason:

```text
New secret created
        ↓
Application still using old value
        ↓
Old credential revoked
        ↓
Application fails
```

Solution:

```text
Rotate
 ↓
Validate new credential
 ↓
Refresh application
 ↓
Smoke test
 ↓
Revoke old credential
```

---

## Problem 2 — Certificate expires

Prevention:

```text
Certificate
     ↓
Expiry monitoring
     ↓
Alert
     ↓
Renewal
     ↓
Deployment/reload
     ↓
Validation
```

---

## Problem 3 — Developer has excessive access

Review:

```bash
az role assignment list \
  --scope <scope> \
  -o table
```

Remove unnecessary roles and apply least privilege.

---

# 30. Senior Interview Questions

### Q1. Why use Azure Key Vault with AKS?

**Answer:**

> I use Key Vault to centralize sensitive configuration and remove secrets from source code, container images, and Helm values. For AKS workloads, I prefer Microsoft Entra Workload Identity so pods can authenticate without storing long-lived Azure credentials.

---

### Q2. How does AKS access Key Vault without a password?

```text
Pod
 ↓
Kubernetes ServiceAccount
 ↓
Workload Identity
 ↓
Federated Identity
 ↓
Managed Identity
 ↓
Key Vault
```

The workload receives a token and exchanges it for Azure access instead of using a stored client secret.

---

### Q3. What is the difference between ServiceAccount and Managed Identity?

**ServiceAccount:**

```text
Kubernetes identity
```

**Managed Identity:**

```text
Azure identity
```

Workload Identity connects them:

```text
Kubernetes ServiceAccount
        ↓
Federated Identity
        ↓
Azure Managed Identity
```

---

### Q4. Why not store secrets directly in Helm values?

Because Helm values are commonly stored in Git.

Bad:

```yaml
mongodbPassword: MyPassword123
```

Better:

```text
Helm
 ↓
ServiceAccount
 ↓
Workload Identity
 ↓
Key Vault
```

---

### Q5. What happens if Key Vault returns 403?

I would check:

1. Which identity the pod is using.
2. ServiceAccount configuration.
3. Federated identity credential.
4. Managed Identity principal ID.
5. Key Vault RBAC role.
6. Role assignment scope.
7. Tenant configuration.
8. Network connectivity if the vault is private.

---

### Q6. How would you implement least privilege?

Example:

```text
product-service
      ↓
Managed Identity A
      ↓
Key Vault Secrets User
      ↓
Specific Key Vault
```

I would avoid granting subscription-wide Contributor or Owner access.

---

### Q7. How would you rotate a production secret?

> I would create the new secret version, validate that the application can authenticate using the new value, refresh or restart the workload if required, perform smoke testing, and only then revoke the old credential. I would also ensure the process is automated and monitored.

---

### Q8. How would you prevent Azure credential leakage in GitLab?

Preferred approach:

```text
GitLab OIDC
     ↓
Microsoft Entra Federated Credential
     ↓
Azure Identity
```

This avoids storing a long-lived Azure client secret in GitLab CI/CD variables.

---

### Q9. What is the difference between Key Vault and Kubernetes Secret?

> Kubernetes Secrets are Kubernetes-native objects and are useful for workload configuration, but Base64 encoding is not encryption. Azure Key Vault provides centralized secret management, Azure RBAC, auditing, rotation capabilities, and integration with Azure identity. For sensitive enterprise credentials, I prefer Key Vault with Workload Identity.

---

### Q10. How would you troubleshoot a Key Vault issue in production?

I would follow the identity chain:

```text
Pod
 ↓
ServiceAccount
 ↓
Workload Identity
 ↓
Federated Credential
 ↓
Managed Identity
 ↓
RBAC
 ↓
Key Vault
 ↓
Secret
```

Then I would check Kubernetes events, pod configuration, Azure role assignments, Key Vault logs, and network/DNS connectivity.

---

# 31. NexCart Identity Architecture

```text
                       GitLab
                          |
                    OIDC / Deploy
                          |
                          v
                    Azure / AKS
                          |
              +-----------+-----------+
              |                       |
       ServiceAccount          AKS Identity
              |
       Workload Identity
              |
              v
      Microsoft Entra ID
              |
      Federated Credential
              |
              v
    User Assigned Managed Identity
              |
              v
        Azure Key Vault
              |
      +-------+--------+
      |       |        |
     DB     API Key   TLS Cert
   Secret   Secret    Secret
```

---

# 32. End-to-End NexCart Flow

```text
Developer
    |
    v
GitLab
    |
    | CI/CD
    v
Docker Build
    |
    v
Azure Container Registry
    |
    v
AKS
    |
    +-- Namespace
    |
    +-- Deployment
    |
    +-- Service
    |
    +-- Ingress
    |
    +-- ServiceAccount
    |
    +-- Workload Identity
              |
              v
       Microsoft Entra ID
              |
              v
         Key Vault
              |
              +-- Database credentials
              +-- API secrets
              +-- Certificates
```

---

# 33. Important Production Principles

```text
Identity > Password

Short-lived token > Long-lived secret

Workload Identity > Client Secret

Key Vault > Git/Helm plaintext secret

RBAC > Broad administrative permissions

Private Endpoint > Public exposure where required

Least Privilege > Convenience

Automated Rotation > Manual Rotation

Monitoring > Waiting for expiry/failure
```

## Senior DevOps Rule

> **Never solve an identity problem by adding another static password. First understand the identity chain, trust relationship, authorization scope, and network path.**