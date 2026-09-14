---
title: "• secrets"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 5
---

- 01-secrets-management
- 02-azure-key-vault

# Secrets Management

## THEORY

Secrets include passwords, tokens, API keys, private keys and database credentials.

Never commit real secrets to Git.

## NEXCART PROGRESSION

```text
Local -> .env (not committed)
GitLab -> CI/CD Variables
Azure -> Key Vault
AKS -> Workload Identity / CSI integration
```

Current CI variables include:

```text
SONAR_TOKEN
NEXUS_USERNAME
NEXUS_PASSWORD
NEXUS_REGISTRY
```

Never put actual values in documentation or YAML.

## BEST PRACTICES

- Do not commit secrets.
- Mask/protect sensitive CI variables where appropriate.
- Rotate exposed credentials.
- Prefer managed secret stores.
- Use least privilege.
- Avoid printing credentials in CI logs.

## INTERVIEW

"I use GitLab CI/CD variables for CI secrets and plan to use Azure Key Vault with AKS Workload Identity for the Azure implementation."

==================================

# Azure Key Vault

## THEORY

Azure Key Vault stores secrets, keys and certificates as a managed Azure service.

## FUTURE NEXCART IMPLEMENTATION

```text
AKS Pod -> Workload Identity -> Azure Key Vault -> Secret
```

The Azure phase can use the Secrets Store CSI Driver to make required secrets available to workloads.

## STATUS

Planned for the Azure AKS phase; not currently the source of secrets for the local lab.

## SECURITY

Use least privilege, avoid long-lived Azure credentials, rotate secrets and audit access.

## INTERVIEW

"In the Azure version of NexCart I plan to use Key Vault and AKS Workload Identity so workloads can access secrets without embedding long-lived cloud credentials."
