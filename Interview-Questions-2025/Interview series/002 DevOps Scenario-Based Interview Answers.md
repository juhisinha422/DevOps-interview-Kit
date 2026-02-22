# 🚀 DevOps Scenario-Based Interview Answers (4+ Years Experience)

**Experience Level:** 4+ Years DevOps / Cloud Engineer  
**Approach:** Monitoring → Rollback → Debugging → Root Cause Analysis → Prevention  
**Golden Rule:** Never say “restart the server.” Always say rollback with RCA and prevention.

---

# 1️⃣ Production Outage

## ❓ Your production website is down. What do you do?

If the production website is down, my first step is to stay calm and immediately check monitoring dashboards like Grafana or CloudWatch to understand the current system health. I analyze CPU, memory, disk usage, network traffic, 5xx error rates, pod restarts, node health, and load balancer target status. This helps me determine whether it is a resource issue, infrastructure issue, or application-level failure.

Next, I verify whether any recent deployment was done through Jenkins or ArgoCD. I check recent Git commits and infrastructure changes such as Terraform apply logs. If the issue started immediately after deployment, I immediately roll back to the previous stable version using Kubernetes rollout undo or by reverting to the previous Docker image.

After stabilizing traffic, I check logs using kubectl logs, kubectl describe pod, and kubectl get events. I review application logs, Nginx logs, and database logs to identify the exact failure point. If the issue is due to load spike, I scale replicas. If it is a configuration issue, I fix and redeploy safely.

Once service is restored, I perform a detailed root cause analysis and prepare a post-mortem including timeline, impact, root cause, resolution steps, and preventive actions. I also ensure proper monitoring alerts are added to prevent recurrence.

---

# 2️⃣ Kubernetes Pods in CrashLoopBackOff

## ❓ Pods are crashing. How do you debug?

When pods are in CrashLoopBackOff, I first describe the pod to check recent events and error messages. Then I check container logs to see why the application is failing during startup. I also inspect environment variables, secrets, configmaps, and database connectivity. Many times the issue is caused by incorrect environment configuration, missing secrets, wrong database host, or insufficient memory limits.

If needed, I exec into the container to manually test connectivity or inspect files. I verify resource requests and limits to ensure the pod is not being killed due to OOM. Once I identify the issue, I fix the configuration or code, rebuild the image if required, and redeploy the application.

---

# 3️⃣ Terraform Deleted a Production Resource

## ❓ A Terraform apply deleted a live resource. What went wrong?

If Terraform deleted a production resource, it usually indicates a process failure rather than just a tool failure. This typically happens when there is no proper approval workflow, no terraform plan review, no remote state locking, or when the same state file is used for multiple environments. Sometimes manual terraform apply from a local machine also causes accidental deletion.

In a proper production setup, Terraform should follow a GitOps workflow where terraform plan is reviewed in a pull request before terraform apply is executed through a CI pipeline. Separate state files must be maintained for each environment using an S3 backend with DynamoDB locking to prevent concurrent modifications.

To prevent recurrence, I would enforce mandatory PR approvals, restrict production access, enable state locking, and implement environment separation strictly.

---

# 4️⃣ Zero-Downtime Deployment

## ❓ How do you deploy without users noticing?

To achieve zero-downtime deployment, I use Kubernetes rolling updates with properly configured readiness and liveness probes. The readiness probe ensures traffic is only sent to healthy pods. When deploying a new version, Kubernetes gradually brings up new pods and waits until they are ready before terminating old ones.

For higher safety, I may use canary or blue-green deployment strategies. Canary allows a small percentage of traffic to hit the new version before full rollout. If any issue is detected via monitoring, automatic rollback is triggered. This ensures users do not experience downtime.

---

# 5️⃣ Database Change Caused Failure

## ❓ New version broke database queries. What do you do?

If a deployment breaks database queries, I immediately roll back the application to the previous stable version to restore service. Then I check database migration logs to understand what schema change caused the failure. If necessary and data is corrupted, I restore from a recent snapshot.

Most database failures occur due to non backward-compatible schema changes such as dropping or renaming columns used by older application versions. To prevent this, I always ensure migrations are backward compatible. I also deploy database changes in staging first and use canary deployment in production to minimize impact.

---

# 6️⃣ CI/CD Pipeline is Too Slow

## ❓ Build takes 40 minutes. How do you optimize?

If a build takes 40 minutes, I analyze which stage consumes the most time. I optimize the pipeline by running independent stages in parallel and enabling dependency caching for tools like Maven, NPM, or Pip. I also use Docker layer caching to prevent rebuilding unchanged layers.

Additionally, I remove unnecessary tests from production builds, use lightweight base images such as Alpine, and split test execution into parallel jobs. These improvements significantly reduce build time and improve developer productivity.

---

# 7️⃣ Secrets Exposed in Git

## ❓ Someone committed AWS keys. What do you do?

If AWS keys are exposed in Git, I immediately revoke the keys to prevent misuse and rotate all related credentials. Then I audit CloudTrail logs to check whether the keys were used maliciously. After that, I remove the secrets from Git history using tools like git filter-repo or BFG.

To prevent such incidents, I use AWS Secrets Manager or Vault for secret storage, avoid hardcoding secrets, enable secret scanning in repositories, and implement pre-commit hooks to block accidental commits of sensitive data.

---

# 8️⃣ One Pod Gets All Traffic

## ❓ Only one pod is overloaded. Why?

If only one pod is receiving all traffic, I first check whether session affinity is enabled in the service configuration. Session affinity can cause traffic to stick to a single pod. I also verify readiness probe configuration because if other pods are not marked ready, traffic will not be distributed evenly.

I check service selectors and ingress configuration to ensure proper load balancing. Once the misconfiguration is identified, I correct it and monitor traffic distribution.

---

# 9️⃣ Kubernetes Node Crashed

## ❓ One node died. What happens?

In a properly designed Kubernetes cluster, if a node crashes, the pods running on that node are automatically rescheduled to other healthy nodes. The load balancer redirects traffic to healthy pods. If cluster autoscaler is enabled, a new node is automatically provisioned.

If the application has multiple replicas and is deployed across multiple availability zones, users should experience minimal or zero downtime.

---

# 🔟 Release Caused High Latency

## ❓ After deployment, response time increased. What do you do?

If latency increases after deployment, I compare performance metrics between the old and new versions. I check CPU and memory usage, database query performance, external API calls, and application logs. If the issue is clearly related to the new release, I roll back immediately.

Then I analyze slow queries, inefficient code, or increased resource consumption. Before redeploying, I ensure proper performance testing and may use canary deployment to validate improvements gradually.

---

# 🎯 How to Answer Like a Senior DevOps Engineer

A senior DevOps engineer always starts with monitoring, performs safe rollback if required, debugs methodically, identifies the root cause, implements preventive controls, and documents the incident. The focus is not just fixing the issue but ensuring it never happens again.

---

# 🏁 Final Mindset

A 4+ year DevOps engineer does not panic, does not randomly restart servers, and does not guess. Instead, they rely on monitoring, automation, rollback strategies, structured debugging, and prevention mechanisms to protect production systems.
