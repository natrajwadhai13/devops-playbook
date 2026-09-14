---
title: "06-fortify"
parent: "• 01-cicd-security"
grand_parent: "• Security_Documentation"
grand_grand_parent: "8. Security"
grand_grand_grand_parent: "NexCart"
grand_grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 6
---

# Fortify SAST - Future Reference

## STATUS
Not implemented in NexCart currently. Licensed Fortify SCA/SSC access is not available in the practice environment.

## THEORY

Fortify SCA is an enterprise SAST solution for static application security analysis.

Key terms:
- Fortify SCA
- `sourceanalyzer`
- FPR
- Fortify SSC
- SAST
- vulnerability
- audit

## FUTURE FLOW

```text
GitLab -> GitLab Runner -> Fortify SCA -> NexCart.fpr -> Fortify SSC
```

Potential CI variables:
```text
FORTIFY_SSC_URL
FORTIFY_SSC_TOKEN
FORTIFY_APP_NAME
FORTIFY_APP_VERSION
```

Exact installation/upload commands should be added only when the licensed Fortify version and organization's SSC authentication method are known.

## TROUBLESHOOTING

Current commands:
```text
sourceanalyzer: command not found
fortifyclient: command not found
```

Root cause: Fortify is not installed.

Do not claim Fortify implementation until it is actually installed and verified.

## INTERVIEW
"Fortify SCA is an enterprise SAST solution. I understand its role in a DevSecOps pipeline, but it is not currently implemented in my NexCart practice environment because licensed access is unavailable."
