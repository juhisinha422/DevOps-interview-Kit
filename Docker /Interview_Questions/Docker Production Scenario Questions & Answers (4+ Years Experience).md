# 🐳 Docker Production Scenario Questions & Answers (4+ Years Experience)

---

## A container is continuously restarting — how do you troubleshoot it?

* Check logs: `docker logs <container>`
* Inspect exit code: `docker inspect`
* Check health checks
* Verify env variables & config
* Run container interactively to debug

---

## Your application inside a container is not accessible — what steps will you take?

* Check port mapping: `docker ps`
* Verify app is listening inside container
* Check firewall / security groups
* Curl inside container: `docker exec`

---

## Container is running but application is not responding — how will you debug?

* Check application logs
* Verify service/port binding
* Check CPU/memory usage
* Debug inside container shell

---

## High CPU usage observed in a container — how do you identify the root cause?

* `docker stats`
* `top` inside container
* Identify process consuming CPU
* Check infinite loops / heavy queries

---

## Container memory usage is increasing continuously — what could be the issue?

* Memory leak in application
* Improper caching
* No memory limits set

---

## Disk space is full on the host due to Docker — how do you resolve it?

* Remove unused containers/images:

```bash
docker system prune -a
```

* Clean volumes and logs

---

## Docker images are taking too much space — how do you optimize?

* Use smaller base images (alpine)
* Multi-stage builds
* Remove unnecessary layers
* Clean cache

---

## A container suddenly stopped in production — how do you investigate?

* Check container logs
* Check exit code
* Inspect events: `docker events`
* Check host resources

---

## Logs are not visible for a container — what could be the issue?

* Logging driver misconfigured
* App not writing to stdout/stderr
* Log rotation issues

---

## Multiple containers are failing after deployment — what could be wrong?

* Bad image version
* Config/env issue
* Dependency failure
* Network misconfiguration

---

## Network connectivity issue between containers — how do you debug?

* Check Docker network
* Use `docker network inspect`
* Ping between containers
* Verify DNS/service name

---

## Container cannot connect to external services — what could be the problem?

* DNS issue
* Firewall/security group
* Proxy misconfiguration

---

## DNS resolution is not working inside container — how do you fix it?

* Check `/etc/resolv.conf`
* Restart Docker daemon
* Configure DNS in daemon.json

---

## Application works locally but fails in Docker container — why?

* Environment mismatch
* Missing dependencies
* Wrong working directory
* Port/config issues

---

## Environment variables are not picked up in container — how to fix?

* Verify `-e` or env file
* Check Dockerfile ENV
* Validate inside container

---

## Data is lost after container restart — what is the issue?

* No persistent volume used
* Containers are ephemeral

---

## Volume is not mounting properly — how do you debug?

* Check mount path
* Verify volume exists
* Inspect container mounts

---

## Permission issues in mounted volumes — how to resolve?

* Fix ownership (`chown`)
* Match UID/GID
* Use proper file permissions

---

## Container is running as root — how to secure it?

* Use non-root user in Dockerfile
* Apply least privilege principle

---

## Secrets exposed in Docker image — how to prevent it?

* Use secrets manager (AWS Secrets Manager)
* Avoid hardcoding secrets
* Use environment variables securely

---

## Docker build is very slow — how do you optimize it?

* Use caching effectively
* Optimize layer order
* Reduce image size

---

## CI/CD pipeline failing at Docker build stage — how to troubleshoot?

* Check build logs
* Validate Dockerfile
* Verify dependencies
* Check permissions

---

## Image works in dev but fails in production — what could be the reason?

* Environment differences
* Missing configs/secrets
* Resource constraints

---

## Container health check is failing — how do you debug?

* Check health check command
* Verify endpoint
* Inspect logs

---

## Rolling deployment causing downtime — how to fix?

* Use proper readiness probes
* Increase replicas
* Configure zero-downtime deployment

---

## Too many containers running on host — performance issues — what to do?

* Limit resources (CPU/memory)
* Remove unused containers
* Scale horizontally

---

## Container crashes under load — how do you analyze?

* Load testing
* Check logs/metrics
* Increase resources or optimize app

---

## Unable to pull image from registry — what could be wrong?

* Network issue
* Wrong image name/tag
* Registry downtime

---

## Authentication issues with private registry — how to resolve?

* Login using:

```bash
docker login
```

* Verify credentials & permissions

---

## Security vulnerability found in image — what steps will you take?

* Scan image (Trivy)
* Update base image
* Patch vulnerabilities
* Rebuild & redeploy

---

## 🎯 Summary

Covers:

* Troubleshooting
* Performance issues
* Networking
* Security
* CI/CD integration

👉 Perfect for **Docker + DevOps interviews (3–6 years experience)**

---
