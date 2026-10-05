# Real DevOps Interview Questions – Detailed Answers (4+ Years Experience)

---

## 1. What exactly happens when a Kubernetes pod gets OOMKilled?
- OOMKilled occurs when a container exceeds its memory limit.
- Kubernetes uses cgroups to enforce memory limits.
- When exceeded:
  - Linux kernel OOM killer terminates the container
  - Pod status shows `OOMKilled`
  - Container restarts based on restart policy

**Fix:**
- Increase memory limits
- Optimize application memory usage
- Use proper requests & limits

---

## 2. Your CI/CD pipeline works in staging but fails in production with permission denied - how do you debug?
- Compare IAM roles / service accounts
- Check:
  - File permissions
  - Secrets/config differences
  - Environment variables
  - RBAC (Kubernetes)
- Verify logs:
  - Jenkins / pipeline logs
  - Application logs
- Reproduce issue in lower environment

---

## 3. How does Kubernetes DNS work across namespaces, and what can break it?
- Kubernetes DNS (CoreDNS) resolves services:
  - service-name.namespace.svc.cluster.local
- Cross-namespace access requires full DNS name

**Break scenarios:**
- CoreDNS crash
- Network policies blocking DNS
- Wrong service name
- kube-dns misconfiguration

---

## 4. Terraform state lock is stuck - how do you recover safely?
- Identify lock holder
- Wait if active process running
- If stale:
  ```
  terraform force-unlock <LOCK_ID>
  ```
- Ensure no parallel execution
- Use DynamoDB locking properly

---

## 5. How do you design observability (metrics, logs, traces) for high-scale systems?
- Metrics: Prometheus (CPU, memory, latency)
- Logs: ELK / Loki
- Traces: Jaeger / Zipkin
- Use centralized dashboard (Grafana)
- Add alerts (SLI/SLO based)

---

## 6. A secret was exposed in GitHub - what’s your incident response plan?
- Immediately revoke/rotate secret
- Remove from repo (git history cleanup)
- Check logs for misuse
- Update secrets in vault/manager
- Implement secret scanning tools

---

## 7. How does HPA work internally in Kubernetes?
- HPA uses metrics-server
- Monitors CPU/memory/custom metrics
- Adjusts replica count:
  ```
  desiredReplicas = currentReplicas * (currentMetric / targetMetric)
  ```

---

## 8. What is error budget & burn rate in SRE?
- Error Budget = Allowed failure (based on SLO)
- Burn Rate = Speed at which error budget is consumed

Used to balance reliability vs feature releases

---

## 9. Your service has latency spikes every 60 seconds - how do you debug?
- Check cron jobs / batch jobs
- GC (Garbage Collection)
- Autoscaling events
- DB queries or locks
- Use metrics + tracing to identify bottleneck

---

## 10. Push vs Pull CD - how does GitOps (ArgoCD) actually work?
- Push: CI pushes deployment to cluster
- Pull (GitOps):
  - ArgoCD watches Git repo
  - Detects changes
  - Pulls and applies to cluster

Benefits:
- Declarative
- Version-controlled
- Secure

---

## 11. What are Dockerfile best practices for security and performance?
- Use minimal base images (alpine)
- Multi-stage builds
- Avoid root user
- Reduce layers
- Use .dockerignore
- Scan images for vulnerabilities

---

## 12. What is Platform Engineering and how would you design an IDP?
- Platform Engineering builds internal platforms for developers

**IDP (Internal Developer Platform):**
- Self-service deployments
- Predefined templates (Helm/Terraform)
- CI/CD automation
- Observability integration
- RBAC & security controls

---

## What Companies Are Testing
- Debugging skills
- System design thinking
- Real production experience
- Problem-solving approach
