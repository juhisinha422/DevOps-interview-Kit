# DevOps Production Scenario Questions (4–6 Years Experience)

This document contains **real-world DevOps production scenarios** often asked in **interviews for 4–6 years experience**.
The questions cover **scalability, troubleshooting, reliability, monitoring, and security**.

---

# 1. Your API suddenly receives 1 million requests per minute. What will you do?

### Immediate Actions

* Enable **auto-scaling** for application servers
* Use **load balancer** to distribute traffic
* Implement **rate limiting**
* Add **caching layer**

### Architecture

```
Users
 ↓
CDN
 ↓
Load Balancer
 ↓
Auto Scaling Application Servers
 ↓
Redis Cache
 ↓
Database
```

### Tools

* AWS Auto Scaling
* Nginx / API Gateway
* Redis
* CloudFront / Cloudflare

---

# 2. Kubernetes pods keep crashing (CrashLoopBackOff). How do you debug?

### Steps

Check pod status

```
kubectl get pods
```

Check logs

```
kubectl logs <pod>
```

Check events

```
kubectl describe pod <pod>
```

### Common Causes

* Application crash
* Wrong environment variables
* Liveness probe failure
* Memory limits exceeded

---

# 3. CI/CD pipeline suddenly takes 45 minutes instead of 10 minutes.

### Investigation

Check:

* Build logs
* Dependency downloads
* Docker build caching
* Test execution time

### Optimization

* Enable caching
* Parallel builds
* Pre-built Docker images
* Artifact reuse

---

# 4. Database CPU suddenly spikes to 95%.

### Steps

1. Identify slow queries
2. Check connection spikes
3. Analyze query plans

Example commands

```
SHOW PROCESSLIST;
EXPLAIN SELECT ...
```

### Solutions

* Add indexes
* Enable query caching
* Use read replicas

---

# 5. Users report slow application response.

### Investigation Layers

1. Application logs
2. Infrastructure metrics
3. Database queries
4. Network latency

### Tools

* Prometheus
* Grafana
* New Relic
* Datadog

---

# 6. One microservice is failing but others are working.

### Debugging Steps

1. Check service logs
2. Verify service health
3. Check dependencies
4. Verify service discovery

Commands

```
kubectl logs <pod>
kubectl describe service <service>
```

---

# 7. Disk usage on server reaches 100%.

### Investigation

Check disk usage

```
df -h
```

Find large files

```
du -sh *
```

### Solutions

* Clean old logs
* Rotate logs using logrotate
* Move data to external storage
* Expand disk volume

---

# 8. Application suddenly crashes in production.

### Steps

1. Check application logs
2. Check system resources
3. Verify recent deployments
4. Rollback if needed

### Monitoring Tools

* Grafana
* Prometheus
* ELK Stack

---

# 9. Kubernetes cluster nodes become NotReady.

### Investigation

Check node status

```
kubectl get nodes
```

Describe node

```
kubectl describe node <node>
```

### Common Causes

* Network failure
* Kubelet stopped
* Resource exhaustion

---

# 10. Your website is down but servers are running.

### Investigation

1. Check load balancer
2. Check DNS configuration
3. Verify SSL certificates
4. Check application logs

Tools

* Route53
* Nginx
* AWS ELB

---

# 11. Deployment caused production outage.

### Steps

1. Immediately rollback deployment
2. Restore previous version
3. Analyze logs
4. Identify root cause

Example

```
kubectl rollout undo deployment app
```

---

# 12. Memory usage keeps increasing in application containers.

### Possible Cause

Memory leak.

### Investigation

* Monitor memory usage
* Analyze application logs
* Restart containers temporarily

Commands

```
docker stats
kubectl top pods
```

---

# 13. Kubernetes service is not reachable.

### Debugging

Check service

```
kubectl get svc
```

Check endpoints

```
kubectl get endpoints
```

Verify pod labels match service selectors.

---

# 14. Log files grow too large.

### Solution

Implement **log rotation**

Example configuration

```
/var/log/app.log {
    daily
    rotate 7
    compress
}
```

---

# 15. Application deployment needs zero downtime.

### Deployment Strategies

* Rolling Deployment
* Blue-Green Deployment
* Canary Deployment

Example

```
kubectl rollout status deployment app
```

---

# 16. Users cannot log in to the application.

### Investigation

1. Check authentication service
2. Verify database connection
3. Check login endpoint logs
4. Validate credentials storage

---

# 17. SSL certificate suddenly expires.

### Solution

Use automated certificate management.

Tools

* Let's Encrypt
* Certbot
* AWS ACM

Automate renewal.

---

# 18. Kubernetes cluster runs out of resources.

### Solutions

* Enable cluster auto-scaling
* Optimize resource limits
* Scale nodes

Commands

```
kubectl top nodes
```

---

# 19. CI pipeline fails randomly.

### Investigation

Check

* Network issues
* Dependency downloads
* Build agent stability

Solutions

* Retry mechanisms
* Stable build agents
* Dependency caching

---

# 20. Monitoring alerts trigger frequently.

### Steps

1. Check alert thresholds
2. Identify false positives
3. Adjust monitoring rules

Tools

* Prometheus Alertmanager
* Grafana alerts

---

# Summary

A **4–6 year DevOps engineer** is expected to handle:

* Production troubleshooting
* Infrastructure scaling
* CI/CD optimization
* Monitoring and alerting
* Kubernetes debugging
* Incident response
* High availability architecture

---
