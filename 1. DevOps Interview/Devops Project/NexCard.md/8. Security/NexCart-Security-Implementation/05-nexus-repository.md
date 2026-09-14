---
title: "• Nexus Repository"
parent: "• Security_Implementation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 5
---


# 05 - Nexus Repository Implementation

## 1. Implementation Status

**Implemented:** Yes

**Server:** `jenkins-node`

**Purpose:** Private Docker image repository.

## 2. Architecture

```text
GitLab
   |
   v
GitLab Runner
   |
   v
Docker Build
   |
   v
NexCart Images
   |
   v
Nexus Docker Hosted Repository
   |
   v
Pull/Deploy Images
```

## 3. Nexus Installation on Ubuntu

Nexus was deployed as a Docker container.

Pull image:

```bash
docker pull sonatype/nexus3:latest
```

Create persistent volume:

```bash
docker volume create nexus-data
```

Start Nexus:

```bash
docker run -d \
  --name nexus-1 \
  --restart unless-stopped \
  -p 8081:8081 \
  -p 8082:8082 \
  -v nexus-data:/nexus-data \
  sonatype/nexus3:latest
```

Verify:

```bash
docker ps
```

Expected port mapping:

```text
8081 -> Nexus Web UI
8082 -> Docker registry connector
```

## 4. Nexus URL

On the NexCart lab VM:

```text
http://192.168.16.130:8081
```

Open this URL from a machine that can reach `jenkins-node`.

## 5. Initial Admin Password

Retrieve from the container:

```bash
docker exec nexus-1 cat /nexus-data/admin.password
```

Use the returned value for the initial `admin` login.

After login, complete the Nexus setup.

## 6. Create Docker Hosted Repository

In Nexus:

```text
Repositories
 -> Create repository
 -> docker (hosted)
```

Repository name:

```text
nexcart-docker
```

Docker connector:

```text
HTTP
8082
```

Deployment policy used during practice:

```text
Allow redeploy
```

## 7. Verify Registry Endpoint

From `jenkins-node`:

```bash
curl http://localhost:8082/v2/
```

The tested result was an authentication response similar to:

```json
{
  "errors": [
    {
      "code": "UNAUTHORIZED",
      "message": "access to the requested resource is not authorized"
    }
  ]
}
```

This is useful: it proves the registry endpoint is reachable and is asking for authentication.

## 8. Docker HTTP Registry Configuration

Because the practice Nexus Docker connector uses HTTP, Docker initially returned:

```text
http: server gave HTTP response to HTTPS client
```

Edit:

```bash
sudo nano /etc/docker/daemon.json
```

Configuration used:

```json
{
  "insecure-registries": [
    "192.168.16.130:8082"
  ]
}
```

Restart Docker:

```bash
sudo systemctl restart docker
```

Verify:

```bash
sudo systemctl status docker
```

## 9. Docker Login

```bash
docker login 192.168.16.130:8082
```

Enter the Nexus username/password.

Successful login confirms Docker can authenticate to the registry.

## 10. Manual Push Test

Example:

```bash
docker tag \
  nexcart/product-service:1.0 \
  192.168.16.130:8082/nexcart/product-service:1.0
```

Push:

```bash
docker push \
  192.168.16.130:8082/nexcart/product-service:1.0
```

The product-service image was successfully pushed and became visible in Nexus.

## 11. GitLab Variables

Create these in:

```text
GitLab
 -> Project
 -> Settings
 -> CI/CD
 -> Variables
```

Required names:

```text
NEXUS_USERNAME
NEXUS_PASSWORD
NEXUS_REGISTRY
```

Recommended values for this lab:

```text
NEXUS_USERNAME = Nexus admin/service account username
NEXUS_PASSWORD = Nexus password
NEXUS_REGISTRY = 192.168.16.130:8082
```

Do not put the real password in `.gitlab-ci.yml`.

Use GitLab variable protection/masking according to the branch/environment design.

## 12. GitLab CI Implementation

The job belongs in:

```text
ci/nexus-push.yml
```

Current implementation:

```yaml
nexus-push:
  stage: publish
  tags:
    - nexcart-runner
  script:
    - echo "$NEXUS_PASSWORD" | docker login "$NEXUS_REGISTRY" -u "$NEXUS_USERNAME" --password-stdin
    - |
      for image in \
        api-gateway \
        frontend \
        notification-service \
        order-service \
        payment-service \
        product-service \
        user-service
      do
        echo "Publishing $image:1.0 to Nexus..."

        docker tag \
          "nexcart/$image:1.0" \
          "$NEXUS_REGISTRY/nexcart/$image:1.0"

        docker push \
          "$NEXUS_REGISTRY/nexcart/$image:1.0"
      done
    - docker logout "$NEXUS_REGISTRY"
```

## 13. Main `.gitlab-ci.yml`

The Nexus job is enabled using:

```yaml
include:
  - local: 'ci/nexus-push.yml'
```

The main file contains the stages:

```yaml
stages:
  - validate
  - test
  - build
  - publish
```

## 14. Pipeline Dependency

The Nexus job expects the seven local Docker images to already exist on the runner:

```text
nexcart/api-gateway:1.0
nexcart/frontend:1.0
nexcart/notification-service:1.0
nexcart/order-service:1.0
nexcart/payment-service:1.0
nexcart/product-service:1.0
nexcart/user-service:1.0
```

Therefore:

```text
Docker Build
     |
     v
Nexus Push
```

must happen on the same Docker-enabled runner/workspace/host unless images are explicitly transferred using artifacts or a registry.

## 15. Verification

On runner:

```bash
docker images | grep nexcart
```

In GitLab:

```text
Pipeline
 -> publish
 -> nexus-push
```

Expected:

```text
nexus-push = Passed
```

In Nexus UI:

```text
http://192.168.16.130:8081
 -> Repositories
 -> nexcart-docker
```

Verify pushed images/tags.

## 16. Actual Errors and Fixes

### Error 1

```text
http: server gave HTTP response to HTTPS client
```

**Root Cause:** Docker expected HTTPS but Nexus registry connector was HTTP.

**Solution:**

```json
{
  "insecure-registries": [
    "192.168.16.130:8082"
  ]
}
```

Then:

```bash
sudo systemctl restart docker
```

**Verification:**

```bash
docker login 192.168.16.130:8082
```

### Error 2

```text
username is empty
```

**Root Cause:** GitLab Nexus variables were protected/unavailable to the branch.

**Solution:** Review:

```text
NEXUS_USERNAME
NEXUS_PASSWORD
NEXUS_REGISTRY
```

and their protected/environment configuration.

**Verification:** `nexus-push` succeeds without printing the password.

## 17. Reproduce From Zero

```text
1. Install Docker
2. Pull sonatype/nexus3
3. Create nexus-data volume
4. Start Nexus container
5. Open port 8081
6. Complete Nexus admin setup
7. Create docker hosted repository
8. Configure port 8082
9. Configure Docker insecure registry
10. Restart Docker
11. Test docker login
12. Manually tag/push one image
13. Create GitLab variables
14. Add nexus-push.yml
15. Include it from .gitlab-ci.yml
16. Build all seven images
17. Run nexus-push
18. Verify images in Nexus
```

## 18. Project Statement

> "I deployed Nexus Repository on the Jenkins-node VM as a private Docker registry. GitLab CI builds the NexCart images and the Nexus publish job authenticates using GitLab CI variables, tags the images with the Nexus registry address and pushes them to the hosted repository."
