# NexCart Security Documentation

Purpose: one reference for **NexCart project implementation + local practice + GitLab CI/CD + study theory + troubleshooting + interviews**.

Every topic follows:
1. Theory: What is it? Why needed? Key concepts and terminology.
2. NexCart implementation: why used, architecture, where it runs, installation, configuration, GitLab CI, commands, verification.
3. Report/output: meaning, severity, investigation.
4. Troubleshooting: Problem -> Root Cause -> Solution -> Command -> Verification.
5. Interview: 30-second answer, 2-minute project explanation, questions.

## Structure

- 00-overview
- 01-cicd-security
- 02-code-quality
- 03-kubernetes-security
- 04-secrets
- 05-network-tls
- 06-gitlab-security
- 07-troubleshooting
- 08-checklists
- 09-interview

## NexCart security flow

```text
GitLab -> GitLab CI/CD
          |-> Gitleaks
          |-> SonarQube
          |-> Dependency-Check
          |-> Docker Build -> Trivy
          |-> ZAP
          -> Nexus Repository
          -> Kubernetes / AKS
```

Fortify is included as future/theory reference only because licensed Fortify SCA/SSC is not currently available.
