---
title: "11-terraform"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 11
---

# 🏗️ 11. Terraform – Infrastructure as Code

## 1. Overview

Terraform is an Infrastructure as Code (IaC) tool used to provision and manage infrastructure through declarative configuration.

For NexCart, Terraform can manage the Azure platform layer:

```text id="t1"
Terraform
   |
   +-- Resource Group
   +-- Virtual Network
   +-- Subnets
   +-- AKS
   +-- ACR
   +-- Key Vault
   +-- Managed Identity
   +-- RBAC
   +-- Monitoring
   +-- Private Endpoints
```

GitLab CI/CD then manages application delivery:

```text id="a2"
Terraform
    ↓
Azure Infrastructure
    ↓
AKS / ACR / Key Vault
    ↓
GitLab CI/CD
    ↓
Docker + Helm
    ↓
Application Deployment
```

---

# 2. Why Terraform?

Without IaC:

```text
Engineer
   ↓
Azure Portal
   ↓
Create resources manually
   ↓
Repeat for TEST
   ↓
Repeat for PROD
```

Problems:

- Manual mistakes
- Configuration drift
- Difficult auditing
- Slow environment creation
- Inconsistent environments
- Poor disaster recovery

With Terraform:

```text
Git
 ↓
Terraform
 ↓
Plan
 ↓
Review
 ↓
Apply
 ↓
Repeatable infrastructure
```

---

# 3. Terraform Core Concepts

| Concept | Purpose |
|---|---|
| Provider | Connects Terraform to a platform |
| Resource | Infrastructure object |
| Variable | Input parameter |
| Output | Exposes useful information |
| Module | Reusable infrastructure component |
| State | Tracks managed infrastructure |
| Plan | Shows proposed changes |
| Apply | Applies changes |
| Backend | Stores Terraform state |
| Data Source | Reads existing information |
| Resource Graph | Determines dependency order |

---

# 4. Terraform Architecture

```text id="b3"
Terraform Configuration
        |
        v
Terraform Provider
        |
        v
Azure API
        |
        v
Azure Resources
```

For Azure:

```hcl id="c4"
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

---

# 5. Terraform Files

Typical NexCart structure:

```text id="d5"
terraform/
├── providers.tf
├── versions.tf
├── variables.tf
├── main.tf
├── outputs.tf
├── locals.tf
├── terraform.tfvars
├── backend.tf
├── modules/
│   ├── network/
│   ├── aks/
│   ├── acr/
│   ├── key-vault/
│   └── monitoring/
└── environments/
    ├── dev/
    ├── test/
    └── prod/
```

For small projects, a simpler structure is acceptable.

---

# 6. Provider

The provider allows Terraform to communicate with Azure.

Example:

```hcl id="e6"
provider "azurerm" {
  features {}
}
```

Authentication should preferably use workload identity/OIDC or another secure non-interactive mechanism in CI/CD.

Avoid hardcoding:

```hcl id="f7"
client_secret = "my-secret"
```

---

# 7. Resource

A Terraform resource represents an infrastructure object.

Example Resource Group:

```hcl id="g8"
resource "azurerm_resource_group" "nexcart" {
  name     = "rg-nexcart-prod"
  location = "Central India"
}
```

Terraform tracks this resource in its state.

---

# 8. Variables

Avoid hardcoding environment-specific values.

Example:

```hcl id="h9"
variable "location" {
  type        = string
  description = "Azure region"
  default     = "Central India"
}
```

Use:

```hcl id="i10"
resource "azurerm_resource_group" "nexcart" {
  name     = var.resource_group_name
  location = var.location
}
```

---

# 9. Variable Types

Common types:

```hcl id="j11"
variable "environment" {
  type = string
}

variable "node_count" {
  type = number
}

variable "enable_monitoring" {
  type = bool
}

variable "tags" {
  type = map(string)
}

variable "subnets" {
  type = list(string)
}
```

---

# 10. `terraform.tfvars`

Example:

```hcl id="k12"
environment        = "prod"
location            = "Central India"
resource_group_name = "rg-nexcart-prod"

node_count = 3

tags = {
  application = "nexcart"
  environment = "prod"
  managed_by  = "terraform"
}
```

Do not commit sensitive `.tfvars` files containing passwords or secrets.

---

# 11. Outputs

Outputs expose useful infrastructure information.

Example:

```hcl id="l13"
output "resource_group_name" {
  value = azurerm_resource_group.nexcart.name
}
```

ACR:

```hcl id="m14"
output "acr_login_server" {
  value = azurerm_container_registry.nexcart.login_server
}
```

Useful for:

- CI/CD
- Other Terraform modules
- Operators
- Automation

---

# 12. Terraform State

Terraform state is one of the most important concepts.

Terraform uses state to understand:

```text id="n15"
Desired Configuration
        +
Current State
        ↓
Required Changes
```

State contains information about managed resources.

Example:

```text id="o16"
terraform.tfstate
```

Do not treat Terraform state as a normal source-code file.

---

# 13. Remote State

Never rely on local state for a team production environment.

Recommended:

```text id="p17"
GitLab
   |
Terraform
   |
Azure Storage Account
   |
Terraform State
```

Azure Blob Storage is commonly used as a remote backend.

Benefits:

- Centralized state
- Team collaboration
- Locking support
- Better recovery
- Reduced local-state risk

---

# 14. Azure Backend Example

Example:

```hcl id="q18"
terraform {
  backend "azurerm" {
    resource_group_name  = "rg-terraform-state"
    storage_account_name = "stnexcarttfstate"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}
```

The backend configuration should be protected because state can contain sensitive infrastructure information.

---

# 15. Terraform State Locking

When two engineers run:

```bash id="r19"
terraform apply
```

at the same time, infrastructure changes can conflict.

Remote state locking prevents simultaneous state modifications.

Concept:

```text id="s20"
Engineer A
    |
terraform apply
    |
State Lock
    X
Engineer B
terraform apply
```

Engineer B waits until the state is available.

---

# 16. Terraform Lifecycle

Standard workflow:

```text id="t21"
Write Code
   ↓
terraform fmt
   ↓
terraform init
   ↓
terraform validate
   ↓
terraform plan
   ↓
Code Review
   ↓
terraform apply
   ↓
Verify
```

---

# 17. `terraform init`

Initializes Terraform.

```bash id="u22"
terraform init
```

It downloads:

- Providers
- Modules
- Backend configuration

Run again after:

- Provider changes
- Module changes
- Backend changes

---

# 18. `terraform fmt`

Formats Terraform code.

```bash id="v23"
terraform fmt -recursive
```

Use it before committing code.

---

# 19. `terraform validate`

Checks configuration syntax and consistency.

```bash id="w24"
terraform validate
```

It does not replace `terraform plan`.

---

# 20. `terraform plan`

Shows the proposed changes.

```bash id="x25"
terraform plan
```

Typical output:

```text
+ create
~ update
- destroy
-/+ replace
```

Senior rule:

> Never blindly run `terraform apply` in production without reviewing the plan.

---

# 21. `terraform apply`

Applies the changes.

```bash id="y26"
terraform apply
```

Production:

```bash id="z27"
terraform apply tfplan
```

Safer CI/CD flow:

```bash id="a28"
terraform plan -out=tfplan
terraform apply tfplan
```

This ensures the approved plan is the plan that gets applied.

---

# 22. Terraform Destroy

```bash id="b29"
terraform destroy
```

This can delete infrastructure.

Never execute blindly in production.

Use:

```text id="c30"
terraform plan
```

and carefully review destructive changes.

---

# 23. Dependency Management

Terraform automatically builds a dependency graph.

Example:

```hcl id="d31"
resource "azurerm_subnet" "aks" {
  name                 = "aks-subnet"
  resource_group_name  = azurerm_resource_group.nexcart.name
  virtual_network_name = azurerm_virtual_network.nexcart.name
  address_prefixes     = ["10.10.1.0/24"]
}
```

Terraform understands:

```text
Resource Group
      ↓
VNet
      ↓
Subnet
      ↓
AKS
```

Explicit dependency:

```hcl id="e32"
depends_on = [
  azurerm_role_assignment.example
]
```

Use `depends_on` only when Terraform cannot infer the dependency automatically.

---

# 24. Data Sources

Data sources read existing infrastructure.

Example:

```hcl id="f33"
data "azurerm_client_config" "current" {}
```

Then:

```hcl id="g34"
tenant_id = data.azurerm_client_config.current.tenant_id
```

Use data sources when infrastructure already exists and Terraform only needs information from it.

---

# 25. Terraform Modules

Modules provide reusable infrastructure.

Example:

```text id="h35"
modules/
├── aks/
├── acr/
├── network/
├── key-vault/
└── monitoring/
```

Usage:

```hcl id="i36"
module "aks" {
  source = "./modules/aks"

  cluster_name = "aks-nexcart-prod"
  location     = var.location
}
```

---

# 26. Why Use Modules?

Without modules:

```text id="j37"
1000 lines
+
Repeated code
+
Difficult maintenance
```

With modules:

```text id="k38"
Network Module
AKS Module
ACR Module
Key Vault Module
```

Benefits:

- Reusability
- Standardization
- Easier testing
- Reduced duplication
- Consistent architecture

---

# 27. NexCart Module Design

Recommended:

```text id="l39"
terraform/
└── modules/
    ├── network/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    ├── acr/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    ├── aks/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    ├── key-vault/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── monitoring/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

# 28. Environment Separation

Avoid copying the complete infrastructure code for every environment.

Preferred:

```text id="m40"
Reusable Modules
       |
       +--> DEV
       |
       +--> TEST
       |
       +--> PROD
```

Example:

```hcl id="n41"
module "aks" {
  source = "../../modules/aks"

  cluster_name = "aks-nexcart-prod"
  node_count   = 3
  environment  = "prod"
}
```

Environment-specific values should be controlled through variables.

---

# 29. Terraform Workspaces

Terraform workspaces can separate state.

Example:

```bash id="o42"
terraform workspace list
terraform workspace new dev
terraform workspace new prod
terraform workspace select prod
```

However, workspaces are not automatically the best choice for environment isolation.

For complex production systems, separate environment directories/backends are often clearer:

```text id="p43"
environments/
├── dev/
├── test/
└── prod/
```

---

# 30. Terraform vs Helm

Terraform manages infrastructure.

Helm manages Kubernetes application resources.

```text id="q44"
Terraform
   |
   +-- Resource Group
   +-- VNet
   +-- AKS
   +-- ACR
   +-- Key Vault
   +-- Identity
   |
   v
Azure Platform

Helm
   |
   +-- Deployment
   +-- Service
   +-- Ingress
   +-- HPA
   +-- ServiceAccount
   |
   v
AKS Application
```

Avoid having both tools manage the same Kubernetes resource unless ownership is explicitly designed.

---

# 31. Terraform vs GitLab CI/CD

Terraform:

```text id="r45"
Infrastructure Provisioning
```

GitLab CI/CD:

```text id="s46"
Application Delivery
```

Example:

```text id="t47"
Terraform
   ↓
Create AKS
   ↓
Create ACR
   ↓
Create Key Vault
   ↓
Configure Identity
   ↓
GitLab CI/CD
   ↓
Build Docker Image
   ↓
Push ACR
   ↓
Helm Deploy AKS
```

---

# 32. Terraform + NexCart Architecture

```text id="u48"
                    GitLab
                       |
            +----------+----------+
            |                     |
            v                     v
       Terraform              CI/CD
            |                     |
            v                     v
     Azure Infrastructure     Docker Build
            |                     |
     +------+------+              v
     |      |      |             ACR
    VNet   AKS    ACR              |
     |      |                     |
     |      |                     v
     |      +------------------> Helm
     |                            |
     |                            v
     |                           AKS
     |
     +--> Key Vault
     |
     +--> Managed Identity
```

---

# 33. AKS with Terraform

Terraform can provision:

- AKS cluster
- Node pools
- Networking
- Managed identities
- Workload Identity configuration
- RBAC
- Monitoring integration

Example simplified:

```hcl id="v49"
resource "azurerm_kubernetes_cluster" "nexcart" {
  name                = "aks-nexcart-prod"
  location            = azurerm_resource_group.nexcart.location
  resource_group_name = azurerm_resource_group.nexcart.name
  dns_prefix          = "nexcart"

  default_node_pool {
    name       = "system"
    node_count = 3
    vm_size    = "Standard_D4s_v5"
  }

  identity {
    type = "SystemAssigned"
  }
}
```

Production configuration should additionally address networking, node pools, upgrade settings, security, monitoring, and workload identity requirements.

---

# 34. ACR with Terraform

Example:

```hcl id="w50"
resource "azurerm_container_registry" "nexcart" {
  name                = "nexcartacr"
  resource_group_name = azurerm_resource_group.nexcart.name
  location            = azurerm_resource_group.nexcart.location
  sku                 = "Premium"

  admin_enabled = false
}
```

Prefer managed identity/RBAC over enabling the ACR admin account unnecessarily.

---

# 35. Key Vault with Terraform

Example:

```hcl id="x51"
resource "azurerm_key_vault" "nexcart" {
  name                = "kv-nexcart-prod"
  location            = azurerm_resource_group.nexcart.location
  resource_group_name = azurerm_resource_group.nexcart.name
  tenant_id            = data.azurerm_client_config.current.tenant_id

  sku_name = "standard"

  rbac_authorization_enabled = true
}
```

Then grant the required identity:

```text id="y52"
Managed Identity
      ↓
Key Vault Secrets User
      ↓
Key Vault
```

---

# 36. Terraform and Secrets

Avoid:

```hcl id="z53"
variable "db_password" {
  default = "Password123"
}
```

Avoid committing:

```text id="a54"
terraform.tfvars
```

when it contains sensitive values.

Also remember:

> Marking a Terraform variable as `sensitive = true` hides it from normal CLI output, but does not automatically prevent the value from being stored in Terraform state.

Better architecture:

```text id="b55"
Application Secret
       ↓
Azure Key Vault
       ↓
AKS Workload Identity
       ↓
Application
```

---

# 37. Terraform Drift

Drift occurs when infrastructure changes outside Terraform.

Example:

```text id="c56"
Terraform
   ↓
AKS node count = 3

Engineer manually changes Azure
   ↓
Node count = 5
```

Terraform configuration still says:

```text id="d57"
3
```

Run:

```bash id="e58"
terraform plan
```

Terraform detects the difference.

---

# 38. Drift Handling

First determine:

```text id="f59"
Was the manual change intentional?
```

If yes:

```text id="g60"
Update Terraform
```

Then:

```bash id="h61"
terraform plan
```

If no:

```text id="i62"
Terraform should restore the desired state
```

Do not blindly apply changes before understanding why the drift occurred.

---

# 39. Terraform Import

If an Azure resource already exists:

```text id="j63"
Azure Resource
      ↓
Terraform import
      ↓
Terraform State
```

Example:

```bash id="k64"
terraform import \
  azurerm_resource_group.nexcart \
  /subscriptions/<subscription-id>/resourceGroups/rg-nexcart-prod
```

Import only adds the resource to state. You still need Terraform configuration representing the resource correctly.

---

# 40. Terraform State Commands

```bash id="l65"
terraform state list
```

Show resource:

```bash id="m66"
terraform state show <resource>
```

Move resource:

```bash id="n67"
terraform state mv <source> <destination>
```

Remove from state:

```bash id="o68"
terraform state rm <resource>
```

Be extremely careful with state manipulation.

---

# 41. Resource Replacement

Terraform may show:

```text
-/+ replace
```

This means the resource cannot be updated in place.

Example:

```text id="p69"
Change immutable property
        ↓
Destroy old resource
        ↓
Create new resource
```

Production concern:

> Always understand the blast radius of a replacement before applying.

---

# 42. Lifecycle Rules

Terraform lifecycle can control resource behavior.

Example:

```hcl id="q70"
lifecycle {
  prevent_destroy = true
}
```

Useful for critical resources.

Example:

```hcl id="r71"
lifecycle {
  ignore_changes = [
    tags
  ]
}
```

Use `ignore_changes` carefully.

It can hide legitimate drift.

---

# 43. Production Protection

For critical resources:

```hcl id="s72"
lifecycle {
  prevent_destroy = true
}
```

Potential candidates:

- Production databases
- Critical Key Vault
- Core networking
- Production state infrastructure

However, this is not a replacement for backups and disaster-recovery procedures.

---

# 44. Terraform CI/CD

Recommended pipeline:

```text id="t73"
terraform fmt
       ↓
terraform validate
       ↓
terraform plan
       ↓
Security / Policy checks
       ↓
Code Review
       ↓
Approval
       ↓
terraform apply
```

Production:

```text id="u74"
Plan
 ↓
Review
 ↓
Approval
 ↓
Apply exact plan
```

---

# 45. GitLab + Terraform Pipeline

Example:

```yaml id="v75"
stages:
  - validate
  - plan
  - apply

terraform-validate:
  stage: validate
  script:
    - terraform fmt -check -recursive
    - terraform init
    - terraform validate

terraform-plan:
  stage: plan
  script:
    - terraform init
    - terraform plan -out=tfplan
  artifacts:
    paths:
      - tfplan

terraform-apply:
  stage: apply
  script:
    - terraform init
    - terraform apply -auto-approve tfplan
  when: manual
```

Production authentication should use secure OIDC/federated identity rather than static Azure client secrets where supported.

---

# 46. Terraform Plan Security

A plan can reveal sensitive infrastructure information.

Do not expose:

```text id="w76"
tfplan
state files
credentials
connection strings
```

through public artifacts or logs.

Use:

- Protected artifacts
- Restricted runners
- Secure state backend
- Access control
- Masked variables

---

# 47. Terraform Security Scanning

Useful tools include:

```text id="x77"
Checkov
tfsec
Trivy config
OPA / Conftest
```

They can detect issues such as:

- Public storage
- Open network rules
- Missing encryption
- Excessive permissions
- Insecure security groups
- Public endpoints

Example:

```bash id="y78"
trivy config .
```

---

# 48. Policy as Code

Large organizations may enforce policies such as:

```text id="z79"
No public Key Vault
No public storage
Mandatory tags
Approved Azure regions
Encryption required
Private endpoints required
No Owner assignment to applications
```

Concept:

```text id="a80"
Terraform Code
      ↓
Policy Check
      ↓
Pass → Plan
Fail → Pipeline stops
```

This prevents insecure infrastructure from reaching Azure.

---

# 49. Terraform State Recovery

Production state is critical.

Recommended:

```text id="b81"
Remote Backend
     +
Storage Protection
     +
Access Control
     +
Backup/Recovery
```

If state becomes unavailable, do not immediately recreate infrastructure.

First determine:

```text id="c82"
Is infrastructure still present?
Is state corrupted?
Was state deleted?
Was backend configuration changed?
```

Then recover carefully.

---

# 50. Common Terraform Problems

| Problem | Likely Cause |
|---|---|
| `Provider configuration not present` | Provider/module issue |
| `State lock` | Another Terraform operation |
| `403 Forbidden` | Azure RBAC |
| `Resource already exists` | Resource not in state |
| Unexpected destroy | Configuration/state mismatch |
| Drift | Manual Azure change |
| Authentication failure | CI identity issue |
| Provider version conflict | Version constraint |
| Backend initialization failure | Storage/backend issue |
| Dependency cycle | Incorrect resource relationships |

---

# 51. Terraform State Lock Error

Example:

```text
Error acquiring the state lock
```

First check whether another pipeline or engineer is running Terraform.

Do not immediately force-unlock.

Check:

```text id="d83"
1. Is another apply running?
2. Is a GitLab job still active?
3. Did a previous job crash?
4. Is the lock actually stale?
```

Only use force-unlock after confirming it is safe.

---

# 52. Authentication Troubleshooting

If GitLab cannot authenticate to Azure:

Check:

```text id="e84"
GitLab OIDC token
        ↓
Federated credential
        ↓
Azure identity
        ↓
RBAC
        ↓
Subscription/resource scope
```

Azure:

```bash id="f85"
az account show
```

Identity:

```bash id="g86"
az identity show \
  -g <rg> \
  -n <identity>
```

Role:

```bash id="h87"
az role assignment list \
  --assignee <principal-id> \
  -o table
```

---

# 53. Terraform Troubleshooting Framework

When Terraform behaves unexpectedly:

```text id="i88"
1. Check Git change
        ↓
2. Check Terraform configuration
        ↓
3. Check provider version
        ↓
4. Check state
        ↓
5. Check actual Azure resource
        ↓
6. Run terraform plan
        ↓
7. Understand dependency graph
        ↓
8. Review destructive changes
        ↓
9. Apply only after approval
```

Useful commands:

```bash id="j89"
terraform version
terraform providers
terraform state list
terraform state show <resource>
terraform plan
```

---

# 54. Terraform Debugging

For deep debugging:

```bash id="k90"
TF_LOG=INFO terraform plan
```

More detailed:

```bash id="l91"
TF_LOG=DEBUG terraform plan
```

Do not enable verbose logs casually in CI/CD because they may expose sensitive information.

---

# 55. Terraform Dependency Graph

Generate graph:

```bash id="m92"
terraform graph
```

Useful for understanding complex dependencies.

Concept:

```text id="n93"
Resource Group
      |
      v
VNet
      |
      v
Subnet
      |
      v
AKS
      |
      +---- Identity
      |
      +---- Monitoring
```

---

# 56. Production Day-2 Problems

### Problem 1 — Manual Azure change

```text
Azure Portal
   ↓
Engineer changes configuration
```

Detection:

```bash
terraform plan
```

Resolution:

```text
Intentional → Update Terraform
Unintentional → Reconcile infrastructure
```

---

### Problem 2 — Provider Upgrade

Before upgrading:

```bash
terraform plan
```

Review:

```text
Provider changelog
Breaking changes
Resource behavior
State migration
```

Then test in DEV.

---

### Problem 3 — Terraform Apply Failed Halfway

Do not immediately rerun blindly.

Check:

```bash
terraform state list
terraform plan
```

Then determine which resources were created successfully and what Terraform still wants to change.

Terraform is designed to converge toward the declared state.

---

# 57. Terraform Upgrade Strategy

Example:

```text id="o94"
Current
Terraform 1.x
Provider 4.x

       ↓

Test in DEV
       ↓
Upgrade provider
       ↓
terraform init -upgrade
       ↓
terraform validate
       ↓
terraform plan
       ↓
Integration testing
       ↓
Production
```

Never upgrade Terraform providers directly in production without testing.

---

# 58. Disaster Recovery

Terraform helps recreate infrastructure but is not itself a complete DR strategy.

For NexCart:

```text id="p95"
Terraform Code
      +
Remote State
      +
Container Images in ACR
      +
Database Backup
      +
Key Vault Recovery
      +
DNS / Certificates
      =
DR Capability
```

Important distinction:

```text
Terraform recreates infrastructure.
It does not automatically recover application data.
```

---

# 59. Cost Optimization with Terraform

Terraform can standardize:

- VM sizes
- AKS node pools
- Autoscaling
- Non-production shutdown policies
- Storage tiers
- Resource tagging

Example:

```text id="q96"
DEV
 ↓
Smaller node pool

PROD
 ↓
Production sizing
```

Avoid copying production sizing into every environment.

---

# 60. Mandatory Resource Tags

Example:

```hcl id="r97"
tags = {
  application = "nexcart"
  environment = "prod"
  owner       = "devops"
  managed_by  = "terraform"
  cost_center = "engineering"
}
```

Tags help with:

- Cost allocation
- Ownership
- Governance
- Automation
- Troubleshooting

---

# 61. Terraform Naming Strategy

Consistent names make operations easier.

Example:

```text id="s98"
rg-nexcart-prod
aks-nexcart-prod
acr-nexcart-prod
kv-nexcart-prod
vnet-nexcart-prod
```

Avoid random resource names such as:

```text id="t99"
resource123
test-final-new
prod-new2
```

---

# 62. Senior Interview Questions

### Q1. Explain Terraform state.

> Terraform state maps Terraform configuration to real infrastructure. Terraform uses it to determine what already exists and what changes are required. In production I store state remotely with locking and protect access because state can contain sensitive infrastructure information.

---

### Q2. Why use remote state?

> Remote state provides centralized collaboration, locking, controlled access, and better recovery compared with local state. For Azure I commonly use an Azure Storage backend.

---

### Q3. What is Terraform drift?

> Drift occurs when infrastructure is changed outside Terraform. I detect it through `terraform plan`, determine whether the manual change was intentional, and then either update the Terraform configuration or reconcile the infrastructure back to the declared state.

---

### Q4. Terraform vs Ansible?

**Terraform:**

```text
Infrastructure provisioning
```

**Ansible:**

```text
Configuration management / operational automation
```

Example:

```text id="u100"
Terraform
   ↓
Create VM / Network / AKS

Ansible
   ↓
Configure operating system/application
```

They can complement each other.

---

### Q5. Terraform vs Helm?

> Terraform manages infrastructure such as AKS, networking, ACR, Key Vault and identities. Helm packages and deploys Kubernetes application resources such as Deployments, Services, Ingress, HPA and ServiceAccounts.

---

### Q6. What happens when two engineers run Terraform simultaneously?

> With a properly configured remote backend, Terraform state locking prevents concurrent state modification. I would check whether another operation is active before considering any manual lock intervention.

---

### Q7. What is `terraform plan`?

> It compares the desired configuration with the current state and provider data, then shows the changes Terraform intends to make. I use the plan as a mandatory review point before production changes.

---

### Q8. What is `-/+` in Terraform plan?

> It indicates resource replacement. Terraform cannot update the resource in place, so it plans to destroy and recreate it. I treat this as a high-risk change and verify the impact before applying it.

---

### Q9. How do you protect production from accidental destroy?

> I use code review, restricted production pipelines, plan approval, protected environments, `prevent_destroy` for selected critical resources, and strict RBAC. I don't rely on one control alone.

---

### Q10. How do you manage secrets in Terraform?

> I avoid hardcoding secrets and understand that sensitive Terraform values can still exist in state. For application secrets I prefer Azure Key Vault with Workload Identity. Terraform manages the infrastructure and identity integration while secret lifecycle is handled securely.

---

### Q11. How do you authenticate GitLab to Azure?

> I prefer GitLab OIDC with Microsoft Entra federated identity because it avoids long-lived Azure client secrets. The GitLab job receives an identity token and exchanges it through the configured federation for Azure access.

---

### Q12. How would you recover if Terraform state was deleted?

> I would first protect the existing infrastructure from accidental recreation, recover the remote state from backend backup/versioning if available, verify the actual Azure resources, and only then reconcile state. I would not immediately run `terraform apply` against an empty state.

---

# 63. Senior Terraform Checklist

```text
Terraform
[ ] Version constraints
[ ] Provider version pinned
[ ] terraform fmt
[ ] terraform validate
[ ] terraform plan
[ ] Code review
[ ] Remote state
[ ] State locking
[ ] State backup/recovery

Architecture
[ ] Resource groups
[ ] VNet
[ ] Subnets
[ ] AKS
[ ] ACR
[ ] Key Vault
[ ] Managed Identity
[ ] RBAC
[ ] Monitoring
[ ] Private endpoints

Security
[ ] No secrets in Git
[ ] OIDC
[ ] Least privilege
[ ] State access restricted
[ ] Policy checks
[ ] Security scanning

Operations
[ ] Drift detection
[ ] Import strategy
[ ] Rollback/recovery plan
[ ] Provider upgrade strategy
[ ] Disaster recovery
[ ] Cost tagging
```

---

# 64. NexCart Infrastructure Ownership

```text id="v101"
                    Terraform
                       |
        +--------------+---------------+
        |              |               |
        v              v               v
     Network          AKS             ACR
        |              |               |
        |              |               |
        +--------------+---------------+
                       |
                 Azure Platform
                       |
             +---------+---------+
             |                   |
             v                   v
        Key Vault             Identity
             |
             v
        Application Secrets


                    GitLab CI/CD
                         |
                  Docker Build
                         |
                         v
                        ACR
                         |
                         v
                       Helm
                         |
                         v
                        AKS
```

---

# 65. End-to-End NexCart IaC + CI/CD Flow

```text id="w102"
                GitLab Repository
                       |
             +---------+---------+
             |                   |
             v                   v
        Terraform             Application
             |                   |
             v                   v
       Azure Resources       GitLab CI/CD
             |                   |
      +------+------+             |
      |      |      |             |
     VNet   AKS    ACR            |
      |      |      |             |
      |      |      +<------------+
      |      |          Docker Push
      |      |
      |      +<------------- Helm Deploy
      |
      +---- Key Vault
      |
      +---- Managed Identity
      |
      +---- RBAC
```

## Core Senior DevOps Principle

> **Terraform should be the source of truth for infrastructure. GitLab CI/CD should control the delivery process, and Helm should manage Kubernetes application releases. Clear ownership between these layers prevents drift, accidental overwrites, and operational confusion.**