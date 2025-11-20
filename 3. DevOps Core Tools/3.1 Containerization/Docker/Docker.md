---
title: "• Docker"
parent: "3. DevOps Core Tools"
nav_order: 2
has_children: true
---


# Docker Overview

Here’s a concise **Docker Roadmap** covering:

1. **Docker Basics**
2. **Dockerfile**
3. **Docker Compose**

Each part is beginner to intermediate friendly and tailored for **DevOps engineers** aiming to integrate Docker in CI/CD workflows and cloud deployments.

---

## 🐳 1. Docker Basics – (Week 1)

### ✅ Concepts

* What is Docker? Why use containers?
* Image vs Container
* Docker vs Virtual Machine
* Docker Architecture: Client, Daemon, Registry, Images, Containers

### 🧪 Key Commands

```bash
docker --version
docker pull nginx
docker images
docker run -d -p 8080:80 nginx
docker ps
docker exec -it <container_id> bash
docker stop <container_id>
docker rm <container_id>
docker rmi <image_id>
```

---

## 🛠️ 2. Dockerfile – (Week 2)

### ✅ Purpose:

A `Dockerfile` is a script that contains instructions to build a Docker image.

### 📘 Basic Structure:

```Dockerfile
# Base image
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy remaining files
COPY . .

# Expose port and start the app
EXPOSE 3000
CMD ["npm", "start"]
```

### 🧪 Commands:

```bash
docker build -t my-node-app .
docker run -p 3000:3000 my-node-app
```

---

## 📦 3. Docker Compose – (Week 3)

### ✅ Purpose:

Manage **multi-container** Docker applications (e.g., web + DB).

### 📘 Example: `docker-compose.yml`

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - mongo

  mongo:
    image: mongo:4.4
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
```

### 🧪 Commands:

```bash
docker-compose up -d
docker-compose ps
docker-compose logs -f
docker-compose down
```

---

## 🔁 Common Use Cases for DevOps

| Use Case                   | Docker Component              |
| -------------------------- | ----------------------------- |
| Run Dev/Test App Locally   | `docker run`                  |
| Package App into Image     | `Dockerfile`                  |
| CI/CD Pipelines            | `docker build`, `docker push` |
| Deploy Multi-Service Stack | `docker-compose`              |
| Push to DockerHub/ECR      | `docker tag`, `docker push`   |

---

## 📚 Bonus: Best Practices

* Keep Docker images small (`alpine` base images)
* Use `.dockerignore` file (like `.gitignore`)
* Don't store secrets in Dockerfile
* Use **named volumes** for data persistence
* Version your Docker images (`:v1.0.1`)

---

## 🚀 Optional Projects

| Project                                   | Tools            |
| ----------------------------------------- | ---------------- |
| Create Dockerized Node.js app             | Dockerfile       |
| Web + Mongo App                           | Docker Compose   |
| Build + Push image to Docker Hub          | Docker CLI       |
| Deploy app on EC2 with Docker Compose     | EC2 + Docker     |
| Use Jenkins to build and deploy container | Jenkins + Docker |



