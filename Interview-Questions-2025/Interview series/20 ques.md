# 🚀 DevOps Interview Guide – Top 20 Must-Know Questions (Advanced)

---

## 1. Pod is Running but returning 503 - how do you debug at network, service, and ingress level?

A 503 error usually indicates the service is unavailable. I start by checking if the pod is healthy and listening on the correct port. Then I verify the Kubernetes Service configuration to ensure selectors match the pod labels and endpoints are correctly registered. Next, I check Ingress rules, backend mappings, and controller logs (like NGINX ingress). I also validate network policies and DNS resolution inside the cluster. Finally, I use tools like `kubectl describe`, `kubectl get endpoints`, and curl from inside the cluster to isolate the issue.

---

## 2. How does Kubernetes scheduling work internally, and what are common causes of scheduling failures?

Kubernetes scheduler assigns pods to nodes based on resource availability and constraints. It filters nodes based on requirements like CPU, memory, taints/tolerations, and node selectors, then ranks them to choose the best fit. Common failures occur due to insufficient resources, strict node affinity rules, taints without tolerations, or volume constraints. Debugging involves checking events using `kubectl describe pod` to identify scheduling issues.

---

## 3. How do you design zero-downtime deployments for stateful applications in Kubernetes?

For stateful applications, I use StatefulSets with persistent volumes and controlled rolling updates. I ensure readiness probes are properly configured so traffic is only routed to healthy pods. Techniques like blue-green or canary deployments are used to avoid disruption. Database migrations are handled carefully with backward-compatible changes, and replication or failover strategies ensure availability during updates.

---

## 4. Explain how CNI plugins work and how cross-node pod communication happens.

CNI plugins manage networking in Kubernetes by assigning IP addresses to pods and configuring routes. Each pod gets its own IP, and CNI ensures routing between nodes using overlay networks or direct routing. Cross-node communication happens via encapsulation (like VXLAN) or routing tables depending on the plugin (e.g., Calico, Flannel). This allows seamless communication across the cluster.

---

## 5. How do you debug intermittent pod restarts with no clear logs?

I start by checking pod events using `kubectl describe pod` to identify reasons like OOMKilled or probe failures. Then I inspect resource usage metrics to detect CPU or memory spikes. I also check liveness/readiness probes, node health, and underlying container runtime logs. If logs are missing, I enable debug logging or sidecar logging. Tools like Prometheus help correlate restarts with system metrics.

---

## 6. CI pipeline builds 50 images and takes 20 minutes - how do you reduce it to <5 minutes?

To optimize pipeline performance, I implement parallel builds so multiple images are built simultaneously. I use Docker layer caching to avoid rebuilding unchanged layers. I also optimize Dockerfiles, reduce unnecessary dependencies, and use pre-built base images. Additionally, I use distributed build agents and caching mechanisms to speed up execution.

---

## 7. How do you design a secure CI/CD pipeline with integrated DevSecOps practices?

I integrate security at every stage of the pipeline. This includes SAST using SonarQube during code analysis, dependency scanning (SCA), container image scanning using Trivy, and IaC scanning for Terraform. Secrets are managed securely using vaults and not hardcoded. Role-based access control and approvals are implemented for production deployments.

---

## 8. Pipeline works in staging but fails in production - how do you systematically debug?

I compare environment differences such as configurations, environment variables, secrets, and infrastructure. I check logs from both environments to identify discrepancies. I also validate network access, permissions, and dependencies. Reproducing the issue in a controlled environment helps isolate the root cause.

---

## 9. Design a multi-region, highly available architecture with failover and data consistency.

I design architecture using multiple regions with load balancing via DNS (like Route 53). Data replication is handled using multi-region databases or replication strategies. Failover mechanisms ensure traffic shifts automatically if one region fails. Consistency models depend on application needs, using eventual or strong consistency accordingly.

---

## 10. Your AWS bill increased 3x overnight - how do you identify the root cause?

I start by analyzing AWS Cost Explorer to identify which service caused the spike. Then I drill down into usage reports and check for anomalies like sudden traffic spikes, misconfigured auto-scaling, or unused resources. I also review logs and recent deployments to identify unintended changes.

---

## 11. How do you handle infrastructure drift in Terraform across multiple teams?

I enforce that all infrastructure changes must go through Terraform pipelines. Regular `terraform plan` runs help detect drift. I use remote state with locking and enable auditing. If drift occurs, I reconcile it by reapplying Terraform or importing resources into state.

---

## 12. Terraform apply failed midway - how do you recover without corrupting state?

I first check the state file consistency. If partial resources were created, I use `terraform plan` to understand the current state. Depending on the situation, I may manually fix resources or use `terraform import` to sync state. Remote state backups help restore if needed.

---

## 13. How do you design secure secrets management across 100+ microservices?

I use centralized secret management systems like Vault or cloud-native services. Secrets are injected dynamically at runtime rather than stored in code. Access is controlled via IAM roles or service accounts. Rotation policies ensure secrets are updated regularly.

---

## 14. Your system passed all scans but got compromised - what security layers were missing?

Security scans alone are not enough. Missing layers could include runtime security, network segmentation, least privilege access, monitoring, and intrusion detection. Defense-in-depth strategy ensures multiple layers of protection beyond static scans.

---

## 15. How do you design observability (logs, metrics, traces) for a distributed system?

I implement centralized logging (ELK stack), metrics collection (Prometheus), and distributed tracing (Jaeger). Correlation IDs help trace requests across services. Dashboards and alerts provide visibility into system health.

---

## 16. How do you implement SLO-based alerting without alert fatigue?

I define clear SLOs and error budgets. Alerts are triggered only when thresholds are breached, avoiding unnecessary noise. I use aggregation and prioritize critical alerts while suppressing low-impact ones.

---

## 17. Service latency spikes every 60 seconds - how do you debug root cause?

I analyze metrics and logs to identify patterns. Regular spikes often indicate scheduled jobs, garbage collection, or resource contention. I correlate timing with cron jobs, autoscaling events, or database queries to identify the root cause.

---

## 18. Production is down - what are your first 5 steps under pressure?

First, I acknowledge the incident and notify stakeholders. Then I check monitoring dashboards to identify impact. Next, I analyze logs and recent changes to find the root cause. I implement a quick fix or rollback to restore service. Finally, I document the issue for postmortem.

---

## 19. How do you design systems for graceful degradation during failures?

I design fallback mechanisms where non-critical features are disabled during failures. Circuit breakers prevent cascading failures. Caching and redundancy ensure core functionality remains available.

---

## 20. How do you ensure zero-downtime cluster upgrades in Kubernetes?

I perform rolling upgrades of nodes and control plane components. Workloads are distributed across nodes to avoid disruption. Readiness probes and PodDisruptionBudgets ensure availability. Testing upgrades in lower environments helps avoid risks.

---

## 🚀 Final Tip

For advanced interviews:

* Focus on **real scenarios + decision making**
* Show **debugging mindset**
* Explain **why, not just what**

---
