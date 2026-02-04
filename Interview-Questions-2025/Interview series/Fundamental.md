# 𝐃𝐞𝐯𝐎𝐩𝐬 𝐅𝐮𝐧𝐝𝐚𝐦𝐞𝐧𝐭𝐚𝐥𝐬 𝐘𝐨𝐮 𝐌𝐮𝐬𝐭 𝐎𝐰𝐧  
*(Expected Knowledge for ~4 Years DevOps Experience)*

This README captures **real-world DevOps fundamentals** along with their **original interview-style questions** and **practical answers** expected from a DevOps engineer with around **4 years of experience**.

---

## 1. What’s the real difference between CPU bottleneck vs I/O bottleneck, and how do you identify each?

### CPU Bottleneck
- CPU usage consistently high (80–100%)
- Load average higher than number of cores
- Threads waiting for CPU time

**How to identify**
- `top`, `htop`
- High user/system CPU, low idle CPU

### I/O Bottleneck
- CPU mostly idle
- High disk or network wait
- Requests slow despite low CPU usage

**How to identify**
- `iostat`, `vmstat`
- High `iowait`
- Slow DB, disk, or network calls

**Key Insight:**  
High latency does not always mean high CPU usage.

---

## 2. Why do systems fail even when CPU and memory look fine?

Because CPU and memory are **not the only failure points**.

Common hidden causes:
- Disk I/O saturation
- Network latency or packet loss
- Database connection pool exhaustion
- Thread locks or deadlocks
- Slow external dependencies
- OS limits (file descriptors, ports)

**Key Insight:**  
Healthy CPU and memory do not guarantee a healthy system.

---

## 3. What actually happens when a Kubernetes Pod restarts?

- The Pod is terminated and recreated
- Containers start as brand-new processes
- New PID and container ID
- Ephemeral storage is lost
- Persistent volumes remain (if attached)
- Traffic interruption occurs if readiness is misconfigured

**Key Insight:**  
A Pod restart is not a reboot — it is a recreation.

---

## 4. Difference between readiness and liveness probes, and how each can break production

### Liveness Probe
- Checks if the application is alive
- Failure causes container restart
- Misconfiguration leads to restart loops

### Readiness Probe
- Checks if application can receive traffic
- Failure removes Pod from Service endpoints
- Misconfiguration routes traffic too early

**Key Insight:**  
Most Kubernetes outages are caused by probe misconfiguration.

---

## 5. What problem does infrastructure as code solve, and when can it become dangerous?

### Problems IaC Solves
- Configuration drift
- Manual provisioning errors
- Environment inconsistency
- Lack of audit trail

### When IaC Becomes Dangerous
- Unprotected or shared Terraform state
- No code reviews
- Manual changes outside IaC
- Blind `terraform apply` in production

**Key Insight:**  
IaC without discipline increases risk instead of reducing it.

---

## 6. How does Terraform state work, and what happens when it drifts?

### Terraform State
- Maps declared configuration to real cloud resources
- Acts as the source of truth

### State Drift
- Manual cloud changes
- Cloud auto-modifies resources
- State no longer matches actual infrastructure

### Impact
- Unexpected resource destruction
- Failed deployments
- Production outages

**Key Insight:**  
Terraform state should be treated like production data.

---

## 7. What’s the difference between horizontal and vertical scaling, and when should you avoid autoscaling?

### Vertical Scaling
- Increase CPU/RAM of a machine
- Simple and quick
- Limited and often causes downtime

### Horizontal Scaling
- Add more instances or Pods
- Improves availability and resilience
- Requires stateless application design

### Avoid Autoscaling When
- Application is stateful
- Startup time is slow
- Traffic patterns are predictable
- Backend systems (DB) cannot scale

**Key Insight:**  
Autoscaling broken architecture only scales failure.

---

## 8. Why do deployments succeed but users still see errors?

Because CI/CD success does not guarantee runtime success.

Common reasons:
- Incorrect environment variables
- Configuration mismatch
- Cache-related issues
- Database migration failures
- Traffic routed before readiness
- Feature flag misconfiguration

**Key Insight:**  
A green pipeline does not mean a healthy application.

---

## 9. How do you decide between rollback vs hotfix during an incident?

### Choose Rollback When
- Issue was introduced by a recent deployment
- Root cause is unclear
- User impact is high
- Rollback is quick and safe

### Choose Hotfix When
- Root cause is clearly identified
- Rollback causes more disruption
- Data migrations are involved

**Key Insight:**  
During incidents, speed matters more than perfection.

---

## 10. What’s the difference between metrics, logs, and traces, and when do you start with each?

### Metrics
- Show **what** is wrong
- CPU, memory, latency, error rates
- First alerting signal

### Logs
- Explain **why** it is wrong
- Errors, stack traces, events

### Traces
- Show **where** it is wrong
- End-to-end request flow across services

### Troubleshooting Order
1. Metrics  
2. Logs  
3. Traces  

**Key Insight:**  
Observability is about asking the right question at the right time.

---

## 🎯 Purpose
- Build strong production intuition
- Prepare for senior-level DevOps interviews
- Design and operate reliable systems

---
