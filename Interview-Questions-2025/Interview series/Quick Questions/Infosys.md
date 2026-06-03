# Infosys DevOps Interview Questions & Answers (4+ Years Experience)

# 1. An application hosted behind an ALB is returning 503 errors. How would you troubleshoot the issue?

When an Application Load Balancer returns HTTP 503 errors, it usually means the load balancer cannot find any healthy backend targets to forward requests to. My first step is checking the Target Group health status in AWS. If targets are marked unhealthy, I review the configured health check path, response codes, timeout values, and intervals. I then verify whether EC2 instances, ECS tasks, or Kubernetes pods are actually listening on the expected ports. Next, I inspect Security Groups, NACLs, and routing rules to ensure traffic can reach the backend. I also review application logs and CloudWatch metrics to identify application-level failures. If the issue started after a deployment, I verify the latest release and perform a rollback if necessary. My troubleshooting flow is ALB → Target Group → Backend Service → Application Logs → Network Configuration.

---

# 2. Users are reporting slow application performance. Which AWS services and metrics would you check first?

I begin by checking Amazon CloudWatch because it provides visibility into infrastructure and application performance. I review EC2 CPU utilization, memory usage, disk I/O, network throughput, and load balancer latency. For databases, I inspect RDS metrics such as CPU utilization, connections, free memory, read latency, and write latency. If the application runs on EKS, I analyze pod resource utilization and HPA activity. I also examine ALB Target Response Time and HTTP error rates. If infrastructure metrics appear normal, I move to application monitoring tools such as Prometheus, Grafana, or APM solutions to identify slow database queries, external API latency, thread contention, or inefficient code paths.

---

# 3. An Auto Scaling Group is not launching new instances during traffic spikes. How would you investigate?

I first verify whether scaling policies are configured correctly. Then I review CloudWatch metrics to determine whether scaling thresholds are being breached. Next, I inspect scaling activities within the Auto Scaling Group to identify failed launch attempts. Common issues include insufficient subnet IP addresses, EC2 capacity shortages, invalid launch templates, IAM permission problems, or reaching AWS service limits. I also verify whether cooldown periods are delaying scaling events. If scaling policies are based on custom metrics, I ensure those metrics are being published correctly. Finally, I review AWS CloudTrail and ASG activity history to determine the exact reason scaling did not occur.

---

# 4. An RDS database becomes unavailable during business hours. What would be your recovery approach?

The first priority is minimizing business impact. I immediately check RDS events and CloudWatch metrics to determine whether the issue is caused by resource exhaustion, storage limitations, network connectivity, failover events, or database engine problems. If Multi-AZ is enabled, I verify whether automatic failover has occurred successfully. If the primary database is unavailable, I initiate recovery procedures using snapshots, read replicas, or failover mechanisms depending on the architecture. Simultaneously, I communicate incident status to stakeholders and application teams. Once service is restored, I perform root cause analysis and implement preventive measures such as performance tuning, scaling, monitoring improvements, or architecture enhancements.

---

# 5. How would you perform a zero-downtime deployment in AWS?

For zero-downtime deployments, I typically use Blue-Green Deployment or Rolling Deployment strategies. In a Blue-Green approach, a new environment is deployed alongside the existing production environment. After validating functionality, traffic is switched using Route 53 or Load Balancer routing rules. In Kubernetes environments, I use rolling updates with proper readiness probes, PodDisruptionBudgets, and multiple replicas. During deployment, healthy instances continue serving traffic while new instances gradually replace old ones. If issues are detected, rollback can be performed immediately by redirecting traffic to the previous version. This approach minimizes user impact and deployment risk.

---

# 6. A production deployment caused downtime. How would you perform rollback and identify the root cause?

When a deployment causes downtime, my first objective is service restoration. If the deployment introduced the issue, I immediately perform a rollback using ArgoCD, Helm, Kubernetes rollout undo, Jenkins pipeline rollback, or previous container images depending on the deployment platform. Once service is restored, I analyze deployment logs, application logs, monitoring dashboards, configuration changes, infrastructure modifications, and release notes. I compare the working version with the failed version to identify differences. Finally, I document findings through a Root Cause Analysis report and implement preventive controls such as deployment validation checks, canary releases, automated testing, or enhanced monitoring.

---

# 7. Monitoring alerts show increased response time but infrastructure appears healthy. How would you investigate?

If infrastructure metrics look healthy, the issue is likely occurring at the application layer. I begin by reviewing application response time metrics, error rates, database query performance, cache hit ratios, external API dependencies, thread pool utilization, and application logs. I also inspect distributed tracing data if available. Many latency issues are caused by inefficient queries, dependency bottlenecks, application-level memory pressure, or third-party service delays. My approach is to trace the complete request path and identify where latency is being introduced rather than focusing solely on infrastructure health.

---

# 8. Disk utilization on production servers reaches 95%. What immediate and long-term actions would you take?

When disk utilization reaches 95%, immediate action is required because applications may soon fail to write data. I first identify the largest consumers using commands such as du, df, find, and log analysis tools. Temporary files, old logs, container images, and backup files are common causes. If necessary, I archive or remove unnecessary data to free space. Long-term improvements include implementing log rotation, monitoring disk growth trends, expanding storage volumes, enforcing retention policies, and automating cleanup procedures. I also configure alerts so the team is notified before disk utilization reaches critical levels.

---

# 9. A sudden traffic spike causes application degradation. How would you stabilize the platform?

The first step is understanding whether the platform is scaling correctly. I check load balancer metrics, application metrics, pod utilization, Auto Scaling Groups, HPA behavior, database performance, and cache effectiveness. If capacity is insufficient, I scale horizontally by increasing pod replicas or adding infrastructure resources. I also verify rate limiting, caching mechanisms, CDN performance, and queue processing systems. If the traffic spike is legitimate, scaling and optimization are prioritized. If it appears malicious, such as a DDoS attack, I activate AWS WAF protections, rate limiting policies, and security controls. Throughout the incident, I continuously monitor recovery metrics and communicate status updates to stakeholders.

# 10. The Jenkins master is running out of disk space. What actions would you take?

When Jenkins starts running out of disk space, my first priority is preventing build failures and service disruption. I begin by identifying what is consuming storage using operating system commands such as df, du, and find. In most cases, old build artifacts, workspace directories, archived logs, Docker images, and temporary files are responsible for excessive disk usage.

As an immediate action, I clean unused workspaces, remove old build histories, delete unnecessary artifacts, clear temporary files, and prune unused Docker images. If Jenkins is hosted on EC2, I verify whether the underlying EBS volume can be expanded safely.

For long-term prevention, I configure build retention policies, artifact expiration policies, external artifact repositories such as Nexus or Artifactory, automated workspace cleanup, monitoring alerts, and storage utilization dashboards. This ensures Jenkins remains stable and scalable over time.

---

# 11. Build artifacts are not getting uploaded to the artifact repository. How would you investigate?

I start by reviewing the pipeline logs to identify the exact stage where the upload fails. The most common causes are authentication failures, incorrect repository URLs, expired credentials, network connectivity issues, insufficient permissions, or storage limitations on the repository server.

I verify that the generated artifact exists and matches the expected file path. Next, I validate connectivity between Jenkins and the artifact repository using curl or network diagnostics. I check repository credentials stored in Jenkins credentials management and confirm that permissions allow artifact uploads.

If everything appears correct, I inspect repository-side logs in Nexus or Artifactory to determine whether uploads are being rejected. Once the root cause is identified, I rerun the pipeline and validate successful artifact publication.

---

# 12. A Docker container keeps restarting in production. How would you identify the root cause?

When a Docker container continuously restarts, I first inspect container logs because application failures are the most common cause. I review startup logs, runtime exceptions, dependency failures, configuration issues, and connectivity errors.

Next, I check the container exit code using Docker inspection commands. Exit codes often indicate whether the container failed because of application crashes, out-of-memory conditions, permission issues, or invalid startup commands.

I also review resource utilization metrics such as CPU and memory consumption. Containers frequently restart because memory limits are too low, causing OOM kills. If the application depends on databases, APIs, or external services, I verify connectivity and authentication. My goal is to identify whether the issue originates from the application, configuration, infrastructure, or dependency layer.

---

# 13. Docker image size has grown from 300MB to 3GB. How would you optimize it?

A sudden increase in image size usually indicates unnecessary dependencies, build artifacts, temporary files, or large base images. I start by reviewing the Dockerfile and identifying layers contributing the most storage consumption.

The first optimization is selecting a smaller base image such as Alpine or Distroless when appropriate. I then implement multi-stage builds so that compilation tools remain in the build stage while only runtime components are included in the final image.

I remove unnecessary packages, caches, package manager metadata, temporary files, and development dependencies. I also consolidate Dockerfile layers where possible to reduce image overhead. Finally, I scan the image using tools such as Docker Scout or Trivy to identify unnecessary content. These optimizations significantly reduce image size and improve deployment speed.

---

# 14. A container works perfectly on a developer machine but fails in Kubernetes. What would you check?

The first step is understanding that local Docker execution differs significantly from Kubernetes environments. I begin by reviewing pod logs and Kubernetes events to identify startup errors.

I verify environment variables, ConfigMaps, Secrets, mounted volumes, resource limits, network policies, DNS resolution, service discovery, and image versions. Many applications work locally because developers use local configurations that are missing in Kubernetes.

I also inspect readiness probes, liveness probes, service accounts, RBAC permissions, and dependency connectivity. If the application communicates with external services, I verify DNS resolution and network accessibility within the cluster. The objective is identifying differences between the developer environment and the Kubernetes environment.

---

# 15. How would you recover if the Docker daemon becomes unavailable on a production server?

The first step is confirming whether the Docker daemon process has stopped or become unresponsive. I inspect Docker service status, system logs, and daemon logs to determine the cause.

Common reasons include resource exhaustion, disk space issues, corrupted Docker metadata, failed upgrades, or operating system problems. If the issue is isolated to the Docker service, I restart the daemon and verify container recovery.

If the underlying server is unhealthy, I fail workloads over to healthy nodes or instances depending on the architecture. In highly available environments such as Kubernetes or ECS, workloads are automatically rescheduled onto healthy nodes. After recovery, I investigate the root cause and implement preventive monitoring and capacity planning measures.

---

# 16. A pod is stuck in CrashLoopBackOff. How would you troubleshoot it?

CrashLoopBackOff indicates Kubernetes is repeatedly attempting to restart a failing container. My first step is reviewing pod logs to identify application-level errors. Next, I inspect pod events to determine whether failures are caused by image issues, probe failures, configuration errors, missing secrets, insufficient resources, or dependency connectivity problems.

I verify resource requests and limits, ConfigMaps, Secrets, startup commands, environment variables, and external service availability. If the application depends on databases or APIs, I test connectivity from inside the cluster.

Once the root cause is identified, I apply the necessary fix and monitor the pod until it stabilizes successfully.

---

# 17. A deployment rollout is stuck and new pods are not becoming ready. What steps would you follow?

If a rollout becomes stuck, I first examine the deployment status and pod events. The most common reason is readiness probes failing. I review readiness probe configurations, application startup times, resource availability, and dependency connectivity.

Next, I inspect pod logs to identify application startup failures. I verify ConfigMaps, Secrets, mounted volumes, database access, and external API connectivity. If resource constraints exist, I check whether pods can be scheduled successfully and whether nodes have sufficient capacity.

If the rollout cannot proceed safely, I perform a rollback to restore service. After stabilization, I analyze the failed deployment and implement corrective actions before attempting another rollout.

---

# 18. Users cannot access an application exposed via Ingress. How would you debug the issue?

I troubleshoot the request path from the user to the application. First, I verify DNS resolution to ensure the hostname points to the correct load balancer or ingress endpoint.

Next, I inspect the Ingress resource configuration, routing rules, TLS settings, and annotations. I verify that the Ingress Controller is running correctly and examine controller logs for routing errors.

I then validate Service configuration, endpoint population, and pod readiness. If Services have no endpoints, traffic cannot reach the application even though the Ingress is configured correctly. By tracing the complete path—DNS → Load Balancer → Ingress → Service → Pod—I can identify the exact failure point.

---

# 19. One worker node becomes NotReady. What actions would you take?

When a node enters the NotReady state, I first determine whether the issue is temporary or persistent. I inspect node conditions, kubelet status, operating system logs, and resource utilization.

Common causes include network failures, kubelet crashes, disk pressure, memory pressure, CPU exhaustion, or cloud infrastructure issues. If workloads are affected, Kubernetes typically reschedules pods onto healthy nodes automatically.

If the node cannot recover quickly, I cordon and drain it to prevent additional workloads from being scheduled. After troubleshooting and remediation, I return the node to service or replace it entirely depending on the severity of the issue.

---

# 20. etcd becomes unavailable in a production cluster. What is your recovery strategy?

etcd is the source of truth for Kubernetes cluster state, making it one of the most critical components. If etcd becomes unavailable, cluster operations may stop because the API Server cannot retrieve or update cluster information.

My first step is determining whether the issue affects a single etcd member or the entire cluster. If quorum still exists, Kubernetes may continue functioning while recovery is performed. I inspect etcd logs, storage health, networking, and resource utilization.

If recovery is not possible through member restoration, I restore etcd from a recent backup snapshot. After recovery, I validate API Server functionality, cluster health, workloads, and data consistency. Regular etcd backups are essential because they significantly reduce recovery time during critical incidents.

---

```markdown
# Infosys DevOps Interview Questions & Answers (4+ Years Experience)

# 21. Pods are getting evicted frequently due to resource pressure. How would you prevent this?

Frequent pod evictions usually indicate that worker nodes are running out of resources such as memory, CPU, ephemeral storage, or disk space. My first step is identifying the specific eviction reason using pod events and node conditions. Common messages include MemoryPressure, DiskPressure, PIDPressure, or EphemeralStoragePressure.

Once the root cause is identified, I review resource requests and limits configured for workloads. Many organizations either under-allocate resources or completely skip defining requests and limits, causing scheduling and resource contention issues. I analyze historical utilization metrics using Prometheus and Grafana to determine actual workload requirements.

To prevent future evictions, I implement proper resource requests and limits, enable Horizontal Pod Autoscaler for workload scaling, and configure Cluster Autoscaler to add worker nodes during resource shortages. I also monitor node utilization proactively and establish alerts before resource pressure reaches critical levels. In production environments, capacity planning and continuous monitoring are essential for avoiding repeated pod evictions.

---

# 22. A service is running but application requests are timing out. How would you troubleshoot networking issues?

When requests are timing out, I troubleshoot the entire network path rather than focusing on a single component. My approach starts from the application and moves outward through each networking layer.

First, I verify that the application is actually listening on the expected port and responding correctly. Next, I check Kubernetes Service configuration, Service selectors, and Endpoint objects to confirm traffic is reaching the correct pods.

I then review Network Policies, Security Groups, NACLs, Ingress configurations, Load Balancer health checks, DNS resolution, and routing rules. If the application communicates with databases or external APIs, I verify outbound connectivity and latency.

I also perform connectivity tests from inside the cluster using tools such as curl, wget, nslookup, dig, traceroute, and netcat. Throughout the investigation, I analyze application logs, ingress controller logs, and network metrics to identify where packets are being dropped or delayed. This layered approach helps isolate the exact networking bottleneck.

---

# 23. Terraform state file is accidentally deleted. How would you recover?

If a Terraform state file is deleted, my first action is stopping all Terraform deployments immediately to prevent additional inconsistencies. The next step depends on the backend configuration.

In production environments, Terraform state files are typically stored in S3 with versioning enabled. I access the S3 bucket and restore the most recent valid version of the state file. After restoration, I verify the integrity of the recovered state and compare it against the actual infrastructure.

I then execute terraform plan to identify any drift between the restored state and existing resources. If certain resources are missing from the state, I use terraform import to bring them back under Terraform management.

Once the environment is stabilized, I investigate why the state file was deleted and implement preventive controls such as access restrictions, backup validation, versioning enforcement, and stronger governance policies.

---

# 24. A Terraform apply fails midway after creating some resources. What would be your next steps?

When Terraform apply fails midway, some resources may already exist while others were never created. My first step is carefully reviewing the error output to understand exactly where the deployment failed.

Next, I inspect the Terraform state file and compare it with the actual infrastructure to determine whether partial changes were successfully recorded. I run terraform plan again to evaluate the current state of the environment.

In many cases, Terraform can continue from where it left off once the underlying issue is resolved. However, if resources were created manually or the state became inconsistent, I may need to use terraform import, state manipulation commands, or resource cleanup procedures.

The goal is never to delete everything and start over. Instead, I safely reconcile the infrastructure and state file while minimizing downtime and avoiding unintended resource destruction.

---

# 25. Multiple engineers are modifying infrastructure simultaneously. How would you handle state locking?

In team environments, state locking is critical to prevent concurrent infrastructure modifications. I use a remote backend such as AWS S3 for state storage and DynamoDB for state locking.

Before Terraform performs any operation, it attempts to acquire a lock in DynamoDB. If one engineer already holds the lock, additional users receive an error indicating the state is locked. This prevents race conditions and state corruption.

I also enforce deployment through CI/CD pipelines rather than allowing direct execution from personal workstations. This creates a controlled deployment process and reduces the risk of conflicting infrastructure changes.

If a lock becomes stuck because a deployment crashed, I first verify that no active deployment is running and then release the lock safely using terraform force-unlock.

---

# 26. A resource was manually changed in AWS but Terraform is showing drift. How would you resolve it?

Infrastructure drift occurs when the actual cloud infrastructure no longer matches the Terraform configuration. The first step is understanding whether the manual change was intentional or accidental.

If the change was approved and should remain, I update the Terraform code to reflect the new configuration. After updating the code, I execute terraform plan and verify that Terraform no longer attempts to revert the change.

If the resource was created manually outside Terraform, I use terraform import to bring it under Terraform management.

If the manual modification was unauthorized, I allow Terraform to restore the infrastructure to its desired state through terraform apply.

The key principle is ensuring Terraform remains the single source of truth for infrastructure management.

---

# 27. How would you safely deploy infrastructure changes to production using Terraform?

Production infrastructure deployments require a controlled and repeatable process. I never apply Terraform changes directly without validation.

The process begins with peer review of infrastructure code through pull requests. After approval, terraform fmt, terraform validate, security scanning, and policy checks are executed automatically.

Next, terraform plan generates an execution plan that is reviewed before approval. Deployments are typically performed through CI/CD pipelines rather than manually from developer machines.

For critical environments, manual approval gates are added before terraform apply. State locking, remote state storage, backups, monitoring, and rollback procedures are verified beforehand.

This approach minimizes deployment risk and provides full auditability for production infrastructure changes.

---

# 28. Kubernetes pods are healthy but users are receiving errors. How would you troubleshoot the complete request flow?

Healthy pods do not necessarily mean users can access the application successfully. I troubleshoot the complete request path from the user to the application.

The flow I investigate is:

User → DNS → Load Balancer → Ingress → Service → Endpoint → Pod → Application

First, I verify DNS resolution and ensure users are reaching the correct endpoint. Next, I check load balancer health, ingress rules, TLS configuration, and ingress controller logs.

I then validate Kubernetes Services and Endpoint objects to ensure traffic is routed correctly. If traffic reaches the pods successfully, I analyze application logs, database connectivity, external API dependencies, and application response codes.

By following the entire request flow systematically, I can identify whether the issue originates from networking, routing, infrastructure, or the application itself.

---

# 29. CI/CD pipelines are successful but application changes are not visible in production. What would you check?

When pipelines complete successfully but changes are not visible, I verify whether the deployment actually reached production.

First, I check the generated container image and confirm a new image version was built and pushed successfully. Next, I verify deployment logs from Jenkins, GitLab CI, ArgoCD, or the deployment platform.

I inspect Kubernetes Deployments, ReplicaSets, and running pods to confirm the new image is actually running. One common issue is using the latest image tag, which can result in stale images being reused.

I also review ArgoCD synchronization status, Helm release history, caching layers, CDN behavior, browser cache, and configuration updates. The investigation focuses on identifying where the deployment process stopped despite reporting success.

---

# 30. A sudden traffic spike causes application degradation. How would you stabilize the platform?

My first objective is stabilizing user experience and preventing complete service failure. I immediately review monitoring dashboards to identify bottlenecks in the application, infrastructure, database, and networking layers.

I verify whether Horizontal Pod Autoscaler and Cluster Autoscaler are functioning correctly. If capacity is insufficient, I scale application replicas and worker nodes. I also evaluate database performance, cache utilization, queue backlogs, and external dependencies.

If the traffic surge is legitimate, I optimize scaling policies and increase available resources. If the spike appears malicious, I activate AWS WAF protections, rate limiting rules, DDoS mitigation controls, and traffic filtering mechanisms.

Throughout the incident, I continuously monitor error rates, latency, throughput, and resource utilization while communicating updates to stakeholders. Once stability is restored, I perform a post-incident review and implement improvements to handle future traffic surges more effectively.
```
