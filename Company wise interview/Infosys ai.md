# DevOps Interview Questions and Answers (Glider AI)

## Question 1

**You mentioned that you would check if entire regions or multiple services are affected — if you ... what specific steps would you take to initiate a cross-region failover and ensure continued compliance?**

### Answer

If I determine that an entire region is unavailable or multiple critical services are impacted, my first step is to verify the outage using cloud service health dashboards, monitoring tools, and application metrics to eliminate false positives.

Once confirmed, I would activate the organization's Disaster Recovery (DR) plan. If Route53, Azure Traffic Manager, or Google Cloud Load Balancer is configured for failover, I would redirect traffic to the secondary region. If DNS-based failover is used, I would update DNS records while considering TTL values.

For Kubernetes workloads, I would ensure the secondary EKS/AKS/GKE cluster is healthy and scale workloads if required. Databases must already have cross-region replication configured, such as Aurora Global Database, Azure SQL Geo-Replication, or Cloud SQL replicas. Before switching traffic, I would verify replication lag to minimize data loss.

After failover, I would validate application health, APIs, authentication, databases, and user transactions. I would continuously monitor logs and metrics to ensure stability.

To maintain compliance, I would ensure encryption remains enabled, IAM policies remain unchanged, audit logs continue to be collected, and infrastructure changes are documented. Finally, I would communicate status updates to stakeholders and prepare a rollback plan once the primary region becomes stable.

---

## Follow-up Question 1

**You mentioned prioritising which services to extract and letting the monolith and microservices coexist... How would you manage deployments for both architectures during this phase?**

### Answer

During coexistence, I would maintain separate CI/CD pipelines for the monolith and each microservice while keeping both in the same source control strategy.

The monolith would continue using its existing deployment pipeline, whereas every microservice would have its own independent build, test, security scan, containerization, and deployment pipeline.

I would use Kubernetes namespaces or separate environments to isolate deployments. Traffic routing would be handled through an API Gateway or service mesh such as Istio, allowing specific requests to be routed either to the monolith or the extracted microservice.

Feature flags and canary deployments would minimize risk. Shared databases would be managed carefully using backward-compatible schema changes and API versioning until services become fully independent.

Monitoring would include centralized logging, distributed tracing, and dashboards to detect issues quickly during coexistence.

---

# Question 2

**Your DevOps team is planning to migrate a complex monolithic application to ... advanced DevOps strategies and tools you would implement to ensure smooth ... robust monitoring throughout the transition.**

### Answer

Migrating a monolith to microservices should be performed incrementally rather than as a big-bang migration.

I would first analyze the application to identify bounded contexts and prioritize low-risk services such as authentication, notifications, or reporting for extraction.

Infrastructure would be provisioned using Terraform. Containers would be built using Docker and deployed on Kubernetes. CI/CD pipelines using Jenkins, GitHub Actions, or Azure DevOps would automate builds, testing, security scans, and deployments.

I would introduce an API Gateway for routing and a service mesh like Istio for traffic management, security, retries, and observability.

Deployment strategies would include Blue-Green or Canary deployments with automatic rollback if health checks fail.

Monitoring would be implemented using Prometheus, Grafana, ELK, and OpenTelemetry for distributed tracing. Alerts would be configured for latency, error rates, CPU, memory, and application failures.

Throughout migration, both the monolith and microservices would coexist until validation confirms successful migration.

---

## Question 3

**Your client's application on Azure is experiencing sporadic authentication failures after recent changes ... and App Service configurations. Describe your troubleshooting approach and which Azure tools or logs ... root cause identification.**

### Answer

I would begin by identifying whether the authentication failures affect all users or only specific users.

First, I would compare recent Azure App Service configuration changes using Activity Logs and deployment history.

Next, I would review Azure AD Sign-in Logs to identify authentication errors, Conditional Access failures, token issues, or expired credentials.

I would examine Application Insights to trace failed authentication requests and correlate them with application logs.

App Service diagnostic logs, Log Stream, and Azure Monitor would help determine whether configuration changes introduced failures.

I would verify Managed Identity, App Registration, redirect URIs, client secrets, certificates, and environment variables.

If networking is involved, I would validate NSGs, private endpoints, firewall rules, and DNS resolution.

Finally, I would rollback the recent configuration if necessary, validate authentication after rollback, and continue monitoring until stability is confirmed.

---

## Follow-up Question 3

**Since you mentioned comparing the current configuration with the previous ... monitoring data to pinpoint where the authentication process is failing?**

### Answer

I would correlate logs across Azure Monitor, Application Insights, and Azure AD Sign-in Logs using timestamps and correlation IDs.

Application Insights would allow me to follow the complete authentication request flow from the client to Azure AD and back to the application.

Azure Monitor Log Analytics with Kusto Query Language (KQL) would help identify failed requests, response codes, latency, and authentication exceptions.

I would compare successful and failed authentication attempts to identify common patterns such as expired tokens, invalid redirect URIs, certificate issues, or Conditional Access policy failures.

This correlation provides an accurate root cause while minimizing troubleshooting time.

---

# Question 4

**A client on Google Cloud Platform wants to minimise downtime during VM patching while ... Explain how you would design the patch management workflow using GCP-native tools to ...**

### Answer

I would use Google Cloud OS Config Patch Management to automate patch deployment.

VMs would be grouped using labels so patches can be applied environment-wise.

The patch process would first execute in development, then testing, staging, and finally production after validation.

Managed Instance Groups behind a Load Balancer would ensure rolling updates where only a small percentage of instances are patched at a time.

Health checks would verify application availability before continuing with additional instances.

Cloud Monitoring and Cloud Logging would validate successful patch deployment, while Cloud Scheduler could automate maintenance windows.

If issues occur, instance templates or machine images would allow rapid rollback.

---

## Follow-up Question 4

**You mentioned validating patches in lower environments before deploying to production ... across those different environments using GCP-native tooling to minimise downtime?**

### Answer

I would maintain separate GCP projects for Development, QA, Staging, and Production.

Infrastructure would be provisioned using Terraform, ensuring consistency across all environments.

Cloud Build would automate deployments while Cloud Deploy would promote releases sequentially after successful validation.

Patch jobs would target VM labels for each environment.

Monitoring, health checks, and automated validation would confirm successful deployments before promotion to the next stage.

This controlled promotion minimizes downtime while ensuring production stability.

---

# Question 5

**Your team is tasked with implementing a CI/CD pipeline for a regulated financial services application to ... enforce compliance and extensive audit trails. Describe the advanced CI/CD design, controls, and tools you would use while satisfying deployment speed.**

### Answer

For a regulated financial application, security, compliance, and traceability are as important as deployment speed.

The pipeline would begin with source code stored in Git using protected branches and mandatory pull request approvals.

Each commit would trigger automated builds, unit tests, integration tests, SAST, dependency scanning, and secret scanning.

Docker images would be scanned before being stored in a secure registry.

Infrastructure would be managed using Terraform with policy validation before deployment.

Deployments would use Blue-Green or Canary strategies with automatic rollback based on health checks.

Every deployment would generate audit logs including commit ID, approver, deployment time, pipeline execution, artifact version, and rollback history.

Secrets would be stored securely using AWS Secrets Manager, Azure Key Vault, or Google Secret Manager.

Continuous monitoring would be implemented using Prometheus, Grafana, ELK, and SIEM tools to ensure compliance.

---

## Follow-up Question 5

**You mentioned enforcing Pull Request reviews and approval policies — how would you ensure a secure approval workflow while maintaining deployment speed?**

### Answer

I would implement branch protection rules requiring at least two reviewers before merging into protected branches.

Automated quality gates would verify code coverage, security scans, and successful builds before approval is permitted.

CODEOWNERS would automatically assign reviewers for sensitive modules.

Emergency production fixes would use an expedited approval process with complete audit logging.

Approvals would integrate with identity providers using RBAC and MFA.

After approval, deployments would proceed automatically to eliminate manual delays while maintaining compliance.

---

# Question 6

**How are automating infrastructure provisioning for multiple cloud environments ... What approaches would you recommend to manage environment-specific configurations across Azure, Google Cloud Platform, and AWS while maintaining consistency?**

### Answer

I would adopt Infrastructure as Code using Terraform because it supports multiple cloud providers with a consistent workflow.

Reusable Terraform modules would define common infrastructure components, while separate variable files or Terraform workspaces would manage environment-specific values.

Sensitive information would be stored in AWS Secrets Manager, Azure Key Vault, or Google Secret Manager instead of configuration files.

State files would be stored remotely using secure backends with state locking.

CI/CD pipelines would validate Terraform code using fmt, validate, and plan before requiring approvals for production apply.

This approach ensures consistent infrastructure across AWS, Azure, and GCP while allowing environment-specific customization.

#7 "You are automating infrastructure provisioning for multiple cloud environments using Terraform. What approach would you recommend to manage environment-specific configurations while maintaining code reusability and consistency?"
