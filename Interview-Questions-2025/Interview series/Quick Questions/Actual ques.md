# DevOps Engineer – 2nd Round Interview

These 20 questions separate good DevOps engineers from great ones.

## 1. Your production Docker containers keep getting OOM-killed but the app "works fine" locally. How do you debug it?

I would first confirm the `OOMKilled` status using `kubectl describe pod` or container runtime logs and compare the container's memory usage in production with its configured memory limit. I would check metrics from Prometheus, Grafana, or CloudWatch to identify whether memory usage increases gradually, spikes during specific requests, or remains consistently close to the limit. I would also compare the production environment with local conditions because production may have higher traffic, different configuration, larger datasets, or different JVM/application settings. I would inspect application logs, heap usage where applicable, thread counts, and dependency behavior to identify a possible memory leak or inefficient workload. I would verify Kubernetes memory requests and limits and check whether the Pod is being throttled or evicted. I would not simply increase the memory limit without understanding the cause. If the application genuinely requires more memory, I would right-size the limit and scale replicas appropriately while continuing to investigate the underlying memory behavior.

---

## 2. Design a blue-green deployment strategy for a monolith that still has a stateful DB. What's your rollback plan?

For a monolith with a stateful database, I would create two application environments, Blue and Green, while keeping the database shared or carefully managed depending on the application's compatibility requirements. The critical point is database backward and forward compatibility because simply switching application versions does not roll back database schema changes safely. I would use an expand-and-contract migration strategy, where new database fields or structures are introduced without immediately removing the old ones. I would deploy and validate the Green application against the compatible schema, test it thoroughly, and then switch traffic using the load balancer. For rollback, I would immediately redirect traffic back to Blue if the application version has issues. Database rollback would only be performed if it is safe and necessary; otherwise, I would use backward-compatible schema changes and restore the application version while preserving the database state. This prevents an application rollback from accidentally causing database corruption or incompatibility.

---

## 3. Your Jenkins pipeline is green but the deployment silently failed on 2 out of 10 nodes. How do you catch this?

I would make deployment validation an explicit stage rather than considering the deployment successful simply because the deployment command returned exit code zero. After deployment, Jenkins should validate all ten target nodes and confirm that the expected application version is actually running on each one. I would use health checks, service status checks, version verification, API smoke tests, and appropriate deployment tooling to verify the result. If Kubernetes is involved, I would check rollout status, Pod readiness, ReplicaSet status, and events. For traditional servers, I would execute remote health checks and verify application processes and versions. The pipeline should fail if even one required node does not reach the expected state. I would also implement clear logging and notifications showing which nodes succeeded or failed, making partial deployments visible rather than allowing a false-green pipeline.

---

## 4. How would you design IAM roles for a team of 15 engineers with least-privilege access across dev/staging/prod?

I would avoid giving individual engineers broad IAM permissions directly. I would create role-based access according to responsibilities and environments. Developers could have broader access in development but read-only or restricted access in staging and production. Production write access would be limited to authorized DevOps or platform engineers and preferably protected through approval workflows and temporary access. I would use IAM roles, groups, permission policies, and AWS Organizations/SCPs where applicable to enforce account-level restrictions. Applications should use IAM roles rather than long-lived access keys. I would also enable CloudTrail and regularly review permissions using IAM Access Analyzer and access activity. The objective is that each engineer gets only the permissions required for their responsibilities and environment.

---

## 5. Your EKS pods are stuck in "Pending" state despite available node capacity. Walk me through your debugging steps.

I would first run `kubectl get pods -o wide` and `kubectl describe pod <pod-name>` because the Events section usually provides the scheduler's reason for the Pod remaining Pending. I would check whether the available capacity actually satisfies the Pod's CPU and memory requests rather than looking only at total node capacity. Then I would investigate taints and tolerations, node selectors, node affinity, topology constraints, Pod anti-affinity, and namespace resource quotas. I would also check whether the Pod requires a PersistentVolume that has not been successfully provisioned or attached. In EKS, I would verify node-group health, IAM permissions, networking, autoscaling behavior, and whether the required instance type or availability zone is available. Finally, I would inspect scheduler events and cluster conditions to determine the exact scheduling constraint and fix that constraint rather than simply adding more nodes.

---

## 6. Design a disaster recovery strategy for an application with a 15-minute RTO and 5-minute RPO.

A 15-minute RTO means the service should be restored within approximately 15 minutes, while a 5-minute RPO means we can tolerate losing at most about five minutes of data. I would first identify the critical components and dependencies and then design the DR architecture around those requirements. For databases, I would use continuous replication or frequent backups depending on the database technology and business requirements. Infrastructure would be defined using Terraform so that the DR environment can be recreated consistently. Application artifacts and container images would be stored in highly available or replicated repositories. Configuration and secrets would be replicated securely. Depending on the required recovery model, I could use a warm standby or pilot-light architecture in another Availability Zone or AWS Region. DNS or traffic management would be configured for controlled failover. I would regularly test restoration and measure the actual recovery time to ensure that the 15-minute RTO and 5-minute RPO are achievable rather than assuming the architecture will meet them.

---

## 7. Your Terraform apply is about to destroy and recreate an RDS instance in production. What went wrong, and how do you prevent it?

I would stop the apply immediately and inspect the `terraform plan` output to understand why Terraform believes the RDS resource must be replaced. Possible causes include changing an immutable attribute, modifying an identifier, changing configuration that requires replacement, importing the resource incorrectly, state drift, or using a different resource definition than what currently exists. I would compare the Terraform configuration, state, and actual AWS resource before making any changes. For a critical production database, I would use safeguards such as `lifecycle { prevent_destroy = true }` where appropriate, require plan review and approval, enable database backups and snapshots, and use separate production deployment controls. I would also investigate whether the state accurately represents the existing RDS resource. I would never approve a destructive production plan without understanding exactly why Terraform wants to replace the database and confirming that the replacement is intentional and safe.

---

## 8. How do you handle secrets rotation for a Kubernetes cluster without causing downtime?

I would use a centralized secret-management solution such as AWS Secrets Manager, HashiCorp Vault, or an External Secrets solution rather than manually updating secrets inside application manifests. The new secret should be created and validated before the old credential is revoked whenever the application supports overlapping credentials. I would update the Kubernetes workload so that new Pods receive the new secret and verify that the application can authenticate successfully. Depending on how the secret is consumed, I could perform a controlled rolling restart because environment variables are generally loaded when the container starts. I would ensure the Deployment has multiple replicas, appropriate readiness probes, and a rolling-update strategy so that existing healthy Pods continue serving traffic while new Pods start with the rotated credential. After successful validation, I would revoke the old credential and monitor the application for authentication failures.

---

## 9. Your monitoring shows CPU and memory are healthy, but users report slow response times. What's your next step?

I would not assume that healthy CPU and memory mean the application is healthy. I would investigate latency at the request and dependency level using application metrics, distributed tracing, logs, load-balancer metrics, and database monitoring. I would determine whether the latency affects all endpoints or only specific APIs and whether it started after a deployment or configuration change. I would check database query latency, connection pools, external APIs, DNS resolution, network latency, thread pools, locks, queue depth, and application-level saturation. Distributed tracing is especially useful because it can show where time is being spent across multiple microservices. I would correlate the latency spike with recent changes and user traffic patterns. The goal is to locate the actual bottleneck rather than simply increasing CPU or memory.

---

## 10. Design an autoscaling strategy for a workload with unpredictable traffic spikes every few weeks.

I would combine application-level and infrastructure-level scaling. At the Kubernetes level, I would use HPA based on CPU, memory, or preferably a business-relevant/custom metric such as requests per second or queue depth when appropriate. I would configure sensible minimum and maximum replicas and define appropriate resource requests so the scheduler can make correct decisions. At the infrastructure level, I would use Cluster Autoscaler or Karpenter to provide additional node capacity when Pods cannot be scheduled. For known events, scheduled or predictive scaling can be used to increase capacity before expected traffic arrives. I would also configure cooldown/stabilization behavior to avoid excessive scale-up and scale-down cycles. Monitoring, load testing, and capacity testing should be used to validate that scaling happens quickly enough for the expected traffic spikes.

---

## 11. How would you set up cost monitoring and alerting to catch a runaway cloud bill before it happens?

I would start by enabling AWS cost allocation tags and organizing resources by application, team, environment, and business unit. I would use AWS Cost Explorer and AWS Budgets to establish expected spending thresholds and configure alerts when actual or forecasted costs exceed those thresholds. I would also monitor major cost drivers such as EC2, EKS, NAT Gateways, S3, RDS, and data transfer. For deeper analysis, I would use AWS Cost and Usage Reports and appropriate dashboards. I would regularly identify unused resources, oversized instances, unattached volumes, excessive snapshots, idle load balancers, unnecessary NAT traffic, and inefficient Kubernetes workloads. For production environments, I would establish ownership for cost anomalies and create an automated response process where appropriate. The objective is to detect abnormal spending early rather than waiting for the monthly bill.

---

## 12. Your CI pipeline builds fine but the Docker image size has grown to 2GB. How do you reduce it?

I would first inspect the Docker image using tools such as `docker history` and image-analysis tools to identify which layers are consuming the most space. I would check whether build tools, caches, source files, package managers, logs, or unnecessary dependencies are being copied into the final image. I would use a smaller appropriate base image and a multi-stage Docker build so that compilers, Maven/Node build dependencies, and other temporary files remain in the builder stage and only the required runtime artifacts are copied into the final image. I would also optimize the Dockerfile layer structure, use `.dockerignore`, remove package caches, and avoid installing unnecessary packages. After optimization, I would scan the smaller image for vulnerabilities and verify that application functionality has not changed.

---

## 13. Design a networking architecture for microservices that need to talk across three separate VPCs.

I would first identify whether the VPCs belong to the same AWS Organization/account or different accounts and determine the required communication patterns. For a simple number of VPCs, VPC peering could work, but for scalable multi-VPC connectivity I would generally consider AWS Transit Gateway. Each VPC would have appropriate private subnets, route tables, security groups, and network ACLs. Routes would be configured so that only the required CIDR ranges can communicate. I would avoid exposing internal services directly to the public internet. For service-to-service communication, I would use private endpoints or internal load balancers where appropriate. DNS resolution across VPCs can be handled using Route 53 private hosted zones and Resolver configurations. Security groups would implement least-privilege access between services, and flow logs and monitoring would help troubleshoot connectivity.

---

## 14. A teammate accidentally pushed AWS credentials to a public GitHub repo. Walk me through your incident response.

I would treat the credentials as compromised immediately and not wait to see whether someone has used them. First, I would revoke or deactivate the exposed access keys and replace them with securely managed credentials if the application still requires access. I would identify the IAM principal and review CloudTrail logs for suspicious API activity during the period in which the credentials were exposed. I would assess the potential impact, including what resources the credentials could access. I would remove the secret from the repository history and ensure the repository is no longer exposing the credential, while understanding that removing it from Git history does not make an already exposed credential safe. I would rotate related credentials if necessary and verify that no other secrets were exposed. Finally, I would document the incident and implement preventive controls such as GitHub secret scanning/push protection, pre-commit scanning, least-privilege IAM, short-lived credentials, and developer awareness training.

---

## 15. How do you implement canary deployments for a service with strict SLA requirements?

I would deploy the new application version alongside the stable version and initially route only a small percentage of traffic to the new version. The canary percentage could start at something like 1–5% and gradually increase based on predefined success criteria. I would monitor error rate, latency, HTTP status codes, resource utilization, application health, and important business metrics. The canary should have readiness and health checks, and automated analysis should stop or roll back the rollout if predefined thresholds are exceeded. In Kubernetes, this can be implemented using tools such as Argo Rollouts with appropriate traffic-management integration. For a strict SLA, I would define the rollback criteria before deployment and ensure the stable version remains available throughout the rollout. This provides controlled exposure and reduces the blast radius of a faulty release.

---

## 16. Your Ansible playbook works on 8 servers but fails silently on 2. How do you make failures visible?

I would first make sure the playbook is not suppressing errors using options such as `ignore_errors`, and I would inspect the Ansible output for changed, failed, skipped, and unreachable hosts. I would use Ansible's `--check` mode where appropriate and run with increased verbosity such as `-vvv` when troubleshooting. I would add explicit validation tasks and use `failed_when` or `changed_when` conditions where the default Ansible behavior does not accurately represent success. I would also add post-deployment health checks to verify that the expected service is running and responding correctly. In CI/CD, the playbook should return a non-zero exit code if any required host fails so that Jenkins or another pipeline marks the deployment as failed. I would also produce clear host-level reporting so the two failed servers are immediately visible.

---

## 17. Design a backup and restore strategy for a self-managed PostgreSQL database running on EC2.

I would first define the business RPO and RTO because those determine the backup architecture. I would use regular full backups combined with PostgreSQL WAL archiving for point-in-time recovery when the required RPO is small. Backups should be stored outside the EC2 instance, such as in Amazon S3, rather than relying only on the local disk. I would encrypt backups, restrict access using IAM, enable versioning where appropriate, and maintain retention policies. I would also monitor backup success and failure and alert when backups are missing. For infrastructure recovery, I would use Terraform or another IaC approach to recreate the EC2 environment consistently. Most importantly, I would regularly perform restore tests because a backup is useful only if it can actually be restored successfully. I would document the recovery procedure and measure the actual restoration time against the required RTO.

---

## 18. How would you migrate 50+ microservices from a single AWS account to a multi-account setup with zero downtime?

I would first inventory the existing services, dependencies, IAM permissions, networking, databases, CI/CD pipelines, DNS, and external integrations. I would design the target AWS Organizations structure based on environments, teams, security requirements, or business domains. I would establish networking and account-level security controls before migrating workloads. For each microservice, I would create the target infrastructure using Terraform and ensure the deployment pipeline can deploy to the new account. I would run the new environment in parallel with the existing environment and validate functionality, performance, security, and observability. Traffic can then be shifted gradually using Route 53, load-balancer routing, or another suitable mechanism. For stateful services, I would use appropriate database replication or migration mechanisms and carefully plan data consistency. I would migrate services incrementally rather than moving all 50+ at once, allowing rollback to the original environment if a problem occurs.

---

## 19. Your load balancer health checks pass, but real users are hitting 502 errors. How do you investigate?

I would first identify where the 502 is being generated and inspect load-balancer access logs and metrics. A health check may only validate a simple endpoint such as `/health`, while actual user requests may exercise a different application path or dependency. I would check target response times, connection resets, listener rules, target ports, security groups, backend application logs, and load-balancer timeout settings. I would also test the application directly from inside the relevant network path using tools such as curl to determine whether the issue is between the load balancer and backend or inside the application itself. In Kubernetes, I would verify the Ingress Controller, Service endpoints, Pod readiness, and application logs. I would investigate recent releases, traffic patterns, connection limits, and backend resource saturation. Once the root cause is identified, I would apply the fix or roll back a faulty release and then monitor the error rate.

# 20. Design an observability stack from scratch for a startup with no existing monitoring.

I would design observability around the three primary signals: metrics, logs, and traces, while also including alerting and dashboards. For infrastructure and Kubernetes metrics, I could use Prometheus with Grafana for visualization and alerting. Application logs would be collected centrally using a logging pipeline such as Fluent Bit and stored in an appropriate log platform such as OpenSearch or another managed logging solution. For distributed tracing, I would use OpenTelemetry and a compatible backend to trace requests across microservices. I would define service-level indicators such as availability, request latency, error rate, throughput, saturation, and resource utilization. Alerts should focus on actionable symptoms and SLO impact rather than creating excessive noise. I would establish dashboards for infrastructure, Kubernetes, applications, databases, and business-critical services. Finally, I would test alerts, document incident-response procedures, define retention and cost controls, and continuously improve observability based on real incidents.


# DevOps Interview Questions & Answers

## 1. What happens when a Kubernetes pod keeps restarting?

When a Kubernetes Pod keeps restarting, Kubernetes continuously tries to maintain the desired state defined by the workload, such as a Deployment. If the container exits, the kubelet restarts it according to the container's restart policy, which commonly results in `CrashLoopBackOff` when the container repeatedly fails. I would first check the Pod status using `kubectl get pods`, then inspect the logs using `kubectl logs <pod-name> --previous` because the previous container instance may contain the actual error. I would also check `kubectl describe pod <pod-name>` for events, probe failures, OOMKilled status, configuration issues, image problems, and exit codes. I would verify ConfigMaps, Secrets, environment variables, resource limits, and volume mounts. Based on the root cause, I would fix the configuration or application issue and monitor the Pod to confirm that it becomes stable.

---

## 2. How would you troubleshoot a `CrashLoopBackOff`?

I would troubleshoot `CrashLoopBackOff` systematically rather than immediately changing the application. First, I would run `kubectl get pods` and `kubectl describe pod <pod-name>` to understand the current state and recent events. Then I would check both current and previous container logs using `kubectl logs <pod-name>` and `kubectl logs <pod-name> --previous`. I would check the container exit code and termination reason to identify whether it was an application failure, `OOMKilled`, failed probe, missing configuration, permission issue, or dependency failure. I would then validate ConfigMaps, Secrets, mounted volumes, image versions, environment variables, resource requests/limits, and liveness probes. If the container is being killed because of memory limits, I would analyze its memory usage before changing the limit. After applying the appropriate fix, I would monitor the rollout and verify that the application is healthy through readiness checks and application-level testing.

---

## 3. What actually happens during `terraform apply`?

When I run `terraform apply`, Terraform first evaluates the configuration, loads the current state, and compares the desired infrastructure defined in the `.tf` files with the existing infrastructure represented in the state. It creates an execution plan showing which resources need to be created, modified, or destroyed. After approval, Terraform builds a dependency graph and executes the required operations in the correct dependency order rather than simply following the order of resources in the files. Terraform communicates with the relevant provider, such as AWS, to make the actual infrastructure changes. After successful operations, Terraform updates the state file with the new resource IDs, attributes, and relationships so that future plans can accurately determine the difference between the desired and actual infrastructure. In a team environment, I also make sure remote state and state locking are configured before applying changes.

---

## 4. How does Terraform state locking work?

Terraform state locking prevents multiple engineers or CI/CD pipelines from modifying the same Terraform state simultaneously. When Terraform starts an operation such as `terraform apply`, it acquires a lock on the remote state before making changes. If another engineer tries to run an operation against the same state while it is locked, Terraform normally waits or fails instead of allowing concurrent state modifications. This prevents race conditions, conflicting infrastructure changes, and state corruption. In a production environment, I would use a remote backend with locking and make sure engineers run Terraform through a controlled CI/CD process. If a lock remains because of an interrupted operation, I would first verify that no Terraform process is actually running and only then remove the stale lock using the appropriate Terraform mechanism rather than forcefully deleting it.

---

## 5. What happens when a Linux server runs out of memory?

When a Linux server runs out of available memory, the system starts using swap if it is configured and available. If memory pressure continues and the system cannot satisfy memory allocations, the Linux Out-Of-Memory (OOM) killer may terminate one or more processes to recover memory. Applications may become slow, fail to allocate memory, or crash. I would troubleshoot this by checking commands such as `free -h`, `top`, `htop`, `vmstat`, and `ps` to identify memory consumption. I would also check system logs using `dmesg` or the appropriate system journal for OOM-killer messages. I would identify whether the problem is caused by a memory leak, an unexpectedly high workload, insufficient server capacity, or an incorrectly configured application. After identifying the root cause, I would optimize the application, adjust resource allocation, configure appropriate swap where suitable, or scale the infrastructure.

---

## 6. How would you troubleshoot high CPU usage?

I would first confirm whether the CPU increase is sustained or a short-term spike by checking monitoring data such as CloudWatch, Prometheus, or Grafana. On the server, I would use `top`, `htop`, `ps`, `uptime`, and `vmstat` to identify which processes or applications are consuming CPU. I would then correlate the CPU spike with application logs, traffic levels, deployments, scheduled jobs, database activity, and recent configuration changes. In Kubernetes, I would check Pod-level CPU usage using `kubectl top pods` and node-level usage using `kubectl top nodes`, and compare the usage against CPU requests and limits. If traffic has increased, I would consider Horizontal Pod Autoscaling or additional nodes through cluster autoscaling. If a particular application is responsible, I would investigate the application or query causing the CPU consumption. The goal is to identify the root cause rather than simply increasing CPU capacity.

---

## 7. How does DNS resolution work?

DNS resolution converts a domain name such as `example.com` into an IP address that a client can use to establish a network connection. When a client needs to resolve a domain, it first checks local sources such as its DNS cache and hosts file. If the answer is not available, the request is sent to a configured DNS resolver. The resolver may query authoritative DNS servers through the DNS hierarchy, including root, TLD, and authoritative name servers, and then return the result to the client. The response is cached according to its TTL. In AWS, Route 53 can provide public DNS resolution, while VPC DNS services handle DNS resolution inside AWS VPCs. In Kubernetes, CoreDNS normally provides cluster DNS, allowing Pods to resolve Kubernetes Services using service DNS names.

---

## 8. What happens when a Kubernetes node goes down?

When a Kubernetes worker node goes down, the control plane detects that the node is no longer responding through the kubelet/node health mechanism. The node eventually becomes `NotReady`, and Kubernetes identifies the Pods that were running on that node. For Pods managed by controllers such as Deployments or ReplicaSets, Kubernetes can create replacement Pods on healthy nodes, provided sufficient resources and scheduling requirements are available. If the workload uses persistent storage, Kubernetes also needs to handle volume attachment and reattachment according to the storage system and workload configuration. I would check `kubectl get nodes`, `kubectl describe node`, Pod status, cluster events, and workload controller status. In an EKS environment, I would additionally check the underlying EC2 instance, Auto Scaling Group, node group, networking, and cluster autoscaling behavior. For critical applications, I would design workloads across multiple nodes and Availability Zones to avoid a single-node failure causing an outage.

---

## 9. How would you debug a failed CI/CD pipeline?

I would start by identifying the exact stage and command where the pipeline failed rather than assuming the entire pipeline is broken. I would review the Jenkins or CI/CD console logs and compare the failure with the last successful build. I would check source-code changes, dependency changes, credentials, environment variables, agent availability, tool versions, Docker build issues, registry connectivity, and deployment permissions. If the failure is during testing, I would validate the test output and application dependencies. If it occurs during Docker image creation, I would reproduce the build outside the pipeline where possible. If it occurs during deployment, I would check Kubernetes events, manifests, image availability, credentials, and target-cluster connectivity. I would also verify whether the failure is environment-specific or branch-specific. After identifying the root cause, I would fix it, rerun the pipeline, and add appropriate validation or automation so that the same failure is less likely to happen again.

---

## 10. What’s the difference between blue-green and canary deployments?

Blue-green deployment maintains two environments: the current production environment, usually called Blue, and a new version, called Green. The new version is deployed and validated separately, and traffic is switched from Blue to Green when the release is considered ready. This provides a fast rollback because traffic can be switched back to the previous environment, although maintaining two environments can increase infrastructure cost. Canary deployment releases the new version to a small percentage of users or traffic first, while the majority continues using the stable version. Metrics such as error rate, latency, CPU usage, and business KPIs are monitored before gradually increasing traffic to the new version. I would generally prefer canary for large or high-risk applications where gradual exposure is important, while blue-green is useful when fast switching and rollback are more important.

---

## 11. How do you manage secrets in production?

I avoid storing production secrets directly in source code, Dockerfiles, Git repositories, or plain Kubernetes Deployment manifests. In AWS environments, I would use services such as AWS Secrets Manager or Systems Manager Parameter Store depending on the requirement, and in Kubernetes environments I can integrate external secret-management systems such as HashiCorp Vault or an External Secrets solution. Kubernetes Secrets can be used, but I would ensure appropriate RBAC and encryption at rest are configured because Base64 encoding by itself is not encryption. Access should follow the principle of least privilege, with applications receiving only the secrets they actually require. I would also implement secret rotation, auditing, access monitoring, and ensure that secrets are never printed in CI/CD logs. For Terraform, sensitive values should also be protected in the state backend because marking a variable as sensitive prevents display in output but does not automatically remove the value from state.

---

## 12. How would you design a highly available application?

I would remove single points of failure across the application stack. In AWS, I would typically deploy resources across multiple Availability Zones and use services such as an Application Load Balancer to distribute traffic across healthy application instances or Kubernetes Pods. For Kubernetes workloads, I would use multiple replicas, readiness and liveness probes, appropriate Pod disruption budgets, and topology spread or anti-affinity rules to distribute Pods across nodes and Availability Zones. The database layer should also provide high availability, such as Multi-AZ deployment where appropriate, with suitable backup and recovery mechanisms. I would use Route 53 for DNS and health-based routing when required, CloudWatch/Prometheus/Grafana for monitoring, and automated scaling for changing workloads. I would also define disaster-recovery procedures, RTO/RPO requirements, regular backups, and recovery testing because high availability and disaster recovery address different failure scenarios.

---

## 13. What happens when a load balancer starts returning 5xx errors?

I would first determine which 5xx response is being generated and whether it is coming from the load balancer, reverse proxy, ingress controller, or backend application. I would check ALB/NLB metrics and logs, target health, listener rules, security groups, target ports, and backend connectivity. For an ALB, I would review metrics such as `HTTPCode_ELB_5XX_Count` and `HTTPCode_Target_5XX_Count` to distinguish load-balancer-side errors from backend-generated errors. In Kubernetes, I would check the Ingress Controller, Service endpoints, Pod readiness, application logs, and recent deployments. I would also verify whether Pods are overloaded, restarting, or failing health checks. If the issue started immediately after a release, I would compare the new version with the previous version and consider rollback if that is the safest way to restore service. Once service is restored, I would perform RCA and implement preventive measures.

---

## 14. How do you investigate a sudden production latency spike?

I would first determine the scope and timing of the latency increase: whether all users, specific APIs, regions, or services are affected. I would compare application latency with infrastructure metrics such as CPU, memory, network, disk I/O, load-balancer metrics, database connections, and query latency. I would check distributed traces and application logs to identify where time is being spent across the request path. I would also investigate recent deployments, configuration changes, infrastructure changes, traffic increases, DNS changes, external dependencies, and database performance. In a microservices environment, I would trace the request from the load balancer through the ingress layer and services to the database or external dependency. If the latency is release-related, I would consider rollback while continuing investigation. After identifying the bottleneck, I would apply the appropriate fix and establish monitoring or alerts to detect the condition earlier.

---

## 15. Production is down at 2 AM. What’s your first move?

My first move is to acknowledge the incident and quickly assess the business impact rather than immediately making random infrastructure changes. I would confirm whether the outage is genuine, identify which services and users are affected, and check monitoring dashboards, alerts, application logs, load-balancer health, Kubernetes/EKS health, database status, and recent changes. I would communicate through the established incident-management process and involve the appropriate application, database, network, security, or cloud teams if required. If there is a known recent deployment or configuration change that clearly caused the outage, I would prioritize a safe rollback to restore service. During a P1 incident, my priority is **restore service → minimize business impact → verify stability → perform RCA**. Once the application is stable, I would document the timeline, root cause, corrective actions, and preventive improvements.


# DevOps Interview Questions & Answers (4 Years Experience)

## 🔧 TERRAFORM

### 1. You lost your Terraform state file and deleted the entire code repository. No backups. What do you do immediately? You log into AWS Console and see 50+ resources running. How do you identify which are Prod resources and which are Dev resources?

The first priority is to stop anyone from making infrastructure changes to prevent accidental deletion or drift. I would identify all running resources using AWS Resource Groups Tag Editor, AWS Config, AWS CLI, and Cost Explorer. If proper tagging exists (Environment=Prod, Dev, Owner, Project), separating production and development resources becomes straightforward. If tags are missing, I would inspect VPCs, subnets, security groups, IAM roles, naming conventions, CloudWatch alarms, load balancers, Route53 records, and attached volumes to determine resource ownership. I would coordinate with application teams to verify critical production workloads. Once resources are identified, I would recreate the Terraform code based on the existing infrastructure, import resources using `terraform import`, rebuild the state file, validate with `terraform plan`, and finally move the recovered state to a remote backend such as S3 with DynamoDB locking. After recovery, I would implement Git repositories, automated backups, version control, remote state management, and tagging policies to avoid similar incidents.

---

### 2. Creating EC2, but S3 bucket gets deleted in Terraform plan. How do you debug this?

I never execute `terraform apply` until I understand why Terraform wants to delete the S3 bucket. First, I inspect the execution plan carefully to identify which resource is marked for deletion. I verify whether the bucket was removed from the Terraform configuration, renamed, moved into another module, or affected by changes in variables. Next, I compare the Terraform state with the actual AWS infrastructure using `terraform state list`, `terraform state show`, and AWS Console. I also check whether someone manually modified the infrastructure, causing state drift. If modules or providers were upgraded recently, I verify compatibility changes. I review the Git history to identify recent code modifications and run `terraform refresh` or `terraform plan` after synchronizing the state. If the bucket must never be deleted, I protect it using `lifecycle { prevent_destroy = true }`. Only after identifying the root cause and confirming that the deletion is unintended would I proceed with corrective actions.

---

### 3. Describe a Terraform module you created. How do you maintain it?

I created reusable Terraform modules for provisioning AWS infrastructure, including VPCs, EC2 instances, Security Groups, IAM Roles, Application Load Balancers, and Amazon EKS clusters. The objective was to eliminate code duplication and maintain standardized infrastructure across development, testing, and production environments. Each module accepts configurable variables while exposing outputs for downstream modules. I maintain modules using semantic versioning, maintain backward compatibility whenever possible, document all inputs and outputs, and store modules in a centralized Git repository. Every change undergoes code review, automated validation using `terraform fmt`, `terraform validate`, and CI/CD pipelines before release. Consumers reference specific module versions instead of the latest release to avoid unexpected production changes.

---

### 4. 2:00 PM: You start `terraform apply` (Security Group rules). 2:01 PM: Team member starts `terraform apply` (EC2 instance). 2:02 PM: You get lock error. 2:35 PM: Still locked. Team member went on leave. Network issue kept lock active. You can't force-unlock. Production is BLOCKED. Manager asking: When will this be fixed?

The lock indicates another Terraform operation is still holding the remote state. My first step is to verify whether an active Terraform process is genuinely running or whether the lock is stale due to a failed session. If the backend uses S3 and DynamoDB, I inspect the lock record in DynamoDB and confirm with the team whether any apply operation is still active. Since production is blocked, I immediately communicate the situation to the manager, explaining that the deployment is blocked due to state locking and providing regular progress updates. If force unlock is restricted, I escalate to the administrator responsible for the backend so they can safely remove the stale lock after verifying that no Terraform process is running. Once the lock is released, I rerun `terraform plan`, validate there are no partial changes, and execute the deployment. To prevent similar incidents, I recommend implementing deployment pipelines, restricting concurrent applies, using remote state locking, and establishing operational procedures for stale lock recovery.

---

### 5. Maintain Terraform version consistency across 15 projects and 4 developers. How?

I standardize the Terraform version by specifying `required_version` inside every project and locking provider versions using the `required_providers` block. Every developer installs Terraform using a version manager such as **tfenv**, ensuring everyone uses the same version. CI/CD pipelines validate the Terraform version before execution and fail immediately if an unsupported version is detected. Provider lock files (`terraform.lock.hcl`) are committed to Git to guarantee consistent provider versions across environments. All projects use shared CI templates and documented upgrade procedures so version upgrades occur in a controlled manner after testing in lower environments before production rollout.

---

# 🐳 DOCKER

### 6. Create Docker image supporting ARM + x86_64. How would you do this?

To build images supporting both ARM and x86_64 architectures, I use Docker Buildx. I first create a Buildx builder, enable QEMU emulation if necessary, and then build a multi-platform image using platforms such as `linux/amd64` and `linux/arm64`. The resulting manifest contains both architectures, allowing Docker to automatically pull the correct image depending on the host platform. This approach enables the same image tag to run seamlessly on Apple Silicon laptops, AWS Graviton instances, Raspberry Pi devices, and traditional Intel servers.

---

### 7. Docker image is 2.1 GB resulting in 5–10 minute deployments. How do you reduce the size?

I first analyze the image layers using Docker history to identify large components. I switch to lightweight base images such as Alpine or Distroless whenever compatible. I implement multi-stage builds so build tools and temporary dependencies are excluded from the final runtime image. I remove package caches, temporary files, documentation, and unnecessary utilities after installation. Dependencies are installed using `--no-cache` options where available, and `.dockerignore` is configured to exclude logs, Git metadata, local files, and test artifacts from the build context. I also optimize application dependencies, compress static assets, and reuse Docker layers efficiently. These optimizations typically reduce image size significantly while also improving deployment speed.

---

### 8. How do you parameterize the Python version instead of hardcoding `FROM python:3.12`?

Instead of hardcoding the version, I define a build argument using `ARG PYTHON_VERSION=3.12` before the `FROM` statement and reference it inside the image declaration. During the build process, the required version can be supplied dynamically using the build command, allowing the same Dockerfile to build different Python versions without modification. This improves flexibility, simplifies upgrades, and allows CI/CD pipelines to test multiple runtime versions.

---

# ☸️ KUBERNETES

### 9. Pod crashed and got deleted. How do you find its logs now?

If the pod restarted, I use `kubectl logs --previous` to retrieve logs from the previous container instance. If the pod was completely deleted, Kubernetes no longer stores its logs locally. In production, I rely on centralized logging solutions such as ELK Stack, Loki, Splunk, or CloudWatch where logs are collected continuously from containers before deletion. I also inspect Deployment events, ReplicaSets, node logs, and monitoring dashboards to understand why the pod terminated.

---

### 10. What replaces kube-proxy in modern Kubernetes? Why would you replace it?

A common replacement for kube-proxy is **Cilium**, which uses eBPF instead of iptables or IPVS for networking. eBPF provides faster packet processing, lower latency, improved scalability, enhanced observability, advanced network policies, and better performance in large Kubernetes clusters. Organizations often replace kube-proxy when they require high-performance networking, improved security, and deep network visibility.

---

### 11. Pod stuck in Pending for 30 minutes. Debug steps? Possible causes?

I begin by running `kubectl describe pod` to inspect scheduling events. I verify whether worker nodes are Ready and have sufficient CPU, memory, and storage. I check node selectors, taints, tolerations, affinity rules, Persistent Volume Claims, storage classes, image pull secrets, quotas, and namespace resource limits. I also verify whether the cluster autoscaler is functioning correctly if no nodes are available. Common causes include insufficient cluster resources, unsatisfied scheduling constraints, missing Persistent Volumes, incorrect node selectors, taints without matching tolerations, image pull authentication failures, or exhausted Pod CIDR capacity.

---

### 12. Pod in CrashLoopBackOff. How do you debug?

I first inspect pod events using `kubectl describe pod` and review application logs using `kubectl logs` or `kubectl logs --previous` if the container restarted. I verify the container's startup command, environment variables, ConfigMaps, Secrets, mounted volumes, and application configuration. I also check readiness and liveness probes, resource limits, image versions, dependency connectivity, and external services such as databases. If required, I reproduce the issue locally using Docker or launch a debug container into the cluster to investigate the runtime environment.

---

### 13. Sidecar vs DaemonSet — when do you use which one?

A Sidecar container runs alongside a specific application pod and shares its lifecycle. It is typically used for log forwarding, service mesh proxies, monitoring agents, or configuration synchronization. A DaemonSet, on the other hand, ensures one pod runs on every node in the cluster and is used for node-level services such as log collectors, monitoring agents, security scanners, and networking components. In summary, Sidecars provide functionality for individual applications, while DaemonSets provide services for every node in the cluster.

---

### 14. How do container-level metrics get sent to Prometheus (or another monitoring tool) in Kubernetes?

Container metrics are exposed by kubelet, cAdvisor, kube-state-metrics, or the application itself using Prometheus-compatible endpoints. Prometheus periodically scrapes these metrics based on configured scrape targets or ServiceMonitors when using the Prometheus Operator. Exporters such as Node Exporter, Blackbox Exporter, or custom application exporters expose additional metrics. Prometheus stores the collected metrics, Grafana visualizes them through dashboards, and Alertmanager sends alerts whenever predefined thresholds are exceeded.

---

# 🐍 PYTHON & CODING

### 15. Write code: Check if a string is a palindrome (handle spaces, punctuation, and case).

```python
import re

def is_palindrome(text):
    cleaned = re.sub(r'[^a-zA-Z0-9]', '', text).lower()
    return cleaned == cleaned[::-1]

print(is_palindrome("A man, a plan, a canal: Panama"))
```

---

### 16. Write code: Find all 500 errors in a 100GB log file.

```python
def find_500_errors(log_file):
    with open(log_file, "r", encoding="utf-8", errors="ignore") as file:
        for line in file:
            if " 500 " in line or "HTTP/1.1\" 500" in line:
                print(line.strip())

find_500_errors("access.log")
```

This solution reads the log file line by line instead of loading the entire 100GB file into memory, making it memory-efficient and suitable for production-scale log analysis.

### 16. Find all 500 errors in a 100GB log file (Using Bash)

Since the log file is very large (100GB), I would avoid opening it in an editor or loading it into memory. Instead, I would use Linux text-processing commands that stream the file line by line, making the solution fast and memory-efficient.

#### Using `grep` (Recommended)

```bash
grep ' 500 ' access.log
```

#### Save the output to another file

```bash
grep ' 500 ' access.log > 500_errors.log
```

#### Count the number of 500 errors

```bash
grep -c ' 500 ' access.log
```

#### Monitor new 500 errors in real time

```bash
tail -f access.log | grep ' 500 '
```

#### If the log is compressed (.gz)

```bash
zgrep ' 500 ' access.log.gz
```

**Interview Explanation:**

For a 100GB log file, I would use `grep` because it processes the file as a stream instead of loading the entire file into memory. This makes it highly memory-efficient and fast. If I only need the total number of HTTP 500 errors, I use `grep -c`. If I need to monitor production logs continuously, I combine `tail -f` with `grep`. For compressed log files, I use `zgrep`, which searches directly inside `.gz` files without extracting them first.


# AWS DevOps Interview Series

---

# 1. What is Git Rebase and how does it differ from Git Merge?

## Answer

Both **Git Merge** and **Git Rebase** are used to integrate changes from one branch into another, but they work differently.

### Git Merge

Merge combines two branches by creating a **new merge commit**.

Example:

```
main

A---B---C

         \
feature   D---E

After Merge

A---B---C---------M
         \       /
          D-----E
```

Advantages:

- Preserves complete history.
- Safe for shared branches.
- Easy to understand.

Disadvantages:

- Creates extra merge commits.
- Commit history becomes complex.

---

### Git Rebase

Rebase moves feature branch commits on top of the latest branch.

```
Before

A---B---C

     \
      D---E

After Rebase

A---B---C---D'---E'
```

Advantages:

- Linear history.
- Cleaner Git log.
- Easier debugging.

Disadvantages:

- Rewrites commit history.
- Dangerous on shared branches.

---

### Production Best Practice

Use:

- **Merge** for shared branches.
- **Rebase** before creating Pull Requests to keep history clean.

Never rebase commits already pushed to shared branches.

---

### Interview Answer

> Merge creates a new merge commit while preserving history. Rebase rewrites commit history by placing commits on top of another branch, resulting in a cleaner linear history. I use rebase locally and merge for shared branches.

---

# 2. What are Git Hooks and how are they used?

## Answer

Git Hooks are scripts that automatically execute before or after specific Git events.

Examples:

- pre-commit
- commit-msg
- pre-push
- post-merge

Example:

```
Developer

↓

git commit

↓

Pre-Commit Hook

↓

Run Linter

↓

Run Unit Tests

↓

Commit Allowed
```

Real-time uses:

- Code formatting
- Secret scanning
- SonarLint
- Unit testing
- Commit message validation

Example:

Prevent AWS Secrets from being committed.

```
git commit

↓

Hook detects AWS Secret

↓

Commit blocked
```

---

### Interview Answer

> Git Hooks automate tasks during Git operations such as validating commit messages, running unit tests, formatting code, and preventing secrets from being committed.

---

# 3. How do you set up a CI/CD pipeline for an AWS-hosted application?

## Answer

Typical production pipeline:

```
Developer

↓

GitHub Push

↓

Webhook

↓

Jenkins Pipeline

↓

Checkout Code

↓

Compile (Maven)

↓

Unit Testing

↓

SonarQube Scan

↓

Trivy Scan

↓

Docker Build

↓

Docker Push (Amazon ECR)

↓

Terraform (Infrastructure)

↓

Helm Deployment

↓

Amazon EKS

↓

Smoke Test

↓

Slack Notification
```

Tools Used

- GitHub
- Jenkins
- Maven
- SonarQube
- Trivy
- Docker
- Amazon ECR
- Terraform
- Helm
- Amazon EKS
- Prometheus
- Grafana

---

### Interview Answer

> I configure a webhook between GitHub and Jenkins. Every code push automatically triggers the pipeline, which performs build, testing, code quality analysis, security scanning, Docker image creation, pushes the image to Amazon ECR, provisions infrastructure using Terraform if required, deploys to Amazon EKS using Helm, runs smoke tests, and finally sends deployment notifications.

---

# 4. How do you handle rollbacks in CI/CD?

## Answer

Rollback depends on the deployment strategy.

### Kubernetes

```
kubectl rollout undo deployment nginx
```

---

### Helm

```
helm history myapp

helm rollback myapp 3
```

---

### ArgoCD

Rollback to previous Git commit.

---

### Blue-Green

Switch traffic back to Blue.

---

### Canary

Reduce traffic:

```
50%

↓

20%

↓

5%

↓

0%
```

---

### Jenkins

Redeploy last successful artifact.

---

### Production Process

1. Stop rollout.
2. Identify failure.
3. Roll back immediately.
4. Validate application.
5. Notify stakeholders.
6. Perform RCA.

---

### Interview Answer

> My rollback strategy depends on the deployment method. For Kubernetes, I use `kubectl rollout undo`; for Helm, `helm rollback`; for Blue-Green deployments, I switch traffic back to the stable environment; and for Canary deployments, I gradually reduce traffic to the faulty version. After rollback, I validate the application and conduct a root cause analysis.

---

# 5. What is the difference between Terraform and AWS CloudFormation?

## Answer

| Feature | Terraform | CloudFormation |
|----------|-----------|----------------|
| Vendor | HashiCorp | AWS |
| Multi-Cloud | ✅ Yes | ❌ AWS Only |
| Language | HCL | JSON/YAML |
| State File | Yes | No local state |
| Modules | Excellent | Nested Stacks |
| Providers | 3000+ | AWS Only |
| Community | Huge | AWS |

---

### When do I choose Terraform?

- AWS
- Azure
- GCP
- Kubernetes
- GitHub
- Cloudflare

One tool manages everything.

CloudFormation only manages AWS resources.

---

### Interview Answer

> Terraform is a cloud-agnostic Infrastructure as Code tool that supports multiple providers using HCL, while CloudFormation is AWS-specific and uses JSON or YAML. I prefer Terraform because it allows me to manage AWS, Kubernetes, GitHub, and other platforms using a single tool with reusable modules.

---

# 6. How do you manage secrets in Terraform?

## Answer

Never store secrets in:

```
terraform.tfvars
```

or

```
variables.tf
```

Production approach:

```
Terraform

↓

AWS Secrets Manager

↓

IAM Role

↓

Terraform Data Source

↓

Infrastructure
```

Options:

- AWS Secrets Manager
- AWS Parameter Store
- HashiCorp Vault
- Azure Key Vault

Mark variables:

```hcl
variable "db_password" {
  sensitive = true
}
```

Enable:

- S3 encryption
- DynamoDB locking
- IAM least privilege

---

### Interview Answer

> I never hardcode secrets in Terraform files. Instead, I store them in AWS Secrets Manager or HashiCorp Vault and retrieve them securely using Terraform data sources. I also mark sensitive variables, encrypt the Terraform state, and restrict access using IAM.


# AWS DevOps Interview Series – Set 16 (Part 2)

---

# 7. How do you debug a Kubernetes Pod in CrashLoopBackOff?

## Answer

CrashLoopBackOff means the container starts, crashes, Kubernetes restarts it, and after repeated failures, Kubernetes increases the restart delay exponentially.

### Troubleshooting Steps

### Step 1: Check Pod Status

```bash
kubectl get pods
```

---

### Step 2: Describe the Pod

```bash
kubectl describe pod <pod-name>
```

Look for:

- Events
- Exit Code
- OOMKilled
- Probe failures
- Mount errors

---

### Step 3: Check Logs

```bash
kubectl logs <pod-name>

kubectl logs <pod-name> --previous
```

---

### Step 4: Verify Image

```bash
kubectl describe pod
```

Check:

- Wrong image
- Wrong image tag
- ImagePullBackOff

---

### Step 5: Verify ConfigMaps & Secrets

```bash
kubectl get configmap

kubectl get secret
```

---

### Step 6: Check Resource Limits

```bash
kubectl top pod
```

If memory exceeds limits:

```
OOMKilled
```

---

### Step 7: Verify Liveness & Readiness Probes

Incorrect probe configuration frequently causes CrashLoopBackOff.

---

### Step 8: Verify Dependencies

Check:

- Database
- Redis
- Kafka
- External APIs

---

### Production Interview Answer

> I first inspect the Pod using `kubectl describe`, review logs with `kubectl logs` and `--previous`, verify events, ConfigMaps, Secrets, image versions, resource limits, probes, and application dependencies. If the issue started after a deployment, I compare with the previous working version and roll back if necessary.

---

# 8. How would you dynamically scale a Kubernetes Deployment?

## Answer

Dynamic scaling is implemented using the **Horizontal Pod Autoscaler (HPA)**.

HPA automatically adjusts the number of Pods based on metrics such as:

- CPU utilization
- Memory utilization
- Custom Metrics
- External Metrics

Example:

```bash
kubectl autoscale deployment nginx \
--cpu-percent=70 \
--min=2 \
--max=10
```

Example behavior:

```
CPU 20%

↓

2 Pods

CPU 75%

↓

4 Pods

CPU 85%

↓

6 Pods

CPU 95%

↓

10 Pods
```

### Components Required

- Metrics Server
- HPA
- Resource Requests
- Resource Limits

### Production Best Practice

Use HPA together with **Cluster Autoscaler**.

- HPA scales Pods.
- Cluster Autoscaler adds worker nodes when required.

---

### Interview Answer

> I use Horizontal Pod Autoscaler based on CPU, memory, or custom metrics. HPA scales Pods automatically, while Cluster Autoscaler provisions additional worker nodes if existing nodes don't have enough capacity.

---

# 9. What is the difference between an ALB and an NLB in AWS?

## Answer

| Feature | ALB | NLB |
|----------|-----|-----|
| Layer | Layer 7 | Layer 4 |
| Protocol | HTTP/HTTPS | TCP/UDP |
| Path Routing | Yes | No |
| Host Routing | Yes | No |
| WAF Support | Yes | No |
| Static IP | No | Yes |
| Performance | High | Very High |

### Use ALB

- Microservices
- APIs
- Web Applications
- Kubernetes Ingress

### Use NLB

- Gaming
- VoIP
- Kafka
- MQTT
- Financial Trading

---

### Interview Answer

> ALB operates at Layer 7 and supports intelligent routing, SSL termination, and WAF integration, making it ideal for web applications. NLB operates at Layer 4, offering ultra-low latency and support for TCP/UDP workloads such as Kafka or gaming platforms.

---

# 10. How do you secure an S3 bucket?

## Answer

Production checklist:

- Enable Block Public Access
- IAM Least Privilege
- Bucket Policies
- Versioning
- Server-Side Encryption (SSE-KMS)
- Access Logging
- CloudTrail
- MFA Delete (if required)
- Lifecycle Policies
- Pre-Signed URLs for temporary access

Never:

- Make buckets public.
- Store secrets.
- Share object URLs directly.

---

### Interview Answer

> I secure S3 buckets by enabling Block Public Access, applying least-privilege IAM policies, encrypting data with SSE-KMS, enabling versioning and access logging, monitoring with CloudTrail, and providing temporary access through pre-signed URLs instead of making objects public.

---

# 11. If your deployment fails, what steps do you take?

## Answer

### Step 1

Identify failure stage:

- Build
- Test
- Docker
- Push
- Deploy

---

### Step 2

Review logs.

---

### Step 3

Check recent code changes.

---

### Step 4

Rollback.

Examples:

```
helm rollback

kubectl rollout undo

ArgoCD rollback
```

---

### Step 5

Verify:

- Pods
- Services
- Ingress
- Database
- Application Health

---

### Step 6

Perform RCA.

---

### Interview Answer

> I identify the failed pipeline stage, analyze logs, verify infrastructure and application health, perform an immediate rollback if production is affected, validate the recovery, and conduct a root cause analysis to prevent recurrence.

---

# 12. Why do people prefer Ingress over LoadBalancers?

## Answer

Without Ingress:

```
Service A

↓

LoadBalancer

Service B

↓

LoadBalancer

Service C

↓

LoadBalancer
```

Three Services require three cloud load balancers.

Higher AWS cost.

---

With Ingress:

```
Internet

↓

One ALB

↓

Ingress Controller

↓

API

↓

Orders

↓

Payments
```

Benefits:

- Single Entry Point
- SSL Termination
- Path Routing
- Host Routing
- Lower Cost
- Easier Management

---

### Interview Answer

> Ingress provides a single entry point for multiple services, reducing cloud load balancer costs while supporting path-based routing, host-based routing, SSL termination, and centralized traffic management.

---

# 13. How do you know Kubernetes nodes are Ready?

## Answer

Run:

```bash
kubectl get nodes
```

Example:

```
NAME

STATUS

worker-1

Ready

worker-2

Ready

worker-3

NotReady
```

Detailed information:

```bash
kubectl describe node worker-1
```

Check:

- Conditions
- Memory Pressure
- Disk Pressure
- PID Pressure
- Network

---

### Interview Answer

> I use `kubectl get nodes` to verify node status and `kubectl describe node` to inspect conditions such as memory pressure, disk pressure, network availability, and kubelet health.

---

# 14. How do you handle high CPU usage in your cluster?

## Answer

Step 1

Identify affected Pods.

```bash
kubectl top pod
```

---

Step 2

Identify affected Nodes.

```bash
kubectl top node
```

---

Step 3

Review Prometheus/Grafana dashboards.

---

Step 4

Check HPA.

---

Step 5

Scale Deployment.

```bash
kubectl scale deployment nginx \
--replicas=10
```

---

Step 6

Review application code.

---

Step 7

Optimize resource requests and limits.

---

### Interview Answer

> I identify high CPU consumers using `kubectl top`, review monitoring dashboards, verify HPA behavior, scale the deployment if necessary, analyze application performance, and optimize resource requests and limits based on production metrics.

---

# 15. How will you plan a Disaster Recovery (DR)?

## Answer

A production DR strategy includes:

- Multi-AZ deployment
- Cross-Region backups
- Automated database snapshots
- Infrastructure as Code (Terraform)
- CI/CD automation
- Amazon Route 53 failover routing
- Regular DR drills
- Documented RTO and RPO

Example architecture:

```
Primary Region

↓

Replication

↓

Secondary Region

↓

Route53 Failover

↓

Users
```

### Interview Answer

> I design disaster recovery using Multi-AZ architecture, cross-region replication, encrypted backups, Terraform for infrastructure recreation, Route 53 failover, and automated CI/CD pipelines. I also define clear RTO/RPO targets and regularly test the recovery process.


# AWS DevOps Interview Series – Set 16 (Part 3)

---

# 16. How will you design a Microservices Architecture application in Kubernetes?

## Answer

In production, I would design the architecture to be **highly available, scalable, secure, and loosely coupled**.

### Architecture

```
                 Users
                   │
         Route53 / DNS
                   │
          AWS Application Load Balancer
                   │
          AWS Load Balancer Controller
                   │
               Ingress
                   │
        ┌──────────┼──────────┐
        │          │          │
    User API   Order API   Payment API
        │          │          │
     ClusterIP  ClusterIP  ClusterIP
        │          │          │
     PostgreSQL   Redis     Kafka
```

### Components

- EKS Cluster
- ALB Ingress Controller
- Ingress
- ClusterIP Services
- Deployments
- ConfigMaps
- Secrets
- Persistent Volumes
- Horizontal Pod Autoscaler
- Cluster Autoscaler
- Prometheus
- Grafana
- Fluent Bit
- ELK/OpenSearch

### Best Practices

- Each microservice has its own Deployment.
- Each service has its own database where applicable.
- Use ConfigMaps for configuration.
- Use Secrets for credentials.
- Enable HPA and Cluster Autoscaler.
- Use Network Policies to isolate traffic.
- Implement RBAC.
- Deploy across multiple Availability Zones.

---

### Interview Answer

> I design Kubernetes microservices using Deployments, ClusterIP Services, Ingress, ConfigMaps, Secrets, Persistent Volumes, Horizontal Pod Autoscaler, and monitoring tools like Prometheus and Grafana. I deploy workloads across multiple Availability Zones and secure communication using RBAC and Network Policies.

---

# 17. What is Liveness Probe and Readiness Probe in Kubernetes?

## Answer

### Liveness Probe

Liveness Probe checks whether the application is **still running properly**.

If it fails repeatedly:

- Kubernetes kills the container.
- The container is restarted automatically.

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
```

---

### Readiness Probe

Readiness Probe determines whether the application is **ready to receive traffic**.

If it fails:

- Pod is NOT restarted.
- Pod is removed from the Service endpoints.
- No traffic is sent until it becomes healthy again.

Example:

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
```

---

### Difference

| Liveness | Readiness |
|-----------|-----------|
| Checks if application is alive | Checks if application is ready |
| Failed → Restart container | Failed → Stop sending traffic |
| Detects deadlocks | Prevents failed requests |

---

### Interview Answer

> Liveness Probe checks whether the application is alive and restarts the container if it fails. Readiness Probe determines whether the application is ready to serve traffic. If the readiness probe fails, Kubernetes stops routing traffic to the Pod without restarting it.

---

# 18. What is Node Selector and Node Affinity?

## Answer

Both are used to control where Pods are scheduled.

### Node Selector

Simplest scheduling mechanism.

Example

```yaml
nodeSelector:
  disktype: ssd
```

Pod will only be scheduled on nodes with:

```
disktype=ssd
```

---

### Node Affinity

Provides advanced scheduling rules.

Supports:

- Required rules
- Preferred rules
- Multiple expressions

Example

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
```

---

### Difference

| Node Selector | Node Affinity |
|--------------|---------------|
| Simple | Advanced |
| Exact Match | Expressions |
| No Preference | Supports Preferred Rules |

---

### Production Example

GPU Nodes

```
gpu=true
```

AI workloads

↓

Node Affinity

↓

GPU Nodes

---

### Interview Answer

> Node Selector is the simplest scheduling mechanism that matches node labels exactly. Node Affinity provides more flexible scheduling with required and preferred rules, making it the preferred choice for production workloads.

---

# 19. Explain ELK Cluster Flow. How did you set it up from scratch?

## Answer

ELK stands for:

- Elasticsearch
- Logstash
- Kibana

Modern Kubernetes deployments often replace Logstash with Fluent Bit or Fluentd.

### Architecture

```
Pods

↓

Container Logs

↓

Fluent Bit

↓

Elasticsearch

↓

Kibana

↓

Dashboard
```

### Setup Steps

1. Create Elasticsearch StatefulSet.
2. Create Persistent Volumes.
3. Deploy Fluent Bit as a DaemonSet.
4. Configure Fluent Bit to read container logs.
5. Send logs to Elasticsearch.
6. Deploy Kibana.
7. Configure Elasticsearch index patterns.
8. Build dashboards.

---

### Production Features

- Log Retention
- TLS Encryption
- Authentication
- Index Lifecycle Management
- Alerting

---

### Interview Answer

> I deploy Elasticsearch as a StatefulSet with persistent storage, Fluent Bit as a DaemonSet to collect logs from every node, and Kibana for visualization. Fluent Bit forwards container logs to Elasticsearch, where they are indexed and displayed through Kibana dashboards.

---

# 20. Scenario: Pods are restarting multiple times and applications are going down. How do you troubleshoot?

## Answer

### Step 1

Check Pod status

```bash
kubectl get pods
```

---

### Step 2

Describe Pod

```bash
kubectl describe pod
```

---

### Step 3

View Logs

```bash
kubectl logs

kubectl logs --previous
```

---

### Step 4

Check Events

```
OOMKilled

CrashLoopBackOff

FailedMount

ImagePullBackOff
```

---

### Step 5

Verify

- Secrets
- ConfigMaps
- Database connectivity
- Resource limits
- Health probes

---

### Step 6

Check Node

```bash
kubectl describe node
```

---

### Step 7

Rollback if deployment introduced the issue.

---

### Interview Answer

> I inspect the Pod status, review events and logs, verify resource limits, ConfigMaps, Secrets, probes, and external dependencies. I also check node health and roll back the deployment if the issue was introduced by a recent release.

---

# 21. How would you troubleshoot an Out of Memory (OOM) issue in a Kubernetes cluster?

## Answer

### Symptoms

```
OOMKilled

Exit Code 137
```

### Troubleshooting

Check Pod events:

```bash
kubectl describe pod
```

Check resource usage:

```bash
kubectl top pod
```

Check limits:

```yaml
resources:
```

Review Prometheus metrics.

Identify memory leaks.

Increase limits only after understanding the application's memory requirements.

Scale horizontally if necessary.

---

### Interview Answer

> I confirm the OOMKilled event, inspect memory usage with `kubectl top`, review resource requests and limits, analyze application logs for memory leaks, and optimize the application before increasing memory limits or scaling the deployment.

---

# 22. Scenario: Deployment is stuck in Pending state. How will you troubleshoot?

## Answer

Check Pod:

```bash
kubectl describe pod
```

Common reasons:

- Insufficient CPU
- Insufficient Memory
- PVC Pending
- Node Selector mismatch
- Node Affinity mismatch
- Taints without matching Tolerations
- Image pull issues
- Unschedulable nodes

Verify:

```bash
kubectl get nodes

kubectl get pvc

kubectl get events
```

---

### Interview Answer

> I start with `kubectl describe pod` to identify scheduling events. Then I verify node resources, Persistent Volume Claims, taints, node affinity, tolerations, image availability, and scheduler events to determine why the Pod remains pending.

# AWS DevOps Interview Series – Set 16 (Part 4)

---

# 23. Scenario: Kubernetes cluster is running fine, but Pods are not able to communicate with Services. How do you troubleshoot this issue?

## Answer

When Pods cannot communicate with a Service, I troubleshoot layer by layer instead of assuming it's a networking issue.

### Step 1: Verify the Service

Check whether the Service exists.

```bash
kubectl get svc
```

Inspect its configuration.

```bash
kubectl describe svc <service-name>
```

Verify:

- Service Type
- ClusterIP
- Ports
- TargetPort
- Selector

---

### Step 2: Verify Endpoints

A Service without endpoints cannot forward traffic.

```bash
kubectl get endpoints
```

If endpoints are empty:

```
ENDPOINTS: <none>
```

The Service is not selecting any Pods.

---

### Step 3: Verify Labels and Selectors

Check Pod labels.

```bash
kubectl get pods --show-labels
```

Example

Pod

```yaml
labels:
  app: nginx
```

Service

```yaml
selector:
  app: nginx
```

Both must match exactly.

---

### Step 4: Verify Pod Readiness

Pods that fail the Readiness Probe are automatically removed from Service endpoints.

```bash
kubectl get pods
```

Check

```
READY

0/1
```

---

### Step 5: Test DNS

Inside another Pod:

```bash
nslookup service-name

dig service-name
```

or

```bash
curl http://service-name
```

---

### Step 6: Verify Network Policies

Check whether Network Policies are blocking communication.

```bash
kubectl get networkpolicy
```

---

### Step 7: Check kube-proxy

```bash
kubectl get pods -n kube-system
```

Ensure kube-proxy is healthy because it manages Service routing.

---

### Step 8: Verify CNI Plugin

Examples

- Calico
- Cilium
- AWS VPC CNI

```bash
kubectl get pods -n kube-system
```

---

### Production Interview Answer

> I first verify the Service configuration, selectors, and endpoints. Then I ensure Pods are Ready, validate DNS resolution, inspect Network Policies, check kube-proxy and the CNI plugin, and confirm that labels match correctly. Most Service communication issues are caused by selector mismatches, failed readiness probes, or network policies.

---

# 24. You are upgrading the Kubernetes cluster. After the upgrade, some applications are failing. How will you solve this issue?

## Answer

Cluster upgrades should always be planned carefully with rollback options.

### Step 1

Identify failing applications.

```bash
kubectl get pods -A
```

---

### Step 2

Describe failed Pods.

```bash
kubectl describe pod
```

---

### Step 3

Review logs.

```bash
kubectl logs
```

---

### Step 4

Check Kubernetes API compatibility.

Some APIs are removed in newer versions.

Example:

```
extensions/v1beta1

↓

networking.k8s.io/v1
```

Review:

```bash
kubectl api-resources
```

---

### Step 5

Verify Helm charts.

Older charts may not support the upgraded Kubernetes version.

```bash
helm list

helm upgrade
```

---

### Step 6

Check CRDs.

```bash
kubectl get crds
```

---

### Step 7

Verify Ingress Controller.

Ensure the controller version supports the new Kubernetes release.

---

### Step 8

Check CSI Drivers.

Storage drivers may require upgrades.

---

### Step 9

Verify Monitoring Stack.

Upgrade:

- Prometheus
- Grafana
- Fluent Bit
- Metrics Server

if required.

---

### Step 10

Rollback application if necessary.

```bash
helm rollback

kubectl rollout undo
```

---

### Production Best Practices

Before upgrading:

- Take etcd backup.
- Upgrade one environment at a time.
- Validate staging first.
- Review Kubernetes Release Notes.
- Verify API deprecations.
- Test applications thoroughly.

---

### Interview Answer

> After a cluster upgrade, I identify failing workloads, inspect logs and events, verify API compatibility, update Helm charts, validate CRDs, CSI drivers, Ingress Controllers, and monitoring components. If necessary, I roll back affected applications while investigating compatibility issues. Before upgrades, I always test in non-production environments and review Kubernetes release notes.

---

# 25. How do you implement multi-branch CI/CD pipelines in GitLab or Jenkins?

## Answer

Multi-branch pipelines automatically build each Git branch independently.

Typical branches:

```
main

develop

feature/*

release/*

hotfix/*
```

Each branch triggers its own pipeline.

---

## Jenkins Multibranch Pipeline

```
GitHub Repository

↓

Webhook

↓

Jenkins Multibranch Pipeline

↓

Discover Branches

↓

Automatically Create Pipeline

↓

Run Jenkinsfile
```

Each branch contains its own:

```
Jenkinsfile
```

Jenkins automatically scans the repository and creates separate jobs for each branch.

---

## Example Workflow

```
Developer

↓

feature/login

↓

Push

↓

Webhook

↓

Jenkins

↓

Build

↓

Unit Test

↓

SonarQube

↓

Trivy

↓

Docker Build

↓

Push ECR

↓

Deploy Dev
```

When merged:

```
main

↓

Production Pipeline

↓

Deploy Production
```

---

## GitLab CI Example

```
stages:

- build

- test

- deploy
```

Example:

```yaml
deploy-prod:

  only:
    - main

deploy-dev:

  only:
    - develop
```

---

## Production Strategy

| Branch | Environment |
|---------|-------------|
| feature/* | Dev |
| develop | QA |
| release/* | UAT |
| main | Production |
| hotfix/* | Production Patch |

---

## Best Practices

- Protect the `main` branch.
- Require Pull Request approvals.
- Run SonarQube scans.
- Perform Trivy image scans.
- Execute unit and integration tests.
- Use reusable Jenkins Shared Libraries.
- Store credentials securely in Jenkins Credentials Manager or Vault.
- Deploy to Kubernetes using Helm or ArgoCD.
- Enable automatic rollback for failed deployments.

---

## Interview Answer

> I implement multi-branch CI/CD using Jenkins Multibranch Pipelines or GitLab CI. Each Git branch has its own pipeline that automatically builds, tests, scans, and deploys to the appropriate environment. Feature branches deploy to Dev, develop to QA, release branches to UAT, and the main branch to Production. I protect the main branch with code reviews, automated quality gates, and security scans before deployment.

---

# ⭐ Senior Interview Tips

For 4+ years of DevOps experience, interviewers expect you to explain:

- **What** the technology is.
- **Why** you chose it.
- **How** you implemented it in production.
- **What challenges** you faced.
- **How you resolved** those challenges.
- **Which best practices** you followed.

Always support your answers with **real production examples**, as this demonstrates practical experience beyond theoretical knowledge.



# Interviewer: "Tell me about a challenging project."


Strong version, using STAR:

𝗦𝗶𝘁𝘂𝗮𝘁𝗶𝗼𝗻: Deployments were taking 45 minutes and failing silently about 1 in 5 times, blocking releases.

𝗧𝗮𝘀𝗸: Reduce deployment time and eliminate silent failures without adding headcount.

𝗔𝗰𝘁𝗶𝗼𝗻: Parallelized independent build stages, added proper health checks instead of fixed sleep timers, and added a rollback step triggered automatically on failed health checks.

𝗥𝗲𝘀𝘂𝗹𝘁: Deployment time dropped from 45 to 12 minutes. Silent failures went to zero because every failure now triggered an automatic rollback and an alert.



# DevOps Interview Preparation (4 Years Experience)

# AWS

## 1. What is the difference between an Application Load Balancer (ALB) and a Network Load Balancer (NLB)?

| ALB | NLB |
|-----|-----|
| Operates at Layer 7 (HTTP/HTTPS) | Operates at Layer 4 (TCP/UDP/TLS) |
| Supports path-based and host-based routing | Routes based on IP and Port |
| Best for web applications and microservices | Best for high-performance and low-latency applications |
| Supports WebSockets, HTTP/2 | Supports static IP and Elastic IP |
| Can integrate with AWS WAF | Handles millions of requests with very low latency |

**Example**

- Use **ALB** for React + Spring Boot microservices.
- Use **NLB** for gaming servers, VoIP, or TCP-based applications.

---

## 2. Explain how Auto Scaling works in AWS.

AWS Auto Scaling automatically adjusts EC2 instances based on demand.

### Components

- Launch Template
- Auto Scaling Group (ASG)
- Scaling Policies
- CloudWatch Alarms

### Types

- Dynamic Scaling
- Scheduled Scaling
- Predictive Scaling

### Example

If CPU > 70% for 5 minutes:

- Launch 2 new EC2 instances.

If CPU < 30%:

- Remove unnecessary instances.

Benefits:

- High Availability
- Cost Optimization
- Automatic Scaling

---

## 3. What are Security Groups and Network ACLs? How do they differ?

| Security Group | Network ACL |
|---------------|-------------|
| Instance level | Subnet level |
| Stateful | Stateless |
| Allow rules only | Allow and Deny rules |
| Evaluates all rules | Evaluates rules in order |
| Default allows outbound | Default allows all traffic |

**Example**

Security Group

- Allow SSH from office IP.
- Allow HTTP/HTTPS from Internet.

NACL

- Block suspicious IP range for entire subnet.

---

## 4. How do you secure an S3 bucket?

Best Practices:

- Block Public Access
- Enable Bucket Versioning
- Enable Server-side Encryption (SSE-S3 or SSE-KMS)
- Use IAM Policies with least privilege
- Use Bucket Policies carefully
- Enable MFA Delete
- Enable Access Logging
- Use VPC Endpoint instead of Internet access
- Enable AWS Config and CloudTrail monitoring

---

## 5. What is the difference between NAT Gateway and Internet Gateway?

### Internet Gateway (IGW)

- Allows public subnet instances to access Internet.
- Public IP required.

### NAT Gateway

- Allows private subnet instances to access Internet.
- Prevents inbound Internet connections.

Example:

Public EC2 → Internet Gateway

Private EC2 → NAT Gateway → Internet Gateway

---

## 6. How does Route 53 perform failover routing?

Route 53 continuously checks health checks.

Primary Server Healthy

User → Primary

Primary Server Down

User → Secondary Region

Uses:

- Disaster Recovery
- High Availability
- Multi-region Applications

---

## 7. Explain the EBS volume types and their use cases.

### gp3

- General Purpose SSD
- Default choice
- Web Applications

### io2

- High IOPS SSD
- Databases (Oracle, SQL Server)

### st1

- Throughput optimized HDD
- Big Data
- Log Processing

### sc1

- Cold HDD
- Backup
- Archive

---

## 8. What is the purpose of IAM Roles compared to IAM Users?

### IAM User

Permanent identity.

Example:

Developer
Administrator

### IAM Role

Temporary credentials.

Used by:

- EC2
- Lambda
- ECS
- EKS

Example

EC2 reads S3 without storing Access Keys.

Best Practice:

Never store Access Keys inside EC2.

Use IAM Roles.

---

# CI/CD

## 9. Explain the stages of a CI/CD pipeline.

Typical Pipeline

Developer
↓
Git Commit
↓
Build
↓
Unit Test
↓
Code Quality (SonarQube)
↓
Docker Build
↓
Push Image (ECR/Docker Hub)
↓
Deploy (Kubernetes)
↓
Smoke Test
↓
Production

Common Tools

- Git
- Jenkins
- GitHub Actions
- GitLab CI
- ArgoCD

---

## 10. How do you implement blue-green and canary deployments?

### Blue-Green Deployment

Blue = Current Version

Green = New Version

Deploy Green

Test

Switch Traffic

Rollback simply by pointing traffic back to Blue.

Advantages

- Zero downtime
- Easy rollback

---

### Canary Deployment

Deploy new version to 10% users.

Monitor.

Increase

10%

25%

50%

100%

Rollback if errors increase.

---

## 11. How do you handle rollback if deployment fails?

Methods

- Kubernetes rollout undo
- Redeploy previous Docker image
- Blue-Green switch back
- Git revert
- Helm rollback

Always monitor

- Error rate
- Response time
- CPU
- Memory

---

## 12. How would you deploy the same application to multiple environments?

Maintain separate configurations.

Example

Development

Testing

Staging

Production

Use

- Terraform Workspaces
- Separate tfvars
- Helm Values
- Kubernetes Namespaces

CI/CD promotes the same artifact across environments.

---

# Terraform

## 13. What are Terraform state files, and why are they important?

Terraform stores infrastructure details in:

terraform.tfstate

Contains

- Resource IDs
- Metadata
- Dependencies

Without state file Terraform cannot determine infrastructure changes.

---

## 14. How do you manage remote state?

Use

- S3 Bucket
- DynamoDB Lock Table

Benefits

- Team Collaboration
- Versioning
- State Locking
- Backup

Example Backend

```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
  }
}
```

---

## 15. Explain Terraform modules and workspaces.

### Modules

Reusable Terraform code.

Example

VPC Module

EC2 Module

RDS Module

Advantages

- Reusability
- Easy Maintenance

### Workspaces

Separate environments using same code.

Example

dev

qa

prod

---

## 16. How do you detect and prevent configuration drift?

Detect

```bash
terraform plan
```

Prevent

- Terraform as single source of truth
- Restrict manual AWS changes
- Regular terraform plan
- AWS Config
- CI/CD validation

---

# Docker & Kubernetes

## 17. What is the difference between Deployment, StatefulSet, and DaemonSet?

### Deployment

Stateless Applications

Examples

- Nginx
- Spring Boot
- React

---

### StatefulSet

Stateful Applications

Examples

- MySQL
- MongoDB
- Kafka

Provides

- Stable hostname
- Persistent storage

---

### DaemonSet

Runs one pod per node.

Examples

- Fluentd
- Prometheus Node Exporter
- Datadog Agent

---

## 18. How do readiness and liveness probes work?

### Readiness Probe

Checks if application is ready to receive traffic.

If failed

Pod removed from Service.

---

### Liveness Probe

Checks application health.

If failed

Pod restarted.

---

## 19. How do you troubleshoot a pod stuck in CrashLoopBackOff?

Steps

```bash
kubectl get pods

kubectl describe pod <pod>

kubectl logs <pod>

kubectl logs --previous <pod>

kubectl exec -it <pod> -- sh

kubectl get events
```

Common Causes

- Wrong image
- Missing Secret
- ConfigMap error
- Database unavailable
- Application crash
- Memory limit exceeded

---

## 20. What are ConfigMaps and Secrets?

### ConfigMap

Stores

- Environment variables
- URLs
- Configuration

Not encrypted.

---

### Secret

Stores

- Passwords
- API Keys
- Certificates

Base64 encoded and can be encrypted using KMS.

---

## 21. Explain Ingress and its advantages.

Ingress provides HTTP/HTTPS routing into Kubernetes.

Features

- Single Load Balancer
- SSL Termination
- Path-based Routing
- Host-based Routing

Example

/api → Backend Service

/ui → Frontend Service

---

# Monitoring & Logging

## 22. How do you monitor AWS infrastructure?

Tools

- Amazon CloudWatch
- CloudTrail
- AWS Config
- X-Ray
- Prometheus
- Grafana

Monitor

- CPU
- Memory (CloudWatch Agent)
- Disk
- Network
- Error Rate
- Latency

---

## 23. What metrics would you monitor for an EC2 instance?

Important Metrics

- CPU Utilization
- Memory Usage
- Disk Usage
- Disk IOPS
- Network In/Out
- Status Checks
- Load Average
- Application Logs

---

## 24. Explain CloudWatch Logs, Metrics, and Alarms.

### Metrics

Numerical data

Example

CPU = 75%

---

### Logs

Application logs

System logs

Audit logs

---

### Alarms

Trigger actions

Example

CPU > 80%

Send SNS Notification

Launch Auto Scaling

---

## 25. How do you centralize application logs?

Common Stack

Application

↓

Fluent Bit / Fluentd

↓

CloudWatch Logs

↓

OpenSearch / Elasticsearch

↓

Kibana / Grafana

Benefits

- Easy Searching
- Troubleshooting
- Alerting

---

# DevOps Scenario Questions

## 26. A production deployment failed. What steps would you take?

1. Check CI/CD logs.
2. Verify deployment status.
3. Check Kubernetes pod status.
4. View pod logs.
5. Verify ConfigMaps and Secrets.
6. Check database connectivity.
7. Roll back if needed.
8. Inform stakeholders.
9. Perform RCA after recovery.
10. Add preventive measures.

---

## 27. How would you reduce deployment downtime?

- Blue-Green Deployment
- Canary Deployment
- Rolling Updates
- Readiness Probes
- Multiple Replicas
- Auto Scaling
- Health Checks
- Load Balancer

---

## 28. How do you ensure High Availability and Disaster Recovery in AWS?

High Availability

- Multi-AZ Deployment
- Auto Scaling
- Load Balancer
- RDS Multi-AZ

Disaster Recovery

- Cross Region Backup
- Route53 Failover
- S3 Cross Region Replication
- Infrastructure as Code
- Regular DR Testing

---

## 29. How would you optimize AWS costs without impacting performance?

- Right-size EC2 instances
- Use Auto Scaling
- Purchase Reserved Instances or Savings Plans for steady workloads
- Use Spot Instances for fault-tolerant jobs
- Delete unused EBS volumes and snapshots
- Enable S3 lifecycle policies
- Use gp3 instead of older gp2 volumes where appropriate
- Monitor with AWS Cost Explorer and Budgets

---

## 30. Describe a challenging production incident you resolved.

**Sample Answer (4 Years Experience)**

In one production release, users started receiving HTTP 503 errors immediately after deployment.

I first checked the Load Balancer health checks and noticed that the new application pods were failing readiness probes. After reviewing the pod logs using `kubectl logs`, I found that a required environment variable was missing because the ConfigMap had not been updated during deployment.

I updated the ConfigMap, restarted the deployment using `kubectl rollout restart`, and verified that all pods became Ready. Traffic was restored without needing a full rollback.

To prevent the issue from happening again, we added:
- Configuration validation in the CI/CD pipeline.
- Helm chart value checks.
- Smoke tests after deployment.
- Automated deployment approval only after health checks passed.

This incident reinforced the importance of validating configuration changes and implementing automated post-deployment verification.

---

# Quick Interview Tips

- Answer with real project examples whenever possible.
- Mention AWS services you have used (EC2, S3, IAM, ALB, ASG, CloudWatch, Route53, RDS, EKS, ECR).
- Explain Terraform modules, remote state, and workspaces with examples.
- Demonstrate Kubernetes troubleshooting using `kubectl` commands.
- Highlight monitoring, automation, security, and cost optimization practices.
- Emphasize CI/CD, Infrastructure as Code, observability, and zero-downtime deployments as core DevOps principles.

# DevOps Interview 2026 — Questions That Are Actually Being Asked

Detailed Answers for 4+ Years Experienced DevOps Engineers

---

# 🔵 Chapter 1 — Kubernetes

## 🔹 Your pod is in CrashLoopBackOff — first 3 commands you run?

When a pod enters CrashLoopBackOff, I first identify whether the issue is application-related, configuration-related, or infrastructure-related.

The first command I run is:

```bash
kubectl get pods -n <namespace>
```

This helps me check restart count, pod status, and node placement.

Next:

```bash
kubectl describe pod <pod-name> -n <namespace>
```

This shows Kubernetes events such as:

* OOMKilled
* Probe failures
* Failed mounts
* Image pull errors
* Scheduling issues

Then:

```bash
kubectl logs <pod-name> --previous
```

Using `--previous` is important because the current container may already be restarting.

In production, I also verify:

* Recent deployments
* ConfigMap/Secret changes
* External dependency failures
* Resource exhaustion

If customer impact exists, rollback is prioritized immediately.

---

## 🔹 Walk me through how a request travels from browser → Ingress → Service → Pod.

When a user accesses:

```txt
https://api.company.com
```

The request first reaches DNS, which resolves the domain to a Load Balancer.

The traffic then reaches the Kubernetes Ingress Controller such as:

* NGINX Ingress
* AWS Load Balancer Controller

Ingress checks routing rules and forwards traffic to the appropriate Kubernetes Service.

The Service uses label selectors to identify backend pods.

kube-proxy handles internal routing using iptables or IPVS.

Finally, the request reaches one of the healthy application pods.

In production environments, we additionally configure:

* TLS termination
* WAF
* Rate limiting
* Sticky sessions
* Health checks

---

## 🔹 When do you pick Deployment vs StatefulSet vs DaemonSet?

Deployment is used for stateless applications such as APIs, frontend applications, and microservices. Deployments support rolling updates, scaling, and self-healing.

StatefulSet is used for stateful applications where stable hostname, ordered startup, and persistent storage are required. Examples include Kafka, MongoDB, MySQL, and Elasticsearch.

DaemonSet is used when one pod must run on every node. This is commonly used for monitoring agents, logging agents, and security tools such as FluentBit or Node Exporter.

In production:

* Stateless apps → Deployment
* Databases/message brokers → StatefulSet
* Node-level utilities → DaemonSet

---

## 🔹 Explain what a PodDisruptionBudget is and why production needs it.

A PodDisruptionBudget (PDB) ensures a minimum number of pods remain available during voluntary disruptions.

Example:

```yaml
minAvailable: 2
```

Voluntary disruptions include:

* Node drain
* Cluster upgrades
* Autoscaler operations
* Maintenance activities

Without a PDB, all replicas could terminate during maintenance, causing downtime.

In production, PDBs are critical for maintaining high availability during infrastructure operations.

---

## 🔹 How does the Kubernetes scheduler decide which node gets a pod?

The Kubernetes scheduler works in two phases:

* Filtering
* Scoring

Filtering removes nodes that do not satisfy requirements such as:

* CPU/memory availability
* Taints & tolerations
* Node affinity
* Storage requirements

Scoring ranks eligible nodes based on:

* Resource balance
* Least utilization
* Affinity preferences
* Topology spread

In production, topology spread constraints are important to distribute pods across availability zones.

---

## 🔹 Tell me the real difference between HPA, VPA, and KEDA in production.

HPA (Horizontal Pod Autoscaler) scales the number of pods based on metrics such as CPU, memory, or Prometheus metrics.

VPA (Vertical Pod Autoscaler) adjusts CPU and memory requests/limits automatically.

KEDA (Kubernetes Event Driven Autoscaling) scales workloads based on event sources such as Kafka, RabbitMQ, or AWS SQS.

Production usage:

* HPA → APIs and web applications
* KEDA → Event-driven workloads
* VPA → Stable workloads requiring resource optimization

HPA is the most commonly used autoscaler in production.

---

## 🔹 List 5 possible reasons a pod stays Pending for 10 minutes.

Common reasons include:

1. Insufficient CPU or memory
2. Unbound PersistentVolumeClaim
3. Taints without toleration
4. Node selector mismatch
5. Missing image pull secret

Other causes may include:

* Autoscaler delays
* Storage class issues
* Node NotReady state

First troubleshooting step:

```bash
kubectl describe pod <pod-name>
```

---

## 🔹 Distinguish between liveness, readiness, and startup probes with examples.

Liveness probe checks whether the application is alive. If it fails repeatedly, Kubernetes restarts the container.

Readiness probe checks whether the application is ready to receive traffic. If it fails, the pod is removed from service endpoints.

Startup probe is used for slow-starting applications such as Spring Boot or JVM-based services.

Examples:

* Liveness → Hung application
* Readiness → DB connection not ready
* Startup → Slow application initialization

Readiness probes are extremely important in production because they prevent traffic from reaching unhealthy pods.

---

## 🔹 Describe how you drain a node without dropping live traffic.

I use:

```bash
kubectl drain <node> --ignore-daemonsets --delete-emptydir-data
```

Before draining:

* Ensure multiple replicas exist
* Configure PodDisruptionBudget
* Verify readiness probes

During draining, Kubernetes safely evicts workloads and reschedules them to healthy nodes.

In production, I monitor:

* Error rates
* Application latency
* Pod health
* Traffic patterns

---

# 🟠 Chapter 2 — CI/CD & GitLab

## 🔹 How would you structure a GitLab pipeline for DEV → UAT → PROD with manual gates?

A standard production GitLab pipeline contains stages like:

```yaml
stages:
  - build
  - test
  - security-scan
  - deploy-dev
  - deploy-uat
  - deploy-prod
```

DEV deployment is automatic.

UAT and PROD deployments require manual approvals.

Production pipelines also include:

* Security scanning
* Artifact retention
* Rollback jobs
* Protected branches
* Environment-specific variables

The same artifact should be promoted across all environments.

---

## 🔹 Define build-once-promote-everywhere — why does rebuilding per env break things?

Build-once-promote-everywhere means creating a single immutable artifact and promoting the same artifact across DEV, UAT, and PROD.

For example, a Docker image is built once and the same image tag is deployed everywhere.

Rebuilding separately per environment is risky because:

* Dependencies may change
* Artifacts may differ
* Environment drift can occur
* Debugging becomes difficult

Using the same artifact ensures consistency and reliability.

---

## 🔹 Share how you handle secrets inside a GitLab CI pipeline.

Secrets should never be hardcoded in repositories.

I manage secrets using:

* GitLab masked variables
* HashiCorp Vault
* AWS Secrets Manager
* SSM Parameter Store
* Kubernetes Secrets

Security best practices include:

* Secret rotation
* Least privilege access
* Environment separation
* Audit logging

For EKS workloads, IRSA is preferred over static AWS access keys.

---

## 🔹 Imagine your pipeline passes but deployment silently fails — how do you catch it?

A successful pipeline does not always guarantee a successful deployment. In production, I implement multiple post-deployment validation checks.

These include:

* Readiness probe validation
* Smoke testing
* Health endpoint checks
* Monitoring alerts
* Log verification
* Synthetic monitoring

For example:

```bash
curl -f https://api.company.com/health
```

I also integrate Prometheus and Grafana alerts to detect increased error rates or latency immediately after deployment.

---

## 🔹 Outline how you implement blue-green deployment in GitLab CI.

In blue-green deployment, two environments exist:

* Blue → current production
* Green → new version

The new application version is deployed to the green environment first.

After validation and testing, traffic is switched from blue to green using ingress rules or load balancer configuration.

Benefits include:

* Near zero downtime
* Fast rollback
* Safer production releases

If any issue occurs, traffic can instantly switch back to the blue environment.

---

## 🔹 What triggers a rollback in your current pipeline setup?

Rollback is triggered when deployment validation fails.

Common triggers include:

* Failed readiness checks
* Increased error rates
* Pod crashes
* High latency
* Failed smoke tests
* Monitoring alerts

Rollback methods include:

* Helm rollback
* Argo Rollouts rollback
* Kubernetes rollout undo

In production, rollback automation significantly reduces outage duration.

---

## 🔹 Compare GitOps using ArgoCD vs a traditional push-based CI/CD.

Traditional CI/CD pipelines directly push deployments into Kubernetes clusters.

GitOps using ArgoCD follows a pull-based model where the cluster continuously syncs its state from Git repositories.

Advantages of GitOps include:

* Better auditability
* Easier rollback
* Declarative infrastructure
* Drift detection
* Improved security

ArgoCD continuously monitors Git and reconciles cluster drift automatically.

GitOps is now widely preferred in enterprise Kubernetes environments.

---

## 🔹 Think about this — how do you stop a broken image from reaching production?

To prevent broken images from reaching production, I implement multiple quality gates inside CI/CD pipelines.

These include:

* Unit tests
* Integration tests
* Vulnerability scanning
* Static code analysis
* Image signing
* Smoke tests
* Manual approvals

Tools commonly used:

* Trivy
* SonarQube
* OPA/Gatekeeper
* Snyk

Only validated and approved artifacts are promoted to production.

---

## 🔹 Clarify what a canary deployment is and when you would NOT use it.

A canary deployment releases a new application version to a small percentage of users before full rollout.

Example:

* 5% traffic → new version
* 95% traffic → existing version

If monitoring remains healthy, traffic gradually increases.

Benefits:

* Reduced deployment risk
* Easier issue detection
* Safer production releases

I avoid canary deployments when:

* Database schema changes are incompatible
* Applications are stateful
* Traffic volume is too low for meaningful analysis

---

# 🟡 Chapter 3 — Terraform & IaC

## 🔹 Your tfstate got deleted in production — what's your immediate action?

If tfstate is deleted in production, the first priority is preventing additional Terraform executions.

Immediate steps:

1. Stop all pipeline executions
2. Restore backup state
3. Verify S3 versioning
4. Reconcile infrastructure drift
5. Validate Terraform state consistency

Prevention methods:

* S3 versioning
* Remote backend
* Access restrictions
* State locking

---

## 🔹 Break down how S3 + DynamoDB remote backend prevents state corruption in a team.

S3 stores the Terraform remote state centrally so all engineers use the same infrastructure state.

DynamoDB provides state locking.

When one engineer runs Terraform:

* DynamoDB lock prevents concurrent modifications.
* Other users must wait until the lock is released.

Benefits:

* Prevents corruption
* Prevents simultaneous updates
* Supports team collaboration
* Maintains consistent infrastructure state

---

## 🔹 Why does Terraform plan show no changes even when infra has drifted?

Terraform may not detect drift for several reasons:

* State not refreshed
* Manual changes ignored
* Provider limitations
* ignore_changes lifecycle configuration
* Incorrect provider credentials

Troubleshooting commands:

```bash
terraform refresh
terraform plan
```

In production, regular drift detection is important for infrastructure consistency.

---

## 🔹 Differentiate between terraform taint and terraform import — real use cases.

`terraform taint` marks a resource for forced recreation.

Example:

* Corrupted EC2 instance
* Broken VM requiring recreation

`terraform import` brings an existing manually-created resource under Terraform management.

Example:

* Existing RDS database
* Existing VPC

Taint recreates resources.
Import adds existing resources into Terraform state.

---

## 🔹 Illustrate how you structure Terraform modules for multi-environment setups.

I separate reusable modules from environment-specific configurations.

Typical structure:

```txt
modules/
envs/dev
envs/uat
envs/prod
```

Modules contain reusable logic such as:

* VPC
* EKS
* RDS
* Security Groups

Each environment has separate:

* Variables
* State files
* Backend configuration

This improves reusability, maintainability, and environment isolation.

---

## 🔹 What danger does terraform destroy pose inside a CI/CD pipeline?

`terraform destroy` can completely remove production infrastructure.

Risks include:

* Application downtime
* Database deletion
* Data loss
* Networking removal

Protection methods:

* Manual approvals
* Restricted IAM permissions
* Separate production pipelines
* prevent_destroy lifecycle rule

Production destroy operations should never run automatically.

---

## 🔹 Contrast count, for_each, and dynamic blocks — when does each one win?

`count` is best for simple repeated resources.

Example:

```hcl
count = 3
```

`for_each` is preferred for maps and sets because resources get stable names.

Example:

* Multiple security group rules
* Multiple IAM users

`dynamic` blocks are used for repeated nested configurations.

Example:

* Dynamic ingress rules
* Repeated nested blocks

In production, `for_each` is usually safer than `count` because index shifting issues are avoided.

---

## 🔹 Mention how you manage sensitive variables without exposing them in state.

Sensitive variables are managed using:

* Vault
* AWS Secrets Manager
* SSM Parameter Store
* Encrypted backends

Terraform sensitive variables only hide values from CLI output.

They can still exist in state files.

Therefore:

* Remote state encryption
* Access restrictions
* Backend security

are extremely important.

---

## 🔹 Justify when you'd use the lifecycle prevent_destroy meta-argument.

`prevent_destroy` protects critical resources from accidental deletion.

Example:

```hcl
lifecycle {
  prevent_destroy = true
}
```

Common production use cases:

* Production databases
* Critical S3 buckets
* Networking components
* Shared infrastructure

This acts as a safety mechanism against human error.

---

# 🟢 Chapter 4 — AWS & EKS

## 🔹 IAM Role vs IAM User — which one do you assign to EKS workloads and why?

IAM Roles should always be used for EKS workloads.

Reasons:

* Temporary credentials
* Better security
* No hardcoded secrets
* Supports IRSA
* Automatic credential rotation

IAM Users should never be embedded inside pods because static credentials create major security risks.

---

## 🔹 Show how aws-auth ConfigMap controls access to your EKS cluster.

The aws-auth ConfigMap maps AWS IAM entities to Kubernetes RBAC.

Example:

```yaml
mapRoles:
- rolearn: arn:aws:iam::123456789:role/admin-role
```

It controls:

* Cluster admin access
* Node authentication
* Developer access
* RBAC integration

Without correct aws-auth configuration, users or worker nodes cannot access the cluster.

---

## 🔹 A pod can't reach an external API — trace your debugging steps end to end.

I troubleshoot from application layer down to networking.

Steps include:

1. Check pod logs
2. Verify DNS resolution
3. Test outbound connectivity
4. Validate NetworkPolicy
5. Check Security Groups
6. Verify NACLs
7. Check route tables and NAT Gateway

Useful commands:

```bash
nslookup
curl
ping
```

In EKS, outbound connectivity issues are often related to NAT Gateway or Security Group configuration.

---

## 🔹 Evaluate EKS managed node group vs self-managed vs Fargate in production.

Managed Node Groups are AWS-managed worker nodes.

Benefits:

* Easier upgrades
* Reduced maintenance
* Better operational simplicity

Self-managed nodes provide full customization.

Used when:

* Custom AMIs required
* Advanced tuning needed
* Specialized workloads exist

Fargate is serverless Kubernetes compute.

Benefits:

* No node management
* Simplified operations

Limitations:

* Higher cost
* Less flexibility

Managed node groups are most common in enterprise production environments.

---

## 🔹 Summarize how IRSA works and why it's safer than node-level IAM roles.

IRSA stands for IAM Roles for Service Accounts.

It allows Kubernetes Service Accounts to assume dedicated IAM roles.

Instead of giving permissions to the entire node, permissions are assigned only to specific pods.

Benefits:

* Least privilege access
* Better security isolation
* No shared credentials
* Reduced blast radius

IRSA is significantly safer than node-level IAM roles.

---

## 🔹 Give a real scenario where you'd need a VPC endpoint over public routing.

A VPC endpoint is required when private resources must access AWS services without using the public internet.

Example:

* Private EKS nodes accessing S3
* Private subnets accessing DynamoDB
* Compliance-sensitive workloads

Benefits:

* Improved security
* Lower latency
* Reduced NAT Gateway cost
* Traffic stays inside AWS network

---

## 🔹 Spot this — an S3 bucket is publicly exposed. How did it happen and how do you fix it at scale?

Public S3 exposure commonly happens due to:

* Public bucket policies
* Misconfigured ACLs
* Disabled Block Public Access
* Overly permissive IAM policies

Fixes include:

* Enable Block Public Access
* Remove public bucket policies
* Use AWS Config rules
* Use SCP policies
* Continuous compliance scanning

At scale, automated governance and compliance tooling are critical.

---

## 🔹 Detail how you handle cross-account deployments in AWS.

Cross-account deployments are usually implemented using IAM AssumeRole.

The CI/CD pipeline assumes a deployment role in the target AWS account using STS.

Flow:

1. Pipeline authenticates
2. Assumes target account role
3. Deploys infrastructure/resources

Benefits:

* Secure account separation
* Centralized CI/CD
* Least privilege access

This approach is widely used in multi-account enterprise AWS environments.

---

## 🔹 Which do you troubleshoot first — Security Group or NACL? Justify your answer.

I troubleshoot Security Groups first because they are stateful and are the most common source of connectivity issues.

Security Groups control instance-level traffic and are easier to validate.

NACLs are stateless subnet-level controls and are usually secondary troubleshooting areas.

Typical troubleshooting order:

1. Security Groups
2. Route tables
3. NACLs
4. Network policies
5. DNS

In production incidents, Security Group misconfigurations are far more common than NACL problems.

---
## "𝗔 𝗰𝗼𝗻𝘁𝗮𝗶𝗻𝗲𝗿 𝗶𝘀 𝗿𝘂𝗻𝗻𝗶𝗻𝗴 𝗶𝗻 𝗽𝗿𝗼𝗱𝘂𝗰𝘁𝗶𝗼𝗻 𝗮𝗻𝗱 𝘀𝘂𝗱𝗱𝗲𝗻𝗹𝘆 𝘀𝘁𝗼𝗽𝘀. 𝗛𝗼𝘄 𝗱𝗼 𝘆𝗼𝘂 𝗱𝗲𝗯𝘂𝗴 𝗶𝘁?"

Here is how an experienced engineer answers this:

𝗦𝘁𝗲𝗽 𝟭 → 𝗖𝗵𝗲𝗰𝗸 𝗹𝗼𝗴𝘀
docker logs <container_id>
This shows exactly what happened before it stopped.

𝗦𝘁𝗲𝗽 𝟮 → 𝗖𝗵𝗲𝗰𝗸 𝗲𝘅𝗶𝘁 𝗰𝗼𝗱𝗲
docker inspect <container_id> --format='{{.State.ExitCode}}'

Exit code 0   = process finished on its own
Exit code 1   = application crashed
Exit code 137 = killed due to out of memory

𝗦𝘁𝗲𝗽 𝟯 → 𝗙𝗶𝘅 𝗯𝗮𝘀𝗲𝗱 𝗼𝗻 𝘄𝗵𝗮𝘁 𝘆𝗼𝘂 𝗳𝗶𝗻𝗱
→ App crashed?     Fix the error in your code or config
→ Out of memory?   Add --memory flag when running container
→ Wrong command?   Fix CMD or ENTRYPOINT in your Dockerfile

 
