---
title: "05-nexus-repository"
parent: "• 01-cicd-security"
grand_parent: "• Security_Documentation"
grand_grand_parent: "8. Security"
grand_grand_grand_parent: "NexCart"
grand_grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 5
---

# Nexus Repository

## THEORY

Nexus Repository is an artifact/package repository manager. In NexCart it is used as a private Docker image repository.

> Nexus is primarily a repository, not a vulnerability scanner.

## NEXCART IMPLEMENTATION

```text
GitLab -> Runner -> Docker Build -> Nexus Docker Hosted Repository
```

Repository:
```text
nexcart-docker
```

Registry:
```text
192.168.16.130:8082
```

### Login
```bash
docker login 192.168.16.130:8082
```

### Tag
```bash
docker tag nexcart/product-service:1.0 192.168.16.130:8082/nexcart/product-service:1.0
```

### Push
```bash
docker push 192.168.16.130:8082/nexcart/product-service:1.0
```

### CI variables
```text
NEXUS_USERNAME
NEXUS_PASSWORD
NEXUS_REGISTRY
```
Never store real values in Git.

## TROUBLESHOOTING

**Problem:** `username is empty`  
**Root Cause:** CI variables were protected/unavailable to the branch.  
**Solution:** review variable protection/environment and names.  
**Verification:** nexus-push succeeds without printing the password.

**Problem:** `http: server gave HTTP response to HTTPS client`  
**Root Cause:** Nexus registry is HTTP while Docker tried HTTPS.  
**Solution:** configure the registry as an insecure registry on the Docker host and restart Docker.  
**Verification:** `docker login 192.168.16.130:8082`.

## INTERVIEW
"I use Nexus as a private Docker repository. GitLab CI tags and pushes the seven NexCart images using credentials stored in GitLab CI variables."
