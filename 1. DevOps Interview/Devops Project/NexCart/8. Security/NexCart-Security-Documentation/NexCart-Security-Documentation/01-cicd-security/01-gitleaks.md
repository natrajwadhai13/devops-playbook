---
title: "• 01-gitleaks"
parent: "• 01-cicd-security"
grand_parent: "• Security_Documentation"
grand_grand_parent: "8. Security"
grand_grand_grand_parent: "NexCart"
grand_grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 1
---

# Gitleaks

## THEORY

### What is it?
A secret-scanning tool that detects credentials and sensitive strings in repositories.

### Why needed?
Committed credentials can be copied from Git and abused.

### Key terms
Secret, token, API key, credential, false positive, secret rotation, Git history.

## NEXCART IMPLEMENTATION

### Where
GitLab Runner using the Gitleaks container image.

### Current job
```yaml
gitleaks:
  stage: security
  image:
    name: zricethezav/gitleaks:latest
    entrypoint: [""]
  script:
    - gitleaks detect --source . --verbose --redact
```

### Local
```bash
gitleaks detect --source . --verbose --redact
```

### Verification
Successful scan with no real secret findings.

## REPORT / OUTPUT
Review rule, file, context and whether the finding is real. Never expose the secret in logs.

## TROUBLESHOOTING
**Problem:** secret finding.  
**Root Cause:** credential/token matched a detection rule.  
**Solution:** remove it, rotate/revoke the credential, move the replacement to secure secret storage.  
**Verification:** run Gitleaks again.

## INTERVIEW
" I use Gitleaks in GitLab CI to detect hard-coded secrets before they progress through the delivery pipeline."

Questions: What if a secret was already pushed? What is rotation? How do you handle false positives?


=======================================

