# Capgemini – AWS DevOps Engineer 
### Experience: 4 Years | Technical Round 1

# 1. AWS

### 1. Explain how you would design a highly available EC2 architecture across multiple AZs for a web application.

I would design the architecture using an Application Load Balancer deployed across multiple Availability Zones, with EC2 instances running in private subnets across at least two AZs. I would configure an Auto Scaling Group to maintain the required number of instances and automatically replace unhealthy instances. The ALB would distribute incoming traffic between healthy instances using health checks. For the database layer, I would use Amazon RDS Multi-AZ for automatic failover. I would also configure CloudWatch monitoring, security groups, IAM roles, and regular backups. This architecture helps eliminate single points of failure and ensures application availability even if one Availability Zone goes down.

### 2. How would you migrate a running EC2 instance to another region with minimal downtime?

First, I would identify the instance configuration, dependencies, attached EBS volumes, security groups, IAM roles, and networking requirements. I would create an AMI of the existing EC2 instance and copy that AMI to the target AWS region. Then I would launch a new instance using the copied AMI and configure the required VPC, subnet, security groups, and IAM permissions. I would validate the application and test connectivity in the target region. To minimize downtime, I would synchronize application data and database changes before switching traffic. Finally, I would update Route 53 or the load balancer to redirect traffic to the new region. I would keep the original instance available until the migration is completely validated.

### 3. If your EC2 instance is not reachable via SSH, what troubleshooting steps would you perform?

First, I would verify whether the EC2 instance is running and check its system and instance status checks. Then I would verify that the security group allows inbound SSH traffic on port 22 from my IP address. I would also check the Network ACL rules, route table, Internet Gateway, and whether the instance has a public IP if it is accessed directly over the internet. Next, I would verify that the correct private key and username are being used. If the instance is in a private subnet, I would connect through a bastion host or AWS Systems Manager Session Manager. If the issue is related to the operating system, I would use EC2 Serial Console or recovery procedures where supported.

### 4. Your web application experiences unpredictable traffic spikes. How would you configure auto scaling policies to handle this while optimizing costs?

I would use an EC2 Auto Scaling Group behind an Application Load Balancer. I would configure target tracking scaling based on metrics such as average CPU utilization or ALB requests per target. For unpredictable traffic, I would also consider step scaling and predictive scaling if historical traffic patterns are available. I would configure appropriate minimum, maximum, and desired capacity values to prevent unnecessary resource consumption. I would use CloudWatch alarms to monitor scaling activities and configure scale-in cooldowns or instance warm-up settings to avoid frequent scaling fluctuations. For cost optimization, I would consider a combination of On-Demand and Spot Instances where the workload permits.

### 5. Explain a scenario where scheduled scaling is more appropriate than dynamic scaling.

Scheduled scaling is more appropriate when an application has predictable traffic patterns. For example, if an e-commerce application regularly experiences high traffic during business hours, I can configure the Auto Scaling Group to increase capacity before business hours and reduce it after working hours. Dynamic scaling depends on real-time metrics such as CPU utilization, whereas scheduled scaling proactively adjusts capacity based on a known schedule. This helps ensure resources are available before traffic increases and avoids unnecessary scaling delays.

### 6. Your S3 bucket storing critical backups becomes unavailable. How would you recover?

First, I would investigate the actual reason for the unavailability, because S3 is designed for high availability and durability. I would check AWS Service Health, IAM policies, bucket policies, KMS permissions, and network access restrictions. If the issue is accidental deletion or modification, I would use S3 Versioning to restore previous object versions. I would also check whether Cross-Region Replication is configured and whether backup copies are available in another region. For critical backup data, I would recommend enabling versioning, replication, Object Lock where required, and lifecycle policies. I would also regularly test backup restoration to ensure recovery procedures work correctly.

### 7. You want to audit all IAM users and roles for compliance. How would you do this?

I would start by reviewing IAM users, groups, roles, attached policies, and permissions using the IAM console or AWS CLI. I would use IAM Access Analyzer to identify unintended external access and overly permissive resource policies. I would also review IAM credential reports to identify inactive users, unused access keys, and password-related compliance issues. CloudTrail would help me audit API activities and identify who performed specific actions. Additionally, I would use AWS Config rules and Security Hub to continuously monitor security configurations. Based on the audit, I would remove unused credentials, enforce least-privilege access, enable MFA, and document compliance findings.

### 8. Your organization needs to grant cross-account access to a partner. How would you implement it?

I would implement cross-account access using IAM roles rather than sharing IAM user credentials. In our AWS account, I would create an IAM role with the required permissions and configure its trust policy to allow the partner's AWS account to assume that role. The partner would use AWS STS AssumeRole to obtain temporary security credentials. I would restrict access using specific permissions, external IDs where appropriate, and conditions in the trust policy. I would also enable CloudTrail logging to monitor role assumptions and resource access. This approach provides secure, temporary, and auditable access without sharing long-term credentials.

### 9. How would you secure S3 data in transit and at rest for compliance?

For data at rest, I would enable server-side encryption using SSE-S3 or SSE-KMS, depending on the organization's compliance requirements. For sensitive data, I would prefer SSE-KMS because it provides additional control over encryption keys and access policies. For data in transit, I would enforce HTTPS using TLS and configure bucket policies to deny requests that do not use secure transport through the aws:SecureTransport condition. I would also enable S3 Block Public Access, configure least-privilege IAM permissions, and enable CloudTrail data events where required. Additionally, I would use versioning, access logging, and lifecycle policies to support data protection and auditing.

### 10. How would you deploy Lambda functions across multiple environments securely?

I would maintain separate development, testing, staging, and production environments using separate configurations and preferably separate AWS accounts. I would manage infrastructure using Terraform or CloudFormation and deploy Lambda functions through a CI/CD pipeline such as Jenkins or GitHub Actions. I would use environment-specific variables, AWS Secrets Manager or Systems Manager Parameter Store for sensitive configurations, and IAM execution roles with least-privilege permissions. I would also use Lambda versions and aliases to manage releases and support controlled deployments. Before promoting changes to production, I would run automated tests and security checks. CloudWatch would be used for monitoring logs, errors, and execution metrics.

---

# 2. Docker

### 1. How do you handle multi-cloud Docker deployments with compliance restrictions?

I would maintain consistent Docker images across cloud environments using a centralized container registry and automated CI/CD pipelines. I would build images using Dockerfiles and scan them for vulnerabilities using tools such as Trivy. I would also use private container registries, image signing, and controlled access to ensure only approved images are deployed. For compliance, I would follow cloud-specific security requirements, encryption standards, network restrictions, and access control policies. I would keep application configurations external to the image so the same image can be deployed across AWS, Azure, or other cloud environments without rebuilding it unnecessarily.

### 2. You need live-patching of Docker host kernel without downtime. How do you achieve it?

Docker containers share the host operating system kernel, so kernel patching must be handled at the host level. I would avoid patching a production Docker host directly while it is serving critical workloads. Instead, I would use multiple Docker hosts behind a load balancer or an orchestrator such as Kubernetes. I would first drain workloads from one host, migrate or reschedule containers to healthy hosts, apply the required kernel patch, and reboot if necessary. After validating the patched host, I would gradually return workloads to it and repeat the process for other hosts. Where supported, live-patching technologies can reduce the need for reboots, but I would still follow the vendor's support and validation requirements.

### 3. How do you troubleshoot container DNS resolution failures?

First, I would check whether the issue affects one container or multiple containers. I would inspect the container's network configuration and DNS settings using commands such as `docker inspect`. Then I would enter the container and test DNS resolution using tools such as `nslookup`, `dig`, or `getent hosts`. I would verify the Docker network, DNS server configuration, and whether the container can reach the configured DNS server. I would also check the host's DNS configuration, firewall rules, and network connectivity. If the issue is related to Docker's embedded DNS, I would investigate network configuration and restart Docker only after understanding the impact on running containers.

### 4. How do you enforce policy-as-code for Docker security?

I would implement security policies as part of the CI/CD pipeline so that insecure Docker images or configurations are rejected before deployment. I would use tools such as Trivy for vulnerability scanning and Checkov or similar policy tools for infrastructure configuration checks. I would enforce rules such as using approved base images, avoiding the root user, not exposing unnecessary ports, and preventing hardcoded secrets. I would also use Docker Bench for Security to assess host and Docker daemon configurations. For stronger enforcement, I would integrate image signing and admission policies in Kubernetes so that only approved and verified images can run in production.

---

# 3. Kubernetes (K8s)

### 1. Your Ingress controller crashes repeatedly under heavy load. How do you stabilize it?

First, I would investigate the Ingress controller pods using `kubectl describe pod` and `kubectl logs` to identify whether the issue is caused by resource exhaustion, configuration problems, or excessive incoming traffic. I would check CPU and memory utilization and increase resource requests and limits if necessary. I would configure multiple Ingress controller replicas and use pod anti-affinity to distribute them across different nodes. I would also configure a Horizontal Pod Autoscaler where supported and ensure the underlying nodes have sufficient capacity. Additionally, I would review connection limits, timeouts, health checks, and load balancer configurations. Finally, I would monitor the controller using Prometheus and Grafana to identify recurring bottlenecks.

### 2. An entire Kubernetes region goes down. How do you failover workloads?

I would design the application with disaster recovery across multiple AWS regions rather than depending on a single EKS cluster. I would maintain infrastructure configurations using Terraform and deploy application manifests through Helm or GitOps tools such as ArgoCD. Container images and required configurations would be available in the secondary region. I would also ensure that database replication or backup restoration is configured based on the application's recovery objectives. If the primary region becomes unavailable, I would activate the secondary EKS environment and redirect traffic using Route 53 failover routing or an appropriate global traffic management solution. After verifying application health and data consistency, I would gradually shift traffic to the recovery region.

### 3. All pods in one namespace suddenly fail readiness checks. What’s your step to troubleshoot?

First, I would check the namespace and list the affected pods using `kubectl get pods -n <namespace>`. Then I would inspect the pod events using `kubectl describe pod` and review container logs using `kubectl logs`. Since all pods are failing readiness checks in the same namespace, I would investigate common dependencies such as ConfigMaps, Secrets, Services, DNS, network policies, and external database connectivity. I would also verify whether the readiness probe configuration, endpoint, port, or timeout has changed recently. I would check node health and resource utilization to identify infrastructure-related issues. Once the root cause is identified, I would fix the configuration or dependency and verify that the pods become Ready.

### 4. Your cluster is hit by a massive traffic surge, and HPA cannot scale pods fast enough. How do you handle it?

I would first check whether the HPA is correctly configured and whether the metrics server is reporting accurate resource utilization. I would increase the minimum replica count and review the HPA scaling behavior to allow faster scale-out. I would also configure Cluster Autoscaler or Karpenter so that additional worker nodes can be provisioned when existing nodes do not have enough capacity. For workloads with predictable demand, I would consider maintaining a baseline of pre-scaled replicas. I would also use an AWS Application Load Balancer, caching through CloudFront, and application-level rate limiting to reduce unnecessary backend traffic. The goal would be to scale both application pods and underlying infrastructure before the cluster becomes overloaded.

### 5. A node hosting critical workloads crashes permanently. How do you ensure workloads recover automatically?

I would make sure critical workloads are managed by Kubernetes controllers such as Deployments or StatefulSets rather than being created as standalone pods. When a node becomes unhealthy, Kubernetes detects the node failure and eventually reschedules eligible pods onto healthy nodes, provided sufficient cluster capacity is available. I would configure multiple replicas, pod anti-affinity, and Pod Disruption Budgets to improve workload availability during disruptions. I would also use Cluster Autoscaler or Karpenter to provision additional worker nodes when required. For persistent workloads, I would verify that storage volumes can be reattached according to their access mode and availability-zone constraints. Finally, I would monitor node health and pod recovery through CloudWatch, Prometheus, and Grafana.

---

# 4. Terraform

### 1. Your Terraform apply succeeded, but some resources are not behaving as expected. How do you debug?

First, I would verify the Terraform output and inspect the actual infrastructure in the AWS console or through AWS CLI. A successful Terraform apply confirms that the provider accepted the operations, but it does not guarantee that the application or infrastructure is functioning correctly. I would run `terraform plan` to identify any configuration drift and review the Terraform state using `terraform state list` and `terraform state show`. I would also inspect resource dependencies, security groups, IAM permissions, networking, and resource-specific configurations. If necessary, I would enable Terraform debug logging using `TF_LOG` for more detailed information. After identifying the root cause, I would update the configuration and apply the required changes carefully.

### 2. How do you run Terraform safely in CI/CD pipelines?

I would integrate Terraform into the CI/CD pipeline using separate stages for formatting, validation, security scanning, planning, approval, and deployment. Initially, I would run `terraform fmt -check` and `terraform validate` to verify the configuration. Then I would use tools such as Checkov or TFLint for security and code-quality checks. I would execute `terraform plan` and store the generated plan artifact for review. For production changes, I would introduce a manual approval step before running `terraform apply`. I would store the Terraform state remotely in an encrypted S3 bucket with versioning and use native S3 state locking where supported by the Terraform version. I would also use IAM roles and short-lived credentials instead of hardcoded AWS access keys.

### 3. Your Terraform state file is getting too large. How do you manage it?

I would first review the existing infrastructure structure and identify whether unrelated environments or services are managed through a single state file. I would split the infrastructure into smaller, logically separated Terraform configurations, such as networking, compute, databases, and application infrastructure. I would maintain separate state files for different environments and use reusable modules to avoid code duplication. For shared infrastructure, I would expose required values through Terraform outputs and remote state references where appropriate. I would also avoid unnecessarily managing resources that do not need Terraform state tracking. Before restructuring the state, I would create backups and carefully migrate resources using supported Terraform state operations.

### 4. A team needs to provision infra in 10 AWS regions simultaneously. How do you structure it?

I would create reusable Terraform modules for common infrastructure components such as VPCs, subnets, EC2 instances, security groups, and IAM resources. I would maintain region-specific configurations through variables and provider configurations. For each region, I would use a separate Terraform root configuration or a clearly isolated state so that deployments can be managed independently. I would automate execution through a CI/CD pipeline and use controlled parallel execution to provision resources across multiple regions. I would also ensure that region-specific AMIs, availability zones, service availability, and resource limits are handled correctly. This structure provides consistency, scalability, and easier troubleshooting.

### 5. You want faster Terraform runs in large projects. What optimizations would you apply?

I would start by dividing large Terraform configurations into smaller modules and independent state files based on infrastructure boundaries. This reduces the number of resources Terraform needs to refresh and evaluate during each execution. I would avoid unnecessary dependencies between resources so Terraform can execute independent operations in parallel. I would use targeted operations only for specific troubleshooting or carefully controlled cases, not as a replacement for normal full plans. I would also review provider configurations, mo
