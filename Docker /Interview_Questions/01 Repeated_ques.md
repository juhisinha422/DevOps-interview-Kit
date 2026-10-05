# 🐳 Docker Interview Questions & Answers
> **Target Level:** 4+ Years Experience  
> **Total Questions:** 23  
> **Format:** Real-world, production-grade answers

---

## Q1. What is docker?

Docker is an **open-source containerization platform** that packages an application along with all its dependencies, libraries, and configuration files into a single portable unit called a **container**. Containers run consistently across different environments — dev, staging, and production — eliminating the classic "it works on my machine" problem.

**Key characteristics:**
- Uses **OS-level virtualization** (not full hardware virtualization like VMs)
- Shares the host OS kernel, making it **lightweight and fast**
- Built on Linux primitives: **namespaces** (isolation) and **cgroups** (resource limits)
- Images are **layered** using a Union File System (OverlayFS)

> **Production insight:** Docker containers start in milliseconds compared to minutes for VMs, making them ideal for microservices, CI/CD pipelines, and auto-scaling workloads.

---

## Q2. What is docker lifecycle?

The Docker object lifecycle flows through these stages:

```
Dockerfile → docker build → Image → docker run → Container
                                                     ↓
                                              [running] ←→ [paused]
                                                     ↓
                                              [stopped/exited]
                                                     ↓
                                              docker rm → [deleted]
```

### Container States:
| State     | Description                                          |
|-----------|------------------------------------------------------|
| `created` | Container created but not yet started                |
| `running` | Container is actively executing                      |
| `paused`  | Process suspended using SIGSTOP (cgroups freezer)    |
| `stopped` | Main process exited or `docker stop` was called      |
| `dead`    | Container couldn't be fully removed (resource issue) |

### Key Lifecycle Commands:
```bash
docker create   # Creates container without starting
docker start    # Starts a stopped container
docker run      # create + start in one step
docker pause    # Freezes container processes
docker unpause  # Resumes frozen container
docker stop     # Graceful shutdown (SIGTERM → wait → SIGKILL)
docker kill     # Immediate shutdown (SIGKILL)
docker rm       # Removes stopped container
docker rm -f    # Force removes even running container
```

---

## Q3. What are the key docker components?

| Component            | Role                                                                 |
|----------------------|----------------------------------------------------------------------|
| **Docker Engine**    | Core daemon (`dockerd`) that manages containers, images, networks    |
| **Docker CLI**       | Command-line client that communicates with `dockerd` via REST API    |
| **Docker Daemon**    | Background service managing Docker objects                           |
| **Docker Image**     | Read-only template used to create containers                         |
| **Docker Container** | Running instance of an image                                         |
| **Docker Registry**  | Storage for images (Docker Hub, ECR, GCR, private registries)        |
| **Docker Compose**   | Tool for defining multi-container apps via `docker-compose.yml`      |
| **Docker Network**   | Virtual networking layer for container communication                 |
| **Docker Volume**    | Persistent storage mechanism decoupled from container lifecycle      |
| **Dockerfile**       | Blueprint/script to build a Docker image                             |

---

## Q4. What are the difference between docker image and docker container?

| Aspect         | Docker Image                              | Docker Container                          |
|----------------|-------------------------------------------|-------------------------------------------|
| **Definition** | Read-only, immutable blueprint            | Running (or stopped) instance of an image |
| **State**      | Stateless — never changes                 | Stateful — has a writable layer           |
| **Storage**    | Stored in registry or local cache         | Lives on the Docker host                  |
| **Layers**     | Made of read-only layers (OverlayFS)      | Adds a thin writable layer on top         |
| **Creation**   | Built via `docker build` or `docker pull` | Created via `docker run` or `docker create`|
| **Lifecycle**  | Persists until explicitly deleted         | Can be started, stopped, removed          |
| **Analogy**    | Class definition in OOP                   | Object/instance in OOP                    |

```
Image (read-only layers)
├── Layer 1: Base OS (ubuntu:22.04)
├── Layer 2: apt-get install python3
├── Layer 3: COPY app.py /app/
└── Layer 4: CMD ["python3", "app.py"]
         +
Container writable layer (ephemeral)
└── Any runtime changes (logs, temp files, DB writes)
```

> **Critical:** Data written in the container layer is **lost** when the container is removed. Use **volumes** for persistence.

---

## Q5. What should do before creating a container?

Before running `docker run`, follow this checklist:

**1. Pull or build the image**
```bash
docker pull nginx:1.25-alpine   # from registry
docker build -t my-app:1.0 .    # from Dockerfile
```

**2. Define resource limits** (CPU, memory) to prevent container from starving the host
```bash
docker run --memory="512m" --cpus="1.0" my-app
```

**3. Plan networking** — decide which network the container joins
```bash
docker network create app-network
```

**4. Set up volumes** for any persistent or shared data
```bash
docker volume create app-data
```

**5. Plan environment variables** — never bake secrets into images
```bash
docker run --env-file .env my-app
```

**6. Decide port mappings** — host:container
```bash
docker run -p 8080:80 nginx
```

**7. Security hardening** — run as non-root, read-only filesystem
```bash
docker run --user 1001 --read-only my-app
```

---

## Q6. What is a docker compose and how do you used it?

Docker Compose is a tool for **defining and running multi-container applications** using a single `docker-compose.yml` file. It manages the entire lifecycle of all services together.

### Sample `docker-compose.yml`:
```yaml
version: "3.9"

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:80"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app-net
    volumes:
      - ./src:/app/src

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - pg-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      retries: 5
    networks:
      - app-net

volumes:
  pg-data:

networks:
  app-net:
    driver: bridge
```

### Key Commands:
```bash
docker compose up -d          # Start all services in background
docker compose down           # Stop and remove containers + network
docker compose down -v        # Also removes volumes
docker compose logs -f web    # Follow logs of a specific service
docker compose ps             # List running services
docker compose exec web bash  # Shell into running service
docker compose build          # Rebuild images
docker compose scale web=3    # Scale a service to 3 replicas
```

---

## Q7. How do you optimze the docker image for better performance?

### 1. Use Minimal Base Images
```dockerfile
# Bad
FROM ubuntu:22.04

# Good
FROM python:3.11-alpine   # or debian:slim variants
```

### 2. Multi-Stage Builds (most impactful)
```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage — only ship the artifact
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```
> Reduces image from 1.2GB → ~25MB in real projects.

### 3. Layer Caching — Order Matters
```dockerfile
# Bad: Source code change invalidates npm install cache
COPY . .
RUN npm install

# Good: Dependencies cached separately
COPY package*.json ./
RUN npm install
COPY . .
```

### 4. Combine RUN Commands
```dockerfile
# Bad: 3 layers
RUN apt-get update
RUN apt-get install -y curl
RUN rm -rf /var/lib/apt/lists/*

# Good: 1 layer
RUN apt-get update && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
```

### 5. Use `.dockerignore`
```
node_modules
.git
*.log
.env
dist
tests
docs
```

### 6. Avoid Installing Unnecessary Packages
```dockerfile
RUN apt-get install -y --no-install-recommends curl
```

### 7. Use Specific Tags — Never `latest`
```dockerfile
FROM node:20.11.1-alpine3.19  # Pinned, reproducible
```

---

## Q8. How would you secure the docker container?

### 1. Run as Non-Root User
```dockerfile
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

### 2. Read-Only Filesystem
```bash
docker run --read-only --tmpfs /tmp my-app
```

### 3. Drop Linux Capabilities
```bash
docker run --cap-drop ALL --cap-add NET_BIND_SERVICE my-app
```

### 4. Use Secrets Management — Never ENV for Secrets
```yaml
# Docker Compose secrets
secrets:
  db_password:
    file: ./db_password.txt
```

### 5. Limit Resources
```bash
docker run --memory="256m" --cpus="0.5" --pids-limit 100 my-app
```

### 6. Scan Images for Vulnerabilities
```bash
docker scout cves my-app:latest    # Docker Scout
trivy image my-app:latest          # Trivy (popular in CI/CD)
```

### 7. Enable Content Trust
```bash
export DOCKER_CONTENT_TRUST=1   # Only run signed images
```

### 8. Use `--security-opt`
```bash
docker run --security-opt no-new-privileges:true my-app
```

### 9. Use Distroless Images
```dockerfile
FROM gcr.io/distroless/nodejs20-debian12
# No shell, no package manager — minimal attack surface
```

---

## Q9. What are the custome image of docker?

A custom image is built from a **Dockerfile** tailored to your application's needs, extending a base image with your own layers.

```dockerfile
# Custom Node.js App Image
FROM node:20-alpine AS base

# Metadata
LABEL maintainer="devops@company.com"
LABEL version="1.0.0"
LABEL description="Custom Node.js API server"

WORKDIR /app

# Dependencies layer (cached separately)
COPY package*.json ./
RUN npm ci --only=production

# App source layer
COPY --chown=node:node . .

# Security: run as non-root
USER node

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

CMD ["node", "server.js"]
```

```bash
# Build and tag
docker build -t my-company/node-api:1.0.0 .

# Push to registry
docker push my-company/node-api:1.0.0

# Tag for multiple environments
docker tag my-company/node-api:1.0.0 my-company/node-api:latest
```

> Custom images are the foundation of any CI/CD pipeline — built once, promoted as immutable artifacts across dev → staging → prod.

---

## Q10. Difference between CMD, RUN, Entrypoint?

| Instruction    | When It Runs     | Purpose                                 | Overridable?                       |
|----------------|------------------|-----------------------------------------|------------------------------------|
| `RUN`          | **Build time**   | Executes commands to build image layers | N/A (baked in permanently)         |
| `CMD`          | **Runtime**      | Default command/args for container      | Yes — `docker run <img> <newcmd>`  |
| `ENTRYPOINT`   | **Runtime**      | Main executable that always runs        | Only with `--entrypoint` flag      |

```dockerfile
# RUN — creates a new image layer at build time
RUN apt-get update && apt-get install -y curl

# CMD — default args, easily overridden at runtime
CMD ["node", "server.js"]

# ENTRYPOINT — fixed executable, always runs
ENTRYPOINT ["nginx", "-g", "daemon off;"]

# Best practice — combine both:
ENTRYPOINT ["python3", "app.py"]
CMD ["--port", "8080"]    # default args passed to ENTRYPOINT
```

```bash
# Override CMD
docker run my-app node other.js        # replaces CMD entirely

# Override only args (keeps ENTRYPOINT)
docker run my-app --port 9090          # replaces CMD, ENTRYPOINT still runs

# Override ENTRYPOINT entirely
docker run --entrypoint /bin/sh my-app
```

> **Best practice:** Always use **exec form** `["cmd", "arg"]` over shell form `cmd arg` to ensure the process runs as PID 1 and receives Unix signals (SIGTERM, etc.) correctly.

---

## Q11. Difference between add and Copy?

| Feature                       | `COPY`                            | `ADD`                                        |
|-------------------------------|-----------------------------------|----------------------------------------------|
| **Basic file copy**           | ✅ Yes                            | ✅ Yes                                        |
| **Copy from URL**             | ❌ No                             | ✅ Yes (downloads from remote URL)            |
| **Auto-extract tar archives** | ❌ No                             | ✅ Yes (`.tar`, `.tar.gz`, `.zip`, etc.)      |
| **Transparency / clarity**    | ✅ Explicit and predictable       | ⚠️ Implicit behavior (harder to reason about)|
| **Recommended for**           | All standard local file copies    | Only when tar auto-extraction is needed      |
| **Cache invalidation**        | On local file change              | URL fetches never use layer cache            |

```dockerfile
# COPY — always prefer for local files
COPY ./config /app/config
COPY package.json /app/

# ADD — use only for tar auto-extraction
ADD ./archive.tar.gz /app/         # auto-extracts into /app/

# For URL downloads, prefer curl (explicit + cached layer)
RUN curl -fsSL https://example.com/file.tar.gz | tar -xz -C /app
```

> **Rule of thumb:** Default to `COPY`. Only reach for `ADD` when you specifically need auto-extraction of a local tar archive.

---

## Q12. What are the target in docker compose?

Targets (also called **build targets**) in Docker Compose let you build different variants of a service by pointing to a specific **stage** in a multi-stage Dockerfile.

```yaml
# docker-compose.yml
services:
  app-dev:
    build:
      context: .
      target: development    # stops build at 'development' stage

  app-prod:
    build:
      context: .
      target: production     # builds through to 'production' stage
```

```dockerfile
# Dockerfile with named targets
FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./
RUN npm install

FROM base AS development
RUN npm install --include=dev
CMD ["npm", "run", "dev"]         # hot reload with nodemon

FROM base AS production
RUN npm ci --only=production
RUN npm run build
CMD ["node", "dist/server.js"]    # optimized production build
```

```bash
# Build specific target
docker compose build app-dev

# Run specific target service
docker compose up app-prod

# Build target directly with docker
docker build --target development -t app:dev .
docker build --target production  -t app:prod .
```

---

## Q13. What are the difference between bridge and overlay network?

| Feature              | Bridge Network                             | Overlay Network                              |
|----------------------|--------------------------------------------|----------------------------------------------|
| **Scope**            | Single Docker host only                    | Multi-host (Docker Swarm / Kubernetes)        |
| **Communication**    | Between containers on same host            | Between containers across multiple hosts     |
| **Driver**           | `bridge`                                   | `overlay`                                    |
| **DNS Resolution**   | Container name (custom bridge only)        | Service name resolution across all nodes     |
| **Use case**         | Local dev, single-node apps                | Production distributed systems, Swarm        |
| **Encryption**       | Not supported                              | Supports IPSEC encryption (`--opt encrypted`)|
| **Requires**         | Nothing extra                              | Docker Swarm mode or external KV store       |

```bash
# Bridge network
docker network create --driver bridge my-bridge
docker run --network my-bridge my-app

# Overlay network (requires Swarm init first)
docker swarm init
docker network create --driver overlay --attachable my-overlay
docker service create --network my-overlay my-app
```

> **Key difference:** Bridge is for single-host container-to-container communication. Overlay stretches across multiple Docker hosts, making it the backbone of distributed microservices in Swarm.

---

## Q14. In docker file which user perform and what action and how I specify the user?

By default, Docker runs containers as **root (UID 0)** — a major security risk. The `USER` instruction specifies which user runs subsequent instructions and the container process.

```dockerfile
# Step 1: Create a dedicated non-root user during build
RUN groupadd -r appgroup --gid 1001 \
    && useradd -r -g appgroup --uid 1001 --no-create-home appuser

# Step 2: Set ownership of app files before switching user
COPY --chown=appuser:appgroup . /app

# Step 3: Switch to non-root user
USER appuser

# Or specify by UID directly (more portable across systems)
USER 1001

# Or user:group format
USER appuser:appgroup
```

```bash
# Override user at runtime
docker run --user 1001:1001 my-app

# Use current host user (avoids permission issues with bind mounts)
docker run --user $(id -u):$(id -g) my-app
```

```yaml
# In docker-compose.yml
services:
  app:
    image: my-app
    user: "1001:1001"
```

> **Why it matters:** A container compromised while running as root can potentially escalate to host root access. Non-root containers limit the blast radius of any security breach.

---

## Q15. What are the different networking type in docker?

| Network Type  | Driver      | Use Case                                              |
|---------------|-------------|-------------------------------------------------------|
| **Bridge**    | `bridge`    | Default; isolated network on a single host            |
| **Host**      | `host`      | Container shares host network stack (no isolation)    |
| **Overlay**   | `overlay`   | Multi-host networking (Docker Swarm)                  |
| **Macvlan**   | `macvlan`   | Container gets its own MAC/IP on the physical network |
| **IPvlan**    | `ipvlan`    | Like macvlan but shares the host MAC address          |
| **None**      | `none`      | No networking — fully isolated, air-gapped container  |

```bash
# Host network — container shares host's network namespace
docker run --network host nginx

# None — no network interface at all
docker run --network none my-secure-app

# Custom bridge with defined subnet
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  --ip-range 172.20.240.0/20 \
  custom-bridge

# Macvlan — container appears as a physical device on the LAN
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 macvlan-net

# Overlay (requires Swarm)
docker network create --driver overlay --attachable swarm-net
```

---

## Q16. Add vs copy?

> *(This is a repeat of Q11 — both questions test the same concept. Below is the concise comparison for quick recall.)*

| Feature                  | `ADD`                                              | `COPY`                         |
|--------------------------|----------------------------------------------------|--------------------------------|
| **Local file copy**      | ✅                                                 | ✅                              |
| **Remote URL fetch**     | ✅ (downloads automatically)                       | ❌                              |
| **Auto-extract tarballs**| ✅ (`.tar`, `.tar.gz`, `.bz2`, etc.)               | ❌                              |
| **Predictability**       | ⚠️ Implicit side effects                           | ✅ Always does exactly what you expect |
| **Best practice**        | Only when you need tar extraction                  | Use by default for all files   |

```dockerfile
COPY src/ /app/src/           # explicit, preferred
ADD  src.tar.gz /app/         # use only for auto-extraction
```

---

## Q17. Entrypoint vs CMD?

> *(This is a repeat of Q10 — both questions test the same concept. Below is the deep-dive comparison.)*

```dockerfile
# Shell form — runs via /bin/sh -c, does NOT receive Unix signals
ENTRYPOINT node server.js
CMD node server.js

# Exec form — runs directly as PID 1, DOES receive signals (recommended)
ENTRYPOINT ["node", "server.js"]
CMD ["node", "server.js"]
```

### Behavior Matrix:
| Dockerfile config                   | `docker run my-img`    | `docker run my-img args` |
|-------------------------------------|------------------------|--------------------------|
| Only `CMD ["a", "b"]`               | Runs `a b`             | Runs `args` (replaces)   |
| Only `ENTRYPOINT ["e"]`             | Runs `e`               | Runs `e args`            |
| `ENTRYPOINT ["e"]` + `CMD ["a"]`    | Runs `e a`             | Runs `e args`            |

```bash
# CMD is completely replaced
docker run my-app python other.py

# Only args replaced, ENTRYPOINT stays
docker run my-app --debug --port 9090

# Override ENTRYPOINT explicitly
docker run --entrypoint /bin/bash my-app
```

> **Summary:** `ENTRYPOINT` = *what* runs. `CMD` = *default arguments* to what runs. Together they give you a flexible, overridable container interface.

---

## Q18. How to remove all unwanted or unused docker object from system?

```bash
# ── Check disk usage first ──────────────────────────────────────
docker system df          # Summary of space used
docker system df -v       # Verbose — per-image and per-container breakdown

# ── Remove stopped containers ───────────────────────────────────
docker container prune

# ── Remove images ───────────────────────────────────────────────
docker image prune          # Only dangling (untagged) images
docker image prune -a       # ALL unused images (not referenced by any container)

# ── Remove volumes ──────────────────────────────────────────────
docker volume prune         # WARNING: deletes data — use carefully!

# ── Remove unused networks ──────────────────────────────────────
docker network prune

# ── Nuclear option — remove EVERYTHING unused ───────────────────
docker system prune                    # containers + networks + dangling images
docker system prune -a                 # + all unused images
docker system prune -a --volumes       # + volumes (DATA LOSS risk)

# ── Targeted cleanup ────────────────────────────────────────────
# Remove all exited containers
docker rm $(docker ps -a -q --filter "status=exited")

# Remove dangling images
docker rmi $(docker images -f "dangling=true" -q)

# Remove images older than 24 hours
docker image prune -a --filter "until=24h"
```

> **Pro tip in CI/CD:** Run `docker system prune -f` at the end of each pipeline run to prevent disk exhaustion on build agents.

---

## Q19. Multistate in docker build?

Multi-stage builds let you use **multiple `FROM` instructions** in a single Dockerfile. Each `FROM` starts a new stage. Only the **final stage** is included in the produced image — intermediate stages are discarded.

```dockerfile
# ─── Stage 1: Build ───────────────────────────────────────────
FROM maven:3.9-eclipse-temurin-21 AS builder
WORKDIR /build
COPY pom.xml .
RUN mvn dependency:go-offline              # cache dependency layer
COPY src ./src
RUN mvn package -DskipTests

# ─── Stage 2: Test ────────────────────────────────────────────
FROM builder AS tester
RUN mvn test

# ─── Stage 3: Production ──────────────────────────────────────
FROM eclipse-temurin:21-jre-alpine AS production
WORKDIR /app
# Only copy the compiled JAR — no Maven, no source code
COPY --from=builder /build/target/app.jar ./app.jar
RUN adduser -D -u 1001 appuser
USER appuser
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```bash
# Build only a specific stage (useful in CI)
docker build --target builder    -t app-builder .
docker build --target tester     -t app-tester  .
docker build --target production -t app:latest  .

# Enable parallel stage builds with BuildKit
DOCKER_BUILDKIT=1 docker build -t app:latest .
```

**Real-world impact:** Java app — from **600MB → 85MB** in production image. Node.js app — from **1.2GB → 25MB**.

---

## Q20. Is docker file is immuitable?

**No** — a Dockerfile itself is just a text file and is **mutable** (you can edit it at any time like any source file).

However, a **Docker image built from a Dockerfile is immutable** once created:
- Image layers are **content-addressable** (identified by SHA256 hash)
- You **cannot modify** an existing image — you must rebuild it
- Any change to the Dockerfile or source files produces a **completely new image** with a new digest
- This immutability is what makes Docker deployments **reproducible and reliable**

```bash
# Each build produces a new image with a different digest
docker build -t my-app:1.0.0 .   # SHA256: abc123...

# Edit Dockerfile or source code, then rebuild
docker build -t my-app:1.0.1 .   # SHA256: def456... (entirely new image)

# Verify image digest
docker inspect my-app:1.0.0 --format '{{.Id}}'
```

> **Production implication:** Immutable images are the foundation of **GitOps** — you tag images with git commit SHAs or semantic versions and promote the *exact same artifact* from dev → staging → prod without rebuilding.

---

## Q21. Difference between docker engine and docker container?

| Aspect         | Docker Engine                                                    | Docker Container                                               |
|----------------|------------------------------------------------------------------|----------------------------------------------------------------|
| **What it is** | The **platform/runtime** that manages all Docker objects         | A **running isolated process** on the host OS                  |
| **Components** | `dockerd` daemon + REST API + CLI client (`docker`)              | Writable layer + image layers + process/network namespace      |
| **Role**       | Creates, runs, stops, networks, and manages containers           | Executes the actual application workload                       |
| **Analogy**    | The JVM (Java Virtual Machine runtime)                           | A Java process running inside the JVM                          |
| **Lifecycle**  | Long-running system service (always on)                          | Short-lived, created and destroyed on demand                   |
| **Config**     | `/etc/docker/daemon.json`                                        | Configured via `docker run` flags or Compose                   |

```
Docker Engine (dockerd — always running)
├── Container A → nginx process
├── Container B → postgres process
├── Container C → node API process
├── Networks: bridge, overlay, host
├── Volumes: pg-data, app-logs
└── Image cache: nginx:alpine, postgres:15, node:20
```

> **In short:** Docker Engine is the *manager*. Docker Containers are the *workers* it manages.

---

## Q22. How do you if two docker container is connected?

There are multiple ways to verify container connectivity:

### Method 1: Inspect shared network
```bash
# Check which network(s) each container belongs to
docker inspect container1 --format '{{range $k,$v := .NetworkSettings.Networks}}{{$k}} {{end}}'
docker inspect container2 --format '{{range $k,$v := .NetworkSettings.Networks}}{{$k}} {{end}}'
# If they share a network name → they are connected
```

### Method 2: Inspect the network directly
```bash
# List all containers attached to a network
docker network inspect my-app-network \
  --format '{{range .Containers}}{{.Name}} {{end}}'
```

### Method 3: Ping test from inside a container
```bash
docker exec container1 ping container2
docker exec container1 curl http://container2:8080/health
```

### Method 4: Full network inspect (JSON)
```bash
docker network inspect bridge
# Look for both container names under the "Containers" key
```

### Method 5: Docker Compose
```bash
docker compose ps         # All services and their status
docker network ls         # Compose-created networks (prefix: project name)
```

### Method 6: `docker network ls` + cross-reference
```bash
docker inspect --format='{{json .NetworkSettings.Networks}}' container1 | jq keys
docker inspect --format='{{json .NetworkSettings.Networks}}' container2 | jq keys
```

> **Key rule:** Two containers can communicate **if and only if they share at least one Docker network**. The **default bridge** network does NOT support DNS resolution by container name — only **custom named networks** do.

---

## Q23. COPY and ADD in dockerfile?

> *(This is a repeat of Q11 and Q16 — the most frequently asked Dockerfile instruction comparison. Here is the most comprehensive version.)*

### Syntax:
```dockerfile
COPY <src> <dest>
ADD  <src> <dest>
```

### Detailed Comparison:
| Feature                       | `COPY`                                    | `ADD`                                              |
|-------------------------------|-------------------------------------------|----------------------------------------------------|
| **Copy local files/dirs**     | ✅ Yes                                    | ✅ Yes                                              |
| **Copy from build context**   | ✅ Yes                                    | ✅ Yes                                              |
| **Copy from another stage**   | ✅ `COPY --from=builder /app .`           | ❌ No                                               |
| **Download from URL**         | ❌ No                                     | ✅ Yes (fetches and copies)                         |
| **Auto-extract tar**          | ❌ No — copies as-is                      | ✅ Yes (`.tar`, `.tar.gz`, `.bz2`, `.xz`, `.zip`)  |
| **Layer cache behavior**      | Invalidated on file content change        | URL downloads always bypass cache                  |
| **Transparency**              | ✅ Predictable — always does what you see | ⚠️ Side effects (auto-extract can surprise you)    |
| **Official recommendation**   | ✅ Preferred by Docker best practices     | Use only when extraction is needed                 |

### Examples:
```dockerfile
# ── COPY examples ──────────────────────────────────────────────
COPY . /app                              # copy entire context
COPY src/ /app/src/                      # copy directory
COPY package.json yarn.lock /app/        # copy multiple files
COPY --chown=node:node . /app            # copy with ownership
COPY --from=builder /app/dist /static    # copy from build stage

# ── ADD examples ───────────────────────────────────────────────
ADD ./archive.tar.gz /app/               # auto-extracts → /app/
ADD https://example.com/cert.pem /certs/ # downloads file

# ── Preferred alternative for URL downloads ────────────────────
# Use curl/wget in RUN — explicit, benefits from layer cache
RUN curl -fsSL https://example.com/app.tar.gz \
    | tar -xz -C /app
```

### Decision Rule:
```
Need to copy local files?                  → COPY ✅
Need to copy from another build stage?     → COPY --from ✅
Need to auto-extract a local .tar.gz?      → ADD ✅
Need to download from a URL?               → RUN curl (preferred) or ADD
Everything else?                           → COPY ✅
```

---

## Quick Reference Cheat Sheet

```bash
# Image Operations
docker build -t name:tag .            # Build image
docker pull image:tag                 # Pull from registry
docker push image:tag                 # Push to registry
docker images                         # List images
docker rmi image:tag                  # Remove image
docker image prune -a                 # Remove all unused images
docker inspect image:tag              # Detailed image info
docker history image:tag              # Show image layers

# Container Operations
docker run -d -p 8080:80 --name web nginx   # Run detached
docker ps                                   # List running
docker ps -a                                # List all
docker stop/start/restart container         # Lifecycle
docker exec -it container bash              # Shell access
docker logs -f container                    # Follow logs
docker stats                                # Live resource usage
docker inspect container                    # Detailed info
docker cp file.txt container:/path/         # Copy files

# Network Operations
docker network create my-net
docker network connect my-net container
docker network disconnect my-net container
docker network inspect my-net

# Volume Operations
docker volume create my-vol
docker volume ls
docker volume inspect my-vol
docker volume prune

# System
docker system df            # Disk usage
docker system prune -a      # Clean everything unused
docker info                 # Engine info
docker version              # Client + server versions
```
