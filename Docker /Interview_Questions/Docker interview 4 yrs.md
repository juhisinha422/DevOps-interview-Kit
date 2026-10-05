# Docker Interview Questions (0–4 Years DevOps Engineer)

---

## Your Docker image size is too large. How will you reduce it?

* Use lightweight base images:

```dockerfile id="dockreduce1"
FROM alpine
```

* Use multi-stage builds
* Remove unnecessary packages/files
* Use `.dockerignore`
* Combine RUN commands
* Avoid storing cache/temp files

---

## A container is crashing repeatedly. How will you troubleshoot it?

1. Check container logs:

```bash id="docklogs1"
docker logs <container-id>
```

2. Inspect container:

```bash id="dockinspect1"
docker inspect <container-id>
```

3. Check resource usage
4. Verify environment variables/configuration
5. Check application startup errors
6. Test image locally

---

## You need to run multiple services (app + DB). How will you manage it?

* Use Docker Compose

Example:

```yaml id="dockcompose11"
services:
  app:
    image: myapp

  db:
    image: mysql
```

---

## You need persistent data even after container deletion. What will you do?

* Use Docker Volumes

Example:

```bash id="dockvolume1"
docker volume create myvolume
```

Mount volume:

```bash id="dockvolume2"
docker run -v myvolume:/data
```

---

## How will you secure sensitive data inside containers?

* Use Docker secrets
* Use environment variables securely
* Avoid hardcoding passwords
* Use IAM roles/secret managers
* Run containers as non-root user

---

## Your container is not accessible from the browser. What could be the issue?

Possible issues:

* Port not exposed/published
* Firewall/Security Group issue
* App listening only on localhost
* Container crashed
* Wrong port mapping

Check:

```bash id="dockps1"
docker ps
```

---

## How will you pass configuration dynamically to containers?

* Environment variables

Example:

```bash id="dockenv1"
docker run -e ENV=prod myapp
```

* Config files
* Docker secrets
* Kubernetes ConfigMaps

---

## You need to deploy the same container across dev, staging, and prod. How will you handle it?

* Use same Docker image
* Change only:

  * Environment variables
  * Configurations
  * Secrets

Best practice:

* Build once, deploy everywhere

---

## How will you monitor running containers in production?

Tools:

* Prometheus
* Grafana
* ELK Stack
* Datadog
* CloudWatch

Commands:

```bash id="dockstats1"
docker stats
```

---

## You need zero downtime deployment using Docker. How will you approach it?

* Blue-Green deployment
* Rolling updates
* Load balancer traffic switching
* Health checks before traffic routing

---

# 🔵 Advanced

---

## What is the difference between Docker and Virtual Machines?

| Docker                | Virtual Machine     |
| --------------------- | ------------------- |
| Lightweight           | Heavy               |
| Shares host OS kernel | Separate OS         |
| Faster startup        | Slower startup      |
| Less resource usage   | More resource usage |

---

## What are namespaces and cgroups in Docker?

### Namespaces

* Provide isolation:

  * Process
  * Network
  * Filesystem

### cgroups

* Control resource usage:

  * CPU
  * Memory
  * Disk

---

## What is Docker Swarm?

* Native Docker container orchestration tool
* Manages clustered Docker nodes
* Supports scaling and load balancing

---

## What is image layering in Docker?

* Every Dockerfile instruction creates a layer
* Layers are cached and reusable
* Improves build efficiency

---

## How does Docker caching work?

* Reuses unchanged image layers during build
* Speeds up builds

Example:

```dockerfile id="dockcache1"
COPY requirements.txt .
RUN pip install -r requirements.txt
```

---

## What is the difference between EXPOSE and -p?

| EXPOSE             | -p                      |
| ------------------ | ----------------------- |
| Documents port     | Publishes port          |
| Inside Dockerfile  | Runtime option          |
| No external access | External browser access |

Example:

```bash id="dockport11"
docker run -p 8080:80 nginx
```

---

## What is a health check in Docker?

* Checks whether container is healthy

Example:

```dockerfile id="dockhealth1"
HEALTHCHECK CMD curl --fail http://localhost || exit 1
```

---

## How do you secure Docker daemon?

* Use TLS authentication
* Restrict Docker socket access
* Run least privilege containers
* Keep Docker updated
* Enable logging/auditing

---

## What are best practices for writing Dockerfiles?

* Use minimal base images
* Use multi-stage builds
* Avoid root user
* Reduce layers
* Use `.dockerignore`
* Pin image versions

---

## How does Docker integrate with CI/CD pipelines?

1. Developer commits code
2. CI pipeline triggered
3. Docker image built
4. Security scans executed
5. Image pushed to registry
6. Deployment to Kubernetes/servers

Tools:

* Jenkins
* GitHub Actions
* GitLab CI/CD

---
