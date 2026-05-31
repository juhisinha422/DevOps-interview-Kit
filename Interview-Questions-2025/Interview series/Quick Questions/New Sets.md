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

# Introduction & Experience

## 1. Tell me about yourself and your DevOps journey.

I have around 4 years of experience as a DevOps Engineer, mainly working on cloud and containerized environments. My experience includes CI/CD pipeline automation, Kubernetes administration, Docker containerization, infrastructure provisioning using Terraform, and cloud services on AWS. I started my career with Linux and deployment support activities and gradually moved into automation and DevOps practices.

Currently, I work extensively with Jenkins, GitLab, Docker, Kubernetes, Helm, Terraform, AWS, Prometheus, Grafana, and monitoring tools. My day-to-day activities involve automating deployments, managing Kubernetes clusters, troubleshooting production issues, monitoring infrastructure, handling CI/CD pipelines, and supporting development teams during releases.

I have also worked on incident management, RCA preparation, deployment optimization, infrastructure automation, and improving application reliability and scalability in production environments.

---

## 2. What are your current roles and responsibilities at your current company?

My current responsibilities include managing CI/CD pipelines using Jenkins and GitLab, deploying applications into Kubernetes clusters, and maintaining AWS cloud infrastructure. I work on Docker image creation, Helm deployments, Terraform-based infrastructure provisioning, monitoring setup, and production support activities.

I also handle:

* Kubernetes troubleshooting
* Production deployments and rollbacks
* Infrastructure monitoring using Prometheus and Grafana
* Secret and ConfigMap management
* Autoscaling and resource optimization
* Incident handling and RCA documentation
* Collaboration with development, QA, and infrastructure teams

Additionally, I support release activities, optimize deployment pipelines, and ensure high availability of applications running in production environments.

---

## 3. Describe the architecture of your current project.

My current project follows a microservices-based architecture deployed on AWS EKS Kubernetes clusters. Applications are containerized using Docker and deployed through Helm charts. CI/CD pipelines are managed using Jenkins integrated with Git repositories.

The architecture includes:

* Frontend microservices
* Backend APIs
* Database layer
* Messaging systems
* Monitoring stack
* Logging stack

Ingress controllers and AWS Load Balancers are used for external traffic routing. Internal communication happens through Kubernetes Services and DNS. Monitoring is implemented using Prometheus and Grafana, while logs are centralized using ELK stack or CloudWatch.

Infrastructure provisioning is automated using Terraform, and deployments follow rolling update strategies with rollback mechanisms. Autoscaling is enabled using HPA and Cluster Autoscaler for dynamic scaling during traffic spikes.

---

## 4. What DevOps tools are you currently using and why?

I currently use Jenkins for CI/CD automation because it provides flexibility and plugin support for different integrations. Docker is used for containerization to ensure consistent application environments. Kubernetes is used for orchestration and scaling of containerized workloads.

Terraform is used for Infrastructure as Code to automate cloud resource provisioning. Helm is used for Kubernetes package management and deployment standardization. AWS services such as EKS, EC2, IAM, S3, CloudWatch, and Route53 are used for cloud infrastructure management.

For monitoring and observability, I use Prometheus and Grafana. For logging, ELK stack and CloudWatch are used. Git and GitLab/GitHub are used for version control and collaboration.

---

## 5. What were the biggest challenges you faced in your current project?

One of the biggest challenges was handling production incidents during peak traffic periods. We faced issues like pod crashes, autoscaling failures, image pull failures, and application downtime due to readiness or dependency issues.

Another challenge was optimizing deployment pipelines because builds and deployments were taking too long. We improved this by implementing caching, parallel execution, Docker layer optimization, and pipeline restructuring.

We also faced infrastructure scaling issues in Kubernetes due to subnet IP exhaustion and autoscaler permission problems. Troubleshooting these required coordination with cloud teams, developers, and infrastructure teams while keeping stakeholders updated during incidents.

---

# CI/CD (Jenkins)

## 1. Explain your Jenkins pipeline architecture.

Our Jenkins architecture follows a master-agent setup. The Jenkins master handles scheduling, pipeline orchestration, and plugin management, while agents execute builds and deployment tasks. Agents can be static or dynamically provisioned using Kubernetes or cloud-based agents.

The pipeline is integrated with Git repositories and triggers automatically on commits, merge requests, or scheduled builds. Build artifacts are stored in repositories, Docker images are pushed to registries, and deployments are performed into Kubernetes environments using Helm or kubectl.

The architecture also includes:

* Source code management
* Build and test stages
* Security scanning
* Artifact management
* Deployment automation
* Notifications and monitoring

---

## 2. Difference between Declarative and Scripted Pipeline.

Declarative Pipeline uses a structured and simplified syntax with predefined stages and blocks. It is easier to read, maintain, and standardize across teams. Scripted Pipeline uses Groovy scripting and provides more flexibility for complex workflows and dynamic logic.

Declarative pipelines are generally preferred for standard CI/CD implementations because they are cleaner and easier to manage, while scripted pipelines are used when advanced custom logic or conditional execution is required.

---

## 3. How do you handle pipeline failures?

When a pipeline fails, I first identify the failed stage from Jenkins console logs. I analyze whether the issue is related to code, dependencies, infrastructure, credentials, network connectivity, or deployment configuration.

I usually troubleshoot in this order:

* Build logs
* Test reports
* Docker build logs
* Deployment logs
* Kubernetes events
* Environment variables
* Credential access

For production pipelines, rollback mechanisms are implemented to restore stable versions quickly. Notifications are also configured through email, Slack, or Teams for faster response.

---

## 4. What stages do you typically include in a CI/CD pipeline?

Typical stages include:

* Code checkout
* Dependency installation
* Code compilation/build
* Unit testing
* Static code analysis
* Security scanning
* Docker image build
* Image scanning
* Push to registry
* Deployment to lower environments
* Integration testing
* Production deployment
* Smoke testing
* Notifications

Additional approval stages may be included for production releases.

---

## 5. How do you secure Jenkins credentials?

Jenkins credentials are managed using Jenkins Credentials Manager. Sensitive data such as passwords, tokens, SSH keys, and API keys are stored securely and injected into pipelines during runtime.

Best practices include:

* Role-based access control
* Restricting credential visibility
* Integrating with secret management systems
* Avoiding hardcoded credentials
* Using masked variables in logs

In cloud-native environments, Jenkins is often integrated with AWS Secrets Manager or HashiCorp Vault for secure secret retrieval.

---

## 6. Have you configured Jenkins agents dynamically?

Yes. I have worked with dynamic Jenkins agents using Kubernetes plugin and cloud-based autoscaling agents. Instead of maintaining permanent build servers, agents are created dynamically whenever a pipeline starts and are destroyed after execution.

This approach improves:

* Resource utilization
* Scalability
* Cost optimization
* Isolation between builds

In Kubernetes environments, Jenkins dynamically launches temporary pods as build agents.

---

## 7. How do you optimize a slow Jenkins pipeline?

To optimize slow pipelines, I first identify which stages consume the most time. Then I apply optimizations such as:

* Parallel execution
* Dependency caching
* Incremental builds
* Docker layer caching
* Reducing unnecessary stages
* Reusing artifacts
* Dynamic agents
* Selective test execution

I also optimize Docker images and reduce network dependency wherever possible.

In one project, we reduced pipeline execution time from 45 minutes to 15 minutes using caching, parallel execution, and optimized Docker builds.

---

## 8. Explain rollback strategy in Jenkins deployment.

Rollback strategies are implemented to quickly restore stable application versions during failed deployments. We usually maintain versioned Docker images and Helm releases.

If deployment fails:

* Previous stable image is redeployed
* Helm rollback is executed
* Kubernetes rollout undo is used

Rollback can be manual or automated depending on pipeline configuration. Health checks and smoke tests help determine whether rollback is required.

---

# Scenario:

## A Jenkins pipeline suddenly starts taking 45 minutes instead of 15 minutes. How would you troubleshoot?

First, I would compare recent pipeline runs to identify which stage is taking additional time. I would check Jenkins console logs, stage timing, agent performance, resource utilization, and recent code or dependency changes.

Possible areas to investigate:

* Slow build agents
* Increased test execution time
* Dependency download delays
* Docker build inefficiencies
* Network latency
* Resource bottlenecks
* Artifact repository slowness
* Infrastructure issues

I would also check:

* CPU and memory utilization on agents
* Disk space
* Parallel execution status
* Docker layer cache availability
* Plugin updates or failures

If recent changes introduced additional tests or large dependencies, I would optimize them through caching or selective execution.

In one real scenario, pipeline duration increased because Docker cache was not being reused after agent recreation. We implemented persistent caching and optimized build stages, reducing execution time significantly.


## Real Production Incident — Kubernetes Nodes Running Out of Disk Space

A few months ago, we faced a production incident where multiple application pods started failing unexpectedly.

### Symptoms observed:

❌ Pods stuck in Pending state

❌ New deployments failing

❌ Frequent container restarts

❌ Alerts from Kubernetes cluster

Initial Investigation:

kubectl get nodes

kubectl describe node <node-name>

kubectl get events --sort-by='.lastTimestamp'

Error Found:

DiskPressure=True

The worker nodes had reached critical disk utilization.

Root Cause Analysis:

After logging into the node:

df -h

du -sh /var/lib/docker/*

We found:

✅ Old Docker images were consuming huge disk space

✅ Unused containers were not cleaned up

✅ Application logs had grown significantly over time

Impact:

- New pods could not be scheduled

- Existing workloads became unstable

- Deployment pipeline was blocked

Immediate Fix:

docker system prune -a

For containerd environments:

crictl images

crictl rmi <image-id>

Additional Actions:

✅ Cleaned old logs

✅ Increased monitoring on node disk usage

✅ Configured image retention policies

✅ Added alerts at 70%, 80%, and 90% disk utilization

Lessons Learned:

👉 Kubernetes failures are not always application-related.

Sometimes the issue lies in:

- Node resources

- Storage management

- Log retention

- Container image lifecycle

As DevOps Engineers, monitoring node health is just as important as monitoring applications


=========

## AWS Security Groups vs NACL — Common Interview Question

Here’s a simple comparison 👇
### ✅ Security Group

Works at INSTANCE level

Acts as a virtual firewall for EC2

Supports only ALLOW rules

Stateful:

Return traffic is automatically allowed

Example:
If port 22 is allowed inbound, response traffic is automatically permitted.

### ✅ Network ACL (NACL)

Works at SUBNET level

Acts as an additional security layer

Supports ALLOW and DENY rules

Stateless:

Need to allow inbound and outbound traffic separately

Example:
If inbound SSH is allowed, outbound response must also be allowed manually.

Quick Summary:
✔ Security Group → Instance Protection
✔ NACL → Subnet Protection
### Real-world usage:

In production environments, Security Groups are commonly used for application-level access control, while NACLs provide an additional subnet-level security layer for stricter network filtering.



______


# 🚨 Kubernetes Production Incident: How One YAML Change Took Down Payment APIs

## Overview

This incident occurred during a routine production deployment of a payment microservice running on Kubernetes. A seemingly harmless change in the deployment YAML resulted in a complete outage of payment APIs, causing failed customer transactions and triggering critical production alerts.

The incident reinforced an important lesson:

> Small Kubernetes configuration mistakes can have massive production impact.

---

## Deployment Configuration

The deployment was configured with the following rolling update strategy:

```yaml
strategy:
  rollingUpdate:
    maxUnavailable: 100%
    maxSurge: 0
```

At first glance, the configuration appeared valid. However, it introduced a critical risk during application rollout.

---

## What Happened During Deployment?

As soon as the deployment started, Kubernetes followed the rollout instructions exactly as defined in the manifest.

Because `maxUnavailable` was set to `100%`, Kubernetes was allowed to terminate every existing pod before bringing up replacement pods.

The sequence of events was:

1. Kubernetes terminated all currently running payment service pods.
2. New pods started getting created.
3. The payment application was a Java-based service that required approximately 90 seconds to initialize completely.
4. During startup, readiness probes continued to fail.
5. Since none of the new pods were ready, Kubernetes removed them from service endpoints.
6. The Service object had zero healthy endpoints available.
7. Incoming traffic had nowhere to be routed.

As a result, every request to the payment service started failing.

---

## Production Impact

The outage immediately affected customer-facing systems.

### Customer Impact

* Customers were unable to complete payment transactions.
* Checkout workflows failed.
* Revenue-generating transactions were interrupted.

### Technical Impact

* API Gateway started returning HTTP 503 Service Unavailable errors.
* Service endpoints became unavailable.
* Error rates increased dramatically.
* Application latency spiked.
* Monitoring dashboards turned completely red.

### Operational Impact

* Critical alerts were triggered instantly.
* On-call engineers were paged.
* Incident response procedures were activated.
* Leadership escalation occurred within minutes.

This quickly became a high-priority production incident.

---

## Root Cause Analysis

The root cause was the following deployment configuration:

```yaml
maxUnavailable: 100%
```

This configuration instructed Kubernetes that it was acceptable to make all existing replicas unavailable during deployment.

Effectively Kubernetes interpreted the deployment strategy as:

> "It is acceptable to terminate every running pod before new pods become available."

Kubernetes behaved exactly as configured.

The platform itself was functioning correctly.

The issue was caused entirely by an unsafe rollout strategy combined with slow application startup times.

---

## Why Readiness Probes Made It Worse

The application required significant startup time before it could begin serving traffic.

Although pods entered the Running state quickly, they were not yet ready to process requests.

Because readiness probes failed during initialization:

* Pods were not added to Service endpoints.
* Traffic was not routed to them.
* Kubernetes correctly considered them unavailable.

With all old pods terminated and all new pods still failing readiness checks, the Service had zero healthy endpoints.

This created a complete service outage.

---

## Resolution

The deployment strategy was updated to perform gradual rolling updates instead of replacing all pods simultaneously.

Updated configuration:

```yaml
strategy:
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 1
```

This configuration ensured that:

* Only one pod could become unavailable at a time.
* One additional pod could be created during deployment.
* Existing healthy pods continued serving traffic.
* Service endpoints remained available throughout the rollout.

After implementing this change, deployments completed successfully without downtime.

---

## Additional Improvements

Following the incident, several improvements were introduced across the deployment process.

### Readiness Probe Optimization

Readiness probe settings were reviewed and adjusted to better reflect actual application startup behavior.

Benefits:

* More accurate traffic routing.
* Reduced false readiness failures.
* Improved deployment stability.

---

### Startup Probe Configuration

Startup probes were introduced for applications with long initialization times.

Benefits:

* Prevent premature liveness failures.
* Allow sufficient application startup time.
* Improve reliability during deployments.

---

### PodDisruptionBudget (PDB)

PodDisruptionBudgets were implemented to guarantee minimum application availability.

Example:

```yaml
minAvailable: 2
```

Benefits:

* Prevents excessive pod eviction.
* Protects application availability during upgrades.
* Reduces risk during node maintenance.

---

### Rollback Validation

Rollback procedures were tested and documented.

Benefits:

* Faster incident recovery.
* Reduced Mean Time To Recovery (MTTR).
* Greater confidence during production releases.

---

## Key Lessons Learned

This incident highlighted that Kubernetes outages are rarely caused by Kubernetes itself.

Most production failures originate from:

### Incorrect Deployment Strategies

Examples:

* Aggressive rolling updates
* Unsafe maxUnavailable values
* Improper surge settings

---

### Poor Probe Configuration

Examples:

* Incorrect readiness probes
* Aggressive liveness probes
* Missing startup probes

---

### Resource Misconfiguration

Examples:

* Missing CPU limits
* Missing memory limits
* Inadequate requests

---

### Assumptions During Deployment

Examples:

* Assuming applications start instantly
* Assuming pods become ready immediately
* Assuming rollback is unnecessary

---

## Production Deployment Checklist

Following this incident, every deployment is reviewed against the following checklist:

### Deployment Strategy

* Verify rolling update configuration
* Validate maxUnavailable values
* Validate maxSurge values

### Readiness Probes

* Confirm readiness checks are accurate
* Validate startup timing

### Resource Configuration

* CPU requests defined
* CPU limits defined
* Memory requests defined
* Memory limits defined

### Rollback Planning

* Rollback tested
* Rollback documented
* Rollback ownership defined

### Monitoring Validation

* Alerts active
* Dashboards operational
* Error tracking enabled

---

## Final Thought

One line of YAML caused a complete payment service outage.

The deployment platform behaved exactly as designed.

The failure came from an unsafe configuration choice.

This incident reinforced a valuable lesson:

> Kubernetes failures are rarely caused by Kubernetes itself.

And perhaps the most important takeaway:

> Production teaches Kubernetes better than any certification.

You can paste this directly into a `README.md` file.

=============

𝗬𝗼𝘂 𝗵𝗮𝘃𝗲 𝟮 𝗺𝗶𝗻𝘂𝘁𝗲𝘀. 𝗧𝗲𝗿𝗿𝗮𝗳𝗼𝗿𝗺 𝗮𝗽𝗽𝗹𝘆 𝗳𝗮𝗶𝗹𝗲𝗱 𝗵𝗮𝗹𝗳𝘄𝗮𝘆. 𝗜𝗻𝗳𝗿𝗮𝘀𝘁𝗿𝘂𝗰𝘁𝘂𝗿𝗲 𝗶𝘀 𝗯𝗿𝗼𝗸𝗲𝗻. 𝗪𝗵𝗮𝘁 𝗱𝗼 𝘆𝗼𝘂 𝗱𝗼?

My answer: Challenge accepted, let’s go!

► Partial apply means state file is now out of sync with real infrastructure.

► First — do NOT run terraform apply again immediately. It can make things worse.

► State mismatch — some resources created, some not. State file shows incomplete picture.

► Locked state — if using S3 + DynamoDB, state may still be locked. Release it first:
 terraform force-unlock LOCK-ID

► Check what failed — read the exact error message carefully. Fix the root cause first.

► Refresh state — sync state file with real infra:
 terraform refresh

► Import missing resources — if resource exists in AWS but not in state:
 terraform import aws_instance.web i-1234567890

► Run terraform plan — see exactly what will change before applying again.

► Then run terraform apply — only after plan looks correct.

𝗛𝗼𝘄 𝗜 𝗽𝗿𝗲𝘃𝗲𝗻𝘁 𝘁𝗵𝗶𝘀 𝗮𝘀 𝗮 𝗗𝗲𝘃𝗢𝗽𝘀 𝗘𝗻𝗴𝗶𝗻𝗲𝗲𝗿:

• Always use remote state with S3 + DynamoDB locking

• Never run apply directly — always plan first

• Use workspaces to separate environments

• Enable versioning on S3 state bucket

• Use terraform validate in CI/CD before apply


-------
# Production Deployment Failed – Users Reporting Errors. What Will You Do?

When a production deployment fails and users start reporting errors, my first priority is not troubleshooting commands but incident management and business impact assessment. As a DevOps Engineer, my responsibility is to restore service as quickly as possible while keeping all stakeholders informed throughout the incident lifecycle.

## Step 1: Assess the Impact

The first step is understanding the severity of the incident.

I immediately determine:

* Which application or service is affected
* Number of impacted users
* Whether the issue is localized or system-wide
* Revenue or business impact
* Whether critical customer workflows are failing

For example, if payment APIs are failing, the priority becomes Critical (P1) because it directly impacts customer transactions and business revenue.

Understanding impact helps determine escalation level and response urgency.

---

## Step 2: Inform Stakeholders

Before making any production changes, I communicate the incident to relevant stakeholders.

Typical stakeholders include:

* Incident Manager
* Product Owners
* Engineering Teams
* Support Teams
* Management
* Customer Success Teams

A typical communication would be:

> "We are currently investigating production issues affecting payment transactions. The issue started after a recent deployment. The team is actively working on service restoration. Further updates will follow every 15 minutes."

Clear communication prevents confusion, reduces panic, and keeps everyone aligned.

---

## Step 3: Check Monitoring Dashboards

Next, I review monitoring tools to understand the scope of the issue.

I check:

* Grafana dashboards
* Prometheus metrics
* CloudWatch metrics
* Application Performance Monitoring tools
* Error rates
* Latency metrics
* CPU and memory utilization
* Pod health
* Node health

The goal is to quickly identify abnormal patterns and determine whether the issue is application-related, infrastructure-related, or deployment-related.

---

## Step 4: Verify Recent Deployments and Changes

Since the issue started after a deployment, I investigate all recent changes.

I verify:

* Latest deployment history
* Jenkins/GitLab pipeline execution
* ArgoCD synchronization status
* Kubernetes rollout history
* Infrastructure changes
* Configuration updates
* Secret modifications
* Database schema changes

One of the most common causes of production incidents is a recent deployment introducing unexpected behavior.

This step helps determine whether the deployment itself is responsible for the outage.

---

## Step 5: Analyze Logs and Alerts

Once I identify the affected components, I begin log analysis.

I review:

* Application logs
* Kubernetes pod logs
* Ingress controller logs
* Load balancer logs
* Database logs
* Container runtime logs

At the same time, I investigate alerts from:

* Prometheus Alertmanager
* Grafana
* CloudWatch
* PagerDuty

The objective is to identify the exact failure point and determine whether the issue is caused by application errors, configuration issues, networking problems, dependency failures, or infrastructure instability.

---

## Step 6: Implement Fix or Rollback

After identifying the root cause, I decide whether a rollback or a forward fix is the safest option.

If the deployment introduced the issue and rollback risk is low, I immediately revert to the last known stable version.

Typical rollback methods include:

* ArgoCD rollback
* Kubernetes rollout undo
* Helm rollback
* Re-deployment of previous container image

If rollback is not possible, I implement a targeted fix and validate it in a controlled manner before full production rollout.

The primary objective is restoring service quickly while minimizing additional risk.

---

## Step 7: Validate Service Recovery

After implementing the fix, I do not assume the issue is resolved.

I verify:

* Application availability
* API responses
* Business transactions
* Error rates
* Latency metrics
* Pod health
* User journeys

Monitoring dashboards should show recovery, and support teams should confirm that customers can successfully use the application again.

Only after validation do I declare the incident resolved.

---

## Step 8: Publish RCA and Preventive Actions

After service restoration, I conduct a Root Cause Analysis (RCA).

The RCA document typically includes:

### Incident Summary

Description of what happened.

### Timeline

Chronological sequence of events.

### Root Cause

Technical explanation of why the incident occurred.

### Business Impact

Number of affected users, downtime duration, and business consequences.

### Resolution

Actions taken to restore service.

### Preventive Measures

Steps to prevent recurrence.

Examples include:

* Additional monitoring alerts
* Better readiness probes
* Deployment validation checks
* Improved rollback automation
* Enhanced testing procedures
* Stronger change management controls

The objective is not assigning blame but improving system reliability.

---

## Final Answer for Interview

If a production deployment fails and users report errors, my approach is to first assess the business impact and communicate the incident to stakeholders. I then review monitoring dashboards, recent deployments, logs, and alerts to identify the root cause. Based on findings, I either perform a rollback or implement a fix to restore service quickly. Once recovery is confirmed through monitoring and business validation, I conduct a detailed Root Cause Analysis and implement preventive actions to avoid similar incidents in the future. As a DevOps Engineer, successful incident handling is not only about technical troubleshooting but also about communication, coordination, and ensuring business continuity.



