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



