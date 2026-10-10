---
title: "• security-interview"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 10
---

# NexCart Security Interview

## 30-second answer

"In NexCart, I integrated security checks into GitLab CI/CD. Gitleaks scans secrets, SonarQube performs static analysis, Dependency-Check scans third-party dependencies, Trivy scans Docker images, and OWASP ZAP performs DAST. Nexus is used as the private Docker repository. I separated security and quality jobs from the core pipeline so they can be enabled independently."

## 2-minute answer

"NexCart is a multi-tier application with Java, Node.js and Python services. The GitLab pipeline validates the repository, runs tests, builds applications and Docker images, then publishes images to Nexus. Security controls cover multiple layers: Gitleaks checks source for credentials, SonarQube analyzes code, Dependency-Check analyzes third-party components, Trivy scans the seven Docker images, and ZAP tests the running frontend. Reports are retained where applicable. In production I would define severity thresholds and enforce gates according to organizational risk policy."

## Questions

- What is DevSecOps?
- What is Shift Left?
- SAST vs DAST vs SCA?
- Why multiple scanners?
- Why Gitleaks?
- Why Dependency-Check?
- Why Trivy?
- Why ZAP?
- What is Nexus?
- Reporting vs blocking?
- How do you protect CI secrets?
- What is Kubernetes RBAC?
- What is a ServiceAccount?
- What is NetworkPolicy?
- What is SecurityContext?
- What is Azure Key Vault?
- Why is SonarQube `allow_failure: true` in this lab?
- How would you fail a pipeline on HIGH/CRITICAL Trivy findings?
