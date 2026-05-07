# Here are the top 10 questions I was asked that every SRE and DevOps Engineer should prep for:

---

## A senior dev manually changed a Cloudfront config in the console. How do you reconcile this into Terraform without a terraform destroy?

* First run:

```bash id="tfdrift1"
terraform plan
```

* Identify drift between Terraform state and actual AWS config
* Update Terraform code to match manual changes
* If resource not in state:

```bash id="tfimport1"
terraform import
```

* Validate using:

```bash id="tfplan2"
terraform plan
```

* Apply carefully:

```bash id="tfapply1"
terraform apply
```

* Avoid `terraform destroy` or forced recreation in production

---

## Production is down. Logs show 504 Gateway Timeouts, but CPU and Memory are at 20%. Where do you look first?

* Check:

  * Load Balancer health checks
  * Application response time
  * Database latency
  * Network latency
  * Thread pool exhaustion
  * Connection pool limits
  * DNS issues
  * Upstream service timeout

* 504 usually indicates backend not responding in expected time, not necessarily CPU issue

---

## How do you handle a database migration on Kubernetes while ensuring zero data loss and minimal downtime?

* Take DB backup/snapshot first
* Use backward-compatible schema changes
* Run migrations separately (Job/init container)
* Use rolling deployment
* Validate migration in staging before production
* Monitor replication lag and DB performance
* Keep rollback plan ready

---

## A developer accidentally committed an AWS Secret Key to a public repo. Walk me through the next 10 minutes of your life.

1. Revoke/disable key immediately
2. Rotate credentials
3. Check CloudTrail for misuse
4. Remove secret from Git history
5. Force push cleaned repo
6. Notify security/team
7. Audit affected systems
8. Enable secret scanning/prevention

* Priority = containment first, blame later

---

## Management wants a 25% reduction in the cloud bill by next month. What is your step-by-step audit process?

1. Analyze billing reports (Cost Explorer)
2. Identify idle resources
3. Rightsize instances
4. Enable Auto Scaling
5. Move workloads to Spot instances
6. Check storage lifecycle policies
7. Remove unused snapshots/IPs
8. Use Reserved/Savings Plans
9. Optimize Kubernetes requests/limits
10. Create cost monitoring dashboards

---

## Where exactly in your CI/CD pipeline do you implement SAST, DAST, and Image Scanning without killing developer velocity?

* **SAST** → during pull request/build stage

* **Image Scanning** → after Docker image build

* **DAST** → staging/pre-production environment

* Keep scans:

  * Incremental
  * Parallelized
  * Severity-based blocking

---

## Explain the packet flow from a user’s browser to a Pod sitting behind an Ingress Controller and a ClusterIP Service.

1. User hits domain
2. DNS resolves to Load Balancer
3. Load Balancer forwards to Ingress Controller
4. Ingress routes request based on host/path
5. Request goes to ClusterIP Service
6. Service forwards to Pod via kube-proxy
7. Pod responds back through same path

---

## If your team exhausts its Error Budget by the 15th of the month, what specific actions do you take regarding the product roadmap?

* Freeze risky feature releases

* Prioritize reliability work

* Increase monitoring and RCA focus

* Conduct incident reviews

* Work with product team to rebalance roadmap

* SRE principle:

  * Reliability takes priority when error budget exhausted

---

## Your HPA is scaling pods correctly, but the application latency is still climbing. What is the likely bottleneck?

Possible bottlenecks:

* Database saturation

* External API latency

* Connection pool exhaustion

* Disk I/O

* Network bottleneck

* App-level locking/thread issue

* Scaling pods alone does not solve downstream bottlenecks

---

## Describe a time you disagreed with a Developer on a "Production Ready" requirement. How did you resolve it?

**Good structure answer:**

* Situation:

  * Developer wanted quick release without proper monitoring/readiness probes

* Action:

  * Explained operational risks with examples
  * Discussed rollback and reliability impact
  * Reached compromise with phased rollout

* Result:

  * Stable deployment with monitoring added
  * Better collaboration between Dev and Ops

---
