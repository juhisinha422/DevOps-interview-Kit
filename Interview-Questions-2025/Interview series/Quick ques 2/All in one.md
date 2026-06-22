# AWS & Kubernetes/EKS Interview Questions and Answers (4+ Years DevOps Engineer)

## 1. Difference between IAM Role and IAM Policy?

An IAM Policy is a JSON document that defines permissions, specifying what actions are allowed or denied on AWS resources. An IAM Role, on the other hand, is an AWS identity that can be assumed by users, applications, AWS services, or external accounts. Policies define permissions, while roles provide temporary credentials that use those permissions. In production environments, roles are preferred over access keys because they eliminate the need to manage long-term credentials and improve security. For example, an EC2 instance can assume an IAM Role to access S3 instead of storing AWS access keys on the server.

---

## 2. What are the different types of IAM Policies?

AWS supports several types of IAM policies. Identity-Based Policies are attached directly to users, groups, or roles. Resource-Based Policies are attached directly to AWS resources such as S3 buckets, KMS keys, or SNS topics. Permissions Boundaries define the maximum permissions a user or role can have. Service Control Policies (SCPs) are used within AWS Organizations to control permissions across accounts. Session Policies provide temporary restrictions during role assumption. Each policy type serves a different purpose and is often combined to implement secure access controls.

---

## 3. What is Service Control Policy (SCP)?

A Service Control Policy is an AWS Organizations feature used to define permission guardrails across AWS accounts. SCPs do not grant permissions directly; instead, they specify the maximum permissions available to accounts within an Organization. Even if a user has AdministratorAccess, actions blocked by an SCP cannot be performed. For example, an organization may create an SCP that denies the deletion of CloudTrail logs or prevents launching resources outside approved regions. SCPs are commonly used to enforce security and compliance requirements across multiple AWS accounts.

---

## 4. What is Permissions Boundary?

A Permissions Boundary is an advanced IAM feature that defines the maximum permissions a user or role can obtain, regardless of the policies attached to it. It acts as a permission ceiling. Even if a user attaches an Administrator policy, actions outside the boundary remain blocked. Permissions Boundaries are commonly used in large organizations where developers are allowed to create roles but should not gain unrestricted access.

---

## 5. What are the use cases of Permissions Boundary?

Permissions Boundaries are useful when delegating IAM administration to teams without granting excessive privileges. For example, developers may be allowed to create IAM roles for applications, but a boundary ensures they cannot grant access to critical services such as IAM, Organizations, or billing resources. Boundaries are also used in self-service environments where teams provision infrastructure but must remain within predefined security constraints.

---

## 6. Have you worked in a Multi-Account AWS environment?

Yes. In enterprise environments, workloads are typically distributed across multiple AWS accounts to improve security, governance, and cost management. Common account structures include Development, QA, UAT, Production, Shared Services, Security, and Logging accounts. AWS Organizations is used to centrally manage these accounts. Cross-account IAM roles enable secure access between accounts. This approach limits blast radius, improves compliance, and simplifies resource isolation.

---

## 7. What is AWS Organizations?

AWS Organizations is a service that allows centralized management of multiple AWS accounts. It enables account grouping using Organizational Units (OUs), centralized billing, Service Control Policies, and governance controls. Organizations help enterprises manage permissions, security policies, and compliance requirements consistently across all AWS accounts. It is commonly used in multi-account architectures.

---

## 8. What is AWS Control Tower?

AWS Control Tower is a service that automates the setup and governance of a secure multi-account AWS environment. It uses AWS Organizations, SCPs, CloudTrail, Config, and predefined guardrails to establish best practices. Control Tower simplifies account provisioning, compliance enforcement, and centralized governance. Organizations use it to accelerate landing zone creation while maintaining security standards.

---

## 9. How do you create Private DNS in AWS?

Private DNS is typically implemented using Route53 Private Hosted Zones. A Private Hosted Zone is associated with one or more VPCs and resolves DNS queries only within those VPCs. Internal services such as databases, internal APIs, and private load balancers can be accessed using friendly domain names without exposing them to the public internet. This improves security and simplifies service discovery.

---

## 10. What is Route53 Private Hosted Zone?

A Route53 Private Hosted Zone is a DNS zone accessible only within associated VPCs. Unlike public hosted zones, records are not resolvable from the internet. Private Hosted Zones are commonly used for internal applications, databases, microservices, and service discovery. For example, an internal application can access a database using db.internal.company.local instead of a private IP address.

---

## 11. Steps to create a fresh EKS cluster?

To create an EKS cluster, I first create a VPC with public and private subnets across multiple Availability Zones. Then I configure IAM roles for the EKS control plane and worker nodes. Next, I create the EKS cluster using Terraform, eksctl, or AWS Console. Managed Node Groups or Karpenter are configured to provide worker nodes. Networking components such as VPC CNI, CoreDNS, and kube-proxy are deployed automatically. After cluster creation, I configure kubectl access, install Ingress Controllers, Metrics Server, Cluster Autoscaler or Karpenter, monitoring tools such as Prometheus and Grafana, logging solutions such as Fluent Bit, and GitOps tools such as ArgoCD.

---

## 12. Have you created EKS using Terraform?

Yes. In my projects, EKS clusters are fully provisioned using Terraform modules. Terraform manages VPCs, subnets, IAM roles, security groups, EKS clusters, node groups, load balancers, and supporting infrastructure. Using Terraform provides consistency, version control, automation, and repeatability. It also allows environments such as Dev, QA, and Production to be deployed using the same codebase with environment-specific variables.

---

## 13. What issues have you faced while creating an EKS cluster?

Common issues include IAM permission errors, subnet tagging problems, insufficient IP addresses, worker nodes failing to join the cluster, VPC CNI misconfigurations, ECR access failures, security group restrictions, and DNS resolution issues. One common production issue occurs when private subnets lack NAT Gateway access, preventing nodes from pulling container images. Another issue is missing IAM permissions required by worker nodes. Troubleshooting typically involves checking CloudFormation events, node logs, IAM policies, subnet configurations, and cluster events.

---

## 14. What EKS version are you using?

The exact version depends on the organization's upgrade cycle. A good interview response is: "We generally stay within one or two supported versions of the latest EKS release. In my recent project, we were running EKS version 1.30 and regularly performed upgrades following AWS recommendations to maintain security and support."

---

## 15. Difference between Public and Private EKS Cluster?

In a Public EKS Cluster, the Kubernetes API Server endpoint is accessible through the internet, though access can be restricted using CIDR ranges and IAM authentication. In a Private EKS Cluster, the API endpoint is accessible only from within the VPC, making it more secure. Production environments often use private clusters to reduce exposure to the public internet. Administrators connect through VPNs, bastion hosts, or AWS Systems Manager Session Manager.

---

## 16. Difference between Gateway Endpoint and Interface Endpoint?

Gateway Endpoints are available only for S3 and DynamoDB and are configured through route tables. They provide direct access without using NAT Gateways or internet access. Interface Endpoints use AWS PrivateLink and create Elastic Network Interfaces (ENIs) within subnets. Interface Endpoints support many AWS services such as Secrets Manager, CloudWatch, and ECR. Gateway Endpoints are generally free, while Interface Endpoints incur hourly and data processing charges.

---

## 17. Types of VPC Endpoints?

AWS provides three types of VPC Endpoints:

1. Gateway Endpoint (S3 and DynamoDB)
2. Interface Endpoint (AWS PrivateLink)
3. Gateway Load Balancer Endpoint

Each type enables private communication between VPC resources and AWS services without traversing the public internet.

---

## 18. Which subnet is required for VPC Endpoint?

For Interface Endpoints, private subnets are typically used because the goal is to allow private access to AWS services. The endpoint creates ENIs within selected subnets. Gateway Endpoints do not require subnet selection because they operate through route table associations.

---

## 19. How can a private EC2 access S3 without internet?

A private EC2 instance can access S3 using a Gateway VPC Endpoint. The endpoint updates route tables so traffic to S3 remains within the AWS network without traversing the internet. This improves security, reduces NAT Gateway costs, and eliminates dependency on internet connectivity.

## 20. Difference between Multi-AZ and Read Replica?

Multi-AZ and Read Replica are both RDS features, but they serve different purposes. Multi-AZ is primarily designed for high availability and disaster recovery. AWS automatically creates a synchronous standby replica in another Availability Zone and automatically performs failover if the primary database becomes unavailable. Applications continue using the same endpoint, making failover transparent. Read Replicas, on the other hand, are designed for read scaling and reporting workloads. Replication is asynchronous, and replicas have separate endpoints. If the primary database fails, a Read Replica does not automatically take over unless it is manually promoted. In production, Multi-AZ is used to improve availability, while Read Replicas are used to handle heavy read traffic and analytics workloads.

---

## 21. How do you enforce mandatory tags in AWS?

Mandatory tags can be enforced using multiple approaches. At the organizational level, Service Control Policies (SCPs) can deny resource creation if required tags are missing. AWS Config Rules can continuously monitor resources and identify non-compliant resources. Infrastructure provisioning through Terraform can include validation rules that prevent deployment without mandatory tags such as Environment, Owner, Project, and CostCenter. In enterprise environments, a combination of SCPs, AWS Config, and Terraform policies ensures consistent tagging across all accounts and resources.

---

## 22. How do you identify unused IAM permissions?

Unused IAM permissions can be identified using IAM Access Advisor and IAM Last Accessed Information. Access Advisor shows which AWS services a user or role has accessed and when they were last used. IAM Access Analyzer can also help identify overly permissive permissions. In production, periodic reviews are performed to remove unused permissions and implement the principle of least privilege. This reduces security risks and minimizes the attack surface.

---

## 23. What is IAM Access Advisor?

IAM Access Advisor is an AWS feature that provides visibility into service usage for IAM users, groups, and roles. It shows which AWS services have been accessed and when they were last used. This information helps administrators identify unnecessary permissions and optimize IAM policies according to least privilege principles. It is commonly used during security audits and permission reviews.

---

## 24. How do you use CloudTrail for auditing?

CloudTrail records all API activity performed within an AWS account. It captures information such as who performed an action, what action was performed, when it occurred, and from which IP address. During audits and incident investigations, CloudTrail logs are analyzed to identify configuration changes, security incidents, unauthorized access attempts, or accidental deletions. In production environments, CloudTrail logs are stored in versioned S3 buckets with encryption enabled and integrated with CloudWatch for alerting.

---

## 25. What are S3 Storage Classes?

Amazon S3 provides multiple storage classes optimized for different access patterns and cost requirements. S3 Standard is used for frequently accessed data. S3 Intelligent-Tiering automatically moves objects between tiers based on access patterns. S3 Standard-IA and One Zone-IA are used for infrequently accessed data. Glacier Instant Retrieval, Glacier Flexible Retrieval, and Glacier Deep Archive are used for archival storage with varying retrieval times and costs. Organizations use lifecycle policies to automatically move data between storage classes and reduce storage costs.

---

## 26. What is the purpose of S3 Lifecycle Policy?

S3 Lifecycle Policies automate object transitions between storage classes and object expiration. For example, application logs may remain in S3 Standard for 30 days, move to Glacier after 90 days, and be deleted after one year. Lifecycle policies reduce operational effort and significantly optimize storage costs while maintaining compliance requirements.

---

## 27. How do you enforce HTTPS for S3?

HTTPS can be enforced using an S3 Bucket Policy that denies requests where the condition `aws:SecureTransport` is set to false. This ensures that all communication with the bucket occurs over encrypted HTTPS connections. In production environments, enforcing HTTPS prevents sensitive data from being transmitted in plain text and helps meet security compliance requirements.

---

## 28. What routing actions are available in ALB?

Application Load Balancer supports several routing actions. Forward actions route requests to one or more target groups. Redirect actions send users to another URL, protocol, host, or port. Fixed Response actions return predefined HTTP responses directly from the load balancer. ALB also supports host-based routing and path-based routing, allowing multiple applications to share a single load balancer while directing traffic based on request characteristics.

---

## 29. What is Sticky Session?

Sticky Sessions, also called Session Affinity, ensure that requests from a client are consistently routed to the same backend target for a specified duration. ALB achieves this using cookies. Sticky Sessions are useful for stateful applications that store session information locally. However, modern cloud-native applications typically store session data in distributed caches or databases, reducing the need for sticky sessions.

---

## 30. What are Route53 Routing Policies?

Route53 supports several routing policies. Simple Routing directs traffic to a single resource. Weighted Routing distributes traffic based on assigned weights. Latency-Based Routing directs users to the region with the lowest latency. Failover Routing supports disaster recovery by directing traffic to standby resources during failures. Geolocation Routing directs users based on geographic location. Geoproximity Routing routes traffic based on user location and resource location. Multi-Value Answer Routing provides multiple healthy endpoints to improve availability.

---

# Kubernetes / EKS

## 31. Difference between Job and Deployment?

A Deployment is used for long-running applications that should always remain available. Kubernetes continuously ensures the desired number of replicas are running. A Job is used for finite tasks that execute once and terminate successfully after completion. Examples include database migrations, backup operations, batch processing, and report generation. Deployments focus on service availability, while Jobs focus on task completion.

---

## 32. When would you use a Job instead of Deployment?

Jobs are used when a task must run to completion and then exit. Examples include database schema migrations, nightly batch processing, data imports, report generation, and backup tasks. Deployments would be inappropriate for these workloads because Deployments continuously restart terminated containers to maintain availability.

---

## 33. Difference between StatefulSet and Deployment?

Deployments are designed for stateless applications where pods are interchangeable. Pod names can change, and storage is typically ephemeral. StatefulSets are designed for stateful applications requiring stable network identities, predictable pod names, and persistent storage. Databases such as MySQL, PostgreSQL, MongoDB, and Kafka commonly use StatefulSets. StatefulSets ensure ordered deployment, scaling, and termination while preserving storage associations.

---

## 34. Difference between StatefulSet and ApplicationSet?

StatefulSet is a Kubernetes workload resource used for managing stateful applications. ApplicationSet is an ArgoCD resource used for managing and generating multiple ArgoCD applications automatically. StatefulSet focuses on application runtime behavior, while ApplicationSet focuses on GitOps automation and deployment management across clusters and environments.

---

## 35. Difference between ReplicaSet and Deployment?

A ReplicaSet ensures a specified number of pod replicas are running at all times. However, it does not provide deployment management features. A Deployment manages ReplicaSets and adds capabilities such as rolling updates, rollbacks, scaling, version control, and deployment history. In production environments, engineers typically create Deployments instead of directly managing ReplicaSets.

---

## 36. What is kube-proxy?

kube-proxy is a Kubernetes networking component running on every worker node. It maintains network rules that enable communication between Services and Pods. It monitors Kubernetes API changes and updates iptables or IPVS rules accordingly. When a Service receives traffic, kube-proxy forwards requests to healthy backend pods using load-balancing mechanisms.

---

## 37. How do you upgrade an EKS cluster?

An EKS upgrade begins by reviewing AWS compatibility documentation and validating application compatibility. The control plane is upgraded first through AWS APIs or Terraform. Managed node groups are then upgraded one by one while maintaining application availability. Critical add-ons such as CoreDNS, kube-proxy, and VPC CNI are upgraded afterward. Rolling upgrades ensure workloads remain available throughout the process. Monitoring and validation are performed after each phase before proceeding further.

---

## 38. How do you troubleshoot unhealthy Kubernetes nodes?

The first step is checking node status using `kubectl get nodes`. If a node is NotReady, I inspect node conditions using `kubectl describe node`. Common causes include kubelet failures, network issues, disk pressure, memory pressure, certificate problems, or container runtime failures. On EKS, I also examine EC2 instance health, CloudWatch metrics, system logs, and kubelet logs. Once the root cause is identified, the node is repaired or replaced while workloads are rescheduled onto healthy nodes.

---

## 39. What critical production issues have you faced in EKS?

Some common production incidents include pods stuck in Pending due to insufficient resources, CrashLoopBackOff caused by application misconfigurations, worker nodes becoming NotReady, ALB ingress misconfigurations leading to 503 errors, ECR image pull failures, exhausted IP addresses in subnets, memory leaks causing OOMKilled pods, and certificate expiration issues. Troubleshooting usually involves analyzing events, logs, monitoring dashboards, resource utilization, networking configurations, and recent deployment changes before applying corrective actions.

---

## 40. What additional components do you install after EKS provisioning?

After provisioning EKS, several components are typically installed to support production operations. These include Metrics Server for autoscaling, AWS Load Balancer Controller for ingress management, Cluster Autoscaler or Karpenter for node scaling, Prometheus and Grafana for monitoring, Fluent Bit for log aggregation, ArgoCD for GitOps deployments, ExternalDNS for DNS automation, Cert-Manager for certificate management, and security tools such as Falco or Kyverno for policy enforcement.

---

## 41. Explain CrashLoopBackOff.

CrashLoopBackOff occurs when a container repeatedly starts, crashes, and Kubernetes continuously attempts to restart it with increasing delays between retries. Common causes include application bugs, invalid configuration values, missing environment variables, database connectivity issues, incorrect startup commands, insufficient resources, or failed dependencies. Troubleshooting involves checking pod logs, events, container exit codes, resource limits, and application configuration.

---

## 42. Explain ImagePullBackOff.

ImagePullBackOff occurs when Kubernetes cannot pull the container image from the registry. Common causes include incorrect image names, invalid tags, authentication failures, missing imagePullSecrets, network connectivity issues, or registry access restrictions. Troubleshooting includes checking pod events, verifying image paths, testing registry access, validating credentials, and ensuring the image exists in the repository.

---

## 43. Explain Pod Pending.

A Pod remains in Pending state when Kubernetes cannot schedule it onto a node. Common reasons include insufficient CPU or memory resources, node affinity restrictions, taints without matching tolerations, unavailable persistent volumes, exhausted IP addresses, or scheduling constraints. Troubleshooting begins with `kubectl describe pod`, reviewing scheduling events, checking node capacity, and verifying resource requests and constraints.


# Jenkins / CI-CD

## 44. Jenkins Master is slow. How do you troubleshoot?

When Jenkins becomes slow, I first check CPU, memory, disk utilization, and thread dumps on the Jenkins controller. I verify whether there are too many builds running simultaneously, a large build queue, plugin issues, or insufficient resources allocated to Jenkins. I review Jenkins logs, GC logs, and monitor heap usage. I also check whether builds are executing on the controller instead of agents, as build execution should be offloaded to worker nodes. In production, I typically move heavy workloads to Jenkins agents, clean old build artifacts, archive logs, increase JVM memory, optimize plugins, and monitor Jenkins performance using Prometheus and Grafana.

---

## 45. How do you execute stages in parallel?

In Declarative Pipeline, parallel execution is achieved using the parallel block. Parallel stages allow multiple tasks such as unit tests, security scans, code quality checks, and integration tests to run simultaneously, reducing pipeline execution time. In my projects, I often run SonarQube analysis, Trivy scanning, and application testing in parallel to speed up deployments while maintaining quality gates.

---

## 46. How do you integrate GitHub with Jenkins?

GitHub integration is typically achieved using webhooks. Whenever developers push code to GitHub, a webhook sends an HTTP request to Jenkins, automatically triggering the pipeline. Jenkins authenticates with GitHub using Personal Access Tokens, GitHub Apps, or SSH keys. We configure webhook events such as push, pull request creation, or merge events so builds start automatically whenever code changes occur.

---

## 47. How do you manually trigger a Jenkins build?

A Jenkins build can be manually triggered through the Jenkins UI using the Build Now option. It can also be triggered using REST APIs, Jenkins CLI, scheduled cron jobs, or parameterized builds. During production support, manual triggering is commonly used for emergency deployments, rollback operations, or rerunning failed builds after fixing issues.

---

## 48. Difference between Build Now and Build with Parameters?

Build Now simply triggers the pipeline using default values configured in the Jenkins job. Build with Parameters allows users to provide input values before execution. Parameters can include environment names, application versions, Docker image tags, regions, deployment strategies, or rollback options. Parameterized builds are commonly used in production to deploy the same pipeline to multiple environments.

---

## 49. What plugin is used for Multi-Branch Pipeline?

The Multibranch Pipeline Plugin is used to automatically discover and build branches from Git repositories. Jenkins scans repositories and creates separate pipelines for each branch. This is useful for GitFlow, feature branches, pull requests, and environment-specific workflows.

---

## 50. Explain your complete CI/CD pipeline.

In my current project, developers commit code to GitHub. A webhook triggers Jenkins automatically. Jenkins checks out the source code, performs Maven builds, executes unit tests, runs SonarQube analysis, performs Trivy image scanning, and packages the application. A Docker image is then built and pushed to Amazon ECR. Terraform provisions infrastructure if required. ArgoCD continuously monitors Git repositories and deploys updated manifests to EKS clusters using GitOps principles. Prometheus, Grafana, and CloudWatch monitor deployments, while rollback procedures are available if health checks fail.

---

## 51. How do you ensure the correct Docker image tag gets deployed?

I avoid using the latest tag in production because it causes version ambiguity. Instead, each build generates a unique immutable tag using Git commit IDs, Jenkins build numbers, or release versions. The deployment manifest is automatically updated with the newly generated image tag during the pipeline. This ensures traceability, reproducibility, and rollback capability.

---

## 52. How do you dynamically update deployment.yaml from Jenkins?

The deployment manifest can be updated using sed commands, Helm values, Kustomize overlays, or GitOps workflows. In our project, Jenkins updates the image tag in deployment manifests and commits the change to a Git repository monitored by ArgoCD. ArgoCD then synchronizes the updated manifest to Kubernetes automatically.

---

## 53. What AWS services do you use in CI/CD?

In our CI/CD pipeline, we use GitHub for source control, Jenkins for automation, Amazon ECR for Docker image storage, EKS for Kubernetes workloads, S3 for artifact storage and Terraform backend, IAM for access control, CloudWatch for monitoring, Route53 for DNS management, ACM for SSL certificates, Secrets Manager for secrets management, and SNS for notifications.

---

# SonarQube & Security

## 54. What metrics do you monitor in SonarQube?

I monitor code coverage, bugs, vulnerabilities, code smells, duplication percentage, maintainability rating, reliability rating, security rating, technical debt, and quality gate status. These metrics help ensure code quality and prevent risky code from reaching production.

---

## 55. Difference between Quality Gate and Quality Profile?

A Quality Profile defines the coding rules that SonarQube uses during analysis. It specifies which rules are enabled and how violations are detected. A Quality Gate defines pass/fail criteria after analysis. For example, a quality gate may require at least 80% test coverage and zero critical vulnerabilities before allowing deployment.

---

## 56. How do you design a Quality Gate?

A production-quality gate usually includes conditions such as no blocker vulnerabilities, no critical bugs, code coverage above 80%, duplication below 5%, and maintainability ratings above defined thresholds. The exact thresholds depend on organizational standards and compliance requirements.

---

## 57. How do you publish code coverage to SonarQube?

Code coverage is generated by testing frameworks such as JaCoCo for Java applications. During the build process, coverage reports are created and passed to SonarQube using scanner configurations. SonarQube then incorporates coverage metrics into quality analysis and quality gate evaluation.

---

## 58. What happens when Quality Gate fails?

When a Quality Gate fails, the pipeline stops automatically, preventing deployment to higher environments. Developers review the issues, fix code quality problems, rerun the pipeline, and proceed only after the gate passes. This ensures defective code never reaches production.

---

## 59. Have you worked with image scanning tools?

Yes. I have worked with Trivy, Aqua Security, Amazon ECR scanning, and Docker image vulnerability scanners. These tools identify vulnerabilities in operating system packages, libraries, and application dependencies before deployment.

---

## 60. Have you used Trivy?

Yes. Trivy is integrated into our Jenkins pipeline immediately after Docker image creation. It scans container images for vulnerabilities and generates reports. The pipeline fails automatically if vulnerabilities exceed predefined severity thresholds such as Critical or High.

---

## 61. What vulnerabilities have you found in Docker images?

Common vulnerabilities include outdated OpenSSL libraries, vulnerable Java packages, insecure Linux packages, outdated Python dependencies, exposed credentials, unnecessary root privileges, and unsupported base image versions. Most findings originate from outdated operating system packages.

---

## 62. How do you fix Docker image vulnerabilities?

I update vulnerable packages, use newer base image versions, remove unnecessary software packages, implement multi-stage builds, run containers as non-root users, minimize image layers, and regularly scan images during CI/CD. Security patches are incorporated into image rebuilds before deployment.

---

## 63. How do you select a secure Docker base image?

I choose official vendor-supported images, prefer minimal distributions such as Alpine or Distroless, verify image signatures, avoid unsupported images, review vulnerability scan reports, and regularly update base image versions. Smaller images generally reduce attack surfaces and improve security.

---

# Terraform

## 64. How does Terraform work?

Terraform follows a declarative Infrastructure as Code model. Engineers define desired infrastructure in configuration files. Terraform compares the desired state against the current state using the state file, generates an execution plan, and applies changes through cloud provider APIs. This process ensures infrastructure remains consistent, repeatable, and version controlled.

---

## 65. Difference between Local State and Remote State?

Local state stores terraform.tfstate on the local machine. This approach is suitable only for learning or small projects. Remote state stores state files in centralized locations such as S3, Azure Storage, or Terraform Cloud. Remote state supports collaboration, state locking, versioning, backup, and disaster recovery.

---

## 66. Why use S3 + DynamoDB backend?

S3 stores the Terraform state file centrally and provides durability, encryption, versioning, and backup capabilities. DynamoDB provides state locking to prevent multiple engineers from modifying infrastructure simultaneously. This combination is considered a production best practice for AWS environments.

---

## 67. What is Terraform State Locking?

State locking prevents concurrent modifications to infrastructure. Before running terraform apply, Terraform creates a lock record in DynamoDB. If another user attempts deployment simultaneously, Terraform blocks the operation until the lock is released. This prevents state corruption and infrastructure inconsistencies.

---

## 68. Have you faced state lock conflicts?

Yes. State lock conflicts typically occur when another engineer is deploying or when a pipeline crashes unexpectedly, leaving a stale lock. Terraform displays a lock acquisition error and prevents further changes until the issue is resolved.

---

## 69. How did you resolve Terraform lock issues?

I first verify whether another deployment is actively running. If no deployment is in progress, I identify the lock ID and safely remove it using terraform force-unlock. Before unlocking, I always confirm with team members to avoid corrupting the state.

---

## 70. What modules have you used?

I have created and used reusable modules for VPCs, subnets, route tables, security groups, EKS clusters, node groups, IAM roles, ALBs, Route53 records, S3 buckets, RDS databases, CloudWatch alarms, and ECR repositories. Modules improve consistency, reduce duplication, and simplify maintenance.

## 71. Difference between count and for_each?

Both count and for_each are used to create multiple instances of resources, but they work differently. Count uses a numerical index and is ideal when resources are nearly identical. For example, creating three EC2 instances can be done using count = 3. However, if one instance is removed from the middle of the list, Terraform may recreate other resources because the indexes shift. for_each uses unique keys and is preferred when resources have unique names or configurations. Since resources are tracked by keys rather than indexes, modifications are safer and do not cause unnecessary recreation. In production environments, I generally prefer for_each because it provides better stability and maintainability.

---

## 72. What is for_each?

for_each is a Terraform meta-argument that creates multiple resource instances from a map or set of values. Each resource gets a unique key, allowing Terraform to track resources individually. This makes updates safer because changes to one resource do not affect others. It is commonly used when provisioning resources such as IAM users, security groups, Route53 records, or multiple EC2 instances with different configurations.

---

## 73. What arguments are passed to for_each?

for_each accepts a map or a set of strings. Terraform automatically provides two objects: each.key and each.value. each.key represents the unique identifier, while each.value contains the associated value. These variables allow dynamic resource creation based on user-defined input collections.

---

## 74. What is Terraform branching strategy?

In enterprise projects, Terraform code follows Git branching strategies such as GitFlow or Trunk-Based Development. Typically, developers create feature branches for changes, raise pull requests for peer review, merge into develop branches for testing, and eventually promote changes to main or production branches. CI/CD pipelines validate Terraform plans before applying changes. This process ensures code quality, approval workflows, and safe infrastructure deployments.

---

## 75. How do you isolate Dev and Prod environments?

Environment isolation can be achieved through separate AWS accounts, separate Terraform state files, different backend configurations, dedicated VPCs, and environment-specific variables. In my projects, Production and Non-Production environments are usually deployed into separate AWS accounts with separate state files. This minimizes blast radius and improves security while preventing accidental modifications to production resources.

---

## 76. What are Terraform Workspaces?

Terraform Workspaces allow multiple state files to be managed from the same Terraform configuration. Each workspace maintains its own state, enabling environments such as Dev, QA, and Test to use the same codebase. However, in large organizations, separate state files and separate accounts are generally preferred for production workloads because workspaces can become difficult to manage at scale.

---

# Linux & Monitoring

## 77. Difference between $ and # prompt?

In Linux, the "$" prompt represents a regular non-root user, while the "#" prompt represents the root user. Root users have unrestricted administrative privileges and can modify critical system files. Most production environments follow the principle of least privilege, so engineers operate as non-root users and elevate privileges only when necessary using sudo.

---

## 78. What are SELinux modes?

SELinux operates in three modes. Enforcing mode actively applies security policies and blocks unauthorized actions. Permissive mode logs violations but does not block them, making it useful for troubleshooting. Disabled mode completely disables SELinux. In production environments, Enforcing mode is recommended because it provides an additional layer of security beyond standard Linux permissions.

---

## 79. Which Linux version are you using?

In my projects, I have primarily worked with RHEL 8, Amazon Linux 2, Ubuntu 20.04/22.04, and CentOS-based systems depending on workload requirements. Production environments generally standardize on enterprise-supported distributions such as RHEL or Amazon Linux because of security updates and vendor support.

---

## 80. How do you manage application logs?

Application logs are centralized using logging solutions such as Fluent Bit, Fluentd, Logstash, Elasticsearch, OpenSearch, CloudWatch Logs, and Grafana Loki. Logs are collected from servers and containers, enriched with metadata, and forwarded to centralized storage. This enables troubleshooting, monitoring, alerting, and compliance auditing across environments.

---

## 81. What is logrotate?

logrotate is a Linux utility used to manage log files by rotating, compressing, archiving, and deleting old logs. It prevents log files from consuming excessive disk space and ensures long-running applications do not fill system partitions. Most Linux distributions include logrotate by default.

---

## 82. What is the default logrotate configuration path?

The primary configuration file is:

```bash
/etc/logrotate.conf
```

Additional application-specific configurations are typically stored in:

```bash
/etc/logrotate.d/
```

These files define rotation schedules, retention periods, compression settings, and cleanup policies.

---

## 83. How do you manually rotate logs?

Logs can be manually rotated using:

```bash
logrotate -f /etc/logrotate.conf
```

The -f flag forces immediate rotation regardless of schedule. This is useful during troubleshooting when large log files are consuming disk space.

---

## 84. Which monitoring tools have you used?

I have worked extensively with Prometheus, Grafana, CloudWatch, Alertmanager, Zabbix, ELK Stack, Loki, and Datadog. Prometheus and Grafana are primarily used for Kubernetes monitoring, CloudWatch for AWS infrastructure, and centralized logging platforms for troubleshooting and observability.

---

## 85. What cloud templates are available in Zabbix?

Zabbix provides templates for AWS services, Azure resources, VMware, Linux servers, Windows servers, Kubernetes clusters, databases, web servers, and network devices. Templates simplify monitoring setup by providing predefined metrics, dashboards, triggers, and alerts.

---

## 86. Difference between Active and Passive Agent?

A Passive Agent waits for the Zabbix Server to request metrics. The server initiates communication and collects data periodically. An Active Agent pushes metrics to the server proactively based on configured intervals. Active Agents reduce server load and scale better in large environments because they initiate communication themselves.

---

# Production Troubleshooting

## 87. Tell me about a critical issue you resolved.

One critical production issue involved a payment application returning 503 errors immediately after deployment. Investigation revealed an incorrect rolling update configuration with maxUnavailable set to 100%, causing all healthy pods to terminate simultaneously. New pods required over a minute to become ready, leaving the service without healthy endpoints. I immediately rolled back the deployment, restored service, reviewed readiness probes, updated deployment strategies, configured PodDisruptionBudgets, and implemented deployment validation checks. Service was restored within minutes and future deployments became significantly safer.

---

## 88. Production application is down. What will you do?

My first priority is assessing business impact and communicating with stakeholders. I immediately check monitoring dashboards, alerts, recent deployments, infrastructure changes, and application logs. I identify whether the issue originates from infrastructure, networking, database, application code, or external dependencies. If a recent deployment caused the outage, I initiate rollback procedures. After service restoration, I perform Root Cause Analysis, document findings, and implement preventive measures to avoid recurrence.

---

## 89. How do you troubleshoot 404 errors?

A 404 error indicates that the requested resource cannot be found. I verify DNS resolution, ALB or Ingress routing rules, application routes, reverse proxy configurations, and backend service mappings. In Kubernetes, I check Ingress resources, service selectors, endpoint availability, and application routing configurations. Access logs often reveal whether requests are reaching the application correctly.

---

## 90. How do you troubleshoot 502 errors?

A 502 Bad Gateway error typically indicates communication failure between a load balancer, reverse proxy, or ingress controller and backend services. I check ALB target group health, NGINX ingress logs, pod health, service endpoints, application logs, network connectivity, and resource utilization. Common causes include backend crashes, incorrect service ports, readiness probe failures, or application startup issues.

---

## 91. How do you troubleshoot high CPU utilization?

I start by identifying which processes are consuming CPU using tools such as top, htop, ps, and CloudWatch metrics. In Kubernetes, I review pod-level CPU metrics using Prometheus and Grafana. I investigate traffic spikes, inefficient queries, application bottlenecks, infinite loops, or insufficient resource allocations. Depending on findings, I scale resources, optimize code, tune configurations, or redistribute workloads.

---

## 92. How do you troubleshoot memory issues?

I examine memory usage patterns, container metrics, JVM heap usage, memory leaks, cache utilization, and OOMKilled events. In Kubernetes, I review pod memory requests, limits, and historical metrics in Grafana. If a memory leak exists, I collect heap dumps, profile the application, and involve developers to address the root cause. Short-term mitigation may involve scaling resources while permanent fixes are implemented.

---

## 93. How do you troubleshoot node failures in EKS?

I begin by checking node status using:

```bash
kubectl get nodes
```

If a node is NotReady, I inspect node conditions, kubelet logs, EC2 health checks, CloudWatch metrics, networking configuration, disk space, and resource pressure events. I verify that worker nodes can communicate with the EKS control plane and that IAM permissions remain intact. If necessary, I cordon and drain the affected node before replacing it with a healthy node.

---

## 94. How do you troubleshoot failed deployments?

I first identify whether the failure occurred during build, image creation, image pull, Kubernetes scheduling, application startup, or readiness validation. I review Jenkins logs, deployment events, pod logs, ingress configuration, resource limits, health probes, and monitoring dashboards. If the deployment introduced customer impact, I immediately roll back to the previous stable version. After stabilization, I perform Root Cause Analysis and implement safeguards such as automated validation, canary deployments, and stronger monitoring to prevent similar incidents.

