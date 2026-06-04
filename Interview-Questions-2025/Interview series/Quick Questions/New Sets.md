# DevOps Scenario-Based Interview Questions & Answers (4 Years Experience)

𝗗𝗼𝗰𝗸𝗲𝗿 𝗶𝗺𝗮𝗴𝗲 𝗶𝘀 𝟮.𝟭 𝗚𝗕. 𝗗𝗲𝗽𝗹𝗼𝘆𝗺𝗲𝗻𝘁 𝗶𝘀 𝘁𝗼𝗼 𝘀𝗹𝗼𝘄. 𝗙𝗶𝘅 𝗶𝘁. 𝗬𝗼𝘂 𝗵𝗮𝘃𝗲 𝟮 𝗺𝗶𝗻𝘂𝘁𝗲𝘀.

𝗠𝘆 𝗮𝗻𝘀𝘄𝗲𝗿: 𝗖𝗵𝗮𝗹𝗹𝗲𝗻𝗴𝗲 𝗮𝗰𝗰𝗲𝗽𝘁𝗲𝗱. 𝗛𝗲𝗿𝗲 𝗶𝘀 𝗵𝗼𝘄 𝗜 𝗿𝗲𝗱𝘂𝗰𝗲 𝗶𝘁 𝘁𝗼 𝘂𝗻𝗱𝗲𝗿 𝟮𝟬𝟬 𝗠𝗕.

► Use alpine base image — biggest win immediately.
 
  FROM python:3.9 → FROM python:3.9-alpine
  
  Saves 800MB+ instantly.

► Use multi-stage build — build in one stage, copy only final output.

  Stage 1: install dependencies and build
  
  Stage 2: copy only binary or built files
  
  Build tools stay in Stage 1. Never go to production image.

► Remove cache after package install:
  
  RUN apt-get install -y curl \
      && rm -rf /var/lib/apt/lists/*

► Combine RUN commands — each RUN creates a new layer.
  
  More layers = bigger image.

► Add .dockerignore — exclude node_modules, .git, logs, test files.
  
  These get copied accidentally and bloat the image.

► Remove dev dependencies — only install what production needs.
  
  npm install --production

► Use docker history image-name — see which layer is the biggest.
  
  Fix the largest layer first.

𝗛𝗼𝘄 𝗜 𝗸𝗲𝗲𝗽 𝗶𝗺𝗮𝗴𝗲𝘀 𝘀𝗺𝗮𝗹𝗹 𝗮𝗹𝘄𝗮𝘆𝘀:

• Set image size limit in CI/CD pipeline — fail if image exceeds limit

• Scan image with Trivy — remove vulnerabilities and unused packages

• Use distroless images for maximum security and minimum size

-----------------------------
## 1. A Kubernetes deployment is healthy, all pods are running, but users are getting 503 errors. How would you troubleshoot it end-to-end?

If all pods are running but users are receiving 503 errors, I would start by checking whether the Kubernetes Service has healthy endpoints using `kubectl get endpoints`. Next, I would verify the Ingress Controller or Load Balancer configuration and review its logs to ensure traffic is correctly routed to backend services. I would test connectivity from inside the cluster to the service and pod IPs using curl commands. I would also validate readiness probes because pods may be running but not ready to receive traffic. Finally, I would inspect application logs, service selectors, network policies, and DNS resolution to identify where the request flow is failing.

---

## 2. Terraform state shows resources that no longer exist in AWS. How would you identify and recover from state drift?

I would run `terraform plan` to compare the Terraform state file with the actual AWS infrastructure and identify drift. If resources were manually deleted outside Terraform, I would verify them in AWS and decide whether to recreate them or remove them from the state file using `terraform state rm`. For existing resources that are not tracked properly, I would use `terraform import` to bring them back into the state. After correcting the drift, I would run another plan to ensure the infrastructure and state are synchronized before applying any changes.

---

## 3. Your GitHub Actions pipeline suddenly starts taking 3x longer than usual. How would you investigate the bottleneck?

I would begin by comparing recent successful and slow pipeline runs to identify which stage has increased execution time. I would review GitHub Actions logs, job durations, cache hit rates, dependency downloads, test execution times, and runner performance. I would verify whether self-hosted runners are overloaded or GitHub-hosted runners are experiencing delays. Additionally, I would check for recent code changes, dependency upgrades, external API calls, or infrastructure issues that could impact pipeline performance. Based on findings, I would optimize caching, parallelize jobs, or allocate better runner resources.

---

## 4. A microservice is intermittently failing under load, but CPU and memory metrics look normal. What would be your debugging approach?

Since CPU and memory appear healthy, I would investigate other bottlenecks such as database connections, thread pools, network latency, API rate limits, and downstream service dependencies. I would analyze application logs, distributed tracing data, Prometheus metrics, and request latency patterns. Load testing tools can help reproduce the issue and identify failure points. I would also review connection pool configurations, timeout settings, and error rates to determine whether the issue is related to resource exhaustion beyond CPU and memory utilization.

---

## 5. How would you perform a zero-downtime deployment for a critical production application?

I would use deployment strategies such as Rolling Updates, Blue-Green Deployments, or Canary Releases. Before deployment, I would ensure readiness and liveness probes are properly configured. During deployment, new instances would be brought up and verified before old instances are terminated. Traffic would gradually shift to the new version while monitoring application health, latency, and error rates. If any issue is detected, rollback procedures would be triggered immediately to restore the previous stable version without impacting users.

---

## 6. A node becomes NotReady during peak traffic. What happens to workloads and how would you handle the situation?

When a node becomes NotReady, Kubernetes stops scheduling new pods on that node and eventually evicts workloads depending on configured tolerations. I would first investigate the node by checking kubelet status, system logs, disk space, network connectivity, and resource utilization. If the node cannot recover quickly, I would cordon and drain it to safely move workloads to healthy nodes. I would also ensure cluster autoscaling and high availability mechanisms are functioning correctly to minimize user impact.

---

## 7. Prometheus shows increased latency, but application logs show no errors. What layers would you investigate?

I would investigate the entire request path including load balancers, ingress controllers, service mesh components, network latency, DNS resolution, databases, caches, and external service dependencies. Distributed tracing tools such as Jaeger or OpenTelemetry can help identify slow requests. I would also review pod restart counts, network throughput, connection saturation, and infrastructure metrics to determine whether latency originates from the application layer or an underlying platform component.

---

## 8. How would you design a multi-environment CI/CD strategy for 100+ microservices?

I would implement a standardized CI/CD framework using reusable pipeline templates and Infrastructure as Code. Each microservice would follow the same workflow for build, test, security scanning, containerization, and deployment. Separate environments such as Development, QA, Staging, and Production would be managed through GitOps practices and environment-specific configurations. Automated approvals, deployment gates, and monitoring integrations would ensure consistent and scalable deployments across all services.

---

## 9. A container works perfectly in development but repeatedly fails in production Kubernetes clusters. What would you check first?

I would compare the development and production environments, including environment variables, ConfigMaps, Secrets, resource limits, storage configurations, and network policies. I would inspect pod logs, describe pod events, and check for CrashLoopBackOff or OOMKilled conditions. Differences in Kubernetes versions, image tags, permissions, or external dependencies can also cause failures. My focus would be on identifying environment-specific differences that do not exist in development.

---

## 10. How would you secure secrets across GitHub Actions, Kubernetes, and cloud infrastructure?

I would avoid storing secrets directly in repositories or container images. GitHub Actions secrets would be used for CI/CD workflows, while Kubernetes secrets would be managed through tools such as External Secrets Operator or HashiCorp Vault. Cloud-native secret managers such as AWS Secrets Manager would be used for centralized secret storage and rotation. Access would follow least privilege principles with proper auditing, encryption, and periodic rotation policies.

---

## 11. During deployment, database schema changes are required. How would you avoid downtime and rollback risks?

I would use backward-compatible database migration strategies. Schema changes would be deployed first without affecting the current application version. The application would then be updated to use the new schema while maintaining compatibility with the previous version. Database migrations would be automated through CI/CD pipelines and tested in lower environments before production rollout. Rollback plans would ensure the previous application version can continue operating without schema conflicts.

---

## 12. How would you identify the root cause of sudden cloud cost spikes in a production environment?

I would analyze cloud cost reports, billing dashboards, and resource utilization metrics to identify which services contributed to the increase. Tag-based cost allocation would help pinpoint affected applications or teams. I would review recent deployments, autoscaling activities, storage growth, data transfer usage, and orphaned resources. After identifying the cause, I would implement cost optimization measures and configure alerts to detect similar spikes proactively.

---

## 13. An application is consuming memory continuously and eventually crashes. How would you prove whether it's a memory leak?

I would monitor memory utilization trends over time using Prometheus, CloudWatch, or Grafana dashboards. Heap dumps and profiling tools would be used to analyze memory allocation patterns and identify objects that are not being released. Load testing can help reproduce the issue consistently. If memory usage continuously grows without returning to normal levels after garbage collection, it strongly indicates a memory leak that requires code-level investigation.

---

## 14. Explain a strategy for disaster recovery if an entire Kubernetes cluster becomes unavailable.

A disaster recovery strategy should include Infrastructure as Code, automated cluster provisioning, backup of application data, and backup of Kubernetes resources. Tools such as Velero can be used for backup and restore operations. Applications should be deployed across multiple availability zones, and critical workloads can be replicated in secondary regions. Recovery procedures should be tested regularly to ensure business continuity during a complete cluster outage.

---

## 15. You receive a critical alert at 2 AM stating "Application Down". What is your first 30-minute action plan?

In the first few minutes, I would acknowledge the alert and assess its impact by checking monitoring dashboards and user-facing services. I would verify application health, infrastructure status, Kubernetes resources, and recent deployments. If a recent release is suspected, I would initiate a rollback. Simultaneously, I would review logs, metrics, and alerts to identify the root cause while keeping stakeholders informed. The priority during the first 30 minutes is to restore service availability quickly and then perform a detailed root cause analysis after stabilization.

---

**Suitable for:** DevOps Engineer (4 Years Experience) | Kubernetes | AWS | Terraform | GitHub Actions | CI/CD | Monitoring | Production Support



"A pod enters CrashLoopBackOff. What do you do?"

━━━━━━━━━━━━━━━━━━━━━

CrashLoopBackOff means your container keeps exiting and Kubernetes is backing off before restarting it. It is NOT the error — it's the symptom.

Here's the exact 5-step playbook:

𝟭. kubectl describe pod → Events section tells you what Kubernetes saw

𝟮. kubectl logs --previous → this shows the LAST crash, not the current one (most people miss this)

𝟯. Read the exit code:

   • 137 = OOMKilled (memory limit hit)
 
   • 1   = Application crash / unhandled error
 
   • 127 = Binary not found in image

   • 139 = Segmentation fault

𝟰. Check ENV vars, ConfigMaps, Secrets — a missing variable kills apps on startup

𝟱. Fix root cause in the deployment spec → redeploy → monitor

𝗕𝗼𝗻𝘂𝘀: On K8s 1.23+ use ephemeral debug containers:

kubectl debug -it <pod> --image=busybox --target=<container>

---------
𝗧𝗲𝗿𝗿𝗮𝗳𝗼𝗿𝗺 𝗮𝗽𝗽𝗹𝘆 𝗶𝘀 𝘀𝘁𝘂𝗰𝗸. 𝗦𝘁𝗮𝘁𝗲 𝗳𝗶𝗹𝗲 𝗶𝘀 𝗹𝗼𝗰𝗸𝗲𝗱. 𝗧𝗲𝗮𝗺 𝗶𝘀 𝘄𝗮𝗶𝘁𝗶𝗻𝗴. 𝗪𝗵𝗮𝘁 𝗱𝗼 𝘆𝗼𝘂 𝗱𝗼?

𝗠𝘆 𝗮𝗻𝘀𝘄𝗲𝗿: 𝗖𝗵𝗮𝗹𝗹𝗲𝗻𝗴𝗲 𝗮𝗰𝗰𝗲𝗽𝘁𝗲𝗱. 𝗛𝗲𝗿𝗲 𝗶𝘀 𝗺𝘆 𝗲𝘅𝗮𝗰𝘁 𝗮𝗽𝗽𝗿𝗼𝗮𝗰𝗵.

► Check who locked the state — DynamoDB table holds the lock record.
  aws dynamodb scan --table-name terraform-lock

► If the previous apply crashed — lock was never released automatically.

► Force unlock only after confirming no apply is running:
  terraform force-unlock LOCK-ID

► Never force unlock blindly — if two applies run simultaneously, state gets corrupted.

► After unlock — run terraform plan first. Never apply directly.

► Check what partial changes were made — some resources may already exist.

► Run terraform refresh — sync state with actual AWS infrastructure.

► Targeted apply if needed — fix only the failed resource:
 
  terraform apply -target=aws_instance.web

𝗛𝗼𝘄 𝗜 𝗽𝗿𝗲𝘃𝗲𝗻𝘁 𝘁𝗵𝗶𝘀:

• Always use remote state with S3 + DynamoDB locking

• Set timeout on CI/CD pipeline — auto cancel stuck apply

• Never run terraform apply locally in production

• Use Atlantis or Terraform Cloud for team collaboration

-----------------------------

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


-----
“ 𝗬𝗼𝘂𝗿 𝗖𝗜/𝗖𝗗 𝗽𝗶𝗽𝗲𝗹𝗶𝗻𝗲 𝗽𝗮𝘀𝘀𝗲𝗱. 𝗕𝘂𝘁 𝗣𝗿𝗼𝗱 𝘀𝘁𝗶𝗹𝗹 𝗵𝗮𝘀 𝗼𝗹𝗱 𝗰𝗼𝗱𝗲. 𝗘𝘅𝗽𝗹𝗮𝗶𝗻. ”

My answer: I have seen this before. Here is exactly why.

► Pipeline passed — but deployment step may have been skipped.
  
  Check pipeline logs — did deploy stage actually run?

► Wrong environment — pipeline deployed to Staging not Production.
 
  Check deploy script — which environment variable is set?

► Cache issue — browser or CDN is serving cached old version.

  Hard refresh: Ctrl + Shift + R — check if new code appears.

  Invalidate CloudFront cache if using CDN.

► Health check rollback — new deployment failed health check.

  Load balancer silently rolled back to old version.

  Check ALB target group — which version is active?

► Wrong image tag — deployment pulled 
latest but latest was not updated.

  Always use specific image tags. Never use :latest in production.

► Blue-Green switch missed — new version deployed but traffic not switched.

  Check load balancer listener rules.

𝗛𝗼𝘄 𝗜 𝘀𝗲𝘁 𝘂𝗽 𝗯𝘂𝗹𝗹𝗲𝘁𝗽𝗿𝗼𝗼𝗳 𝗖𝗜/𝗖𝗗:
• Add smoke test after deployment — verify new version is live

• Use specific image tags — never :latest in production

• Add deployment verification step in pipeline

• Set up CloudWatch alarm on deployment events

• Always check target group after blue-green switch


# Kubernetes & Terraform Interview Questions (4+ Years Experience)

---

# Kubernetes

## 1. Explain the Kubernetes architecture.

Kubernetes follows a master-worker architecture. The Control Plane (Master Node) manages the cluster, while Worker Nodes run application workloads. The Control Plane consists of components such as API Server, etcd, Scheduler, and Controller Manager. The API Server acts as the entry point for all cluster operations. etcd is a distributed key-value store that stores cluster state and configuration. The Scheduler decides which worker node should run a pod based on resource availability and scheduling constraints. The Controller Manager continuously monitors the cluster and ensures the desired state matches the actual state.

Worker Nodes contain components such as kubelet, kube-proxy, and the container runtime. Kubelet communicates with the Control Plane and manages pods on the node. Kube-proxy handles networking and service communication. The container runtime, such as containerd or Docker, is responsible for running containers. Together these components provide container orchestration, scaling, self-healing, service discovery, and automated deployments.

---

## 2. Difference between Deployment, StatefulSet, and DaemonSet.

A Deployment is used for stateless applications where pod identity does not matter. It supports rolling updates, rollbacks, scaling, and self-healing. Examples include web applications, APIs, and frontend services.

A StatefulSet is used for stateful applications where pod identity and storage persistence are important. Each pod gets a unique hostname and stable storage. Examples include databases such as MySQL, PostgreSQL, MongoDB, and Kafka.

A DaemonSet ensures that exactly one pod runs on every worker node. Whenever a new node joins the cluster, Kubernetes automatically schedules a DaemonSet pod on that node. Examples include Fluentd, Prometheus Node Exporter, Datadog agents, and log collection services.

In production, Deployments are used for stateless microservices, StatefulSets for databases, and DaemonSets for node-level monitoring and logging agents.

---

## 3. What happens when a pod crashes?

When a pod crashes, Kubernetes automatically attempts to recover it based on the restart policy. The kubelet running on the node detects that the container has exited unexpectedly. If the restart policy is set to Always, Kubernetes restarts the container automatically.

If the application repeatedly crashes during startup, Kubernetes enters a CrashLoopBackOff state. In this condition, Kubernetes keeps restarting the container but introduces increasing delays between restart attempts to avoid excessive resource consumption.

If the underlying node fails completely, the controller responsible for the workload, such as Deployment or StatefulSet, creates replacement pods on healthy nodes. This self-healing capability is one of the key advantages of Kubernetes.

---

## 4. How do readiness and liveness probes work?

Readiness and liveness probes are health checks used by Kubernetes to determine application availability and health.

A readiness probe determines whether a pod is ready to receive traffic. If the readiness probe fails, Kubernetes removes the pod from service endpoints, preventing user requests from reaching it. The pod remains running but does not receive traffic.

A liveness probe determines whether the application is healthy internally. If the liveness probe fails repeatedly, Kubernetes restarts the container automatically.

For example, during application startup, readiness probes may fail because the application is still initializing. Once initialization completes, readiness succeeds and traffic is routed to the pod. If the application later becomes unresponsive due to a deadlock or memory issue, the liveness probe fails and Kubernetes restarts the container.

Together these probes improve application reliability and reduce downtime.

---

## 5. Explain HPA (Horizontal Pod Autoscaler).

Horizontal Pod Autoscaler automatically adjusts the number of pod replicas based on workload demand. It continuously monitors metrics such as CPU utilization, memory utilization, or custom Prometheus metrics and scales pods up or down accordingly.

For example, if CPU utilization exceeds 80%, HPA may increase pod replicas from three to six. When traffic decreases and resource usage falls below configured thresholds, HPA reduces the number of replicas to save resources and costs.

In production environments, HPA is commonly combined with Cluster Autoscaler. HPA scales application pods, while Cluster Autoscaler adds or removes worker nodes when additional infrastructure capacity is required.

This combination provides elasticity and ensures applications can handle traffic spikes efficiently.

---

## 6. How do you perform zero-downtime deployments?

Zero-downtime deployments ensure application availability throughout the deployment process. The most common approach is Rolling Update deployment strategy.

In a rolling update, Kubernetes gradually replaces old pods with new ones while keeping a minimum number of healthy pods available. Proper readiness probes ensure traffic is routed only to healthy pods. PodDisruptionBudgets prevent excessive pod termination during updates.

Other deployment strategies include Blue-Green Deployment and Canary Deployment. Blue-Green Deployment maintains two identical environments and switches traffic after validation. Canary Deployment gradually shifts a small percentage of traffic to the new version before full rollout.

In production, readiness probes, multiple replicas, rolling updates, and rollback plans are essential for achieving zero downtime.

---

## 7. What is a ConfigMap and Secret?

A ConfigMap is used to store non-sensitive configuration data such as application settings, environment variables, URLs, feature flags, and configuration files. It allows applications to consume configuration without hardcoding values into container images.

A Secret is used to store sensitive information such as passwords, API keys, database credentials, tokens, and certificates. Kubernetes stores secrets in a base64-encoded format and can integrate with external secret management systems such as AWS Secrets Manager or HashiCorp Vault.

Using ConfigMaps and Secrets improves security, configuration management, and deployment flexibility by separating configuration from application code.

---

## 8. How do you troubleshoot a pod in CrashLoopBackOff state?

When a pod enters CrashLoopBackOff, it means Kubernetes is repeatedly restarting the container because the application is failing to start successfully.

The first step is checking pod logs to identify application errors. Next, I inspect pod events to determine whether the issue is related to image pull failures, resource constraints, failed probes, configuration errors, or dependency issues.

I verify:
- Application logs
- Environment variables
- Secrets and ConfigMaps
- Resource limits
- Resource requests
- Readiness and liveness probes
- Image versions
- External dependency connectivity

Common causes include application crashes, invalid configuration, insufficient memory, missing secrets, database connectivity failures, and startup script errors.

After identifying the root cause, I apply the fix, redeploy the workload, and monitor pod stability.

---

## Scenario: Application is inaccessible but all pods are running. How will you troubleshoot?

If all pods are running but the application is inaccessible, I do not immediately assume the pods are healthy because a Running state only indicates that containers are running, not that the application is serving traffic.

I first verify pod readiness status because pods may be running while failing readiness probes. Next, I check the Service configuration to ensure selectors match the correct pods and endpoints are populated properly.

After verifying Services, I inspect Ingress resources, ingress controller logs, load balancer health checks, DNS resolution, and network policies. I also test connectivity between pods and services to identify networking issues.

If infrastructure components appear healthy, I review application logs and metrics to determine whether the application is experiencing internal errors despite running containers.

My troubleshooting flow is:

Application → Pod Health → Readiness → Service → Endpoints → Ingress → Load Balancer → DNS → Network Policies → Logs → Monitoring

This systematic approach helps identify the exact failure point quickly in production environments.

---

# Terraform (IaC)

## 1. What is Infrastructure as Code?

Infrastructure as Code (IaC) is the practice of managing and provisioning infrastructure through code rather than manual processes. Instead of creating resources through cloud consoles, engineers define infrastructure using configuration files.

With IaC, infrastructure becomes version-controlled, repeatable, automated, and auditable. It enables teams to provision environments consistently across development, testing, and production.

Terraform, CloudFormation, Pulumi, and Ansible are common Infrastructure as Code tools used in modern DevOps environments.

---

## 2. Explain Terraform state file.

Terraform state file is a metadata file that stores information about infrastructure resources managed by Terraform. It acts as the source of truth that maps Terraform configuration to actual cloud resources.

The state file contains:
- Resource IDs
- Resource attributes
- Dependency information
- Infrastructure metadata

Terraform uses the state file to determine which resources already exist and what changes need to be applied during future deployments.

Without the state file, Terraform cannot accurately track or manage infrastructure.

---

## 3. What is remote state and why is it required?

Remote state refers to storing Terraform state files in a centralized backend rather than on a local machine. Common backends include AWS S3, Azure Storage Accounts, and Terraform Cloud.

Remote state is required because multiple engineers and CI/CD pipelines may manage the same infrastructure. Centralized state storage ensures consistency, collaboration, backup, and recovery.

In AWS environments, S3 is typically used for state storage while DynamoDB provides state locking to prevent concurrent modifications.

Remote state is considered a production best practice.

---

## 4. Difference between Terraform and CloudFormation.

Terraform is an Infrastructure as Code tool developed by HashiCorp that supports multiple cloud providers including AWS, Azure, Google Cloud, and Kubernetes. It uses HCL (HashiCorp Configuration Language) and provides a provider-based architecture.

CloudFormation is AWS's native Infrastructure as Code service and only supports AWS resources. It uses JSON or YAML templates and integrates deeply with AWS services.

Terraform is preferred in multi-cloud environments, while CloudFormation is suitable for organizations operating exclusively within AWS.

---

## 5. What are Terraform modules?

Terraform modules are reusable collections of Terraform resources that encapsulate infrastructure logic. They help eliminate code duplication and improve maintainability.

For example, instead of repeatedly writing VPC configurations for multiple environments, a reusable VPC module can be created and invoked with different parameters.

Modules improve standardization, scalability, and code organization, making them essential in enterprise Terraform projects.

---

## 6. How do you manage multiple environments?

Multiple environments such as Development, UAT, and Production can be managed using separate state files, workspaces, variable files, and environment-specific configurations.

A common approach is maintaining separate directories or variable files for each environment while reusing the same Terraform modules. Each environment has its own backend configuration and state file to ensure isolation.

This approach enables consistent infrastructure provisioning while preventing accidental changes across environments.

---

## 7. How do you secure Terraform state files?

Terraform state files often contain sensitive information such as passwords, resource identifiers, and infrastructure metadata. Therefore, securing them is critical.

In production environments, state files are stored in encrypted S3 buckets with versioning enabled. Access is restricted using IAM roles and policies following the principle of least privilege.

Additional security measures include:
- State encryption
- Access logging
- Remote state storage
- State locking with DynamoDB
- Secret management integration
- Regular backups

Sensitive values should never be hardcoded into Terraform code or exposed publicly.

---

## Scenario: Terraform state file gets corrupted. What steps will you take?

If a Terraform state file becomes corrupted, the first step is preventing additional deployments to avoid further inconsistencies. I immediately verify the extent of corruption and check whether backups or previous state versions are available.

If the state is stored in S3 with versioning enabled, I restore a previous healthy version of the state file. Before restoring, I compare the infrastructure currently running in the cloud with the state version being restored to avoid introducing drift.

After recovery, I run Terraform plan to verify that the restored state accurately reflects the actual infrastructure. If required, I use terraform import to bring unmanaged resources back into state tracking.

Finally, I identify the root cause of corruption, whether it was caused by manual modifications, concurrent deployments, backend issues, or failed CI/CD pipelines. I then implement preventive measures such as state locking, backup validation, restricted access controls, and improved deployment governance to prevent recurrence.

# Terraform, Kubernetes, Git, AWS & Linux Interview Questions (4+ Years Experience)

---

# 1. What is Terraform workspace?

Terraform Workspace is a feature that allows multiple state files to be managed using the same Terraform configuration. Each workspace maintains its own separate state file, enabling teams to deploy the same infrastructure code across multiple environments such as Development, UAT, and Production.

For example, a single Terraform codebase can create separate resources for dev, test, and prod by switching between workspaces. While workspaces are useful for smaller projects, many enterprise organizations prefer separate backend configurations and state files for production environments to ensure better isolation and security.

---

# 2. Where do we store the state file for Terraform?

In production environments, Terraform state files are usually stored remotely rather than on local machines. The most common approach is storing the state file in an AWS S3 bucket while using DynamoDB for state locking.

Remote storage provides centralized access, versioning, backup, security, collaboration, and disaster recovery. Storing state remotely ensures all engineers and CI/CD pipelines work with the same source of truth.

---

# 3. If I don't want to store the state file in S3, what are the other options?

Terraform supports multiple backend options for storing state files. Besides S3, state files can be stored in:

- Terraform Cloud
- Azure Storage Account
- Google Cloud Storage (GCS)
- HashiCorp Consul
- HTTP Backend
- Kubernetes Backend
- Local Backend
- PostgreSQL-based remote backends

Terraform Cloud is often preferred because it provides state storage, locking, collaboration, policy enforcement, and execution management in a single platform.

---

# 4. If two people run terraform apply at the same time, whose apply will be successful?

When Terraform state locking is configured correctly using a backend such as S3 and DynamoDB, only one user can acquire the lock.

The first person who acquires the lock successfully proceeds with the deployment. The second user receives a state lock error and must wait until the first deployment completes.

This mechanism prevents concurrent modifications, state corruption, and infrastructure inconsistencies.

---

# 5. What is deadlock in Terraform?

Terraform itself does not commonly experience traditional database deadlocks, but interviewers usually refer to a stale or stuck state lock situation.

For example, if a Jenkins job acquires a DynamoDB lock and crashes before releasing it, Terraform believes another deployment is still running. Future deployments fail with:

```bash
Error acquiring the state lock
```

This situation is often referred to as a Terraform lock deadlock. The lock must be verified and manually released using:

```bash
terraform force-unlock <LOCK_ID>
```

after ensuring no active deployment is running.

---

# 6. What all things happen when we run terraform init?

Terraform init is the first command executed in a Terraform project.

When it runs, Terraform:

- Initializes the working directory
- Downloads required provider plugins
- Configures backend storage
- Downloads Terraform modules
- Validates backend configuration
- Creates the .terraform directory
- Sets up state management components

No infrastructure changes occur during terraform init. It only prepares the environment for future plan and apply operations.

---

# 7. If a colleague manually changes/deletes resources in the cloud console and the manager says those changes are correct, how do you incorporate them into Terraform?

Infrastructure should always remain aligned with Terraform code. If manual changes are approved and need to become permanent, I first identify exactly what was modified.

I then update the Terraform code to reflect the approved changes. After updating the code, I run terraform plan to verify the desired state matches the current infrastructure.

If resources were created manually outside Terraform, I use terraform import to bring them under Terraform management.

The goal is to eliminate infrastructure drift and ensure Terraform remains the single source of truth.

---

# 8. How many types of Services are there in Kubernetes?

Kubernetes provides four primary service types:

### ClusterIP

Default service type used for internal communication within the cluster.

### NodePort

Exposes the application on a static port across worker nodes.

### LoadBalancer

Creates an external cloud load balancer and exposes the application publicly.

### ExternalName

Maps a Kubernetes service to an external DNS name.

ClusterIP is commonly used for internal microservices, while LoadBalancer and Ingress are preferred for external access.

---

# 9. What is the purpose of Ingress?

Ingress provides centralized HTTP and HTTPS routing for applications running inside Kubernetes.

Instead of creating a separate load balancer for every application, Ingress allows multiple services to share a single entry point. It supports:

- Path-based routing
- Host-based routing
- SSL termination
- Load balancing
- Authentication integration

Ingress simplifies traffic management and reduces infrastructure costs.

---

# 10. How many branching strategies are there in Git?

There are several Git branching strategies commonly used in organizations:

### Git Flow

Uses feature, develop, release, and hotfix branches.

### Feature Branch Workflow

Each feature is developed in a separate branch before merging.

### GitHub Flow

Simple branching model focused on continuous deployment.

### GitLab Flow

Combines environment-based deployment workflows.

### Trunk-Based Development

Developers commit frequently to a shared main branch.

Modern DevOps teams often prefer Trunk-Based Development because it supports rapid CI/CD workflows.

---

# 11. What is Git rebase?

Git rebase moves or reapplies commits from one branch onto another branch.

Instead of creating a merge commit, rebase creates a cleaner and more linear commit history.

For example:

```bash
git rebase main
```

This replays current branch commits on top of the latest main branch changes.

Rebase helps keep commit history clean but should be used carefully on shared branches.

---

# 12. How do you rename a branch in Git?

To rename the current branch:

```bash
git branch -m new-branch-name
```

To rename a different branch:

```bash
git branch -m old-branch-name new-branch-name
```

After renaming, update the remote repository:

```bash
git push origin -u new-branch-name
git push origin --delete old-branch-name
```

---

# 13. How do you remove the second last commit from Git history?

One common approach is interactive rebase:

```bash
git rebase -i HEAD~2
```

The editor opens and displays the last two commits.

Change:

```text
pick
pick
```

to:

```text
drop
pick
```

for the commit you want to remove.

After saving, Git rewrites history and removes the selected commit.

---

# 14. What is Git history?

Git history is the complete record of changes made to a repository over time.

It contains:

- Commits
- Authors
- Commit messages
- Timestamps
- Branch information

Common commands:

```bash
git log
```

```bash
git log --oneline
```

Git history helps teams track changes, audit modifications, troubleshoot issues, and understand code evolution.

---

# 15. What is NACL and Security Group in AWS?

Security Groups and Network ACLs are AWS security mechanisms used to control network traffic.

Security Groups operate at the instance level and act as virtual firewalls. They are stateful, meaning return traffic is automatically allowed.

Network ACLs operate at the subnet level and control inbound and outbound traffic. They are stateless, meaning rules must be explicitly configured for both directions.

Security Groups are generally the first layer checked during application connectivity troubleshooting.

---

# 16. Between NACL rule 99 and 100, which has higher priority?

Lower numbered NACL rules have higher priority.

Therefore:

```text
Rule 99
```

has higher priority than:

```text
Rule 100
```

AWS evaluates NACL rules from lowest to highest number and applies the first matching rule.

---

# 17. What are the key components of the Kubernetes Control Plane (Master Plane)?

The Kubernetes Control Plane consists of:

### API Server

Entry point for all cluster operations.

### etcd

Distributed key-value database storing cluster state.

### Scheduler

Assigns pods to worker nodes.

### Controller Manager

Maintains desired cluster state.

### Cloud Controller Manager

Integrates Kubernetes with cloud provider services.

These components collectively manage cluster operations, scheduling, scaling, and resource lifecycle.

---

# 18. Developers need view access and DevOps need write access in production. How would you define this in Kubernetes?

This is implemented using RBAC (Role-Based Access Control).

I would create:

- A Read-Only Role for developers
- A Read/Write Role for DevOps engineers

Developers receive permissions such as:

- get
- list
- watch

DevOps teams receive additional permissions:

- create
- update
- patch
- delete

Roles are attached to users or groups using RoleBindings or ClusterRoleBindings.

This follows the principle of least privilege and improves production security.

---

# 19. How do you block pod-to-pod communication in Kubernetes?

Pod-to-pod communication can be controlled using Network Policies.

A Network Policy defines which pods can communicate with other pods based on:

- Labels
- Namespaces
- Ports
- Protocols

By default, Kubernetes allows unrestricted pod communication.

To block communication, I create restrictive Network Policies and allow only required traffic paths.

This is commonly used in production to implement micro-segmentation and improve security.

---

# 20. What are the different forms of scaling in Kubernetes?

Kubernetes supports multiple scaling mechanisms.

### Horizontal Pod Autoscaler (HPA)

Scales pod replicas based on resource utilization.

### Vertical Pod Autoscaler (VPA)

Adjusts CPU and memory allocations.

### Cluster Autoscaler

Adds or removes worker nodes automatically.

### KEDA

Scales workloads based on external events such as queue length or message count.

Production environments often combine HPA and Cluster Autoscaler for complete elasticity.

---

# 21. What are maxUnavailable and maxSurge in Kubernetes?

These settings control rolling updates.

### maxUnavailable

Maximum number of pods that can be unavailable during deployment.

Example:

```yaml
maxUnavailable: 1
```

allows only one unavailable pod.

### maxSurge

Maximum number of extra pods created during deployment.

Example:

```yaml
maxSurge: 1
```

allows one additional pod beyond the desired replica count.

Proper configuration helps achieve zero-downtime deployments.

---

# 22. What is a Pod Disruption Budget (PDB) in Kubernetes?

A Pod Disruption Budget defines the minimum number of application pods that must remain available during voluntary disruptions.

Examples:

- Node upgrades
- Cluster maintenance
- Pod evictions

Example:

```yaml
minAvailable: 2
```

ensures at least two healthy pods remain available.

PDBs help maintain application availability during infrastructure operations.

---

# 23. What is an inode in Linux?

An inode is a data structure that stores metadata about a file.

It contains:

- Owner information
- Permissions
- File size
- Timestamps
- Disk block locations

An inode does not store the filename itself.

Every file and directory has a unique inode number within a filesystem.

---

# 24. If I change file permissions, will the inode number change?

No.

Changing permissions updates metadata stored inside the inode, but the inode number itself remains unchanged.

Operations such as:

```bash
chmod
```

or

```bash
chown
```

modify inode contents but do not create a new inode.

---

# 25. Can I search for a file or work with it using the inode number?

Yes.

Files can be searched using inode numbers.

Example:

```bash
find / -inum 123456
```

This locates a file based on its inode.

You can also perform operations such as:

- Delete
- Change permissions
- Change ownership

after locating the file through its inode.

However, most commands operate on filenames rather than directly using inode numbers, so the inode is usually used for identification and troubleshooting purposes.

