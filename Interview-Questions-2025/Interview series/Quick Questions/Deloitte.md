# Deloitte Interview Experience

## Position: DevOps Engineer

### Production-Level Scenario-Based Interview Questions & Answers

---

## 1. Your application is healthy, but users experience intermittent timeouts.

### How would you identify whether the issue is in the application, network, infrastructure, or load balancer?

**Answer:**

I would first determine whether the timeout is affecting all requests or only a percentage of requests. I would correlate the failures using timestamps, request IDs, source IPs, backend Pods, nodes, and availability zones.

I would start from the user request path:

**User → DNS → Load Balancer → Ingress → Service → Pod → Application → Database/Downstream Services**

First, I would check load balancer metrics such as request count, latency, HTTP 5xx, target health, and connection errors. Then I would check Ingress and application logs to see whether the request actually reaches the application.

If the application logs don't show the failed requests, I would investigate the network or load-balancer layer. I would check node health, packet loss, connection tracking, NAT/SNAT capacity, security groups, NetworkPolicies, and connection timeouts.

If the requests reach the application, I would check application latency, database calls, thread pools, connection pools, and downstream dependencies.

I would also compare successful and failed requests to identify whether the problem is isolated to a particular Pod, node, AZ, or load-balancer target.

---

## 2. A deployment completed successfully, but response time increased from 200 ms to 3 seconds.

### Walk me through your troubleshooting approach.

**Answer:**

A successful deployment only means the deployment mechanism completed; it doesn't prove that the new version is performing correctly.

First, I would compare the current version with the previous version and check when the latency increase started.

I would check application latency metrics, HTTP status codes, CPU and memory usage, Pod restarts, thread pools, connection pools, and garbage collection if it is a JVM application.

Then I would identify where the additional latency is coming from. For example, I would check database query latency, external API calls, cache hit ratio, DNS resolution, and network latency.

I would compare the new application's logs and metrics with the previous version.

If the latency is clearly introduced by the new release and customer impact is significant, I would stop the rollout or roll back to the previous stable version while continuing the root-cause investigation.

For future deployments, I would use canary or progressive deployment with latency and error-rate thresholds so that the rollout can automatically stop when performance degrades.

---

## Your CI/CD pipeline has been stable for months but suddenly starts failing without any pipeline changes.

### How would you isolate the root cause?

**Answer:**

I would first compare the current failure with the last successful pipeline run.

Since there were no pipeline changes, I would investigate external dependencies and environment changes.

I would check:

* Jenkins agent health
* Disk space and memory
* Java/Maven/Docker versions
* Git repository availability
* Credentials and token expiration
* Docker registry availability
* Nexus availability
* Network/DNS connectivity
* Dependency repositories
* Certificate expiration
* Third-party API changes

For example, if the pipeline suddenly fails while downloading Maven dependencies, I would check whether Nexus or the upstream repository is unavailable rather than changing the Jenkinsfile.

I would also inspect the exact error from the failing stage and reproduce that stage manually on the same Jenkins agent.

Then I would compare environment variables, tool versions, credentials, and external service responses between the last successful and failed executions.

My approach would be:

**Last successful build → First failed build → Identify changed dependency → Reproduce → Isolate → Fix → Verify**

---

## 3. A production deployment failed halfway through, leaving different service versions running.

### How would you recover with minimal customer impact?

**Answer:**

First, I would stop the deployment from progressing further so the situation does not become worse.

Then I would identify exactly which services and Pods are running the old and new versions and determine whether the two versions are compatible.

If the new version is causing errors, I would route traffic away from the affected version or roll back the affected services to the last known stable version.

I would be particularly careful with database changes. If the deployment included a schema migration, I would not blindly roll back the application if the database schema is no longer backward compatible.

I prefer backward-compatible migrations using the **expand-and-contract approach**, so old and new application versions can coexist safely during a deployment.

After stabilizing production, I would verify:

* Error rate
* Latency
* Pod health
* Service endpoints
* Database health
* Business transactions

Then I would identify why the deployment partially completed and improve the deployment strategy.

For example, using **blue-green, canary, or progressive delivery** can reduce the impact of a failed deployment.

---

## 4. A container keeps restarting, but there are no application errors in the logs.

### What would you investigate next?

**Answer:**

If application logs don't show an error, I would not assume the application is healthy. I would check the container state and Kubernetes events.

I would run:

```bash
kubectl describe pod <pod-name>
kubectl get pod <pod-name> -o wide
kubectl logs <pod-name> --previous
```

I would specifically check the **restart count, exit code, termination reason, and events**.

Possible causes include:

* OOMKilled
* Liveness probe failure
* Failed startup probe
* Container process exiting
* Incorrect command or entrypoint
* Missing environment variables
* Missing ConfigMap/Secret
* Volume mounting issues
* Dependency failures
* Resource limits

If the container is being killed because of memory, I would check actual memory consumption rather than immediately increasing the limit.

If the liveness probe is failing, I would verify whether the probe configuration is correct and whether the application actually needs more startup time.

I would also use `kubectl logs --previous` because the current container may have already restarted and the useful logs may belong to the previous instance.

---

## 5. Your monitoring dashboards look healthy, but customers report slow application performance.

### What would be your next steps?

**Answer:**

I would treat the customer reports as an important signal even if the dashboards appear healthy.

First, I would verify whether our monitoring is measuring the same user experience. Infrastructure metrics such as CPU and memory can remain normal while application latency or dependency latency increases.

I would check:

* Request latency percentiles, especially P95/P99
* HTTP error rates
* Database latency
* External API latency
* Cache hit ratio
* Connection pool usage
* Thread pool saturation
* Queue depth
* DNS latency
* Network latency

I would also compare the affected users, regions, endpoints, and time periods.

For example, average latency may still look normal while P99 latency has increased significantly. Similarly, CPU may be only 40%, but the database connection pool could be completely exhausted.

I would use distributed tracing or request correlation where available to identify which part of the request is consuming the additional time.

The main question I want to answer is:

**"Where is the customer's request spending those extra milliseconds?"**

---

## 6. Your company wants to migrate manually managed infrastructure to Terraform.

### How would you approach the migration without disrupting production?

**Answer:**

I would not start by creating new Terraform resources because that could result in duplicate infrastructure.

First, I would inventory the existing infrastructure and understand its dependencies.

I would identify resources such as:

* EC2
* VPC
* Subnets
* Security Groups
* Load Balancers
* IAM
* RDS
* S3
* EKS
* Route 53

Then I would create Terraform configurations that accurately represent the existing infrastructure.

Instead of recreating resources, I would import the existing resources into Terraform state.

For example:

```bash
terraform import aws_instance.app <instance-id>
```

After importing, I would run:

```bash
terraform plan
```

The goal initially is to reach a **no unexpected changes** state.

I would carefully review the plan and make the Terraform configuration match the existing production environment.

I would migrate incrementally rather than importing everything at once.

I would also configure remote state with appropriate locking and access control, use modules where appropriate, and establish a review process through CI/CD.

The important principle is:

**Import existing infrastructure → Match configuration → Review plan → Validate → Gradually manage through Terraform**

I would avoid applying destructive changes until the Terraform plan has been thoroughly reviewed.

---

## 7. A critical vulnerability is discovered in a production container image.

### What are your immediate actions and long-term preventive measures?

**Answer:**

My immediate priority would be to understand the severity, exploitability, affected image versions, and whether the vulnerable component is actually used by the application.

I would identify every running workload using the vulnerable image and determine whether the vulnerability is actively exploitable.

If there is a patched image or dependency version available, I would build and test a new image and deploy it as quickly as safely possible.

If the vulnerability is actively being exploited or presents severe risk, I would consider temporary mitigation such as restricting network access, disabling the affected functionality, or isolating the workload while the patched version is prepared.

I would also verify whether secrets or credentials could have been exposed and rotate them if necessary.

For long-term prevention, I would integrate security scanning into CI/CD using tools such as **Trivy**, and scan both container images and dependencies.

I would also implement:

* Base image updates
* Dependency scanning
* SBOM generation
* Image signing and verification
* Regular image rebuilds
* Automated vulnerability alerts
* Registry scanning
* Least-privilege container configuration
* Non-root containers
* Patch management
* Admission policies where appropriate

The goal is to prevent vulnerable images from reaching production in the first place and to have an automated process that identifies and remediates vulnerabilities quickly.

---

# Interview Troubleshooting Framework

For almost all of these Deloitte-style scenarios, I would follow the same production approach:

**1. Understand the customer impact**

↓

**2. Identify when the issue started**

↓

**3. Compare healthy vs unhealthy instances**

↓

**4. Check metrics, logs, events and traces**

↓

**5. Isolate the failing layer**

**Application → Container → Pod → Service → Ingress/LB → Network → Infrastructure → Dependency**

↓

**6. Mitigate customer impact**

↓

**7. Fix the root cause**

↓

**8. Verify recovery**

↓

**9. Add monitoring/automation to prevent recurrence**

### Strong Interview Line

> **"I don't start changing infrastructure immediately. I first collect evidence, isolate the failing layer, mitigate customer impact, and then make the smallest safe change based on the evidence."**

This demonstrates production troubleshooting rather than simply knowing commands.
