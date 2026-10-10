---
title: "• 02-dependency-check"
parent: "• 01-cicd-security"
grand_parent: "• Security_Documentation"
grand_grand_parent: "8. Security"
grand_grand_grand_parent: "NexCart"
grand_grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 2
---


# OWASP Dependency-Check

## THEORY

Dependency-Check performs Software Composition Analysis for known vulnerabilities in dependencies.

Key terms: direct dependency, transitive dependency, CVE, NVD, CVSS, vulnerable version, fixed version.

## NEXCART IMPLEMENTATION

Runs on `jenkins-node` through the GitLab Runner shell executor.

```yaml
dependency-check:
  stage: security
  tags:
    - nexcart-runner
  script:
    - mkdir -p dependency-check-report
    - dependency-check
      --project "NexCart"
      --scan .
      --format HTML
      --format JSON
      --out dependency-check-report
      --exclude "**/node_modules/**"
      --exclude "**/target/**"
      --exclude "**/.venv/**"
      --exclude "**/__pycache__/**"
      --exclude "**/dist/**"
      --exclude "**/.git/**"
      --disableYarnAudit
      --disableNodeAudit
```

Reports:
```text
dependency-check-report/
├── dependency-check-report.html
└── dependency-check-report.json
```

## ACTUAL NEXCART ERROR

**Problem:** could not create Dependency-Check data directory.  
**Root Cause:** `gitlab-runner` lacked write permission.  
**Solution:**
```bash
sudo mkdir -p /opt/dependency-check/data
sudo chown -R gitlab-runner:gitlab-runner /opt/dependency-check/data
```
**Verification:** GitLab job passed and artifacts were generated.

## REPORT
Review component, CVE, CVSS/severity, installed version and fixed version.

## INTERVIEW
"I integrated Dependency-Check to identify known vulnerabilities in third-party libraries and publish HTML/JSON reports as CI artifacts."
