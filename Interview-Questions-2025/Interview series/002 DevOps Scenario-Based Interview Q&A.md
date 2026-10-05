# 🚀 DevOps Scenario-Based Interview Q&A (4+ Years Experience)

**Experience Level:** 4+ Years DevOps / Cloud Engineer  
**Mindset:** Monitoring → Rollback → Debug → Root Cause → Prevention  
**Golden Rule:** Never say “restart server.” Say Rollback + RCA + Prevention.

---

# 1️⃣ Production Outage

## ❓ Your production website is down. What do you do?

### ✅ Senior-Level Answer

### 1. Check Monitoring First
- Grafana / CloudWatch dashboards
- CPU, Memory, Disk, Network
- 5xx error rate
- Pod restarts
- Node health
- Load Balancer health checks

### 2. Check Recent Changes
- Jenkins / ArgoCD latest deployment
- Recent Git commits
- Terraform changes

### 3. Rollback If Deployment Caused Issue
```bash
kubectl rollout undo deployment <app-name>
```
- Revert to last stable Docker image
- Rollback via ArgoCD if using GitOps

### 4. Check Logs
```bash
kubectl logs <pod>
kubectl describe pod <pod>
kubectl get events
```
- Application logs
- Nginx logs
- Database logs

### 5. Scale or Restart Targeted Components
- Increase replicas if traffic spike
- Restart only failing pods

### 6. Identify Root Cause
- DB connection timeout
- Memory leak
- Misconfigured environment variable
- Failed migration

### 7. Post-Mortem
- Timeline
- Root cause
- Impact
- Resolution
- Preventive measures
- Add alerts if missing

---

# 2️⃣ Kubernetes Pods in CrashLoopBackOff

## ❓ Pods are crashing. How do you debug?

### Debug Commands
```bash
kubectl describe pod <pod>
kubectl logs <pod>
kubectl get events
kubectl exec -it <pod> -- sh
```

### What I Check
- Environment variables
- Secrets & ConfigMaps
- DB connectivity
- Application startup errors
- Resource limits (CPU/Memory)

### Common Causes
- Wrong DB host
- Missing secret
- Image build issue
- Low memory limit

### Fix
- Correct configuration
- Increase resources
- Rebuild image
- Redeploy

---

# 3️⃣ Terraform Broke Production

## ❓ Terraform apply deleted a live resource. What went wrong?

### Likely Mistakes
- No approval workflow
- No terraform plan review
- No remote state locking
- Same state for dev & prod
- Manual apply from local machine

### Correct Production Design
- GitOps workflow
- terraform plan → PR review → terraform apply
- Separate state per environment
- S3 backend for state
- DynamoDB for locking

### Prevention
- Mandatory PR approvals
- Restricted production access
- Separate workspaces
- Enable state locking

---

# 4️⃣ Zero-Downtime Deployment

## ❓ How do you deploy without users noticing?

### Strategy
- Kubernetes Rolling Update
- Proper Readiness & Liveness probes
- Load Balancer health checks
- Canary or Blue-Green deployment
- Automatic rollback

### Flow
1. Deploy new pods
2. Wait for readiness success
3. Gradually remove old pods
4. Monitor metrics
5. Auto rollback if unhealthy

---

# 5️⃣ Database Change Caused Failure

## ❓ New version broke DB queries. What do you do?

### Immediate Action
- Rollback application version
- Stop faulty deployment
- Check migration logs
- Restore DB snapshot if corrupted

### Root Cause
- Non backward-compatible schema
- Dropped/modified column used by old version

### Prevention
- Backward-compatible migrations
- Versioned migration tools
- Deploy in canary mode first
- Test migrations in staging

---

# 6️⃣ CI/CD Pipeline is Too Slow

## ❓ Build takes 40 minutes. How do you optimize?

### Improvements
- Parallel pipeline stages
- Cache dependencies (Maven/NPM/Pip)
- Docker layer caching
- Remove unnecessary tests
- Use smaller base images (Alpine)
- Run tests in parallel

### Result
- Faster build time
- Improved feedback cycle

---

# 7️⃣ Secrets Exposed in Git

## ❓ Someone committed AWS keys. What do you do?

### Immediate Action
1. Revoke keys immediately
2. Rotate all related secrets
3. Audit logs (CloudTrail)
4. Remove secrets from Git history (git filter-repo / BFG)

### Prevention
- Use AWS Secrets Manager / Vault
- Store secrets in environment variables
- Add pre-commit hooks
- Enable secret scanning

---

# 8️⃣ One Pod Gets All Traffic

## ❓ Only one pod is overloaded. Why?

### Possible Causes
- Session affinity enabled
- Readiness probe misconfigured
- Service not load balancing correctly
- Ingress misconfiguration

### Fix
- Disable session affinity (if not required)
- Correct readiness probe
- Verify Service selectors
- Validate Ingress configuration

---

# 9️⃣ Kubernetes Node Crashed

## ❓ One node died. What happens?

### Expected Behavior
- Pods rescheduled to healthy nodes
- Load Balancer redirects traffic
- Auto Scaling launches new node
- Minimal or zero downtime

### Best Practice
- Multiple replicas
- Multi-AZ cluster
- HPA enabled
- Cluster autoscaler configured

---

# 🔟 Release Caused High Latency

## ❓ After deployment, response time increased. What do you do?

### Investigation
- Compare old vs new version
- Check CPU & memory usage
- Analyze DB queries
- Check external API calls
- Monitor APM tools

### Immediate Action
- Rollback to stable version
- Identify slow query or bug
- Fix issue before redeploy

### Prevention
- Performance testing before release
- Canary deployment
- APM monitoring

---

# 🎯 How to Answer Like a Senior DevOps Engineer

Always show:
1. Monitoring first
2. Rollback strategy
3. Structured debugging
4. Root Cause Analysis
5. Prevention & automation
6. Documentation (Post-mortem)

---

