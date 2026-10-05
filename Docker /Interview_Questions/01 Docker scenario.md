# Docker Scenario-Based Interview Questions (4+ Years Experience)

This document contains **21 common Docker scenario-based interview questions with practical answers**, suitable for **DevOps engineers with ~4 years of experience**. The focus is on **production troubleshooting, optimization, security, and deployment strategies**.

---

# 1. A container keeps restarting. How do you troubleshoot it?

### Steps

1. Check container status

```bash
docker ps -a
```

2. View container logs

```bash
docker logs <container_id>
```

3. Inspect container details

```bash
docker inspect <container_id>
```

4. Check exit code

```bash
docker inspect --format='{{.State.ExitCode}}' <container_id>
```

5. Run container interactively

```bash
docker run -it <image_name> /bin/bash
```

### Common Causes

* Application crash
* Wrong entrypoint or CMD
* Missing environment variables
* Dependency failure
* Port conflicts

---

# 2. Docker image size is very large. How will you reduce it?

### Best Practices

* Use **smaller base images**
* Use **multi-stage builds**
* Remove unnecessary packages
* Use `.dockerignore`
* Combine RUN commands

### Example

```dockerfile
FROM node:18 AS build
WORKDIR /app
COPY . .
RUN npm install

FROM node:18-alpine
WORKDIR /app
COPY --from=build /app .
CMD ["node","app.js"]
```

### .dockerignore Example

```
node_modules
.git
logs
tmp
```

---

# 3. Application inside a container cannot be accessed externally. How do you debug it?

### Steps

1. Check port mapping

```bash
docker ps
```

2. Verify service inside container

```bash
docker exec -it <container> netstat -tulnp
```

3. Check logs

```bash
docker logs <container>
```

4. Run container with port mapping

```bash
docker run -p 8080:80 nginx
```

5. Check firewall or security groups.

---

# 4. Container loses data after restart. What went wrong?

Containers are **stateless by default**.

### Solution: Use Volumes

```bash
docker run -v myvolume:/data mysql
```

or bind mount

```bash
docker run -v /host/data:/container/data nginx
```

Volumes persist data even if the container is removed.

---

# 5. High CPU usage by containers impacts the host. How do you control it?

Use **resource limits**.

```bash
docker run --cpus="1.5" nginx
```

Memory limit:

```bash
docker run --memory="512m" nginx
```

Monitor usage:

```bash
docker stats
```

---

# 6. Secrets are hardcoded in a Dockerfile. How do you fix this?

Secrets should **never be stored in images**.

### Solutions

* Environment variables
* Docker Secrets
* Secret managers

Example:

```bash
docker run -e DB_PASSWORD=xxxx myapp
```

Better approaches:

* AWS Secrets Manager
* HashiCorp Vault
* Kubernetes Secrets

---

# 7. You need different configs for dev, stage, and prod. How do you manage them?

### Options

* Environment variables
* Config files
* Docker Compose environments

Example:

```
docker-compose.dev.yml
docker-compose.stage.yml
docker-compose.prod.yml
```

Run command:

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

---

# 8. Docker build is slow in CI pipelines. How do you optimize it?

### Optimization Techniques

* Use **layer caching**
* Use `.dockerignore`
* Move frequently changing files later
* Use multi-stage builds

Example:

```dockerfile
COPY package.json .
RUN npm install
COPY . .
```

This avoids reinstalling dependencies each build.

---

# 9. A container works locally but fails in production. How do you investigate?

### Steps

* Compare environment variables
* Check logs
* Validate networking
* Check dependency versions
* Verify resource limits

Commands:

```bash
docker logs <container>
docker inspect <container>
docker exec -it <container> bash
```

---

# 10. Multiple containers need to communicate securely. How do you design networking?

Use **Docker networks**.

```bash
docker network create app-network
```

Run containers:

```bash
docker run --network app-network app
docker run --network app-network db
```

Use container names as hostnames.

For security:

* Use internal networks
* Enable TLS communication

---

# 11. You need zero-downtime deployment using Docker. How will you achieve it?

### Deployment Strategies

* Blue-Green Deployment
* Rolling Updates
* Canary Releases

Example process:

1. Deploy new container
2. Perform health checks
3. Switch traffic through load balancer
4. Remove old container

Tools used:

* Docker Swarm
* Kubernetes
* Nginx load balancer

---

# 12. Logs are not visible from containers. How do you centralize logging?

Use logging drivers.

```bash
docker run --log-driver=json-file nginx
```

### Centralized Logging Stack

* ELK (Elasticsearch, Logstash, Kibana)
* Fluentd
* Grafana Loki

Example architecture:

```
Containers → Fluentd → Elasticsearch → Kibana
```

---

# 13. Containers need persistent storage across nodes. How do you solve this?

Use **shared or distributed storage**.

Examples:

* NFS
* AWS EFS
* Ceph
* GlusterFS

Docker volume example:

```bash
docker volume create shared-data
```

In Kubernetes, use **Persistent Volumes (PV)**.

---

# 14. A container is running as root. Why is this risky and how do you fix it?

### Risks

* Security vulnerability
* Container escape possibility
* Host compromise

### Solution

Run containers as **non-root users**.

Example Dockerfile:

```dockerfile
RUN useradd appuser
USER appuser
```

---

# 15. You need to scan images for vulnerabilities before deployment. How do you implement it?

Use **image scanning tools**.

Common tools:

* Trivy
* Clair
* Anchore
* Snyk

Example:

```bash
trivy image nginx:latest
```

Integrate scanning into **CI/CD pipelines**.

---

# 16. Docker daemon crashes on a production host. What is your recovery plan?

### Steps

Check Docker service:

```bash
systemctl status docker
```

Restart Docker:

```bash
systemctl restart docker
```

Check logs:

```bash
journalctl -u docker
```

Verify disk space:

```bash
df -h
```

Re-deploy containers if required.

---

# 17. You need rollback support for Docker-based deployments. How do you implement it?

Use **versioned images**.

Example:

```
myapp:v1
myapp:v2
myapp:v3
```

Rollback command:

```bash
docker run myapp:v1
```

In orchestrators:

* Kubernetes → `kubectl rollout undo`
* Docker Swarm → `docker service rollback`

---

# 18. Multiple teams push images to the same registry. How do you manage access?

### Use Access Controls

* RBAC (Role-Based Access Control)
* Namespace separation
* Private repositories

Example registries:

* Docker Hub
* AWS ECR
* Harbor
* GitHub Container Registry

Example policy:

```
team-a/*
team-b/*
```

---

# 19. Docker networking causes IP conflicts. How do you fix it?

### Solutions

Create custom network with subnet.

```bash
docker network create --subnet 172.20.0.0/16 mynetwork
```

Other approaches:

* Avoid overlapping CIDR ranges
* Use bridge networks
* Use overlay networks for clusters

---

# 20. You are asked to replace Docker. What limitations would you highlight?

### Possible Limitations

* Dependency on Docker daemon
* Root-based container execution
* Resource overhead
* Not optimized for Kubernetes runtime

### Alternatives

* Podman
* containerd
* CRI-O
* Buildah
* Kaniko

Example:

```bash
podman run -d -p 8080:80 nginx
```

Podman is **daemonless and more secure**.

---

# 21. When would you consider replacing Docker?

Organizations may consider alternatives when they need:

* **Daemonless container runtime**
* **Better security controls**
* **Native Kubernetes runtime**
* **Lower resource overhead**
* **Better compliance requirements**

In many modern Kubernetes environments:

```
Docker → replaced by containerd or CRI-O
```

These runtimes integrate better with Kubernetes and follow **OCI standards**.

---

# Summary

Key Docker skills expected from a **4+ years DevOps engineer**:

* Container troubleshooting
* Image optimization
* Secure secret management
* Networking and service communication
* CI/CD pipeline optimization
* Logging and monitoring
* Deployment strategies
* Storage and persistence
* Container security

---
# 22. Your login endpoint is receiving 100k requests per minute from bots.

How do you stop the attack without blocking real users?

This is usually a **bot attack or credential stuffing attack**. The solution should involve **rate limiting, bot detection, and infrastructure protection** while ensuring legitimate users are not blocked.

---

## 1. Enable Rate Limiting

Limit the number of requests per IP address.

Example using **Nginx**:

```nginx
limit_req_zone $binary_remote_addr zone=login_limit:10m rate=10r/s;

server {
    location /login {
        limit_req zone=login_limit burst=20 nodelay;
    }
}
```

This prevents a single IP from sending thousands of login requests.

---

## 2. Add CAPTCHA for Suspicious Traffic

Introduce **CAPTCHA after multiple failed login attempts**.

Example:

```
After 5 failed attempts → show CAPTCHA
```

This stops automated bots while allowing real users.

Common tools:

* Google reCAPTCHA
* hCaptcha

---

## 3. Use Web Application Firewall (WAF)

Deploy a **WAF to filter malicious traffic**.

Examples:

* AWS WAF
* Cloudflare WAF
* Akamai

WAF can block:

* Known bot signatures
* SQL injection attempts
* Credential stuffing patterns

---

## 4. Implement IP Reputation Blocking

Block traffic from **known malicious IPs or bot networks**.

Examples:

* Cloudflare Bot Management
* AWS Shield
* IP reputation databases

---

## 5. Add Account Lockout Policy

Temporarily lock accounts after repeated failed logins.

Example:

```
5 failed attempts → lock account for 10 minutes
```

This prevents credential stuffing attacks.

---

## 6. Use CDN and Edge Protection

Deploy a **CDN in front of the application**.

Examples:

* Cloudflare
* AWS CloudFront
* Akamai

Benefits:

* Absorbs high traffic
* Filters bots
* Reduces load on backend servers

---

## 7. Monitor Traffic and Create Alerts

Use monitoring tools:

* Prometheus + Grafana
* Datadog
* ELK Stack

Example alert:

```
If login requests > 5000/min → trigger alert
```

---

## 8. Behavioral Analysis

Identify bot patterns such as:

* Same user agent
* Very fast login attempts
* Requests without cookies or JavaScript

Block or challenge such traffic.

---

## 9. Add Multi-Factor Authentication (MFA)

Even if bots guess passwords, **MFA prevents unauthorized access**.

Example methods:

* OTP
* Authenticator apps
* SMS verification

---

## 10. Production-Level Architecture

A secure architecture would look like:

```
Users
   ↓
CDN / WAF
   ↓
Load Balancer
   ↓
Rate Limiting Layer (Nginx / API Gateway)
   ↓
Application Servers
   ↓
Authentication Service
```

This ensures bots are **filtered before reaching the application**.

---

## Final Answer (Interview Summary)

To mitigate a bot attack on the login endpoint without blocking real users, I would implement **multi-layer protection** including:

* Rate limiting on login endpoints
* CAPTCHA after repeated login attempts
* Web Application Firewall (WAF) filtering
* CDN-based bot protection
* IP reputation blocking
* Account lockout policies
* Traffic monitoring and alerts
* MFA for stronger authentication

This approach protects the system while ensuring **legitimate users can still log in normally**.

---

