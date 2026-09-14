---
title: "• Trivy Implementation"
parent: "• Security_Implementation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 3
---


# 03 - Trivy Implementation

## 1. Implementation Status

**Implemented:** Yes

**Where:** `jenkins-node`

**Execution:** GitLab Runner Shell executor.

**Installed version during implementation:** Trivy 0.71.2.

## 2. Architecture

```text
NexCart source
     |
     v
Docker Build
     |
     v
7 NexCart Docker images
     |
     v
Trivy
     |
     v
JSON reports
     |
     v
GitLab artifacts
```

## 3. Ubuntu Installation

Trivy was installed on:

```text
jenkins-node
```

Verify:

```bash
trivy --version
```

Expected implementation version:

```text
0.71.2
```

## 4. Docker Images Scanned

The NexCart pipeline scans:

```text
nexcart/api-gateway:1.0
nexcart/frontend:1.0
nexcart/notification-service:1.0
nexcart/order-service:1.0
nexcart/payment-service:1.0
nexcart/product-service:1.0
nexcart/user-service:1.0
```

## 5. Pre-Check

Before scanning:

```bash
docker images | grep nexcart
```

Verify an image:

```bash
docker image inspect nexcart/product-service:1.0
```

## 6. GitLab CI Implementation

The job belongs in:

```text
ci/security.yml
```

Current implementation:

```yaml
trivy:
  stage: security
  tags:
    - nexcart-runner
  script:
    - mkdir -p trivy-reports
    - trivy image --format json --output trivy-reports/product-service.json nexcart/product-service:1.0
    - trivy image --format json --output trivy-reports/notification-service.json nexcart/notification-service:1.0
    - trivy image --format json --output trivy-reports/frontend.json nexcart/frontend:1.0
    - trivy image --format json --output trivy-reports/order-service.json nexcart/order-service:1.0
    - trivy image --format json --output trivy-reports/user-service.json nexcart/user-service:1.0
    - trivy image --format json --output trivy-reports/payment-service.json nexcart/payment-service:1.0
    - trivy image --format json --output trivy-reports/api-gateway.json nexcart/api-gateway:1.0
  artifacts:
    when: always
    paths:
      - trivy-reports/
    expire_in: 7 days
```

## 7. Important Current Design

The current pipeline is **reporting-oriented**.

It does not use:

```text
--exit-code 1
```

Therefore, this:

```bash
trivy image --severity HIGH,CRITICAL --exit-code 1 <image>
```

is an example of a future enforcement approach, not the current NexCart implementation.

## 8. Local Verification

```bash
trivy image nexcart/product-service:1.0
```

JSON example:

```bash
trivy image \
  --format json \
  --output product-service.json \
  nexcart/product-service:1.0
```

Check:

```bash
ls -lh product-service.json
```

## 9. GitLab Verification

```text
Pipeline
 -> security
 -> trivy
 -> Job artifacts
 -> trivy-reports/
```

Verify seven JSON reports are present.

## 10. Actual NexCart Error Pattern

### Problem

```text
image not found
```

### Root Cause

The image is missing on the runner or the image tag is different.

### Solution

```bash
docker images | grep nexcart
```

Check the exact tag.

### Verification

```bash
docker image inspect nexcart/product-service:1.0
```

## 11. Docker Permission Requirement

The GitLab Runner needs Docker access.

The NexCart runner was configured with Docker access using:

```bash
sudo usermod -aG docker gitlab-runner
sudo systemctl restart gitlab-runner
```

Verify:

```bash
sudo -u gitlab-runner docker ps
```

## 12. Reproduce From Zero

```text
1. Install Docker
2. Install Trivy
3. Verify trivy --version
4. Ensure gitlab-runner can access Docker
5. Build NexCart images
6. Add Trivy job to ci/security.yml
7. Include security.yml in main pipeline
8. Commit/push
9. Run pipeline
10. Review seven JSON artifacts
```

## 13. Project Statement

> "I installed Trivy on the GitLab Runner host and integrated it after Docker image creation. The pipeline scans all seven NexCart images and stores JSON vulnerability reports as GitLab artifacts. Currently it is reporting-oriented; production enforcement can be enabled with severity thresholds and exit-code policies."
