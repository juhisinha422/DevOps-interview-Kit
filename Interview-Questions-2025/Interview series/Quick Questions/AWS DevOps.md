Here's a single README.md you can copy directly.

# Advanced AWS Interview Questions & Answers (4+ Years DevOps Engineer)

## 1. An EC2 instance passes status checks but the app on port 80 doesn't respond. Walk through every layer you check.

When an EC2 instance passes both system and instance status checks but the application is not responding on port 80, I troubleshoot layer by layer. First, I verify whether the application process is actually running using commands such as `ps -ef`, `systemctl status`, or `docker ps` if containerized. Next, I check whether the application is listening on port 80 using `netstat -tulnp` or `ss -tulnp`. If the process is not listening, the issue is application-related. If it is listening, I verify Security Group rules to ensure inbound HTTP traffic is allowed. Then I check NACL rules, route tables, and whether the instance has a public IP or is behind a Load Balancer. If an ALB is used, I validate target group health checks and target registration. I also review web server logs such as Nginx or Apache logs, application logs, and CloudWatch metrics. Finally, I test connectivity locally using curl and externally from another system. This structured approach helps isolate whether the issue is application, OS, networking, or AWS infrastructure related.

---

## 2. Explain a real scenario where adding more EC2 capacity made latency worse, not better.

One common production scenario occurs when applications depend heavily on a backend database. During a traffic spike, engineers often increase EC2 instances assuming it will improve performance. However, adding more application servers can increase the number of concurrent database connections, overwhelming the database. Instead of reducing latency, database contention, locking, and connection pool exhaustion cause response times to increase significantly. I experienced a situation where scaling application servers doubled the number of database connections, causing RDS CPU utilization to reach 95% and query latency to increase dramatically. The solution was database optimization, query tuning, connection pooling, and read replicas rather than simply adding more compute capacity.

---

## 3. How do you safely rotate IAM credentials in production without breaking active sessions?

The safest approach is to follow a phased rotation strategy. First, create new credentials while keeping existing credentials active. Update applications, CI/CD pipelines, Lambda functions, and automation scripts to use the new credentials. Validate that all services can authenticate successfully using the new credentials. Monitor logs for authentication failures and verify production traffic remains healthy. After confirming successful usage of the new credentials, disable the old credentials temporarily while continuing to monitor. Finally, permanently delete the old credentials. Wherever possible, I prefer IAM Roles over Access Keys because roles eliminate manual credential rotation and significantly improve security.

---

## 4. What problems arise when multiple services share the same Security Group, and how do you design around it?

Sharing a single Security Group across multiple services often leads to excessive permissions and reduced security. Over time, teams keep adding rules to support new applications, resulting in a broad attack surface. Troubleshooting also becomes difficult because changes intended for one service can unintentionally affect others. In production, I prefer assigning dedicated Security Groups per application or service tier. For example, web servers, application servers, and databases each receive their own Security Group. Communication is controlled through Security Group references rather than wide-open CIDR ranges. This follows least-privilege principles and improves maintainability.

---

## 5. Difference between Security Group and NACL - and why misunderstanding this opens production to attack.

Security Groups operate at the instance level and are stateful, meaning return traffic is automatically allowed. NACLs operate at the subnet level and are stateless, requiring both inbound and outbound rules to be explicitly defined. Many engineers mistakenly assume NACLs provide the same protection as Security Groups. Misconfigured NACLs can unintentionally allow or block traffic across entire subnets. In production environments, Security Groups are typically used as the primary firewall mechanism, while NACLs provide an additional subnet-level security layer. Understanding the distinction is critical because overly permissive NACLs or Security Groups can expose applications to unauthorized access.

---

## 6. How do you handle secrets in AWS without exposing them in environment variables or Lambda config?

I use AWS Secrets Manager or AWS Systems Manager Parameter Store to securely store sensitive information such as database passwords, API keys, and tokens. Applications retrieve secrets dynamically at runtime through IAM-authenticated API calls. This ensures secrets are encrypted at rest using KMS and are never hardcoded in source code, Terraform files, Docker images, or Lambda configurations. Secret rotation can also be automated through Secrets Manager. This approach significantly improves security and compliance while reducing operational risk.

---

## 7. Explain cost drift. How do you detect a silent billing spike before it shows up on the invoice?

Cost drift occurs when cloud spending gradually increases without intentional infrastructure changes. Common causes include idle resources, oversized instances, unattached EBS volumes, data transfer growth, snapshot accumulation, or inefficient architecture changes. To detect cost drift early, I configure AWS Budgets, Cost Explorer alerts, Cost Anomaly Detection, and CloudWatch billing alarms. Daily cost monitoring dashboards allow teams to identify unusual spending patterns before monthly invoices arrive. Regular FinOps reviews and tagging strategies also help pinpoint cost ownership and optimization opportunities.

---

## 8. What happens internally when an S3 bucket policy and IAM policy conflict on the same action?

AWS follows an explicit deny model. First, AWS evaluates all applicable policies, including IAM policies, bucket policies, SCPs, and permission boundaries. If any policy explicitly denies access, the request is denied regardless of any allow statements. If there are no explicit denies and at least one allow exists, access is granted. For example, even if an IAM policy allows access to an S3 bucket, a bucket policy containing an explicit deny will override the allow and block access. Understanding policy evaluation order is essential for troubleshooting authorization issues.

---

## 9. How do you design a multi-account AWS setup without permissions becoming tightly coupled?

I follow a multi-account strategy using AWS Organizations. Separate accounts are maintained for Production, Non-Production, Security, Shared Services, and Logging. Access between accounts is controlled using IAM Roles and STS AssumeRole instead of long-term credentials. Shared services such as CI/CD, monitoring, and logging operate from centralized accounts. Service Control Policies enforce governance at the organizational level. This architecture improves security, isolation, compliance, and operational scalability while preventing tightly coupled permission structures.

---

## 10. Explain NAT Gateway vs NAT Instance vs VPC Endpoint - when does choosing wrong waste money?

A NAT Gateway is a managed AWS service that allows private subnet resources to access the internet. It offers high availability but incurs hourly and data processing charges. A NAT Instance is a self-managed EC2 instance providing similar functionality but requires maintenance and scaling. A VPC Endpoint allows private communication with AWS services without internet access or NAT usage. Choosing a NAT Gateway for workloads that only access S3 or DynamoDB can waste significant money because a VPC Endpoint would eliminate NAT data transfer costs. Selecting the right option depends on traffic patterns, availability requirements, and cost considerations.

---

## 11. How does cross-account role assumption actually work, and why is it dangerous if misconfigured?

Cross-account role assumption uses AWS Security Token Service (STS). An IAM user or role in one account assumes a role in another account based on trust policies. Temporary credentials are generated and used for authorized actions. If trust relationships are overly permissive, unauthorized accounts may gain access to critical resources. Misconfigured AssumeRole permissions can create privilege escalation paths. Therefore, trust policies should be tightly controlled, least privilege should be enforced, and CloudTrail monitoring should be enabled for auditing.

---

## 12. How do you refactor a flat VPC into private/public subnets without taking production down?

The migration must be gradual. First, create public and private subnets alongside the existing infrastructure. Deploy NAT Gateways, route tables, and Security Groups. Then move stateless workloads incrementally into the new subnet architecture while validating connectivity. Load Balancers are used to direct traffic between old and new environments during migration. Database and stateful components are migrated carefully with replication and failover planning. Infrastructure as Code and extensive testing are critical. The goal is to perform migration in phases rather than attempting a risky big-bang cutover.

---

## 13. What are partial failovers, and how do you recover safely during a Multi-AZ RDS failure?

A partial failover occurs when some components recover while others remain unavailable or degraded. In a Multi-AZ RDS setup, AWS automatically promotes the standby instance during failures. However, applications may still experience connection issues, DNS propagation delays, stale connection pools, or transaction retries. During recovery, I verify RDS failover completion, application connectivity, DNS resolution, database replication status, and application health. Monitoring dashboards help confirm full service restoration before closing the incident.

---

## 14. What is the real difference between an Elastic IP and a Public IP, and why does that matter at scale?

A Public IP is automatically assigned by AWS and may change when an instance stops and starts. An Elastic IP is a static public IPv4 address allocated to an AWS account and retained until released. At scale, Elastic IPs are important for systems requiring fixed IP allowlists, partner integrations, VPN connections, or external firewall configurations. However, unused Elastic IPs incur charges, so proper lifecycle management is important for cost optimization.

---

## 15. Describe a real incident caused by an overly permissive IAM policy. How did you fix it?

A common production incident involved an IAM policy containing `s3:*` permissions on all buckets using the `*` wildcard. A developer accidentally deleted objects from a production bucket while testing automation scripts. Although versioning allowed recovery, the incident highlighted excessive permissions. The fix involved implementing least-privilege IAM policies, restricting actions to specific buckets, enabling MFA for sensitive operations, introducing approval workflows for production changes, and conducting periodic IAM access reviews. We also implemented IAM Access Analyzer and automated compliance checks to prevent similar issues in the future.



# AWS DevOps Engineer (4 Years Experience) - Interview Questions & Answers

## 1. Can you describe the different types of Load Balancers and provide examples?

AWS provides three main types of load balancers. The Application Load Balancer (ALB) operates at Layer 7 and is used for HTTP and HTTPS traffic. It supports advanced routing features such as path-based and host-based routing, making it ideal for microservices and web applications. The Network Load Balancer (NLB) operates at Layer 4 and is designed for handling TCP and UDP traffic with very low latency and high performance. It is commonly used for applications requiring millions of requests per second. The Classic Load Balancer (CLB) is the older generation load balancer that supports both Layer 4 and Layer 7 features but is generally used only for legacy applications. In my projects, I have primarily used ALB for web applications hosted on EC2 and EKS.

## 2. What is the maximum runtime for a Lambda function?

AWS Lambda supports a maximum execution timeout of 15 minutes, which is equivalent to 900 seconds. If a function exceeds this duration, AWS automatically terminates the execution. For long-running workflows, I typically use AWS Step Functions to orchestrate multiple Lambda functions instead of increasing the execution duration.

## 3. What is the maximum memory size for a Lambda function?

AWS Lambda allows memory allocation from 128 MB up to 10,240 MB (10 GB). Increasing the memory allocation also increases the CPU and network throughput available to the function. During performance optimization, I monitor CloudWatch metrics and adjust memory settings based on execution time and resource utilization.

## 4. How can you increase the runtime for a Lambda function?

The maximum timeout of a Lambda function can be configured up to 15 minutes in the function settings. If the workload requires more processing time than the Lambda limit allows, I typically optimize the code, split the process into multiple Lambda functions, or use AWS Step Functions to manage longer workflows. In some cases, containerized workloads on ECS or EKS may be a better solution for long-running processes.

## 5. What automations have you performed using Lambda in your project?

In my projects, I have used Lambda for several automation tasks, including automatic EC2 start and stop operations based on schedules, monitoring S3 bucket events, sending SNS notifications for infrastructure alerts, automating backup processes, rotating IAM access keys, cleaning up unused snapshots, and processing CloudWatch log events. These automations helped reduce manual intervention and improve operational efficiency.

## 6. Why did you choose Terraform over CloudFormation for infrastructure provisioning?

I prefer Terraform because it is cloud-agnostic and supports multiple cloud providers such as AWS, Azure, and GCP. Terraform uses HashiCorp Configuration Language (HCL), which is simple and easy to maintain. It provides modularity, reusable code, state management, and a large community ecosystem. Compared to CloudFormation, Terraform offers better flexibility and easier integration with CI/CD pipelines. In my projects, Terraform has been used extensively to provision VPCs, EC2 instances, IAM roles, Lambda functions, and networking resources.

## 7. What modules have you used in your Lambda function?

In Python-based Lambda functions, I have commonly used modules such as boto3 for AWS service interactions, json for handling data, logging for monitoring and debugging, datetime for scheduling operations, os for environment variables, and requests for calling external APIs. These modules help build scalable and maintainable serverless applications.

## 8. Have you created an SNS topic for your project?

Yes, I have created and configured SNS topics for infrastructure monitoring and alerting. SNS was integrated with CloudWatch alarms to send email notifications whenever CPU utilization, memory consumption, or application errors crossed predefined thresholds. I have also used SNS to trigger Lambda functions and fan-out notifications to multiple subscribers.

## 9. If you've exhausted IP addresses in your VPC, how would you provision new resources?

If the IP address range in a VPC is exhausted, I would first evaluate the existing subnet utilization and remove unused resources if possible. If additional IP addresses are required, I would associate a secondary CIDR block with the VPC and create new subnets from that range. Another option is to redesign the network architecture with larger CIDR ranges or migrate workloads to a new VPC with sufficient address space.

## 10. What is Groovy, and how is it used in Jenkins?

Groovy is a scripting language that runs on the Java Virtual Machine (JVM). In Jenkins, Groovy is used to create Pipeline as Code through Jenkinsfiles. It allows developers and DevOps engineers to define build, test, and deployment stages programmatically. Groovy provides flexibility for creating complex CI/CD workflows and automation logic.

## 11. Why do you use Groovy in Jenkins, and where do you save Jenkins files?

Groovy is used in Jenkins because it enables the implementation of CI/CD pipelines as code, making them version-controlled and reusable. Jenkinsfiles are typically stored in the application's source code repository, such as GitHub, GitLab, or Bitbucket. This approach ensures that pipeline definitions are maintained alongside application code and can be tracked through version control.

## 12. What is Ansible, and what is its purpose?

Ansible is an open-source automation and configuration management tool used for provisioning, configuration management, application deployment, and orchestration. It simplifies infrastructure management by automating repetitive tasks across multiple servers. In my projects, I have used Ansible to install software packages, configure web servers, deploy applications, and manage operating system configurations.

## 13. What language do you use in Ansible?

Ansible playbooks are written in YAML (Yet Another Markup Language). YAML provides a simple and human-readable format for defining automation tasks. Ansible also uses Jinja2 templates for dynamic configurations and Python modules for backend execution.

## 14. Where do you run Terraform code, remotely or locally?

I have experience running Terraform both locally and through CI/CD pipelines. In enterprise environments, Terraform is typically executed through Jenkins pipelines, while the state file is stored remotely in an S3 bucket with DynamoDB used for state locking. This setup ensures collaboration, security, and consistency across team members.

## 15. What is the purpose of access keys and secret keys in AWS?

AWS Access Keys and Secret Keys are used for programmatic authentication to AWS services. They enable applications, scripts, Terraform, and AWS CLI commands to securely interact with AWS resources. As a best practice, I prefer IAM roles whenever possible to avoid hardcoding credentials and improve security.

## 16. What are Terraform modules, and have you used any in your project?

Terraform modules are reusable collections of Terraform resources that simplify infrastructure deployment and maintenance. They help standardize infrastructure across environments and reduce code duplication. In my projects, I have created and used modules for VPCs, EC2 instances, security groups, IAM roles, Lambda functions, and S3 buckets.

## 17. What environments have you set up for your project?

I have worked with multiple environments, including Development (Dev), Quality Assurance (QA), User Acceptance Testing (UAT), Staging, and Production. Each environment has separate infrastructure and configurations to ensure proper testing before changes are deployed to production.

## 18. Do you use the same AWS account for all environments?

No, following AWS best practices, we use separate AWS accounts for Development, Testing, and Production environments. This approach improves security, governance, cost management, and resource isolation. AWS Organizations is typically used to manage multiple accounts centrally.

## 19. Do you have separate Jenkins servers for each environment?

In most projects, a single Jenkins server is used to manage deployments across multiple environments. Different pipelines, credentials, and deployment stages are configured for Dev, QA, UAT, and Production. However, for highly secure environments, separate Jenkins instances may be maintained for production workloads.

## 20. Where do you write and save your Lambda function code?

I usually develop Lambda functions locally using Visual Studio Code and maintain the source code in Git repositories such as GitHub or GitLab. The deployment process is automated through Jenkins pipelines and Terraform. The deployment packages are stored in S3 and then deployed to AWS Lambda.

## 21. How do you manage cost optimization in cloud environment services?

I follow several cloud cost optimization strategies, including right-sizing EC2 instances, using Auto Scaling groups, purchasing Reserved Instances or Savings Plans for predictable workloads, utilizing Spot Instances for non-critical workloads, implementing S3 lifecycle policies, removing unused resources, monitoring costs through AWS Cost Explorer and AWS Budgets, and optimizing Lambda memory allocations. Regular audits help identify and eliminate unnecessary expenses.

## 22. Do you have any questions for me?

Yes. I would like to understand the team's current CI/CD process and deployment strategy. I am also interested in learning about the cloud architecture used in the organization, the tools used for monitoring and automation, and the opportunities available for working on modern technologies such as Kubernetes, serverless computing, and Infrastructure as Code. Additionally, I would like to know how success is measured for this role and what growth opportunities are available within the team.



----
# Common DevOps Networking & Port Interview Questions

## Which port is used by SSH?

SSH (Secure Shell) uses **TCP port 22** by default. It is used for secure remote login, command execution, file transfers (SCP/SFTP), and server administration. In production environments, some organizations change the default port for additional security, but 22 remains the standard SSH port.

---

## Which port is used by HTTP and HTTPS?

HTTP uses **TCP port 80** and HTTPS uses **TCP port 443**.

* **HTTP (Port 80):** Transfers data in plain text and is generally used for redirects to HTTPS.
* **HTTPS (Port 443):** Encrypts communication using SSL/TLS and is the standard protocol for secure web traffic.

Most modern applications use HTTPS to ensure secure communication between users and servers.

---

## Which port is required for Amazon EFS?

Amazon Elastic File System (EFS) uses **TCP port 2049**.

EFS is based on the Network File System (NFS) protocol. When EC2 instances, EKS worker nodes, or containers need to mount an EFS filesystem, Security Groups and NACLs must allow TCP traffic on port 2049 between clients and EFS mount targets.

---

## Which port does MySQL use?

MySQL uses **TCP port 3306** by default.

Applications connect to MySQL databases through this port for executing queries, reading data, and performing transactions. When troubleshooting database connectivity issues, verifying Security Groups, NACLs, and firewall access to port 3306 is a common step.

---

## Difference between TCP and UDP?

TCP (Transmission Control Protocol) is a connection-oriented protocol that guarantees reliable data delivery. UDP (User Datagram Protocol) is a connectionless protocol that prioritizes speed over reliability.

### TCP Characteristics:

* Connection-oriented
* Reliable communication
* Error checking and retransmission
* Ordered packet delivery
* Slower than UDP

### UDP Characteristics:

* Connectionless
* Faster communication
* No delivery guarantee
* No retransmission
* Lower overhead

### Examples:

* TCP: HTTP, HTTPS, SSH, FTP, MySQL
* UDP: DNS, VoIP, Video Streaming, Online Gaming

In production systems, TCP is used when reliability is critical, while UDP is preferred for latency-sensitive workloads.

---

## Which port is used by Jenkins?

Jenkins uses **TCP port 8080** by default.

This port provides access to the Jenkins web interface where users can create pipelines, monitor builds, manage plugins, and configure jobs. Organizations often place Jenkins behind a reverse proxy such as NGINX or an Application Load Balancer and expose it through HTTPS on port 443.

---

## Which port is used by Kubernetes API Server?

The Kubernetes API Server uses **TCP port 6443**.

All Kubernetes components communicate with the API Server through this port, including:

* kubectl
* kubelets
* controllers
* schedulers
* external automation tools

When kubectl commands fail, one of the first troubleshooting steps is verifying connectivity to port 6443.

---

## Which port is used by Docker daemon?

The Docker daemon typically uses:

* **Unix Socket:** `/var/run/docker.sock` (most common)
* **TCP Port 2375:** Unsecured Docker API
* **TCP Port 2376:** Secure Docker API with TLS

In production environments, Docker communication generally occurs through the Unix socket or TLS-secured port 2376 rather than the unsecured 2375 port.

---

## Which port is used by Grafana?

Grafana uses **TCP port 3000** by default.

Users access dashboards, metrics visualizations, alerts, and monitoring data through this port. In production environments, Grafana is often placed behind NGINX, Ingress Controllers, or Load Balancers and exposed through HTTPS.

---

## Which port is used by Prometheus?

Prometheus uses **TCP port 9090** by default.

This port provides:

* Prometheus UI
* Query interface (PromQL)
* Metrics exploration
* Alert configuration

Grafana commonly connects to Prometheus on port 9090 to retrieve monitoring metrics for dashboard visualization.

---

# Quick Revision Table

| Service                | Protocol | Default Port |
| ---------------------- | -------- | ------------ |
| SSH                    | TCP      | 22           |
| HTTP                   | TCP      | 80           |
| HTTPS                  | TCP      | 443          |
| Amazon EFS (NFS)       | TCP      | 2049         |
| MySQL                  | TCP      | 3306         |
| Jenkins                | TCP      | 8080         |
| Kubernetes API Server  | TCP      | 6443         |
| Docker Daemon (TLS)    | TCP      | 2376         |
| Docker Daemon (No TLS) | TCP      | 2375         |
| Grafana                | TCP      | 3000         |
| Prometheus             | TCP      | 9090         |

# Most Asked DevOps Interview Ports

| Component             | Port        |
| --------------------- | ----------- |
| SSH                   | 22          |
| HTTP                  | 80          |
| HTTPS                 | 443         |
| DNS                   | 53          |
| MySQL                 | 3306        |
| PostgreSQL            | 5432        |
| Jenkins               | 8080        |
| Grafana               | 3000        |
| Prometheus            | 9090        |
| Kubernetes API Server | 6443        |
| Docker Daemon         | 2375 / 2376 |
| EFS                   | 2049        |
| Redis                 | 6379        |
| MongoDB               | 27017       |
| Elasticsearch         | 9200        |
| Kibana                | 5601        |



