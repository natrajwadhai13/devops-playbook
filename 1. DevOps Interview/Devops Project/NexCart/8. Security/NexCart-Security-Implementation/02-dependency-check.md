---
title: "• OWASP Dependency-Check"
parent: "• Security_Implementation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 2
---


# 02 - OWASP Dependency-Check Implementation

## 1. Implementation Status

**Implemented:** Yes

**Where:** `jenkins-node`

**Execution:** GitLab Runner Shell executor.

**Installed version during implementation:** Dependency-Check 12.1.0.

## 2. Architecture

```text
GitLab
   |
   v
GitLab Runner
   |
   v
jenkins-node
   |
   v
Dependency-Check 12.1.0
   |
   v
NexCart repository
   |
   +---- HTML report
   |
   +---- JSON report
   |
   v
GitLab artifacts
```

## 3. Ubuntu Installation Used

Dependency-Check was installed under:

```text
/opt/dependency-check
```

The executable is available as:

```bash
dependency-check
```

Verify:

```bash
dependency-check --version
```

Expected implementation version:

```text
12.1.0
```

## 4. Required Data Directory Permission

Dependency-Check needs a writable data directory.

Create it:

```bash
sudo mkdir -p /opt/dependency-check/data
```

Give the GitLab Runner user access:

```bash
sudo chown -R gitlab-runner:gitlab-runner /opt/dependency-check/data
```

Verify:

```bash
ls -ld /opt/dependency-check/data
```

The owner should be:

```text
gitlab-runner gitlab-runner
```

## 5. GitLab CI Implementation

The job is maintained in:

```text
ci/security.yml
```

Current implementation:

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
  artifacts:
    when: always
    paths:
      - dependency-check-report/
    expire_in: 7 days
```

## 6. Why These Directories Are Excluded

NexCart contains generated/dependency directories that should not be scanned as source input for this job:

```text
node_modules/
target/
.venv/
__pycache__/
dist/
.git/
```

## 7. Reports

The job generates:

```text
dependency-check-report/
├── dependency-check-report.html
└── dependency-check-report.json
```

GitLab uploads this directory as a pipeline artifact.

## 8. GitLab Variables

No Nexus/Sonar variable is required for this Dependency-Check job.

The NVD data is downloaded/updated by Dependency-Check during its operation.

## 9. Verification

On Ubuntu:

```bash
dependency-check --version
```

Check permissions:

```bash
ls -ld /opt/dependency-check/data
```

In GitLab:

```text
Pipeline
 -> security
 -> dependency-check
```

Verify:

```text
Job = Passed
Artifacts = dependency-check-report/
```

## 10. Actual NexCart Error

### Problem

Dependency-Check initially failed because it could not create/use:

```text
/opt/dependency-check/data
```

### Root Cause

The GitLab Runner executes as:

```text
gitlab-runner
```

That user did not have write permission to the Dependency-Check data directory.

### Solution

```bash
sudo mkdir -p /opt/dependency-check/data
sudo chown -R gitlab-runner:gitlab-runner /opt/dependency-check/data
```

### Verification

The GitLab Dependency-Check job passed and generated HTML/JSON reports.

## 11. Reproduce From Zero

```text
1. Install Dependency-Check under /opt/dependency-check
2. Make dependency-check available in PATH
3. Verify dependency-check --version
4. Create /opt/dependency-check/data
5. Give data directory to gitlab-runner
6. Add dependency-check job to ci/security.yml
7. Include ci/security.yml in main pipeline
8. Commit/push
9. Run pipeline
10. Download/review dependency-check-report artifacts
```

## 12. Project Statement

> "I installed OWASP Dependency-Check on the GitLab Runner host and integrated it into the NexCart security stage. It scans the repository while excluding generated dependency/build directories and publishes HTML and JSON reports as GitLab artifacts."
