---
title: "• OWASP ZAP"
parent: "• Security_Implementation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 4
---


# 04 - OWASP ZAP Implementation

## 1. Implementation Status

**Implemented:** Yes

**Execution:** Docker container on `jenkins-node` through the GitLab Runner.

**Scan type:** DAST baseline scan.

## 2. Architecture

```text
NexCart frontend image
        |
        v
Temporary frontend container
        |
        v
Host port 8080
        |
        v
Docker bridge gateway
        |
        v
ZAP container
        |
        v
Baseline scan
        |
        v
zap-report.html
        |
        v
GitLab artifact
```

## 3. ZAP Image

The implementation uses:

```text
ghcr.io/zaproxy/zaproxy:stable
```

Pull/verify manually:

```bash
docker pull ghcr.io/zaproxy/zaproxy:stable
```

Check:

```bash
docker images | grep zaproxy
```

## 4. Start NexCart Frontend Locally

The tested frontend image was:

```text
nexcart/frontend:1.0
```

Start temporary container:

```bash
docker run -d \
  --name nexcart-frontend-zap \
  -p 8080:80 \
  nexcart/frontend:1.0
```

Verify:

```bash
docker ps
```

From another container:

```bash
docker run --rm \
  curlimages/curl:latest \
  http://172.17.0.1:8080
```

The NexCart HTML response confirms the target is reachable.

## 5. Why `172.17.0.1`

On the Linux Docker host, `host.docker.internal` was not usable in the tested setup.

The Docker bridge gateway was identified as:

```text
172.17.0.1
```

The ZAP target therefore became:

```text
http://172.17.0.1:8080
```

## 6. Manual ZAP Scan

The tested command was:

```bash
docker run --rm \
  -v "$(pwd)/zap-reports:/zap/wrk:rw" \
  ghcr.io/zaproxy/zaproxy:stable \
  zap-baseline.py \
  -t http://172.17.0.1:8080 \
  -r zap-report.html \
  -I
```

Create the report directory first:

```bash
mkdir -p zap-reports
chmod 777 zap-reports
```

## 7. Actual Manual Result

The manual scan completed with:

```text
FAIL-NEW: 0
FAIL-INPROG: 0
WARN-NEW: 9
WARN-INPROG: 0
INFO: 0
IGNORE: 0
PASS: 58
```

One warning observed was:

```text
Cross-Origin-Embedder-Policy Header Missing or Invalid
```

The project decision was to review warnings instead of modifying application code just to remove a scanner warning.

## 8. GitLab CI Implementation

The job belongs in:

```text
ci/security.yml
```

Current implementation:

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
  artifacts:
    when: always
    paths:
      - zap-reports/
    expire_in: 7 days
  after_script:
    - docker rm -f nexcart-frontend-zap || true
```

## 9. GitLab Variables

No secret GitLab variable is required by the current ZAP job.

## 10. Verification

Check frontend:

```bash
docker ps
```

Check target:

```bash
docker run --rm curlimages/curl:latest http://172.17.0.1:8080
```

Check GitLab:

```text
Pipeline
 -> security
 -> zap-dast
 -> Artifacts
 -> zap-reports/
```

## 11. Actual Errors and Fixes

### Error 1: `host.docker.internal` did not work

**Root Cause:** Linux Docker setup did not provide the expected hostname route.

**Solution:** Use the Docker bridge gateway:

```text
172.17.0.1
```

**Verification:**

```bash
docker run --rm curlimages/curl:latest http://172.17.0.1:8080
```

### Error 2: ZAP report permission error

**Root Cause:** Mounted report directory was not writable.

**Solution:**

```bash
mkdir -p zap-reports
chmod 777 zap-reports
```

**Verification:** `zap-report.html` appears in the artifact directory.

### Error 3: Warning caused non-zero behavior

**Solution:** Current baseline command uses:

```text
-I
```

This is the current lab/reporting setup.

## 12. Reproduce From Zero

```text
1. Install Docker
2. Pull ZAP image
3. Build NexCart frontend image
4. Start frontend on port 8080
5. Find/verify Docker bridge gateway
6. Test target using curl container
7. Create zap-reports
8. Run zap-baseline.py
9. Review HTML report
10. Add job to ci/security.yml
11. Run GitLab pipeline
12. Review artifact
```

## 13. Project Statement

> "I implemented OWASP ZAP as a DAST baseline scan in GitLab CI. The pipeline starts the NexCart frontend container, scans it from a ZAP container through the Docker bridge gateway, generates an HTML report and stores it as a GitLab artifact."
