---
title: "04-git-gitlab"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 4
---

# 🔀 04 — Git & GitLab

## 4.1 Git & GitLab Overview

Git is the distributed version control system used to manage application source code.

GitLab provides the collaboration and DevOps platform around Git, including:

- Source code repository
- Branch management
- Merge Requests
- Code review
- CI/CD pipelines
- Package/container integration
- Security scanning
- Deployment automation
- Release management
- Auditability

For NexCart, GitLab acts as the central source-code and CI/CD platform.

```text
Developer
    |
    v
Git
    |
    v
GitLab Repository
    |
    +----> Merge Request
    |
    +----> Code Review
    |
    v
GitLab CI/CD
    |
    v
Docker Build
    |
    v
Azure Container Registry
    |
    v
AKS
```

---

# 4.2 NexCart Repository Structure

A practical repository structure can be organized as:

```text
NexCart/
│
├── product-service/
│   ├── src/
│   ├── package.json
│   ├── Dockerfile
│   └── ...
│
├── order-service/
│   ├── src/
│   ├── Dockerfile
│   └── ...
│
├── payment-service/
│   ├── app/
│   ├── requirements.txt
│   ├── Dockerfile
│   └── ...
│
├── notification-service/
│   ├── src/
│   ├── Dockerfile
│   └── ...
│
├── helm/
│   └── nexcart/
│       ├── Chart.yaml
│       ├── values.yaml
│       ├── values-dev.yaml
│       ├── values-test.yaml
│       ├── values-prod.yaml
│       └── templates/
│
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── ...
│
└── .gitlab-ci.yml
```

The exact repository structure can vary depending on whether NexCart uses a monorepo or multiple repositories.

---

# 4.3 Monorepo vs Multi-Repo

## Monorepo

All services are maintained in one Git repository.

```text
nexcart/
├── product-service/
├── order-service/
├── payment-service/
└── notification-service/
```

### Advantages

- Centralized versioning
- Easier cross-service changes
- Single CI/CD configuration
- Easier common tooling
- Easier repository-level security controls

### Disadvantages

- Large repository
- Pipeline complexity
- Unnecessary builds if change detection is not implemented
- Teams can become tightly coupled

---

## Multi-Repo

Each service has its own repository.

```text
nexcart-product
nexcart-order
nexcart-payment
nexcart-notification
```

### Advantages

- Independent lifecycle
- Smaller repositories
- Independent pipelines
- Strong service ownership

### Disadvantages

- More repositories to manage
- Cross-service changes are harder
- Version compatibility becomes more important
- Common configuration must be standardized

---

# 4.4 Git Configuration

Configure Git identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

Check:

```bash
git config --global --list
```

Repository-specific configuration:

```bash
git config user.name "Your Name"
git config user.email "your-email@example.com"
```

---

# 4.5 Clone Repository

```bash
git clone <gitlab-repository-url>
cd NexCart
```

Check:

```bash
git status
git remote -v
```

Example:

```text
origin  <repository-url> (fetch)
origin  <repository-url> (push)
```

---

# 4.6 Git Working Areas

Git has three important areas:

```text
Working Directory
       |
       | git add
       v
Staging Area
       |
       | git commit
       v
Local Repository
       |
       | git push
       v
GitLab Repository
```

### Commands

```bash
git status
git add .
git commit -m "Add product service API"
git push
```

---

# 4.7 Git Branching Strategy

A production-oriented branching strategy can be:

```text
main
 |
 +---- develop
         |
         +---- feature/product-api
         |
         +---- feature/payment-api
         |
         +---- bugfix/order-validation
```

For controlled production environments:

```text
feature/*
    |
    v
develop
    |
    v
release/*
    |
    v
main
```

The exact branching model should depend on release frequency and team size.

---

# 4.8 Feature Branch

Create a feature branch:

```bash
git checkout -b feature/product-api
```

Modern equivalent:

```bash
git switch -c feature/product-api
```

Check:

```bash
git branch
```

Push:

```bash
git push -u origin feature/product-api
```

---

# 4.9 Daily Git Workflow

Typical developer workflow:

```text
Pull latest code
      |
      v
Create feature branch
      |
      v
Implement change
      |
      v
Run tests
      |
      v
Git add
      |
      v
Git commit
      |
      v
Git push
      |
      v
Create Merge Request
      |
      v
Code Review
      |
      v
Pipeline
      |
      v
Merge
```

Commands:

```bash
git switch main
git pull --rebase origin main

git switch -c feature/new-change

git status
git add .
git commit -m "Implement new change"
git push -u origin feature/new-change
```

---

# 4.10 Git Pull vs Fetch

## git fetch

Downloads remote changes but does not modify the current working branch.

```bash
git fetch origin
```

Then inspect:

```bash
git log --oneline HEAD..origin/main
```

## git pull

Normally performs:

```text
git fetch
+
git merge
```

or, when configured:

```text
git fetch
+
git rebase
```

For controlled development, explicitly choosing merge or rebase is better than blindly using `git pull`.

---

# 4.11 Rebase

Rebase moves your commits on top of the latest target branch.

```bash
git fetch origin
git rebase origin/main
```

Example:

```text
Before:

A---B---C---D  main
     \
      E---F    feature
```

After rebase:

```text
A---B---C---D---E'---F'  feature
```

Benefits:

- Cleaner history
- Easier review
- Fewer unnecessary merge commits

Important:

Do not casually rebase shared branches because rebase rewrites commit history.

---

# 4.12 Merge

Merge combines branch histories.

```bash
git switch main
git merge feature/product-api
```

In GitLab, merging is normally performed through a Merge Request after review and successful pipeline validation.

---

# 4.13 Merge Conflict

Typical situation:

```text
Developer A
     |
     v
Changes file
     |
     v
Push

Developer B
     |
     v
Changes same section
     |
     v
Merge conflict
```

Check:

```bash
git status
```

Conflict markers:

```text
<<<<<<< HEAD
Current branch code
=======
Incoming branch code
>>>>>>> feature/product-api
```

Resolve manually, then:

```bash
git add <file>
git commit
```

If rebasing:

```bash
git add <file>
git rebase --continue
```

Abort if required:

```bash
git rebase --abort
```

---

# 4.14 Git Reset

## Soft Reset

Moves HEAD but keeps changes staged.

```bash
git reset --soft HEAD~1
```

## Mixed Reset

Default reset; changes remain in working directory.

```bash
git reset HEAD~1
```

## Hard Reset

Deletes local changes.

```bash
git reset --hard HEAD~1
```

Use `--hard` carefully.

---

# 4.15 Git Revert

`git revert` creates a new commit that reverses an existing commit.

```bash
git revert <commit-id>
```

For production branches, revert is generally safer than rewriting history.

Example:

```text
Production
    |
    v
Bad Commit
    |
    v
Incident
    |
    v
git revert
    |
    v
Known Good State
```

---

# 4.16 Git Stash

Temporarily save uncommitted work:

```bash
git stash
```

List:

```bash
git stash list
```

Apply:

```bash
git stash pop
```

Named stash:

```bash
git stash push -m "payment-service changes"
```

---

# 4.17 Git Log

Basic:

```bash
git log --oneline
```

Graph:

```bash
git log --oneline --graph --decorate --all
```

Inspect a commit:

```bash
git show <commit-id>
```

Find a change:

```bash
git log --all --grep="payment"
```

---

# 4.18 Git Diff

Check working-directory changes:

```bash
git diff
```

Staged changes:

```bash
git diff --staged
```

Compare branches:

```bash
git diff main..feature/product-api
```

This is useful before creating a Merge Request.

---

# 4.19 Git Tags

Tags identify releases.

```bash
git tag v1.0.0
git push origin v1.0.0
```

List:

```bash
git tag
```

Production release:

```text
v1.0.0
v1.1.0
v1.2.0
```

Tags can be connected to Docker image versions and release pipelines.

---

# 4.20 GitLab Merge Request

A Merge Request is used to review and merge code.

Typical flow:

```text
Feature Branch
      |
      v
Push to GitLab
      |
      v
Merge Request
      |
      +----> Code Review
      |
      +----> Automated Tests
      |
      +----> Security Scan
      |
      +----> Quality Gate
      |
      v
Approval
      |
      v
Merge
```

### Merge Request Controls

A production GitLab project should consider:

- Protected branches
- Required approvals
- Successful pipeline requirement
- No direct push to `main`
- Code-owner approval
- Security scanning
- Commit standards
- Merge strategy

---

# 4.21 Protected Branches

Production branches such as `main` should be protected.

Recommended:

```text
main
 |
 +-- No direct developer push
 |
 +-- Merge Request required
 |
 +-- Pipeline must pass
 |
 +-- Approval required
```

This prevents accidental production changes.

---

# 4.22 CODEOWNERS

CODEOWNERS can automatically assign reviewers based on changed files.

Example:

```text
/product-service/       @product-team
/payment-service/       @payment-team
/helm/                  @platform-team
/terraform/             @cloud-team
```

This improves ownership and governance.

---

# 4.23 GitLab CI/CD

GitLab CI/CD is defined using:

```text
.gitlab-ci.yml
```

Typical NexCart pipeline:

```text
Validate
   |
   v
Unit Test
   |
   v
Code Quality
   |
   v
Security Scan
   |
   v
Docker Build
   |
   v
Push to ACR
   |
   v
Deploy Dev
   |
   v
Smoke Test
   |
   v
Approval
   |
   v
Deploy Production
```

---

# 4.24 Basic GitLab Pipeline

Example:

```yaml
stages:
  - test
  - build
  - deploy

test:
  stage: test
  script:
    - npm ci
    - npm test

build:
  stage: build
  script:
    - docker build -t product-service:$CI_COMMIT_SHA .
```

The actual pipeline should be adapted to each service's technology.

---

# 4.25 GitLab Variables

Sensitive values should not be hardcoded in `.gitlab-ci.yml`.

Avoid:

```yaml
variables:
  PASSWORD: "mypassword"
```

Use GitLab CI/CD Variables.

Examples:

```text
AZURE_CLIENT_ID
AZURE_TENANT_ID
AZURE_SUBSCRIPTION_ID
ACR_NAME
KEY_VAULT_NAME
```

Sensitive variables should be:

- Masked
- Protected
- Environment-scoped where appropriate

Prefer OIDC/federated identity for Azure authentication instead of long-lived client secrets.

---

# 4.26 GitLab Runner

GitLab Runner executes CI/CD jobs.

Architecture:

```text
GitLab
   |
   v
Pipeline
   |
   v
GitLab Runner
   |
   +----> Test
   +----> Build
   +----> Scan
   +----> Deploy
```

Runner types can include:

```text
Shared Runner
Group Runner
Project Runner
```

---

# 4.27 Runner Failure Troubleshooting

If pipeline is stuck:

```text
Pipeline
   |
   v
Pending
```

Check:

```text
Runner available?
Runner online?
Runner tags correct?
Runner has required executor?
Runner has sufficient CPU/memory?
Runner has Docker access?
Runner has Azure access?
```

GitLab UI:

```text
Project
 → Settings
 → CI/CD
 → Runners
```

---

# 4.28 Runner Tags

Example:

```yaml
deploy:
  tags:
    - azure-runner
```

The runner must have the matching tag.

If no runner matches:

```text
Job → Pending
```

This is a common GitLab CI troubleshooting issue.

---

# 4.29 Docker Build in GitLab

Example:

```yaml
build-image:
  stage: build

  script:
    - docker build -t $ACR_NAME.azurecr.io/product-service:$CI_COMMIT_SHA .
    - docker push $ACR_NAME.azurecr.io/product-service:$CI_COMMIT_SHA
```

The important concept is:

```text
Git Commit SHA
      |
      v
Docker Image Tag
      |
      v
ACR
      |
      v
Helm
      |
      v
AKS
```

This creates deployment traceability.

---

# 4.30 Azure Authentication from GitLab

Preferred architecture:

```text
GitLab CI
    |
    v
OIDC Token
    |
    v
Microsoft Entra ID
    |
    v
Federated Identity
    |
    v
Azure Resource
```

Avoid long-lived Azure credentials where possible.

Legacy architecture may use:

```text
Client ID
Client Secret
Tenant ID
Subscription ID
```

If client secrets are used, expiry and rotation must be actively managed.

---

# 4.31 Azure Container Registry

After Docker build:

```text
GitLab Runner
      |
      v
Docker Image
      |
      v
Azure Container Registry
```

Example:

```bash
docker build \
  -t <acr>.azurecr.io/product-service:$CI_COMMIT_SHA .

docker push \
  <acr>.azurecr.io/product-service:$CI_COMMIT_SHA
```

Verify:

```bash
az acr repository list \
  --name <acr> \
  -o table
```

Check tags:

```bash
az acr repository show-tags \
  --name <acr> \
  --repository product-service \
  -o table
```

---

# 4.32 GitLab to Helm Deployment

Typical deployment flow:

```text
GitLab Pipeline
      |
      v
Authenticate to Azure
      |
      v
Get AKS Credentials
      |
      v
Helm Lint
      |
      v
Helm Template
      |
      v
Helm Upgrade
      |
      v
AKS
```

Commands:

```bash
az aks get-credentials \
  --resource-group <resource-group> \
  --name <aks-cluster>
```

Validate:

```bash
kubectl get nodes
```

Helm:

```bash
helm lint ./helm/nexcart
```

```bash
helm template nexcart ./helm/nexcart \
  -f ./helm/nexcart/values-prod.yaml
```

Deploy:

```bash
helm upgrade --install nexcart ./helm/nexcart \
  --namespace nexcart \
  --create-namespace \
  -f ./helm/nexcart/values-prod.yaml
```

---

# 4.33 Environment Promotion

Do not rebuild a different image for every environment.

Preferred:

```text
Build Once
    |
    v
Security Test
    |
    v
Same Image
    |
    +----> DEV
    |
    +----> TEST
    |
    +----> PROD
```

Example:

```text
product-service:8f32a91
```

The same immutable image can be promoted through environments.

Only environment-specific configuration should change.

---

# 4.34 GitLab Environment Strategy

```text
Development
     |
     v
Testing
     |
     v
Production
```

Example:

```yaml
deploy-dev:
  environment:
    name: dev

deploy-test:
  environment:
    name: test

deploy-prod:
  environment:
    name: production
```

Production deployment should normally require an approval or controlled promotion mechanism.

---

# 4.35 GitLab Pipeline Optimization

A large microservices pipeline should not rebuild every service for every commit.

Example:

```text
Change:
product-service/src/*
```

Pipeline should primarily execute:

```text
Product Service
```

instead of unnecessarily rebuilding:

```text
Product
Order
Payment
Notification
```

Use path-based rules such as GitLab `rules:changes` where appropriate.

Concept:

```yaml
rules:
  - changes:
      - product-service/**/*
```

This reduces:

- Pipeline execution time
- Runner consumption
- Build cost
- Deployment risk

---

# 4.36 GitLab CI Pipeline Quality Gates

A mature pipeline can include:

```text
Code Validation
      ↓
Unit Tests
      ↓
Code Quality
      ↓
SAST
      ↓
Dependency Scan
      ↓
Container Scan
      ↓
Docker Build
      ↓
Push to ACR
      ↓
Deployment
```

Production deployment should stop if critical security or quality gates fail.

---

# 4.37 Trivy Container Scan

Example:

```bash
trivy image <acr>.azurecr.io/product-service:$CI_COMMIT_SHA
```

The objective is to identify:

```text
OS vulnerabilities
Package vulnerabilities
Dependency vulnerabilities
Known CVEs
```

Critical findings should be evaluated before production deployment.

---

# 4.38 GitLab Pipeline Failure Troubleshooting

When a pipeline fails:

```text
1. Identify failed stage
2. Read job logs
3. Identify first actual error
4. Check recent code/config changes
5. Reproduce locally if possible
6. Validate credentials
7. Validate runner
8. Validate external dependency
9. Fix
10. Re-run
```

Do not start troubleshooting from the last log line blindly.

The first meaningful error is usually more useful.

---

# 4.39 Docker Build Failure

Check:

```text
Dockerfile
Build context
Base image
Dependency installation
Network access
Package manager
File paths
Permissions
Runner environment
```

Example:

```bash
docker build -t product-service:test .
```

Run locally before re-running the pipeline.

---

# 4.40 ACR Push Failure

Typical causes:

```text
Authentication failure
Incorrect registry name
Insufficient permissions
Expired credential
Network restriction
Repository permission
Runner configuration
```

Verify Azure identity and permissions.

For AKS image pulling, ensure the appropriate identity has:

```text
AcrPull
```

on the ACR.

---

# 4.41 Deployment Failure

If GitLab reports successful Helm execution but the application is unavailable:

```bash
helm status nexcart -n nexcart
kubectl get pods -n nexcart
kubectl get deploy -n nexcart
kubectl get svc -n nexcart
kubectl get ingress -n nexcart
```

Then:

```bash
kubectl get events \
  -n nexcart \
  --sort-by=.lastTimestamp
```

Check:

```text
Image
Pod
Service
Ingress
Configuration
Secret
Identity
Readiness
Application logs
```

---

# 4.42 GitLab Deployment Rollback

Check Helm history:

```bash
helm history nexcart -n nexcart
```

Rollback:

```bash
helm rollback nexcart <revision> -n nexcart
```

Validate:

```bash
helm status nexcart -n nexcart
kubectl get pods -n nexcart
```

Rollback should be part of the standard release process, not an emergency-only activity.

---

# 4.43 GitLab Auditability

A mature DevOps platform should answer:

```text
Who changed the code?
Who approved it?
Which pipeline built it?
Which Docker image was created?
Which image is running?
Who deployed it?
When was it deployed?
Which Helm revision is active?
What changed between releases?
```

Traceability:

```text
Developer
   ↓
Commit SHA
   ↓
Merge Request
   ↓
Pipeline ID
   ↓
Docker Image
   ↓
ACR
   ↓
Helm Release
   ↓
AKS
```

---

# 4.44 GitLab Security Best Practices

```text
[ ] Protect main branch
[ ] Require Merge Requests
[ ] Require approvals
[ ] Protect production environment
[ ] Mask sensitive variables
[ ] Protect sensitive variables
[ ] Avoid secrets in repository
[ ] Use OIDC where supported
[ ] Scan dependencies
[ ] Scan Docker images
[ ] Enable SAST where applicable
[ ] Use least-privilege Azure permissions
[ ] Rotate legacy credentials
[ ] Review runner permissions
[ ] Audit access regularly
```

---

# 4.45 GitLab Day-2 Operational Problems

After the platform has been running for years, common issues include:

### 1. Runner Failure

```text
Pipeline → Pending
```

Check runner availability and tags.

### 2. Expired Azure Credential

```text
Pipeline
   |
   v
Azure Login Failed
```

Move toward OIDC/federated identity where possible.

### 3. Expired GitLab Token

Check:

```text
Project
 → Settings
 → Access Tokens
```

Replace the token and update dependent automation.

### 4. Docker Base Image Vulnerability

```text
Image Scan
   |
   v
Critical CVE
```

Update the base image and rebuild.

### 5. GitLab Pipeline Becomes Slow

Check:

```text
Runner capacity
Parallel jobs
Dependency downloads
Docker build cache
Unnecessary service builds
Large artifacts
Test execution time
```

### 6. Repository Becomes Very Large

Check:

```text
Large binaries
Build artifacts
Generated files
Logs
Dependencies
```

Use `.gitignore` and Git LFS where appropriate.

---

# 4.46 `.gitignore`

Example:

```text
node_modules/
.env
.venv/
__pycache__/
*.log
dist/
build/
coverage/
.idea/
.vscode/
```

Never commit:

```text
.env
private keys
passwords
production credentials
cloud secrets
```

---

# 4.47 Common Git Mistakes

### Mistake 1 — Direct Push to Main

Avoid:

```bash
git push origin main
```

Use Merge Requests.

### Mistake 2 — Committing Secrets

Bad:

```text
.env
passwords
API keys
private keys
```

### Mistake 3 — Using `latest`

Bad:

```text
image: product-service:latest
```

Prefer:

```text
image: product-service:<commit-sha>
```

### Mistake 4 — Force Push to Shared Branch

Avoid:

```bash
git push --force
```

If absolutely necessary, prefer:

```bash
git push --force-with-lease
```

and follow team policy.

### Mistake 5 — No Rollback Information

Every production release should be traceable to:

```text
Commit
Image
Helm Revision
Deployment
```

---

# 4.48 Important Git Commands

```bash
git status
git branch
git switch -c feature/test
git switch main
git pull --rebase
git fetch origin
git add .
git commit -m "message"
git push
git log --oneline --graph --decorate --all
git diff
git stash
git stash pop
git show <commit>
git revert <commit>
git reset --soft HEAD~1
git tag
git remote -v
```

---

# 4.49 Important GitLab/Azure Commands

```bash
az login

az account set \
  --subscription <subscription-id>

az aks get-credentials \
  --resource-group <resource-group> \
  --name <aks>

az acr repository list \
  --name <acr> \
  -o table

az acr repository show-tags \
  --name <acr> \
  --repository product-service \
  -o table

kubectl get nodes
kubectl get pods -n nexcart
kubectl get deploy -n nexcart
kubectl get svc -n nexcart

helm list -n nexcart
helm status nexcart -n nexcart
helm history nexcart -n nexcart
```

---

# 4.50 Senior Interview Answer

### Question

**"Explain how GitLab CI/CD is integrated with your NexCart project."**

### Answer

> We use GitLab as the source-control and CI/CD platform. Developers work on feature branches and create Merge Requests against the protected branch. The Merge Request triggers automated validation, testing, quality and security checks.
>
> Once the changes pass the required gates, the pipeline builds Docker images for the affected microservices. We tag the images using the Git commit SHA rather than using `latest`, which gives us immutable artifacts and deployment traceability.
>
> The images are pushed to Azure Container Registry. The deployment stage authenticates to Azure using an identity-based mechanism where possible, obtains AKS access, and deploys the application using Helm with environment-specific values.
>
> After deployment, we validate Pod health, readiness, Service endpoints and application smoke tests. For production releases, we use controlled promotion and maintain Helm release history so that we can quickly roll back if the new version introduces an issue.
>
> From an operational perspective, I also consider runner availability, credential expiry, image vulnerabilities, pipeline performance, branch protection, secret management and auditability. The important point is that the pipeline is not just a build mechanism; it provides a controlled path from source commit to a traceable production deployment.

---

# 4.51 Git & GitLab Interview Checklist

```text
[ ] Git vs GitLab
[ ] Working tree vs staging vs repository
[ ] Branching strategy
[ ] Merge vs rebase
[ ] Reset vs revert
[ ] Git stash
[ ] Merge conflicts
[ ] Protected branches
[ ] Merge Requests
[ ] CODEOWNERS
[ ] GitLab Runner
[ ] Runner tags
[ ] GitLab CI/CD
[ ] .gitlab-ci.yml
[ ] CI/CD variables
[ ] Protected variables
[ ] OIDC authentication
[ ] Docker build
[ ] ACR push
[ ] Helm deployment
[ ] Environment promotion
[ ] Immutable image tags
[ ] Security scanning
[ ] Pipeline optimization
[ ] Rollback
[ ] Auditability
[ ] Long-term credential rotation
```