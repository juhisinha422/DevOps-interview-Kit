𝗜𝗻𝘁𝗲𝗿𝘃𝗶𝗲𝘄𝗲𝗿: 𝗬𝗼𝘂 𝗵𝗮𝘃𝗲 𝟮 𝗺𝗶𝗻𝘂𝘁𝗲𝘀. 𝗬𝗼𝘂𝗿 𝗞𝘂𝗯𝗲𝗿𝗻𝗲𝘁𝗲𝘀 𝗽𝗼𝗱 𝗶𝘀 𝗿𝘂𝗻𝗻𝗶𝗻𝗴 𝗯𝘂𝘁 𝘂𝘀𝗲𝗿𝘀 𝗮𝗿𝗲 𝗴𝗲𝘁𝘁𝗶𝗻𝗴 𝟱𝟬𝟯. 𝗪𝗵𝗮𝘁 𝗱𝗼 𝘆𝗼𝘂 𝗱𝗼?

𝗠𝘆 𝗮𝗻𝘀𝘄𝗲𝗿: 𝗖𝗵𝗮𝗹𝗹𝗲𝗻𝗴𝗲 𝗮𝗰𝗰𝗲𝗽𝘁𝗲𝗱, 𝗹𝗲𝘁’𝘀 𝗴𝗼!

► 503 means the pod is running but not serving traffic correctly.

► Service misconfiguration — selector labels on Service don’t match pod labels. Traffic goes nowhere.

► Pod not ready — Readiness probe is failing. Pod is running but not ready to accept traffic.

► Wrong target port — Service is pointing to wrong container port.

► All pods are crashing — Deployment has 0 ready pods. Check replicaset.

► Resource limits — Pod is OOMKilled due to memory limits. Gets restarted repeatedly.

► Ingress misconfiguration — Wrong path or host rules in ingress resource.

► Network Policy — Blocking traffic between service and pod.

𝗛𝗼𝘄 𝗜 𝗱𝗲𝗯𝘂𝗴 𝗮𝘀 𝗮 𝗗𝗲𝘃𝗢𝗽𝘀 𝗘𝗻𝗴𝗶𝗻𝗲𝗲𝗿:
• kubectl get pods — check pod status
• kubectl describe pod — check events and errors
• kubectl logs pod-name — check app logs
• kubectl get svc — verify service selector matches pod labels
• kubectl get endpoints — if empty, label mismatch is the issue
• kubectl describe ingress — check routing rules
• curl pod-ip directly — bypass service to isolate issue


=========

# Scenario-Based DevOps Interview Questions & Answers (3+ Years Experience)

## 1️⃣ Your microservice works in isolation but breaks in the cluster. How do you debug networking issues inside Kubernetes?

First, I would verify whether the issue is application-related or Kubernetes networking-related. I would start by checking pod status, logs, readiness probes, and service endpoints. Then I would verify whether the pod is reachable internally from another pod using commands like `curl`, `ping`, or `nslookup`.

I would check:

* Pod IP
* Service configuration
* Endpoints
* DNS resolution
* Network policies
* Ingress rules

I usually troubleshoot in this sequence:

1. Verify pod health
2. Verify service selectors
3. Check endpoints
4. Test DNS resolution using CoreDNS
5. Check network policies/firewall restrictions
6. Verify ingress/load balancer routing

Commands commonly used:

```bash id="4m6qza"
kubectl get svc
kubectl get endpoints
kubectl describe svc
kubectl exec -it <pod> -- nslookup service-name
kubectl logs -n kube-system coredns
```

In one real incident, pod-to-pod communication failed because CoreDNS pods were crashing due to memory pressure. We increased CoreDNS memory limits and restarted the deployment, which resolved the issue.

---

## 2️⃣ Your team pushed 50 commits today. CI ran for every single one. How do you optimize the pipeline without breaking quality?

Running full CI pipelines for every commit wastes compute resources and increases feedback time. I would optimize the pipeline using multiple strategies.

First, I would separate pipelines into stages:

* Fast validation pipeline for every commit
* Full regression pipeline for merge requests or release branches

I would implement:

* Incremental builds
* Dependency caching
* Parallel job execution
* Test splitting
* Conditional pipeline triggers
* Branch-based execution rules

For example:

* Unit tests and linting run on every commit
* Integration and security tests run only on PR merge or scheduled runs

I would also use Docker layer caching and reusable artifacts to reduce build time.

In one project, pipeline execution time was reduced from 45 minutes to 15 minutes by introducing caching, parallel stages, and selective test execution.

---

## 3️⃣ A Terraform variable was hardcoded with a prod secret by mistake. How do you handle secrets management the right way?

Hardcoding secrets is a major security risk because secrets can leak through Git history, Terraform state files, logs, or CI pipelines.

The first step would be:

* Immediately rotate the exposed secret
* Remove the secret from code
* Clean Git history if required
* Audit usage and access logs

For proper secrets management, I prefer:

* AWS Secrets Manager
* HashiCorp Vault
* Azure Key Vault
* Kubernetes Secrets with encryption enabled

In Terraform:

* Sensitive variables should use `sensitive = true`
* Secrets should come from secret managers dynamically
* Never store secrets in `.tfvars` files inside repositories

I also ensure:

* IAM-based access control
* Secret rotation policies
* CI/CD masking
* Restricted RBAC permissions

In production environments, I usually integrate Terraform with AWS Secrets Manager so pipelines fetch secrets securely during runtime.

---

## 4️⃣ Your Docker image size is 2.1 GB and deployment is slow. How do you bring it under 200 MB?

Large Docker images slow down:

* CI/CD pipelines
* Deployments
* Autoscaling
* Node pull times

I would first analyze the image using tools like:

```bash id="vthbsm"
docker history
docker image inspect
```

Then I would optimize using:

* Lightweight base images like Alpine
* Multi-stage builds
* Removing unnecessary packages
* Cleaning cache files
* Using `.dockerignore`
* Combining RUN commands
* Avoiding build tools in final image

For example:

* Build application in one stage
* Copy only compiled artifacts to final runtime image

In one Node.js project, we reduced image size from 1.8 GB to around 180 MB using Alpine images and multi-stage builds.

This significantly improved deployment speed and Kubernetes scaling performance.

---

## 5️⃣ An incident happened at 3 AM. No one was alerted. How do you redesign your on-call and alerting setup?

First, I would perform RCA to understand why alerts failed:

* Alert rule misconfiguration
* Monitoring failure
* Wrong thresholds
* Notification channel failure
* Alert fatigue causing ignored alerts

Then I would redesign the alerting strategy.

I would implement:

* Proper severity levels (P1/P2/P3)
* Escalation policies
* On-call rotation schedules
* Multi-channel alerts
* Alert deduplication
* Service-level monitoring

Tools commonly used:

* Prometheus Alertmanager
* PagerDuty
* Opsgenie
* Grafana Alerts
* CloudWatch Alarms

Critical production alerts should notify through:

* Phone call
* SMS
* Slack
* Email

I also prefer defining SLO/SLI-based alerting instead of infrastructure-only alerts.

After one missed incident in production, we redesigned our alerting system with PagerDuty escalation and health-check monitoring, which significantly improved incident response time.

---

## 6️⃣ Leadership wants a report of every infra change made last quarter. How do you produce it using your existing DevOps tools?

I would collect infrastructure change history from multiple DevOps systems.

Main sources:

* Git repositories
* Terraform state/version history
* CI/CD deployment logs
* Kubernetes audit logs
* CloudTrail/AWS Config
* Jenkins/GitLab pipeline history

The process would include:

1. Extract merged PRs and commits
2. Review Terraform apply history
3. Validate deployment timestamps
4. Correlate infrastructure changes with change requests

In AWS environments:

* CloudTrail helps track API-level infrastructure changes
* Terraform Git history provides IaC-level traceability

I usually prepare:

* Change summary
* Impacted resources
* Deployment timeline
* User/team responsible
* Rollback history if applicable

This provides leadership with complete auditability and compliance visibility.

---

## 7️⃣ You joined a new team and their pipeline has no documentation. Where do you start?

First, I would avoid making immediate changes without understanding the existing workflow.

I would start by:

1. Understanding application architecture
2. Reviewing pipeline YAML/Jenkinsfile
3. Identifying build, test, scan, and deploy stages
4. Mapping environments and dependencies
5. Reviewing secrets and credentials handling
6. Understanding rollback and approval flow

Then I would:

* Run the pipeline in lower environment
* Review logs
* Speak with developers and senior engineers
* Create flow diagrams and documentation

I usually document:

* Trigger mechanism
* Pipeline stages
* Tools used
* Deployment strategy
* Environment variables
* Failure handling
* Rollback process

In one project, the pipeline had no documentation and only one senior engineer understood it. I created detailed Confluence documentation and architecture flowcharts, which reduced onboarding and troubleshooting time for the entire team.

