---
title: "04-owasp-zap"
parent: "• 01-cicd-security"
grand_parent: "• Security_Documentation"
grand_grand_parent: "8. Security"
grand_grand_grand_parent: "NexCart"
grand_grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 4
---

# OWASP ZAP

## THEORY

ZAP is a web application security testing tool. DAST tests the application while it is running.

SAST vs DAST:
- SAST: code/static representation.
- DAST: running application from an external perspective.

## NEXCART IMPLEMENTATION

```text
Frontend image -> temporary container -> ZAP baseline scan -> HTML report -> GitLab artifact
```

Current job:
```yaml
zap-dast:
  stage: security
  tags:
    - nexcart-runner
  script:
    - docker rm -f nexcart-frontend-zap || true
    - docker run -d --name nexcart-frontend-zap -p 8080:80 nexcart/frontend:1.0
    - sleep 10
    - mkdir -p zap-reports
    - chmod 777 zap-reports
    - docker run --rm
      -v "$(pwd)/zap-reports:/zap/wrk:rw"
      ghcr.io/zaproxy/zaproxy:stable
      zap-baseline.py
      -t http://172.17.0.1:8080
      -r zap-report.html
      -I
```

## ACTUAL RESULT
Baseline scanning produced warnings including a Cross-Origin-Embedder-Policy header warning. A warning should be evaluated before changing application code.

## TROUBLESHOOTING
**Problem:** report permission error.  
**Root Cause:** mounted report directory was not writable.  
**Solution:** `mkdir -p zap-reports; chmod 777 zap-reports`.  
**Verification:** report appears in GitLab artifacts.

**Problem:** ZAP cannot reach frontend.  
**Root Cause:** container-to-host routing.  
**Solution:** use the Docker bridge gateway discovered from the bridge network.  
**Verification:** curl the target from a container.

## INTERVIEW
"I use ZAP for DAST by starting the NexCart frontend and running a baseline scan against the running application."
