# 🚀 DevOps Scenario-Based Interview Answers (4+ Years Experience)

Experience Level: 4+ Years DevOps / Cloud Engineer  
Approach: Architecture Thinking → High Availability → Automation → Security → Cost Optimization → Disaster Recovery  

---

# 🔹 AWS

## Explain how you would design a highly available EC2 architecture across multiple AZs for a web application.

To design a highly available architecture, I would create a VPC spanning at least two Availability Zones with public and private subnets in each AZ. An Application Load Balancer would be deployed in public subnets to distribute traffic across EC2 instances running in private subnets across multiple AZs. The EC2 instances would be managed by an Auto Scaling Group configured to maintain minimum capacity across AZs for redundancy. For the database layer, I would use RDS with Multi-AZ enabled to ensure automatic failover. CloudWatch alarms would monitor instance health, and health checks on the load balancer would automatically replace unhealthy instances. This removes single points of failure and ensures high availability.

---

## How would you migrate a running EC2 instance to another region with minimal downtime?

To migrate a running EC2 instance with minimal downtime, I would create an AMI of the instance and copy it to the target region. I would launch a new EC2 instance from that AMI in the new region, configure networking, and validate application functionality. If the application uses a database, I would enable replication to sync data before cutover. Finally, I would update Route 53 DNS with a low TTL to redirect traffic to the new region, ensuring minimal downtime during transition.

---

## If your EC2 instance is not reachable via SSH, what troubleshooting steps would you perform?

If SSH is not working, I would first verify that the Security Group allows inbound traffic on port 22 from my IP. Then I would check Network ACL rules, route tables, and ensure the instance has a public IP or NAT access if in a private subnet. I would confirm the instance state and review system status checks. If still inaccessible, I would use Session Manager or EC2 Instance Connect. I would also check system logs for disk full issues or SSH daemon failures.

---

## Your web application experiences unpredictable traffic spikes. How would you configure auto scaling policies to handle this while optimizing costs?

For unpredictable traffic, I would configure dynamic scaling policies using target tracking based on CPU utilization or ALB request count. This allows the Auto Scaling Group to automatically adjust capacity to maintain performance. To optimize costs, I would combine On-Demand instances with Spot instances where applicable and configure scale-in cooldowns to avoid rapid fluctuations. Minimum capacity would handle baseline traffic while scaling absorbs peak loads.

---

## Explain a scenario where scheduled scaling is more appropriate than dynamic scaling.

Scheduled scaling is appropriate when traffic patterns are predictable, such as an e-commerce site experiencing daily evening spikes or increased traffic during business hours. Instead of relying purely on dynamic metrics, scheduled scaling ensures resources are provisioned in advance to handle known load increases, reducing latency risks and avoiding reactive scaling delays.

---

## Your S3 bucket storing critical backups becomes unavailable. How would you recover?

If an S3 bucket becomes unavailable, I would first check AWS Service Health Dashboard for regional issues. If it is a regional outage, I would rely on cross-region replication to restore backups from a secondary region. I would also ensure versioning is enabled to recover deleted or corrupted objects. For compliance-critical data, I would implement lifecycle policies and backup copies across regions to ensure disaster recovery capability.

---

## You want to audit all IAM users and roles for compliance. How would you do this?

To audit IAM users and roles, I would use IAM Access Analyzer to detect unused or overly permissive roles. I would review policies attached to users and roles, check for inactive credentials, and ensure MFA is enabled. AWS Config rules can be used to evaluate compliance continuously. CloudTrail logs help audit activity history, and I would generate reports to ensure least privilege principles are followed.

---

## Your organization needs to grant cross-account access to a partner. How would you implement it?

To grant cross-account access, I would create an IAM role in our account with a trust policy allowing the partner account to assume it. The role would have only required permissions. The partner would assume the role using STS AssumeRole API. This ensures secure temporary access without sharing long-term credentials.

---

## How would you secure S3 data in transit and at rest for compliance?

To secure S3 data at rest, I would enable server-side encryption using SSE-S3 or SSE-KMS with customer-managed keys. For data in transit, I would enforce HTTPS by applying bucket policies that deny non-SSL requests. I would also enable versioning, access logging, and restrict public access using Block Public Access settings to ensure compliance requirements are met.

---

## How would you connect an on-premises network securely to your VPC?

To connect on-premises securely to AWS VPC, I would use AWS Site-to-Site VPN for encrypted connectivity over the internet or AWS Direct Connect for dedicated private connectivity. For high availability, I would configure redundant VPN tunnels or Direct Connect links. Route tables and security groups would be configured properly to control traffic flow securely.

---

## How would you deploy Lambda functions across multiple environments securely?

To deploy Lambda securely across environments, I would use infrastructure-as-code tools like Terraform or CloudFormation to maintain separate configurations for dev, staging, and production. I would use IAM roles with least privilege permissions and store secrets in AWS Secrets Manager. Environment-specific variables would be managed securely, and CI/CD pipelines would control deployments with approval gates for production.

---

# 🔹 Docker

## How do you handle multi-cloud Docker deployments with compliance restrictions?

For multi-cloud deployments, I would standardize container images using minimal base images and scan them for vulnerabilities using tools like Trivy. Images would be stored in compliant container registries per region. I would ensure compliance policies are enforced via CI/CD pipelines and use infrastructure automation to maintain consistency across cloud providers.

---

## You need live-patching of Docker host kernel without downtime. How do you achieve it?

To achieve live patching, I would use tools like Kernel Live Patching solutions provided by the OS vendor. Alternatively, I would use rolling node replacement strategy in cluster environments, draining nodes one by one while patching them. This ensures no application downtime while maintaining security updates.

---

## How do you troubleshoot container DNS resolution failures?

If DNS fails inside containers, I would first check if the container can resolve external domains using tools like nslookup. Then I would verify Docker daemon DNS configuration and ensure correct DNS servers are configured. In Kubernetes environments, I would check CoreDNS pods and network policies to ensure DNS traffic is allowed.

---

## How do you enforce policy-as-code for Docker security?

To enforce Docker security, I would use tools like Open Policy Agent or admission controllers to enforce image scanning, prevent privileged containers, and restrict root usage. Policies would be integrated into CI/CD pipelines to block insecure images before deployment.

---

# 🔹 Kubernetes (K8s)

## Your Ingress controller crashes repeatedly under heavy load. How do you stabilize it?

I would first check resource usage and increase CPU and memory limits if necessary. Then I would enable horizontal scaling for the Ingress controller. I would analyze logs to check for configuration errors and tune rate limits. Additionally, I would verify load balancer configuration and implement autoscaling policies for better resilience.

---

## An entire Kubernetes region goes down. How do you failover workloads?

For regional failure, I would implement multi-region cluster deployment with traffic managed via Route 53 latency-based routing or failover routing. Applications and data would be replicated across regions. In case of failure, DNS automatically redirects traffic to the healthy region.

---

## All pods in one namespace suddenly fail readiness checks. What’s your step to troubleshoot?

I would first inspect pod logs and describe events to identify failures. Then I would verify readiness probe configuration and check dependencies like database or external services. I would ensure there are no network policy restrictions blocking traffic.

---

## Your cluster is hit by a massive traffic surge, and HPA cannot scale pods fast enough. How do you handle it?

I would ensure cluster autoscaler is enabled to add nodes automatically. I would tune HPA metrics and increase max replica limits. Pre-scaling during expected traffic events and optimizing container startup time also help handle sudden surges.

---

## A node hosting critical workloads crashes permanently. How do you ensure workloads recover automatically?

If deployments are configured with replicas across multiple nodes, Kubernetes automatically reschedules pods to healthy nodes. I would ensure PodDisruptionBudgets, anti-affinity rules, and autoscaler are properly configured for resilience.

---

## A Pod is stuck in ImagePullBackOff. How do you troubleshoot?

I would check the image name and tag for correctness, verify registry credentials, and inspect events using kubectl describe pod. I would ensure imagePullSecrets are configured correctly and confirm registry connectivity.

---

# 🔹 Terraform

## Your Terraform apply succeeded, but some resources are not behaving as expected. How do you debug?

I would inspect Terraform state to confirm resource configuration. Then I would review the execution plan and compare actual cloud resources with desired configuration. Logs and provider debug mode can help identify discrepancies.

---

## How do you run Terraform safely in CI/CD pipelines?

I would implement terraform plan in pull requests for review and terraform apply only after approval. Remote backend using S3 with DynamoDB locking ensures state safety. Separate workspaces per environment prevent accidental changes.

---

## Your Terraform state file is getting too large. How do you manage it?

I would split infrastructure into multiple modules and maintain separate state files per environment or component. This improves performance and reduces state file size.

---

## A team needs to provision infra in 10 AWS regions simultaneously. How do you structure it?

I would use provider aliases for multiple regions and modularize the infrastructure. Automation via CI/CD pipelines would run Terraform across regions using reusable modules.

---

## You want faster Terraform runs in large projects. What optimizations would you apply?

I would split state files, reduce unnecessary dependencies, use targeted applies when required, and enable parallelism. Modular design improves execution efficiency.

---

## A production deployment failed halfway, leaving some resources created and others not. How do you recover?

I would inspect Terraform state to identify partially created resources, reconcile state using terraform refresh or import if needed, and re-run apply after resolving errors. In critical cases, manual cleanup may be required before reapplying.

---

# 🔹 Prometheus & Grafana

## How do you implement Kubernetes cluster-level monitoring using Prometheus?

I would deploy Prometheus using Helm charts and configure it to scrape metrics from Kubernetes components such as kubelet, API server, nodes, and pods. ServiceMonitor resources would define scraping targets. Grafana would visualize metrics using dashboards.

---

## How can you integrate Prometheus with Alertmanager and Slack for real-time notifications?

I would configure Alertmanager with Slack webhook integration and define alert rules in Prometheus for critical metrics such as high CPU, memory, or pod restarts. When thresholds are breached, Alertmanager sends notifications to Slack channels in real time, enabling quick incident response.

---
