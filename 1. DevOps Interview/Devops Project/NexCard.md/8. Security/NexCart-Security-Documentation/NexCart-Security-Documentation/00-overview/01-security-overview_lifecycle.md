---
title: "• 01-security-overview_lifecycle"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 1
---

# Security Overview

## THEORY

DevSecOps integrates security into the software delivery lifecycle.

### NexCart coverage

| Area | Tool/topic | Purpose |
|---|---|---|
| CI/CD Security | Gitleaks | Secret scanning |
| CI/CD Security | Dependency-Check | Dependency vulnerability scanning |
| CI/CD Security | Trivy | Container image scanning |
| CI/CD Security | OWASP ZAP | DAST |
| CI/CD / Repository | Nexus | Artifact/container image repository |
| CI/CD Security | Fortify | Future SAST; not implemented |
| Code Quality | SonarQube | Static analysis and quality |
| Kubernetes | RBAC | Authorization |
| Kubernetes | ServiceAccount | Workload identity |
| Kubernetes | SecurityContext | Container/pod security |
| Kubernetes | NetworkPolicy | Network access control |
| Secrets | Azure Key Vault | Secret storage |
| Network/TLS | NSG, Firewall, TLS | Network and transport security |

## NEXCART IMPLEMENTATION

```text
Developer -> GitLab -> CI
                    -> Gitleaks
                    -> SonarQube
                    -> Dependency-Check
                    -> Docker Build -> Trivy
                    -> ZAP
                    -> Nexus
                    -> Kubernetes / AKS
```

Core CI is kept separate from security/quality YAML files so security can be enabled or disabled without making `.gitlab-ci.yml` large.

## INTERVIEW

### 30-second
"In NexCart I integrated security checks into GitLab CI/CD. Gitleaks scans secrets, SonarQube performs static code analysis, Dependency-Check scans third-party dependencies, Trivy scans Docker images and ZAP performs DAST. Nexus stores private container images."

### Questions
- What is DevSecOps?
- What is Shift Left?
- SAST vs DAST vs SCA?
- Why multiple scanners?
- Reporting vs enforcement?


===========================

# Security Lifecycle

## THEORY

```text
Code -> Commit -> Secret Scan -> Static Analysis
     -> Dependency Scan -> Build -> Container Scan
     -> Publish -> Deploy -> DAST -> Runtime
```

### Key concepts
- Shift Left: perform security checks earlier.
- SAST: static application security testing.
- DAST: dynamic testing of a running application.
- SCA: third-party/open-source dependency analysis.
- Secret scanning: detects credentials/tokens in source.

## NEXCART
Gitleaks = secrets; SonarQube = code; Dependency-Check = dependencies; Trivy = containers; ZAP = running web application.
