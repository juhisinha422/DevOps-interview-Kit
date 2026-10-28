# Round 1 Interview Prep: Lead DevOps Engineer (HashedIn by Deloitte)

Answers are based on Juhi's resume and GitHub projects. Anything marked **[FILL]** needs your own real details. Speak in your own words and don't claim anything you haven't done.

---

## 1. Introduction and Resume

**Q: Walk me through your experience.**

I'm a DevOps Engineer with 4+ years of experience, and I'm an AWS Certified DevOps Engineer - Professional. I started at TCS in August 2021 as a Cloud Support Engineer doing L1/L2 AWS support, then moved to a DevOps Engineer role in February 2022. I design CI/CD pipelines for 10+ microservice repositories, build Terraform modules, run workloads on Amazon EKS, and manage observability with Prometheus and Grafana. I also handle incident response, review pipelines and code, and mentor junior engineers. Outside work I build DevSecOps and GitOps projects and have explored AI-assisted DevOps with Amazon Q CLI and MCP servers.

**Q: Explain your current project.**

I work on the delivery and infrastructure platform for 10+ microservices. Pipelines are trunk-based YAML pipelines that promote builds through Dev, UAT, Pre-Prod and Production, which increased deployment frequency by 40%. Terraform modules and templates let teams provision environments themselves, cutting setup time by 60%. Workloads run on EKS with auto-scaling, rolling updates and health checks, and resource utilization improved by 45%. I also designed a 3-account AWS setup (Production, Non-Production, Management) and built 20+ Grafana dashboards with Prometheus alerts, supporting 99.9% availability.

**Q: What is your role in the team? Have you led anyone?**

I'm hands-on, and I act as the technical point of contact for pipeline and infrastructure work. I review code and pipelines across 10+ repositories through branch policies, which cut merge conflicts by 30%. I mentor junior engineers on CI/CD, IaC and automation, and I lead incident response. I haven't had the formal title of squad lead yet, but I do the core of it: setting standards, reviewing work, mentoring and owning delivery. This role is the natural next step.

**Q: How did you measure the 40%, 60% and 70% improvements?**

*[FILL with your real method. A good structure:]*
- **40% deployment frequency:** compared deployments per week before and after the new pipelines, using pipeline run history.
- **60% setup time:** compared the time to create a new environment manually against with the Terraform template.
- **70% security risk:** compared the gaps in the old setup (flat network, broad access) against the new account separation, IAM controls and private networking.

Only use numbers you can explain.

---

## 2. CI/CD and GitOps

**Q: Explain your pipeline from commit to production.**

A commit or merge to the main branch triggers the pipeline through a webhook. The stages are: checkout, build and unit tests, code-quality and security scans (SonarQube, Trivy, OWASP Dependency-Check), build the Docker image, push it to ECR, then deploy to Dev. After that the same artifact is promoted to UAT, Pre-Prod and Production, with approval gates before the higher environments. After each deployment there are health checks, and failed deployments are rolled back.

**Q: What is trunk-based development, and why did you choose it?**

Developers merge small changes frequently into one main branch, instead of keeping long-lived feature branches. It reduces merge conflicts, keeps code always releasable, and supports faster deployments. It works well with feature flags and automated tests, and it's why our deployment frequency went up.

**Q: Jenkins vs GitHub Actions: when would you use each?**

Jenkins is highly customizable, supports shared libraries and many plugins, and suits complex pipelines, but you have to host and maintain it. GitHub Actions is managed, tightly integrated with GitHub, and quick to set up with reusable workflows. For new projects on GitHub I'd choose Actions. For heavy customization or existing Jenkins setups, Jenkins.

**Q: How does Argo CD work? What happens if someone changes the cluster manually?**

Argo CD is a GitOps controller. Git is the source of truth for the desired state, and Argo CD compares it with the live cluster state and syncs them. If someone changes the cluster manually, Argo CD shows the app as OutOfSync (drift). With self-heal enabled it reverts the change automatically, otherwise it flags it for manual sync. I used it in my DevSecOps project to deliver to a K3s cluster.

**Q: What is progressive delivery? Rolling vs blue/green vs canary?**

- **Rolling:** replace pods gradually. It's the Kubernetes default and I use it on EKS.
- **Blue/green:** run two full environments and switch traffic at once. Rollback is instant, but it needs double the capacity.
- **Canary:** send a small percentage of traffic to the new version and increase it as metrics look healthy. Tools like Argo Rollouts support this.

---

## 3. Kubernetes and Containers

**Q: A pod is in CrashLoopBackOff. How do you debug it?**

1. `kubectl get pods` and `kubectl describe pod <name>` to see events, exit codes and probe failures.
2. `kubectl logs <pod>` and `kubectl logs <pod> --previous` to see why the last container crashed.
3. Check common causes: bad config or env vars, missing secret or ConfigMap, wrong image or command, failing liveness probe, out-of-memory kills (OOMKilled), or a dependency that isn't reachable.
4. Fix the cause and redeploy. If needed, run a debug container or exec in.

**Q: A pod is stuck in Pending. Why?**

Usually the scheduler can't place it: insufficient CPU or memory, node selectors, taints without tolerations, an unbound PersistentVolumeClaim, or no nodes available. `kubectl describe pod` shows the scheduling events with the exact reason.

**Q: Liveness vs readiness probe?**

Liveness checks whether the container is alive, and Kubernetes restarts it if it fails. Readiness checks whether it can receive traffic, and failing pods are removed from the Service endpoints. Readiness matters most for zero-downtime rolling updates.

**Q: Deployment vs StatefulSet?**

A Deployment is for stateless apps where pods are interchangeable. A StatefulSet gives pods stable identities, ordered startup and stable storage, which suits databases and other stateful systems.

**Q: How does the Horizontal Pod Autoscaler (HPA) work?**

It watches metrics such as CPU, memory or custom metrics and adjusts the replica count between a min and max. It needs resource requests set, and metrics-server installed.

**Q: How did you reduce Docker image size by 40%?**

Multi-stage builds: build in one stage with all the tooling, then copy only the final artifact into a slim runtime image. I also use minimal base images and avoid unnecessary packages and layers.

**Q: Why use Helm?**

It packages Kubernetes manifests as templated charts with values, so the same chart deploys to different environments with different configs. It also supports versioned releases and rollbacks (`helm rollback`). I used it to install Prometheus and Grafana and to deploy workloads.

**Q: How would you upgrade an EKS cluster with minimal downtime?**

Check version compatibility and deprecated APIs first, upgrade the control plane, then upgrade add-ons (CoreDNS, kube-proxy, VPC CNI), then roll the node groups. Use Pod Disruption Budgets and multiple replicas so draining nodes doesn't cause downtime. Test in Non-Prod first.

**Q: Explain Kubernetes RBAC.**

Roles (or ClusterRoles) define permissions on resources, and RoleBindings (or ClusterRoleBindings) attach them to users, groups or service accounts. I apply least privilege and used RBAC in my EKS multibranch pipeline project.

---

## 4. Terraform and IaC

**Q: How do you structure Terraform and manage state?**

I use reusable modules for common building blocks, such as VPC, EKS or IAM, and environment-specific variable files. State is stored remotely, for example in S3 with DynamoDB locking, so teams share state safely and two people can't apply at once.

**Q: What happens if two people run `terraform apply` at the same time?**

With a remote backend and locking, the second run fails with a lock error until the first finishes. This protects state from corruption.

**Q: How do you handle drift?**

Run `terraform plan` regularly (or in a scheduled pipeline) to detect differences between state and real infrastructure. Then either update the code to match an intentional change, or apply to restore the desired state. Restricting manual console changes with IAM helps prevent drift.

**Q: How do you handle secrets in Terraform?**

Never hardcode them. Use AWS Secrets Manager or SSM Parameter Store, mark variables as sensitive, and make sure the state backend is encrypted and access-controlled, because secrets can end up in state.

**Q: Terraform vs CloudFormation?**

Terraform is multi-cloud, has a large module ecosystem, and has a clear plan/apply workflow. CloudFormation is AWS-native, with no state file to manage and tight service integration. I've used both and prefer Terraform for reusable, multi-environment setups.

---

## 5. AWS Architecture

**Q: Explain your 3-account AWS setup.**

I set up Production, Non-Production and Management accounts. Separating accounts isolates blast radius, so a mistake in Non-Prod can't affect Production, and it allows different IAM controls per environment. The accounts connect through VPC Peering with controlled routing, and access to private resources goes through bastion hosts. NAT Gateways give private subnets outbound access, and load balancers expose services. IAM roles follow least privilege.

**Q: Public vs private subnets?**

Public subnets route to an Internet Gateway and hold things like load balancers and NAT Gateways. Private subnets have no direct inbound internet access, and hold app servers and databases. Private instances reach the internet outbound through a NAT Gateway.

**Q: What are the limits of VPC Peering?**

It's not transitive, so if A peers with B and B with C, A can't reach C through B. CIDR ranges can't overlap. For many VPCs, Transit Gateway scales better.

**Q: How do you design for high availability and fault tolerance?**

Spread workloads across multiple Availability Zones, use load balancers with health checks, auto-scaling groups or Kubernetes replicas, Multi-AZ RDS, and automated backups. Avoid single points of failure and test failover.

**Q: IAM roles vs users? What is least privilege?**

Roles give temporary credentials and are preferred for services and cross-account access. Users have long-term credentials and should be limited. Least privilege means granting only the permissions needed for a task and reviewing them regularly.

---

## 6. Migration and Rollback (JD priority)

**Q: Tell me about an RDS rollback you handled.** (Use STAR)

*[FILL with your real details. Structure:]*
- **Situation:** A release/deployment to [environment] failed [what happened, for example a schema change or app error].
- **Task:** Restore the database to a stable state quickly with minimal downtime and no data loss.
- **Action:** [Which method you used: snapshot restore, point-in-time recovery, or reverting the migration.] Steps taken: [stop traffic or the pipeline, restore, validate data, repoint the app, verify].
- **Result:** Service restored in [time]. Afterwards, [what you changed: backups before release, rollback scripts, pipeline checks, runbook].

**Q: How would you plan a cloud migration?**

1. **Discovery:** inventory applications, dependencies, data size and compliance needs.
2. **Strategy per workload:** rehost, replatform, refactor or retire.
3. **Landing zone:** accounts, networking, IAM and guardrails as code.
4. **Wave planning:** group by dependency and risk, starting with low-risk workloads.
5. **Cutover:** automated, with a tested rollback plan, and data sync until cutover.
6. **Validation and rollback criteria** defined before cutover.
7. **After:** optimize cost, modernize, decommission old systems.

I'll be honest: my hands-on experience is the cutover and rollback side (like the RDS rollback) and the multi-account AWS setup, not a full end-to-end migration program.

**Q: How do you do a zero-downtime database migration?**

Use continuous replication, for example AWS DMS or native replication, to keep the target in sync with the source. Validate the data, then cut over in a short window by switching application connections, and keep the source until you're confident so rollback is possible. Use backward-compatible schema changes (expand, then migrate, then contract).

---

## 7. Observability

**Q: What did you monitor and alert on?**

Infrastructure metrics (CPU, memory, disk), Kubernetes metrics (pod restarts, node health, resource usage), application metrics (error rate, latency, request rate), and availability. I built 20+ Grafana dashboards and Prometheus alert rules for proactive detection.

**Q: What are the golden signals?**

Latency, traffic, errors and saturation.

**Q: How does Prometheus work?**

It scrapes metrics from targets over HTTP on an interval and stores them in a time-series database. You query with PromQL, and alert rules fire to Alertmanager, which handles grouping, routing and notifications. Grafana visualizes the data.

**Q: How do you reduce alert fatigue?**

Alert on symptoms that affect users rather than every cause, set sensible thresholds and durations, group and deduplicate alerts, assign severity levels, and review noisy alerts regularly.

**Q: Do you know OpenTelemetry?**

I understand the three pillars (metrics, logs and traces) and the concept of OpenTelemetry as a vendor-neutral instrumentation standard. My hands-on experience is with Prometheus, Grafana, ELK and CloudWatch, and I'd be happy to ramp up on OpenTelemetry. *(Be honest if you haven't used it.)*

---

## 8. DevSecOps

**Q: Where do your security tools fit in the pipeline?**

I shift security left. SonarQube does static code analysis (SAST), OWASP Dependency-Check scans dependencies for known vulnerabilities (SCA), and Trivy scans container images. They run before deployment, and the pipeline can fail on critical findings.

**Q: How do you manage secrets?**

Use a secrets manager (AWS Secrets Manager or SSM), not hardcoded values or plain Git. Inject secrets at runtime, rotate them, and restrict access with IAM. In Kubernetes, avoid storing sensitive values as plain Secrets without encryption and access controls.

**Q: A critical CVE is found in production. What do you do?**

Assess exposure and exploitability, identify affected images and services, patch or upgrade the dependency or base image, rebuild, rescan and redeploy through the pipeline. If the risk is high, use temporary mitigations (network restrictions, WAF rules) first. Then confirm the fix and document it.

**Q: Have you worked with SOC 2 or HIPAA?**

*(Answer honestly.)* I haven't worked directly under SOC 2 or HIPAA audits, but I apply the underlying controls: least-privilege IAM, account separation, encryption, secrets management, scanning and audit logging.

---

## 9. AI-Assisted DevOps and MCP

**Q: What is MCP, and how did you use it?**

Model Context Protocol is an open standard that lets AI assistants connect to external tools and data through MCP servers. In my project I used Amazon Q CLI with MCP servers to generate AWS architecture diagrams and infrastructure plans (VPC, EC2, RDS, microservices) from natural-language prompts, which cut design time from hours to minutes. I used existing MCP servers rather than building my own.

**Q: How would you build a safe agent for incident triage?**

- **Permissioned access:** read-only by default, narrowly scoped IAM roles.
- **Human in the loop:** require approval before any change such as restarts or rollbacks.
- **Audit logging:** record every tool call and action.
- **Evals:** test the agent's answers against known incidents before trusting it.
- **Cost monitoring:** track token usage and set limits.

**Q: How do you use AI in daily work, and where don't you trust it?**

I use GitHub Copilot, Claude and ChatGPT to speed up scripting, IaC drafts, log analysis and runbooks. I never apply AI-generated changes to production without review and testing. I'm careful with security-sensitive configs, IAM policies and anything involving secrets or data.

---

## 10. Scripting and Troubleshooting

**Q: Write a Bash script to remove old Docker images.**

```bash
#!/bin/bash
# Remove dangling images and images unused for more than 7 days
docker image prune -f
docker image prune -a -f --filter "until=168h"
```

**Q: Write a Bash script to rotate or clean old logs.**

```bash
#!/bin/bash
LOG_DIR="/var/log/myapp"
find "$LOG_DIR" -type f -name "*.log" -mtime +7 -exec gzip {} \;
find "$LOG_DIR" -type f -name "*.gz" -mtime +30 -delete
```

**Q: The deployment succeeded but the site is down. How do you troubleshoot?**

1. Check pod status and recent events (`kubectl get pods`, `describe`).
2. Check application logs for errors.
3. Verify the Service, Ingress and load balancer target health.
4. Check DNS, security groups and network policies.
5. Check dependencies (database, external APIs) and recent config or secret changes.
6. If the cause isn't found quickly, roll back (`helm rollback` or the previous image) to restore service, then investigate.

**Q: How do you handle a production incident?**

Acknowledge and communicate, assess impact, mitigate first (rollback, scale, failover), find the root cause, and then write a post-mortem with a timeline, root cause and preventive actions. I lead this at TCS and implement preventive fixes after root cause analysis.

---

## 11. Gap Questions (be honest)

**Q: Do you have Azure or GCP experience?**

My hands-on experience is on AWS. The concepts map directly, for example EKS to AKS or GKE, IAM to Azure RBAC or GCP IAM, and VPC to VNet. I can ramp up quickly, and I've done this before with tools like Argo CD and Prometheus.

**Q: Do you have MLOps or data pipeline experience?**

I haven't run MLOps or data pipelines in production yet. I understand the train, validate, deploy and monitor lifecycle and that it builds on the same CI/CD, IaC and monitoring skills I use every day. I'm interested in growing in this area.

**Q: Have you led a squad?**

Not with the formal title, but I review work across 10+ repositories, mentor juniors, lead incident response and act as the technical point of contact. I'm ready to take on formal ownership.

---

## 12. Questions to Ask the Interviewer

- What does the pod structure look like, and how big would my squad be?
- What cloud platforms and migration projects would I work on first?
- How does the team use AI and agentic tooling in DevOps today?
- What does success look like in the first 90 days?

---

## Quick Tips

- Use **STAR** (Situation, Task, Action, Result) for experience questions.
- Know the **why** behind every number on your resume.
- Say "I haven't done that, but here's how I'd approach it" instead of bluffing.
- Prepare one **failure story** and one **conflict or leadership story**.
- Re-read your three GitHub projects before the interview. Interviewers click them.
