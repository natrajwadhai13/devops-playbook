---
title: "• 03-trivy"
parent: "• 01-cicd-security"
grand_parent: "• Security_Documentation"
grand_grand_parent: "8. Security"
grand_grand_grand_parent: "NexCart"
grand_grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 3
---

# Trivy

## THEORY

Trivy scans container images for known vulnerabilities and can also support other security scanning use cases.

Key terms: image, base image, OS package, application dependency, CVE, HIGH, CRITICAL.

## NEXCART IMPLEMENTATION

Seven images are scanned:
```text
api-gateway
frontend
notification-service
order-service
payment-service
product-service
user-service
```

Current approach produces JSON reports:
```yaml
trivy image --format json --output trivy-reports/product-service.json nexcart/product-service:1.0
```

### Reporting vs enforcement

Reporting:
```bash
trivy image nexcart/product-service:1.0
```

Possible production enforcement:
```bash
trivy image --severity HIGH,CRITICAL --exit-code 1 nexcart/product-service:1.0
```

The current NexCart scan is reporting-oriented; it does not use `--exit-code 1`.

## TROUBLESHOOTING
**Problem:** image not found.  
**Root Cause:** image is absent on the runner or tag differs.  
**Solution:** `docker images | grep nexcart` and build/correct the tag.  
**Verification:** `docker image inspect nexcart/product-service:1.0`.

## INTERVIEW
"I run Trivy after Docker image creation to scan all NexCart images. Reports are retained as CI artifacts, while production can enforce severity thresholds."
