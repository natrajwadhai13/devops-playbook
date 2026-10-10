---
title: "• security-checklist"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 9
---

# NexCart Security Checklist

## Source
- [ ] No passwords/API keys in Git
- [ ] Gitleaks enabled when required
- [ ] Exposed credentials rotated

## Dependencies
- [ ] Dependency-Check enabled
- [ ] CVEs reviewed
- [ ] Vulnerable dependencies updated where appropriate

## Containers
- [ ] Trivy enabled
- [ ] HIGH/CRITICAL policy defined for production
- [ ] Base images updated
- [ ] No secrets baked into images
- [ ] Non-root where compatible

## Web
- [ ] ZAP baseline
- [ ] Findings reviewed
- [ ] HTTPS/TLS
- [ ] Security headers reviewed

## GitLab
- [ ] Sensitive variables protected/masked appropriately
- [ ] Runner access restricted
- [ ] Production branches protected
- [ ] CI changes reviewed

## Kubernetes
- [ ] RBAC
- [ ] ServiceAccounts
- [ ] SecurityContext
- [ ] NetworkPolicy
- [ ] Least privilege
- [ ] Secure secret handling

## Azure
- [ ] NSG
- [ ] Firewall/network controls
- [ ] Key Vault
- [ ] Workload Identity
- [ ] TLS certificates
