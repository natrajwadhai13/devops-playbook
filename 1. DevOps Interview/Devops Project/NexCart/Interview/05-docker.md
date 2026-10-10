---
title: "05-docker"
parent: "3. NexCard Interview"
grand_parent: "NexCart"
grand_grand_parent: "• Devops Project"
grand_grand_grand_parent: "1. DevOps"
nav_order: 5
---

# 🐳 05 — Docker

## 5.1 Docker Overview

Docker is used to package NexCart microservices into portable, consistent and deployable containers.

The basic flow is:

```text
Source Code
    |
    v
Dockerfile
    |
    v
Docker Build
    |
    v
Docker Image
    |
    v
Azure Container Registry
    |
    v
AKS
    |
    v
Container
```

For NexCart, each microservice is packaged independently.

```text
product-service       → Docker Image
order-service         → Docker Image
payment-service       → Docker Image
notification-service  → Docker Image
```

---

# 5.2 Why Docker Is Used

Without containers, application deployment depends heavily on the server environment.

```text
Application
   +
OS Dependencies
   +
Runtime
   +
Libraries
   +
Configuration
```

Docker packages the application and its runtime dependencies into an image.

Benefits:

- Consistent environment
- Portable deployment
- Faster application startup
- Easier CI/CD
- Versioned artifacts
- Easier rollback
- Isolation between services
- Works consistently across developer, test and production environments

---

# 5.3 Docker Architecture

```text
                    Docker Client
                         |
                         v
                  Docker Engine
                         |
          +--------------+--------------+
          |              |              |
       Images         Containers      Volumes
          |
          v
       Registry
          |
          v
        ACR
```

Important components:

```text
Docker Client
Docker Engine
Docker Image
Docker Container
Dockerfile
Docker Registry
Docker Volume
Docker Network
```

---

# 5.4 Docker Image vs Container

## Image

An image is an immutable template used to create containers.

Example:

```text
product-service:8f32a91
```

## Container

A container is a running instance of an image.

```text
Image
  |
  +----> Container 1
  |
  +----> Container 2
```

Multiple containers can be created from the same image.

---

# 5.5 Dockerfile

A Dockerfile defines how the application image is built.

Example Node.js service:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY . .

EXPOSE 8082

USER node

CMD ["node", "server.js"]
```

Important Dockerfile instructions:

```text
FROM
WORKDIR
COPY
ADD
RUN
ENV
ARG
EXPOSE
USER
ENTRYPOINT
CMD
HEALTHCHECK
```

---

# 5.6 Dockerfile Instruction Order

A commonly used pattern is:

```text
FROM
    ↓
WORKDIR
    ↓
COPY dependency files
    ↓
RUN dependency installation
    ↓
COPY application source
    ↓
EXPOSE
    ↓
USER
    ↓
ENTRYPOINT / CMD
```

The order matters because Docker builds images using layers.

---

# 5.7 FROM

Defines the base image.

```dockerfile
FROM node:22-alpine
```

For Python:

```dockerfile
FROM python:3.14-slim
```

### Production Considerations

Avoid blindly using:

```dockerfile
FROM node:latest
```

Prefer controlled versions:

```dockerfile
FROM node:22-alpine
```

or a specific digest when stronger reproducibility is required.

Base images should be regularly scanned and updated for security vulnerabilities.

---

# 5.8 WORKDIR

Defines the working directory.

```dockerfile
WORKDIR /app
```

Instead of:

```dockerfile
RUN cd /app
```

Use `WORKDIR` because it applies to subsequent instructions and container execution.

---

# 5.9 COPY

Copies files from the build context into the image.

```dockerfile
COPY package*.json ./
```

Then:

```dockerfile
COPY . .
```

For security and performance, do not blindly copy unnecessary files.

Use `.dockerignore`.

---

# 5.10 ADD vs COPY

### COPY

Used for normal file copying.

```dockerfile
COPY . .
```

### ADD

Provides additional functionality such as archive extraction.

```dockerfile
ADD application.tar.gz /app/
```

For most application Dockerfiles:

```text
Prefer COPY
```

Use `ADD` only when its additional behavior is actually required.

---

# 5.11 RUN

Executes commands during image build.

Example:

```dockerfile
RUN npm ci --omit=dev
```

Another example:

```dockerfile
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
```

Every `RUN` can create an image layer.

Combine related commands when appropriate to reduce unnecessary layers and image size.

---

# 5.12 CMD vs ENTRYPOINT

## CMD

Provides default command/arguments.

```dockerfile
CMD ["node", "server.js"]
```

It can be overridden when starting the container.

## ENTRYPOINT

Defines the primary executable.

```dockerfile
ENTRYPOINT ["node"]
```

Then:

```dockerfile
CMD ["server.js"]
```

Conceptually:

```text
ENTRYPOINT = executable
CMD        = default arguments
```

For application containers, either pattern can be used depending on how much runtime override is required.

---

# 5.13 EXPOSE

Example:

```dockerfile
EXPOSE 8082
```

`EXPOSE` documents the intended container port.

It does not publish the port by itself.

To publish a port:

```bash
docker run -p 8082:8082 product-service
```

Meaning:

```text
Host Port       Container Port
   8082    →        8082
```

---

# 5.14 ENV vs ARG

## ENV

Available during image build and container runtime.

```dockerfile
ENV PORT=8082
```

## ARG

Primarily available during image build.

```dockerfile
ARG APP_VERSION
```

Build:

```bash
docker build \
  --build-arg APP_VERSION=1.0.0 \
  -t product-service:1.0.0 .
```

Do not use `ARG` or `ENV` as a secure mechanism for storing secrets.

---

# 5.15 Docker Build

Build an image:

```bash
docker build \
  -t product-service:1.0.0 .
```

Using Git commit SHA:

```bash
docker build \
  -t product-service:8f32a91 .
```

Check:

```bash
docker images
```

---

# 5.16 Run Container

```bash
docker run \
  --name product-service \
  -p 8082:8082 \
  product-service:1.0.0
```

Check:

```bash
docker ps
```

Check all containers:

```bash
docker ps -a
```

---

# 5.17 Test Container

```bash
curl http://localhost:8082/health
```

Expected:

```json
{
  "service": "product-service",
  "status": "UP"
}
```

From PowerShell:

```powershell
Invoke-RestMethod http://localhost:8082/health
```

---

# 5.18 Container Logs

```bash
docker logs product-service
```

Follow logs:

```bash
docker logs -f product-service
```

Last 100 lines:

```bash
docker logs --tail 100 product-service
```

Logs are one of the first troubleshooting points when a container exits or fails health checks.

---

# 5.19 Execute Inside Container

```bash
docker exec -it product-service sh
```

Then:

```bash
pwd
ls
env
```

For images containing Bash:

```bash
docker exec -it product-service bash
```

Do not assume every minimal image contains Bash.

---

# 5.20 Stop and Remove Container

Stop:

```bash
docker stop product-service
```

Remove:

```bash
docker rm product-service
```

Force remove:

```bash
docker rm -f product-service
```

Remove image:

```bash
docker rmi product-service:1.0.0
```

---

# 5.21 Docker Image Layers

Docker images are composed of layers.

Example:

```text
Application Image
       |
       +-- Application Layer
       |
       +-- Dependencies Layer
       |
       +-- Runtime Layer
       |
       +-- Base Image Layer
```

Layers improve build performance because unchanged layers can be reused.

---

# 5.22 Docker Build Cache

Consider:

```dockerfile
COPY package*.json ./
RUN npm ci

COPY . .
```

If only application source changes:

```text
package.json unchanged
       |
       v
npm ci layer can be cached
```

This is faster than:

```dockerfile
COPY . .
RUN npm ci
```

because every source-code change can invalidate the dependency-installation layer.

---

# 5.23 Dockerfile Optimization

Bad:

```dockerfile
COPY . .
RUN npm install
```

Better:

```dockerfile
COPY package*.json ./
RUN npm ci --omit=dev

COPY . .
```

Benefits:

- Better layer caching
- Faster builds
- Smaller image
- More predictable dependencies

---

# 5.24 `.dockerignore`

Example:

```text
node_modules/
.git/
.gitlab/
.env
*.log
coverage/
dist/
build/
.vscode/
.idea/
README.md
```

For Python:

```text
.venv/
__pycache__/
*.pyc
.pytest_cache/
.git/
.env
```

The purpose is to keep unnecessary files out of the Docker build context.

---

# 5.25 Multi-Stage Docker Build

Multi-stage builds are useful when compilation/build dependencies are not required in the runtime image.

Example:

```dockerfile
FROM node:22-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build


FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80
```

Architecture:

```text
Builder Image
    |
    | Compile / Build
    v
Application Artifact
    |
    v
Runtime Image
```

The final image does not need the complete build environment.

---

# 5.26 Why Multi-Stage Builds Matter

Benefits:

```text
Smaller image
Less attack surface
Fewer packages
Faster deployment
Lower storage usage
Better security
```

For production workloads, keep runtime images minimal.

---

# 5.27 Node.js Dockerfile

Example:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY . .

ENV NODE_ENV=production

EXPOSE 8082

USER node

CMD ["node", "server.js"]
```

Important points:

```text
Pinned runtime
Dependency installation
Production mode
Non-root user
Minimal base image
Correct port
Explicit startup command
```

---

# 5.28 Python FastAPI Dockerfile

Example:

```dockerfile
FROM python:3.14-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8084

USER 10001

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8084"]
```

The exact module path should match the application structure.

---

# 5.29 Non-Root Containers

Running applications as root increases the impact of container compromise.

Avoid:

```dockerfile
USER root
```

Prefer:

```dockerfile
USER node
```

or a dedicated non-root UID.

Example:

```dockerfile
RUN addgroup -S appgroup \
    && adduser -S appuser -G appgroup

USER appuser
```

The application must have the required permissions to read/write its directories.

---

# 5.30 Read-Only Filesystem

Where possible, production containers can use a read-only root filesystem.

Kubernetes example:

```yaml
securityContext:
  readOnlyRootFilesystem: true
```

If the application needs temporary files, provide a writable temporary volume instead of making the entire filesystem writable.

---

# 5.31 Docker Healthcheck

A Docker image can define a health check.

Example:

```dockerfile
HEALTHCHECK \
  --interval=30s \
  --timeout=5s \
  --retries=3 \
  CMD wget --spider -q http://localhost:8082/health || exit 1
```

However, when running the application in Kubernetes, Kubernetes liveness/readiness/startup probes are usually the primary health mechanism.

---

# 5.32 Docker Networking

Containers can communicate through Docker networks.

Create:

```bash
docker network create nexcart-network
```

Run:

```bash
docker run -d \
  --name product-service \
  --network nexcart-network \
  product-service:1.0.0
```

Another container on the same network can access it using the container/service name rather than relying on a hardcoded IP.

Example:

```text
http://product-service:8082
```

---

# 5.33 Docker Compose

For local development, multiple services can be started using Docker Compose.

Concept:

```text
Docker Compose
      |
      +---- Product Service
      |
      +---- Order Service
      |
      +---- Payment Service
      |
      +---- Notification Service
```

Example:

```yaml
services:

  product-service:
    build: ./product-service
    ports:
      - "8082:8082"

  order-service:
    build: ./order-service
    ports:
      - "8083:8083"

  payment-service:
    build: ./payment-service
    ports:
      - "8084:8084"

  notification-service:
    build: ./notification-service
    ports:
      - "8085:8085"
```

Run:

```bash
docker compose up -d
```

Check:

```bash
docker compose ps
```

Logs:

```bash
docker compose logs -f
```

Stop:

```bash
docker compose down
```

---

# 5.34 Docker Compose vs Kubernetes

| Docker Compose | Kubernetes |
|---|---|
| Primarily local/dev | Production orchestration |
| Simple configuration | Advanced orchestration |
| Limited scaling | Automated scaling |
| Simple networking | Service discovery/networking |
| Local testing | Production workloads |
| Small environments | Large distributed systems |

For NexCart:

```text
Local Development
      ↓
Docker / Docker Compose
      ↓
CI/CD
      ↓
ACR
      ↓
AKS
```

---

# 5.35 Docker Registry

A Docker registry stores images.

Examples:

```text
Docker Hub
Azure Container Registry
GitLab Container Registry
Amazon ECR
Google Artifact Registry
```

For NexCart:

```text
GitLab CI
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

# 5.36 Login to ACR

```bash
az login
```

Authenticate:

```bash
az acr login --name <acr-name>
```

Then:

```bash
docker push \
  <acr-name>.azurecr.io/product-service:8f32a91
```

---

# 5.37 ACR Image Naming

Recommended:

```text
<acr>.azurecr.io/product-service:<git-sha>
```

Example:

```text
nexcartacr.azurecr.io/product-service:8f32a91
```

Other services:

```text
nexcartacr.azurecr.io/order-service:8f32a91
nexcartacr.azurecr.io/payment-service:8f32a91
nexcartacr.azurecr.io/notification-service:8f32a91
```

---

# 5.38 Immutable Image Tags

Avoid:

```text
product-service:latest
```

Prefer:

```text
product-service:8f32a91
```

or:

```text
product-service:1.4.2
```

Best practice is to ensure the production deployment can always identify the exact image digest or immutable version.

---

# 5.39 Image Digest

Tags can be moved to different images.

A digest uniquely identifies image content.

Example:

```text
product-service@sha256:<digest>
```

For high-control production environments, digest-based deployment provides stronger immutability.

---

# 5.40 Docker Security

Important Docker security practices:

```text
[ ] Use trusted base images
[ ] Keep base images updated
[ ] Scan images
[ ] Run as non-root
[ ] Minimize installed packages
[ ] Use multi-stage builds
[ ] Do not store secrets in images
[ ] Use .dockerignore
[ ] Pin dependencies where practical
[ ] Use immutable image versions
[ ] Avoid unnecessary Linux capabilities
[ ] Use read-only filesystem where possible
```

---

# 5.41 Secrets in Docker

Never do this:

```dockerfile
ENV DB_PASSWORD=myPassword123
```

Never:

```dockerfile
COPY .env /app/.env
```

Secrets can become part of image layers and may remain recoverable.

Use runtime secret management instead:

```text
Azure Key Vault
      |
      v
AKS
      |
      v
Pod
```

---

# 5.42 Docker Image Scanning

Use tools such as Trivy to identify vulnerabilities.

Example:

```bash
trivy image product-service:1.0.0
```

For ACR:

```bash
trivy image <acr>.azurecr.io/product-service:8f32a91
```

Scan for:

```text
OS vulnerabilities
Application dependencies
Known CVEs
Misconfigurations
Secrets where supported
```

A production pipeline should define severity thresholds rather than blindly failing on every finding.

---

# 5.43 Dockerfile Security Issue

Example:

```dockerfile
FROM ubuntu:latest

RUN apt-get update
RUN apt-get install -y curl vim wget

COPY . .

USER root

CMD ["./start.sh"]
```

Problems:

```text
Uncontrolled base version
Unnecessary packages
Potentially large image
Root execution
Poor layer optimization
Potential security exposure
```

A senior engineer should challenge the Dockerfile rather than simply make it work.

---

# 5.44 Docker Resource Limits

Docker can limit resources.

Example:

```bash
docker run \
  --memory=512m \
  --cpus=0.5 \
  product-service:1.0.0
```

In Kubernetes, resource requests and limits are preferred for production orchestration.

---

# 5.45 Container Restart Policy

Example:

```bash
docker run \
  --restart=unless-stopped \
  product-service:1.0.0
```

Possible policies:

```text
no
always
on-failure
unless-stopped
```

In Kubernetes, Pod restart behavior is managed by Kubernetes controllers rather than relying on Docker restart policies.

---

# 5.46 Docker Troubleshooting Flow

When a container is not working:

```text
Container Problem
       |
       v
docker ps -a
       |
       v
docker logs
       |
       v
docker inspect
       |
       v
Check Environment
       |
       v
Check Port
       |
       v
Check Network
       |
       v
Check Application
       |
       v
Check Dependencies
```

Commands:

```bash
docker ps -a
docker logs <container>
docker inspect <container>
docker exec -it <container> sh
docker port <container>
docker network inspect <network>
```

---

# 5.47 Container Exits Immediately

Check:

```bash
docker ps -a
docker logs <container>
```

Common causes:

```text
Application startup failure
Wrong CMD
Missing environment variable
Missing dependency
Incorrect working directory
Port/configuration issue
Application process exits
```

Important concept:

```text
Container lifetime = Main process lifetime
```

If the main process exits, the container stops.

---

# 5.48 Container Starts but Application Is Unreachable

Check:

```text
1. Application listening address
2. Container port
3. Host port mapping
4. Docker network
5. Firewall
6. Application logs
```

Common mistake:

Application listens only on:

```text
127.0.0.1
```

inside the container.

For containerized applications, the application generally needs to listen on:

```text
0.0.0.0
```

Example FastAPI:

```bash
uvicorn app.main:app \
  --host 0.0.0.0 \
  --port 8084
```

---

# 5.49 Container Cannot Connect to Another Service

Do not use:

```text
localhost
```

to reach another container.

Inside a container:

```text
localhost = current container
```

Use the Docker service/container DNS name on the shared network.

Example:

```text
http://product-service:8082
```

instead of:

```text
http://localhost:8082
```

---

# 5.50 Docker Disk Usage

Check:

```bash
docker system df
```

Remove unused resources carefully:

```bash
docker system prune
```

More aggressive cleanup:

```bash
docker system prune -a
```

Do not run aggressive cleanup blindly on shared build servers.

Check:

```bash
docker images
docker ps -a
docker volume ls
docker network ls
```

---

# 5.51 Docker Volumes

Containers are ephemeral.

If application data must survive container recreation, use persistent storage.

```text
Container
    |
    v
Volume
```

Example:

```bash
docker volume create nexcart-data
```

Mount:

```bash
docker run \
  -v nexcart-data:/data \
  product-service:1.0.0
```

For Kubernetes production workloads, use PersistentVolumes/PersistentVolumeClaims and an appropriate storage backend.

---

# 5.52 Docker Logging

Do not depend only on files inside containers.

Prefer application logs to stdout/stderr:

```text
Application
    |
    +---- stdout
    |
    +---- stderr
    |
    v
Container Runtime
    |
    v
Log Collector
```

This integrates naturally with Kubernetes and centralized logging systems.

---

# 5.53 Graceful Shutdown

Containers may receive termination signals during deployment.

Applications should:

```text
Receive SIGTERM
      |
      v
Stop accepting new requests
      |
      v
Finish active requests
      |
      v
Close connections
      |
      v
Exit
```

This becomes especially important when Docker containers are later deployed into Kubernetes.

---

# 5.54 Docker and Kubernetes Relationship

Docker builds the artifact.

Kubernetes manages the workload.

```text
Docker
  |
  +---- Build
  +---- Image
  +---- Package
  |
  v
Container Registry
  |
  v
Kubernetes / AKS
  |
  +---- Scheduling
  +---- Scaling
  +---- Service Discovery
  +---- Self-Healing
  +---- Rolling Updates
```

Kubernetes does not replace the need for container images.

---

# 5.55 NexCart End-to-End Docker Flow

```text
Developer
    |
    v
GitLab
    |
    v
Dockerfile
    |
    v
GitLab Runner
    |
    v
docker build
    |
    v
Security Scan
    |
    v
ACR
    |
    v
Helm
    |
    v
AKS
    |
    v
Pods
    |
    v
Microservices
```

Example:

```text
product-service source
        |
        v
product-service Dockerfile
        |
        v
product-service:8f32a91
        |
        v
nexcartacr.azurecr.io/product-service:8f32a91
        |
        v
AKS Deployment
        |
        v
Product Service Pods
```

---

# 5.56 Docker Build Failure Troubleshooting

### Scenario

GitLab pipeline fails during Docker build.

### What to Check

```text
Dockerfile syntax
Base image availability
Dependency installation
Build context
File paths
Package registry
Network connectivity
Runner resources
Docker daemon
```

### Commands

```bash
docker build --no-cache -t product-service:test .
```

Check:

```bash
docker history product-service:test
```

### Root Cause Examples

```text
Wrong COPY path
Missing package.json
Dependency installation failure
Base image unavailable
Insufficient disk
Network timeout
```

---

# 5.57 Docker Image Too Large

Check:

```bash
docker images
docker history <image>
```

Typical causes:

```text
Large base image
Build tools included in runtime
Unnecessary packages
node_modules/dev dependencies
Copied source/build files
Package caches
Logs
```

Solutions:

```text
Multi-stage build
Smaller base image
Production dependencies only
.dockerignore
Clean package caches
Remove unnecessary packages
```

---

# 5.58 Docker Container Memory Problem

Symptoms:

```text
Container becomes slow
Container gets killed
Application crashes
OOMKilled in Kubernetes
```

Check:

```bash
docker stats
```

Investigate:

```text
Memory usage
Application memory leak
Heap configuration
Traffic pattern
Container limit
Dependency behavior
```

Do not simply increase memory without understanding why memory usage increased.

---

# 5.59 Docker Image Vulnerability Appears After Deployment

Scenario:

```text
Production image
      |
      v
New CVE discovered
      |
      v
Security alert
```

Response:

```text
1. Identify vulnerable package
2. Identify affected image
3. Check severity/exploitability
4. Update base image/dependency
5. Rebuild image
6. Run tests
7. Scan again
8. Deploy new image
9. Retire vulnerable image
```

This is a normal Day-2 container management activity.

---

# 5.60 Long-Term Docker Management

After 2–3 years, common issues include:

```text
Base image EOL
Node/Python runtime EOL
OS package vulnerabilities
Old Dockerfile syntax
Large image size
Unused images
Registry storage growth
Dependency vulnerabilities
Build cache problems
Certificate issues in private registries
Registry authentication changes
CI runner compatibility
Container startup changes
```

A mature team should periodically review:

```text
Base Images
Runtime Versions
Dependencies
Security Scans
Image Size
Build Time
Registry Retention
Dockerfile Standards
```

---

# 5.61 Image Retention

ACR can accumulate many image versions.

Example:

```text
product-service:commit-1
product-service:commit-2
product-service:commit-3
...
product-service:commit-10000
```

Without retention policies:

```text
Registry Storage
       |
       v
Cost increases
```

Use retention/cleanup policies while protecting versions required for rollback and compliance.

---

# 5.62 Production Docker Checklist

```text
[ ] Minimal trusted base image
[ ] Controlled runtime version
[ ] Multi-stage build where appropriate
[ ] Efficient layer ordering
[ ] .dockerignore
[ ] Production-only dependencies
[ ] Non-root user
[ ] No secrets in image
[ ] No unnecessary packages
[ ] Immutable image tag
[ ] Image digest available
[ ] Vulnerability scanning
[ ] Dependency scanning
[ ] Health endpoint
[ ] Graceful shutdown
[ ] stdout/stderr logging
[ ] Resource requirements understood
[ ] Registry retention policy
[ ] Base image update process
[ ] Rollback image available
```

---

# 5.63 Important Docker Commands

## Images

```bash
docker images
docker pull <image>
docker build -t <image>:<tag> .
docker rmi <image>:<tag>
docker history <image>
docker inspect <image>
```

## Containers

```bash
docker ps
docker ps -a
docker run <image>
docker start <container>
docker stop <container>
docker restart <container>
docker rm <container>
docker exec -it <container> sh
docker inspect <container>
```

## Logs

```bash
docker logs <container>
docker logs -f <container>
docker logs --tail 100 <container>
```

## Network

```bash
docker network ls
docker network create <network>
docker network inspect <network>
```

## Storage

```bash
docker volume ls
docker volume create <volume>
docker volume inspect <volume>
```

## Cleanup

```bash
docker system df
docker system prune
```

---

# 5.64 Senior Interview Question — Dockerfile Design

### Question

**How would you design a production Dockerfile for a microservice?**

### Answer

> I start with a trusted and controlled runtime image and avoid unnecessary packages. I structure the Dockerfile to maximize layer caching, install only production dependencies, and use a multi-stage build when compilation tools are not required at runtime. I run the application as a non-root user, avoid embedding secrets, expose only the required port, and use an explicit startup command.
>
> I also scan the final image for vulnerabilities, keep the base image and dependencies patched, use immutable image tags, and ensure the image can be traced back to the Git commit that produced it.

---

# 5.65 Senior Interview Question — Why Not `latest`?

### Answer

> I avoid `latest` in production because the tag is mutable. Two deployments using `latest` could potentially run different image contents. I prefer immutable Git SHA or version tags and, for stronger reproducibility, image digests. This makes deployment auditing and rollback predictable.

---

# 5.66 Senior Interview Question — Docker Container Is Running but Application Is Not Accessible

### Approach

```text
Container running?
       |
       v
Application process running?
       |
       v
Correct listening port?
       |
       v
Listening on 0.0.0.0?
       |
       v
Correct host/container port mapping?
       |
       v
Network connectivity?
       |
       v
Application logs?
```

Commands:

```bash
docker ps
docker logs <container>
docker port <container>
docker exec -it <container> sh
docker inspect <container>
```

---

# 5.67 Senior Interview Question — Image Is 1.5 GB. What Will You Do?

### Answer

> I would first inspect the image layers rather than immediately changing the base image. I would use `docker history` to identify the largest layers, check whether build dependencies are included in the runtime image, inspect unnecessary packages and caches, and review whether development dependencies are being copied. I would then use a multi-stage build, a smaller trusted runtime image, production-only dependencies and a proper `.dockerignore`. I would validate that the smaller image still has the required runtime functionality and security posture.

---

# 5.68 Senior Interview Question — How Do You Secure Docker Images?

### Answer

> I use trusted and maintained base images, keep runtime versions and dependencies patched, scan images for CVEs, avoid running as root, minimize installed packages, use multi-stage builds, prevent secrets from entering image layers, and use immutable image versions. I also integrate image scanning into CI/CD so vulnerable images are detected before production deployment.

---

# 5.69 Senior Interview Question — Docker vs Kubernetes

### Answer

> Docker is primarily responsible for building and packaging applications as container images and running containers. Kubernetes is the orchestration platform responsible for scheduling, scaling, networking, service discovery, self-healing and rolling deployments of containerized workloads. In NexCart, Docker creates the microservice images, ACR stores them, and AKS runs and manages those workloads.

---

# 5.70 Senior Docker Interview Checklist

```text
[ ] Docker architecture
[ ] Image vs container
[ ] Dockerfile
[ ] FROM
[ ] WORKDIR
[ ] COPY
[ ] ADD
[ ] RUN
[ ] ENV
[ ] ARG
[ ] EXPOSE
[ ] CMD
[ ] ENTRYPOINT
[ ] Docker layers
[ ] Build cache
[ ] Multi-stage builds
[ ] .dockerignore
[ ] Non-root containers
[ ] Docker networking
[ ] Docker volumes
[ ] Docker Compose
[ ] ACR
[ ] Immutable tags
[ ] Image digest
[ ] Image scanning
[ ] Container troubleshooting
[ ] Container resource usage
[ ] Graceful shutdown
[ ] Registry cleanup
[ ] Base image lifecycle
[ ] Security
[ ] Production rollback
```

# 5.71 Key Senior-Level Takeaway

```text
Docker is not just:

Dockerfile → docker build → docker run

A production Docker strategy is:

Source Code
     ↓
Reproducible Dockerfile
     ↓
Optimized Image
     ↓
Security Scan
     ↓
Immutable Tag/Digest
     ↓
Azure Container Registry
     ↓
AKS
     ↓
Monitoring
     ↓
Patch / Upgrade / Rollback
```

The senior DevOps responsibility is to make container images **secure, reproducible, small, traceable, maintainable and production-ready**, not merely to make the container start successfully.