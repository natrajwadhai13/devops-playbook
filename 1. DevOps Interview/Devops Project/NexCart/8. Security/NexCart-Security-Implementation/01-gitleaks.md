---
title: "• Gitleaks Implementation"
parent: "• Security_Implementation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 1
---


# 01 - Gitleaks Implementation

> **Purpose of this note:** Reproduce the Gitleaks implementation in NexCart. This file contains implementation only: where it runs, installation approach actually used, configuration, GitLab CI, verification, and the error/fix history.

## 1. Implementation Status

**Implemented:** Yes

**NexCart usage:** GitLab CI secret scanning.

**Current implementation method:** Gitleaks runs as a Docker image inside GitLab CI. A standalone Gitleaks binary was **not installed on `jenkins-node`** during the NexCart implementation.

## 2. Architecture

```text
Developer
   |
   v
GitLab Repository
   |
   v
GitLab CI
   |
   v
GitLab Runner (jenkins-node)
   |
   v
Gitleaks Docker Container
   |
   v
Repository scan
   |
   +---- Secret found ---> Job fails
   |
   +---- No secret ------> Job passes
```

## 3. Prerequisites

GitLab Runner must be working.

NexCart runner tag:

```text
nexc​art-runner
```

The runner is installed on:

```text
jenkins-node
```

## 4. Local / Ubuntu Understanding

The actual NexCart implementation did not require a local Gitleaks installation.

The CI job pulls/uses:

```text
zricethezav/gitleaks:latest
```

If you want to reproduce the same method manually with Docker on Ubuntu:

```bash
docker pull zricethezav/gitleaks:latest
```

Verify:

```bash
docker images | grep gitleaks
```

Run against a local NexCart checkout:

```bash
docker run --rm \
  -v "$(pwd):/repo" \
  zricethezav/gitleaks:latest \
  detect --source /repo --verbose --redact
```

## 5. GitLab CI Implementation

The Gitleaks job belongs in:

```text
ci/security.yml
```

Current implementation:

```yaml
gitleaks:
  stage: security
  image:
    name: zricethezav/gitleaks:latest
    entrypoint: [""]
  script:
    - gitleaks detect --source . --verbose --redact
```

The main `.gitlab-ci.yml` includes the security file when security scanning is enabled:

```yaml
include:
  - local: 'ci/security.yml'
```

## 6. GitLab Pipeline Flow

```text
GitLab commit
      |
      v
security stage
      |
      v
gitleaks job
      |
      v
gitleaks detect
      |
      v
Repository scan
```

## 7. How to Verify

### Check runner

On `jenkins-node`:

```bash
sudo systemctl status gitlab-runner
```

Check:

```bash
gitlab-runner verify
```

### Check Docker

```bash
docker --version
docker images | grep gitleaks
```

### Check GitLab

Open:

```text
GitLab Project
 -> Build
 -> Pipelines
 -> security
 -> gitleaks
```

A successful scan shows a green job.

## 8. If a Secret Is Found

Do not simply delete the line and continue.

If the value is a real credential:

1. Revoke/rotate it.
2. Remove it from source.
3. Move the replacement to secure storage.
4. Check Git history if necessary.
5. Run Gitleaks again.

Never print the real secret into CI logs.

## 9. Troubleshooting

### Problem: `gitleaks: command not found` on Ubuntu

**Root Cause:** A standalone Gitleaks binary is not installed.

**NexCart Solution:** Use the GitLab CI Docker image method:

```yaml
image:
  name: zricethezav/gitleaks:latest
  entrypoint: [""]
```

**Verification:**

```text
GitLab gitleaks job -> Passed
```

### Problem: GitLab pipeline does not start

**Root Cause:** `.gitlab-ci.yml` syntax/include problem.

**Check:**

```text
GitLab
 -> CI/CD
 -> Editor
 -> Validate
```

Make sure the include is top-level:

```yaml
include:
  - local: 'ci/security.yml'
```

It must NOT be inside `script:`.

## 10. Reproduce From Zero

```text
1. Install/start Docker on Ubuntu
2. Ensure GitLab Runner is working
3. Create ci/security.yml
4. Add Gitleaks job
5. Include ci/security.yml from .gitlab-ci.yml
6. Commit
7. Push
8. Open GitLab Pipeline
9. Check gitleaks job
10. Review result
```

## 11. Project Statement

> "I integrated Gitleaks into the NexCart GitLab CI security stage. The GitLab Runner executes Gitleaks in a container and scans the repository for exposed secrets. The current lab implementation is reporting/blocking based on Gitleaks' exit status; no standalone Gitleaks installation is required on the runner."
