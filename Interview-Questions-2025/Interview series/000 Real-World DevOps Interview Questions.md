# 🚀 Real-World DevOps Interview Questions (4+ Years Experience)

This document contains structured, interview-ready answers for real-world DevOps questions covering:

- CI/CD & Deployment Strategy
- Kubernetes
- Terraform & Infrastructure
- AWS Production
- Monitoring & SRE
- Production Troubleshooting

Answers are written in a practical, spoken format suitable for 4+ years of experience.

---

# 🔹 CI/CD & Deployment Strategy

## Q1. How do you design a CI/CD pipeline for a microservices-based application?

For a microservices-based architecture, I design a modular CI/CD pipeline where each service has its own independent pipeline.

The flow typically looks like:

Git (GitHub/Bitbucket) → Build → Test → Security Scan → Package → Push to Registry → Deploy

I use:
- Jenkins or GitHub Actions for CI
- Docker for containerization
- SonarQube & Trivy for code and image scanning
- Helm for Kubernetes packaging
- ArgoCD for GitOps-based deployment

For deployment strategies, I prefer:
- Rolling updates for normal releases
- Blue-Green for major releases
- Canary deployments for risky features

Rollback strategy:
- For CI/CD-based push deployments → revert image tag
- For GitOps → revert Git commit and ArgoCD sync handles rollback automatically

This ensures automation, traceability, and minimal downtime.

---

## Q2. How do you prevent a bad deployment from breaking production?

I follow multiple safety layers:

1. Canary Deployment – Release to small % of traffic first  
2. Feature Flags – Disable feature without redeploying  
3. Health Checks – Liveness & Readiness probes  
4. Automated Rollback – Triggered if metrics cross threshold  
5. SLO/SLA Monitoring – Monitor latency, error rate, availability  

We integrate Prometheus alerts with CI/CD so if error rate increases beyond threshold, rollback is triggered automatically.

This reduces blast radius and protects production stability.

---

## Q3. What is the difference between GitOps and CI/CD?

CI/CD:
- Push-based model
- Jenkins/GitHub Actions deploy directly
- Rollback can be manual
- Credentials stored in pipeline

GitOps:
- Pull-based model
- ArgoCD watches Git repo
- Deployment state always matches Git
- Rollback = revert Git commit
- More secure (cluster pulls changes)

In GitOps, Git is the single source of truth.

---

# 🔹 Kubernetes (Must Know)

## Q4. How does Kubernetes decide where to run a Pod?

The Kubernetes Scheduler is responsible.

It considers:

- Node resource availability (CPU/Memory)
- Taints & tolerations
- Node affinity rules
- Pod anti-affinity rules
- Node selectors
- Resource requests & limits

Scheduler filters nodes first, then scores them, then binds Pod to best node.

---

## Q5. What happens when a Pod crashes?

If a Pod crashes:

1. kubelet detects container failure
2. restartPolicy determines restart behavior
3. If part of Deployment → ReplicaSet ensures desired replicas
4. If continuously failing → CrashLoopBackOff
5. Liveness probe may restart container
6. Controller recreates Pod if needed

Kubernetes ensures desired state is maintained.

---

## Q6. How do you do zero-downtime deployment in Kubernetes?

I use Rolling Update strategy.

Key components:
- Readiness probe ensures traffic only goes to healthy pods
- Service load balances traffic
- maxUnavailable and maxSurge configured properly
- HPA can scale pods if needed

This ensures no downtime during deployment.

---

# 🔹 Terraform & Infrastructure

## Q7. How do you manage multiple environments in Terraform?

I manage environments using:

Option 1: Separate environment folders (dev/qa/prod)
Option 2: Workspaces
Option 3: Separate state files

Backend:
- S3 for remote state
- DynamoDB for state locking

Each environment has isolated state to avoid accidental impact.

---

## Q8. How do you avoid breaking production while applying Terraform?

I follow strict workflow:

1. terraform plan before apply
2. Code review via pull request
3. GitOps flow
4. Remote backend with state locking
5. Apply only via CI/CD pipeline

No manual terraform apply in production.

This ensures controlled infrastructure changes.

---

## Q9. How do you handle secrets in Terraform?

Best practices:

- Use HashiCorp Vault
- Use AWS Secrets Manager
- Use encrypted environment variables in pipeline
- Mark variables as sensitive
- Never store secrets in tfvars or Git

Secrets are injected at runtime, not stored in code.

---

# 🔹 Cloud & AWS Production

## Q10. What happens if an EC2 instance running Kubernetes dies?

If worker node dies:

1. Auto Scaling Group detects failure
2. Launches new EC2 instance
3. Node joins cluster
4. Pods rescheduled automatically
5. Load Balancer reroutes traffic

High availability is maintained automatically.

---

## Q11. How do you make applications highly available in AWS?

I design for HA using:

- Multi-AZ architecture
- Application Load Balancer
- Auto Scaling Group
- RDS Multi-AZ
- Kubernetes replicas
- Health checks enabled

No single point of failure.

---

# 🔹 Monitoring & Reliability

## Q12. What do you monitor in production Kubernetes?

I monitor:

- Pod CPU & memory usage
- Node health
- Disk usage
- API server health
- Latency
- Error rate
- Restart count

Tools:
- Prometheus
- Grafana
- Alertmanager

Monitoring is aligned with SLOs.

---

## Q13. What is SRE Error Budget?

Error Budget is:

Allowed failure rate before we stop deploying new features.

If service exceeds error budget, focus shifts from feature release to reliability improvements.

It balances innovation and stability.

---

# 🔹 DevOps Troubleshooting (Most Important)

## Q14. Production is down. Website not loading. What do you do?

My approach:

1. Check monitoring dashboard
2. Check recent deployments
3. Rollback if needed
4. Check logs
5. Identify root cause
6. Fix issue
7. Conduct post-mortem

Never panic. Follow structured debugging.

---

## Q15. Pod is in CrashLoopBackOff — how do you debug?

Steps:

kubectl logs <pod>
kubectl describe pod <pod>
kubectl exec -it <pod> -- /bin/sh

Then check:
- Environment variables
- ConfigMaps/Secrets
- Database connectivity
- Resource limits

Most common causes:
- Wrong configuration
- App crash
- DB connection failure
- OOMKilled

---

# 🎯 Final Interview Tip (4+ Years Level)

Always answer in:

Problem → Approach → Tools → Production Example → Outcome

Speak confidently. Show production mindset. Emphasize automation, monitoring, rollback, and reliability.

---

⭐ This document is designed for real DevOps interviews (4+ years experience).
