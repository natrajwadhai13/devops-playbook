---
title: "Docker AI "
parent: "• Docker"
grand_parent: "3. DevOps Core Tools"
nav_order: 3
---

# Docker AI Notes

## Table of Contents

1. Introduction to Docker AI
2. Docker Scout
3. Docker Hardened Images (DHI)
4. Ask Gordon (Docker AI Assistant)
5. Docker Model Runner
6. AI Models in Docker
7. Security Best Practices
8. Interview Questions
9. Hands-on Lab
10. Useful Commands
11. Real-world Examples

---

# 1. Introduction to Docker AI

## What is Docker AI?

Docker AI is a collection of AI-powered features integrated into Docker Desktop that help developers:

- Detect security vulnerabilities
- Generate Dockerfiles
- Explain Docker commands
- Optimize Docker images
- Run local AI models
- Improve container security
- Build applications faster using AI assistance

Instead of switching between ChatGPT, documentation, and Docker Hub, many tasks can now be performed directly inside Docker Desktop.

---

## Features

- Docker Scout
- Docker Hardened Images
- Ask Gordon
- Docker Model Runner
- AI Model Catalog
- AI-powered Dockerfile generation
- Vulnerability detection
- Base image recommendations

---

# 2. Docker Scout

## What is Docker Scout?

Docker Scout is Docker's built-in security analysis tool.

It scans Docker images and reports:

- CVEs (Common Vulnerabilities and Exposures)
- High-risk packages
- Outdated base images
- Security recommendations
- Supply chain information
- Image health

---

## Why Docker Scout?

Without Docker Scout:

```
Build Image

↓

Deploy

↓

Unknown vulnerabilities
```

With Docker Scout:

```
Build Image

↓

Scan Image

↓

Fix Vulnerabilities

↓

Deploy Secure Image
```

---

## How Docker Scout Works

```
Docker Image

↓

Extract Packages

↓

Compare CVE Database

↓

Generate Report

↓

Suggest Fixes
```

---

## Command

### List Images

```bash
docker images
```

Example

```bash
docker images | grep devboard
```

---

### Quick Security Overview

```bash
docker scout quickview local://devboard-fe:full
```

Output shows:

- Base image
- Vulnerabilities
- Recommendations
- Package summary

---

### Detailed Vulnerability Report

```bash
docker scout cves local://devboard-fe:full
```

Example Output

```
Critical : 0

High : 2

Medium : 8

Low : 14
```

---

### Recommendations

```bash
docker scout recommendations local://devboard-fe:full
```

Example

```
Current Image

↓

node:18

↓

Recommended

↓

node:20-alpine
```

---

### Organization Policy

```bash
docker scout quickview local://devboard-fe:full --org company-name
```

Checks organization security policy.

---

## Benefits

- Detect vulnerabilities
- Secure deployments
- Compliance
- Image optimization
- Easy security reporting

---

# 3. Docker Hardened Images (DHI)

## What are Hardened Images?

Docker Hardened Images are officially maintained secure container images.

They are built with:

- Minimal packages
- Reduced attack surface
- Continuous security updates
- Enterprise support
- Signed images

---

## Why use DHI?

Normal Image

```
Ubuntu

↓

300+ packages
```

Hardened Image

```
Minimal packages

↓

Smaller attack surface

↓

Fewer vulnerabilities
```

---

## Advantages

- More secure
- Smaller size
- Better performance
- Enterprise ready
- Continuously patched

---

## Supported Images

- Node
- Nginx
- Python
- Java
- Ubuntu
- Alpine
- Go
- .NET

---

## Example

Instead of

```dockerfile
FROM node:20
```

Use

```dockerfile
FROM <Docker Hardened Node Image>
```

---

## Best Practice

Always use:

- Latest version
- Official images
- Hardened images
- Minimal images

---

# 4. Ask Gordon

## What is Ask Gordon?

Ask Gordon is Docker's built-in AI assistant.

It understands Docker concepts and can answer Docker-related questions directly inside Docker Desktop.

---

## Example Questions

```
Explain Docker volumes.
```

```
Create Dockerfile for React Vite app.
```

```
Why is my container restarting?
```

```
How do I reduce image size?
```

```
Explain docker compose.
```

```
Generate Dockerfile for Node Express.
```

---

## Real Example

Prompt

```
Create Dockerfile for Node.js application using Vite frontend.
```

AI returns:

- Dockerfile
- Multi-stage build
- Nginx configuration
- Best practices

---

## Benefits

- Saves time
- Official Docker knowledge
- Reduces documentation search
- Great for beginners

---

# 5. Docker Model Runner

## What is Docker Model Runner?

Docker Model Runner allows developers to run Large Language Models (LLMs) locally using Docker Desktop.

No cloud API is required for supported local models.

---

## Why Use It?

- Offline AI
- Privacy
- Local execution
- No API cost
- Fast development

---

## Architecture

```
Docker Desktop

↓

Model Runner

↓

AI Model

↓

Prompt

↓

Response
```

---

## Check Status

```bash
docker model status
```

---

## Download Model

```bash
docker model pull ai/gemma4
```

---

## Run Model

```bash
docker model run ai/gemma4
```

---

## Running Models

```bash
docker model ps
```

---

## Remove Model

```bash
docker model rm ai/gemma4
```

---

## Stop Model

```bash
docker model stop ai/gemma4
```

---

## Example Conversation

Prompt

```
Create Dockerfile for Node.js project.
```

Prompt

```
Generate Kubernetes deployment.
```

Prompt

```
Explain Docker networking.
```

Prompt

```
Create CI/CD pipeline.
```

Prompt

```
Optimize this Dockerfile.
```

---

# 6. AI Models

Example Models

- Gemma
- Llama
- Mistral
- Phi
- Qwen (if supported)
- Other OCI-compatible models available through Docker

---

# 7. Security Best Practices

Always

- Use official images
- Use Docker Scout
- Use Hardened Images
- Update base images
- Remove unused packages
- Scan before deployment
- Never store secrets inside images
- Use non-root users
- Sign images when possible
- Keep Docker Desktop updated

---

# 8. Interview Questions

### What is Docker Scout?

Docker Scout is a security scanning tool that analyzes container images, detects vulnerabilities, and provides recommendations for more secure base images.

---

### What is Docker Hardened Image?

A Docker Hardened Image is an enterprise-grade, security-focused image with a minimal attack surface and regular security updates.

---

### What is Ask Gordon?

Ask Gordon is Docker's AI assistant that helps developers understand Docker concepts, troubleshoot issues, and generate Docker-related configurations.

---

### What is Docker Model Runner?

Docker Model Runner enables running supported AI models locally inside Docker Desktop without relying on external cloud APIs.

---

### Difference Between Docker Scout and Ask Gordon

| Docker Scout           | Ask Gordon               |
| ---------------------- | ------------------------ |
| Security scanning      | AI assistant             |
| Detects CVEs           | Answers Docker questions |
| Recommends base images | Generates Dockerfiles    |
| Analyzes images        | Explains commands        |

---

### Difference Between Docker Scout and Docker Model Runner

| Docker Scout          | Docker Model Runner   |
| --------------------- | --------------------- |
| Security tool         | Local AI runtime      |
| Scans images          | Runs LLMs             |
| Finds vulnerabilities | Executes AI models    |
| Improves security     | Provides AI responses |

---

# 9. Hands-on Lab

1. Build a Node.js image.
2. Scan it with `docker scout quickview`.
3. Review CVEs using `docker scout cves`.
4. Apply recommended base image updates.
5. Compare results before and after rebuilding.
6. Pull a local AI model with `docker model pull`.
7. Use the model to generate an optimized multi-stage Dockerfile.
8. Verify that the generated Dockerfile follows security best practices.

---

# 10. Common Commands

```bash
docker images
docker scout quickview local://IMAGE:TAG
docker scout cves local://IMAGE:TAG
docker scout recommendations local://IMAGE:TAG
docker model status
docker model pull ai/gemma4
docker model run ai/gemma4
docker model ps
docker model stop ai/gemma4
docker model rm ai/gemma4
```
