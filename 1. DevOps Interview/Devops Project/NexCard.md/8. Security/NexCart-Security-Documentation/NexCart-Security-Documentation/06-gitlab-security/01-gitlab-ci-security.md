---
title: "• 06-gitlab-security"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 7
---

- 01-gitlab-ci-security
- 02-runner-security

# GitLab CI/CD Security

## THEORY

Important controls:

- CI/CD variables
- Masked variables
- Protected variables
- Runner security
- access tokens
- branch protection
- artifact security

## NEXCART CI DESIGN

Main `.gitlab-ci.yml` contains limited orchestration. Jobs are separated:

```text
ci/backend-build.yml
ci/frontend-build.yml
ci/docker-build.yml
ci/nexus-push.yml
ci/security.yml
ci/quality.yml
```

Main file:

```yaml
include:
  - local: "ci/backend-build.yml"
  - local: "ci/frontend-build.yml"
  - local: "ci/docker-build.yml"
  - local: "ci/nexus-push.yml"
  # - local: 'ci/security.yml'
  # - local: 'ci/quality.yml'
```

## SECURITY VARIABLES

Do not commit actual values for Nexus or SonarQube credentials.

## INTERVIEW

"I separate GitLab CI responsibilities into dedicated YAML files so the main pipeline remains maintainable and optional security/quality checks can be enabled independently."

=====================================

# GitLab Runner Security

## THEORY

A runner executes CI commands, so it is a security boundary.

NexCart uses a Shell executor on `jenkins-node`.

Because shell jobs execute on the host:

- control who can run pipelines
- protect the runner
- restrict Docker access
- avoid untrusted code on trusted runners
- patch the host
- protect credentials

## NEXCART

Docker access was granted to `gitlab-runner` because Docker build/scan/push jobs require it.

## INTERVIEW

"A Shell executor is powerful because jobs run directly on the host. I therefore treat the runner as trusted infrastructure and restrict repository access and host permissions."
