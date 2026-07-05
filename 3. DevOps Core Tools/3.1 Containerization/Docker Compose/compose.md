---
title: "Docker Compose"
parent: "• Docker"
grand_parent: "3. DevOps Core Tools"
nav_order: 2
---

# Docker Compose

- docker-compose up -d
- docker-compose.yml structure

---

# Table of Contents

1. Introduction
2. Why Docker Compose?
3. Architecture
4. docker-compose.yml Structure
5. Installation
6. Basic Commands
7. Services
8. Networks
9. Volumes
10. Environment Variables
11. Build vs Image
12. Depends On
13. Restart Policies
14. Health Checks
15. Scaling
16. Profiles
17. Multi-file Compose
18. Best Practices
19. Real-world Examples
20. Troubleshooting
21. Interview Questions

---

# 1. What is Docker Compose?

Docker Compose is a tool used to **define and manage multi-container Docker applications** using a single YAML configuration file (`compose.yaml` or `docker-compose.yml`).

Instead of running multiple `docker run` commands manually, you describe your application in one file and manage it with a few simple commands.

**Example:**

A web application may require:

- React/Vite Frontend
- Node.js Backend
- MongoDB Database
- Redis Cache

Docker Compose starts all of them together.

---

# 2. Why Docker Compose?

Without Docker Compose:

```bash
docker run ...
docker run ...
docker run ...
docker run ...
```

You must manually:

- Create networks
- Attach containers
- Configure volumes
- Set environment variables
- Start containers in the correct order

With Docker Compose:

```bash
docker compose up -d
```

Everything is created automatically.

---

# 3. Architecture

```
compose.yaml

        │
        ▼
Docker Compose

        │
 ┌──────┼────────┐
 ▼      ▼        ▼
Frontend Backend Database

        │
     Network
        │
      Volumes
```

---

# 4. Compose File Structure

```yaml
services:
  frontend:
    image: nginx

  backend:
    image: node:20

volumes:

networks:
```

Main sections:

- services
- volumes
- networks
- secrets (optional)
- configs (optional)

---

# 5. File Name

Recommended:

```
compose.yaml
```

Also supported:

```
docker-compose.yml
docker-compose.yaml
```

---

# 6. Simple Example

```yaml
services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
```

Run:

```bash
docker compose up
```

---

# 7. Docker Compose Commands

### Start Services

```bash
docker compose up
```

Detached mode:

```bash
docker compose up -d
```

---

### Stop Containers

```bash
docker compose stop
```

---

### Start Stopped Containers

```bash
docker compose start
```

---

### Restart

```bash
docker compose restart
```

---

### Remove Containers

```bash
docker compose down
```

Remove volumes also:

```bash
docker compose down -v
```

---

### View Running Containers

```bash
docker compose ps
```

---

### View Logs

```bash
docker compose logs
```

Follow logs:

```bash
docker compose logs -f
```

Specific service:

```bash
docker compose logs backend
```

---

### Build Images

```bash
docker compose build
```

Rebuild without cache:

```bash
docker compose build --no-cache
```

---

### Pull Images

```bash
docker compose pull
```

---

### Execute Commands

```bash
docker compose exec backend bash
```

---

### Run One-Time Command

```bash
docker compose run backend npm test
```

---

### Validate Configuration

```bash
docker compose config
```

---

# 8. Services

Each container is defined as a **service**.

Example:

```yaml
services:
  frontend:
    image: nginx

  backend:
    image: node:20

  mongodb:
    image: mongo
```

---

# 9. Build vs Image

Use **image** when pulling from Docker Hub:

```yaml
image: nginx:latest
```

Use **build** when building locally:

```yaml
build:
  context: .
```

Specify a Dockerfile:

```yaml
build:
  context: .
  dockerfile: Dockerfile
```

---

# 10. Port Mapping

```yaml
ports:
  - "3000:3000"
```

Format:

```
Host:Container
```

Examples:

```
8080:80
5000:5000
3000:3000
```

---

# 11. Volumes

Persist container data.

Example:

```yaml
volumes:
  - mongodb-data:/data/db
```

Named volume:

```yaml
volumes:
  mongodb-data:
```

Bind mount:

```yaml
volumes:
  - ./src:/app/src
```

---

# 12. Networks

Containers communicate over networks.

Example:

```yaml
networks:
  app-network:
```

Assign service:

```yaml
services:
  backend:
    networks:
      - app-network
```

---

# 13. Environment Variables

Inline:

```yaml
environment:
  NODE_ENV: production
  PORT: 5000
```

Using an `.env` file:

```yaml
env_file:
  - .env
```

Example `.env`:

```
PORT=5000
MONGO_URI=mongodb://mongo:27017/app
JWT_SECRET=mysecret
```

---

# 14. depends_on

Controls startup order.

```yaml
services:
  backend:
    depends_on:
      - mongodb
```

> **Note:** `depends_on` starts containers in order but **does not wait** until a service is fully ready. Use a **healthcheck** if readiness is required.

---

# 15. Restart Policy

```yaml
restart: always
```

Options:

- `no`
- `always`
- `on-failure`
- `unless-stopped`

---

# 16. Health Check

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
  interval: 30s
  timeout: 10s
  retries: 3
```

Use this to verify an application is actually healthy.

---

# 17. Scaling

Run multiple instances:

```bash
docker compose up --scale backend=3
```

Example:

```
backend-1
backend-2
backend-3
```

---

# 18. Profiles

Start only selected services.

```yaml
services:
  redis:
    profiles:
      - development
```

Run:

```bash
docker compose --profile development up
```

---

# 19. Multi-file Compose

Base file:

```
compose.yaml
```

Override:

```
compose.prod.yaml
```

Run:

```bash
docker compose -f compose.yaml -f compose.prod.yaml up
```

Useful for separate development and production configurations.

---

# 20. Real-world MERN Example

```yaml
services:
  frontend:
    build: ./frontend
    ports:
      - "80:80"

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    depends_on:
      - mongodb

  mongodb:
    image: mongo:7
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
```

---

# 21. Best Practices

- Use `compose.yaml` as the default filename.
- Pin image versions (avoid `latest` in production).
- Use `.env` for configuration.
- Store secrets securely; don't hard-code them in Compose files.
- Use named volumes for persistent data.
- Use health checks for dependent services.
- Keep development and production Compose files separate.
- Use restart policies for long-running services.
- Validate files with `docker compose config`.

---

# 22. Common Troubleshooting

### Port already in use

```
Bind for 0.0.0.0:80 failed
```

**Solution:** Stop the process using the port or change the host port.

---

### Container exits immediately

Check logs:

```bash
docker compose logs
```

---

### Environment variables not loading

Verify:

- `.env` exists
- `env_file` path is correct
- Variable names match

---

### Volume data not updating

Recreate containers if needed:

```bash
docker compose down -v
docker compose up -d
```

---

# 23. Docker Compose vs Docker Run

| Docker Run          | Docker Compose                             |
| ------------------- | ------------------------------------------ |
| Single container    | Multi-container applications               |
| CLI only            | YAML configuration                         |
| Manual networking   | Automatic networking                       |
| Manual volume setup | Built-in volume support                    |
| Harder to manage    | Easier for complex applications            |
| Best for testing    | Best for development and production stacks |

---

# 24. Interview Questions

1. What is Docker Compose?
2. What is the difference between `docker run` and Docker Compose?
3. What is the purpose of `compose.yaml`?
4. Explain `services`, `volumes`, and `networks`.
5. What is the difference between `build` and `image`?
6. What does `depends_on` do? Does it wait for service readiness?
7. How do you persist database data in Compose?
8. How do you use environment variables?
9. How do you scale a service?
10. What is the difference between `docker compose up`, `start`, `stop`, and `down`?
11. How do you troubleshoot a failing Compose service?
12. What are health checks, and why are they important?
