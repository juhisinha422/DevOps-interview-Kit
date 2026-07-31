# Infosys DevOps Interview Experience – Round 1 (Detailed Answers)

---

# 1. Tell me about your current role and DevOps experience.

## Answer

"I'm currently working as a DevOps Engineer with around 4 years of IT experience. My primary work is on AWS cloud, Kubernetes (EKS), Docker, Jenkins, Terraform, Git, Helm, Linux, and CI/CD automation.

My day-to-day responsibilities include developing and maintaining CI/CD pipelines, containerizing applications using Docker, deploying microservices on Amazon EKS, provisioning infrastructure using Terraform, monitoring applications with Prometheus and Grafana, troubleshooting production issues, and collaborating with developers to ensure smooth application releases.

I also work on Infrastructure as Code, Kubernetes deployments, Helm charts, image management in Amazon ECR, security scanning using Trivy, and production incident support."

---

# 2. Is your DevOps experience primarily on AWS or other cloud platforms?

## Answer

Yes. My primary experience is on AWS.

Services I've worked on include:

- EC2
- VPC
- IAM
- EKS
- ECR
- S3
- CloudWatch
- Route53
- ALB
- NLB
- Auto Scaling
- EBS
- EFS
- RDS
- Secrets Manager

Apart from AWS, I have basic exposure to Kubernetes and Terraform which are cloud-agnostic.

---

# 3. Have you worked with on-premises infrastructure / VMware vSphere?

## Answer

I have primarily worked on AWS cloud infrastructure.

I have basic knowledge of VMware and understand concepts like:

- Virtual Machines
- ESXi Hosts
- Datastores
- vCenter

However, my production experience is mainly focused on AWS cloud and Kubernetes.

---

# 4. Did you create CI/CD pipelines from scratch or work on existing pipelines?

## Answer

I have done both.

Initially I worked on enhancing existing Jenkins pipelines.

Later I created pipelines from scratch for new microservices.

My pipeline generally includes:

Developer

↓

Git Push

↓

Webhook

↓

Jenkins

↓

Build

↓

Unit Testing

↓

SonarQube

↓

Trivy Scan

↓

Docker Build

↓

Push to Amazon ECR

↓

Terraform (if infrastructure changes)

↓

Helm Deployment

↓

Amazon EKS

↓

Smoke Testing

↓

Slack/Email Notification

---

# 5. Explain the CI/CD pipeline you created for CSV validation and S3 upload.

## Answer

The pipeline flow was:

CSV uploaded

↓

Git Trigger

↓

Jenkins Pipeline

↓

Validate CSV format

↓

Run Python validation script

↓

Reject if invalid

↓

Upload valid CSV to Amazon S3

↓

Send success notification

If validation failed, the pipeline stopped immediately and notified the team with detailed error logs.

---

# 6. If an L3 team created the pipeline, how do you understand and troubleshoot it?

## Answer

My approach is:

- Review the Jenkinsfile.
- Understand pipeline stages.
- Check Shared Libraries.
- Review environment variables.
- Verify credentials.
- Check Jenkins console logs.
- Review previous successful builds.
- Compare recent Git commits.
- Identify the failed stage.
- Fix the issue or coordinate with the relevant team if necessary.

---

# 7. Have you written a Dockerfile?

## Answer

Yes.

A typical Dockerfile I write includes:

- Base Image
- Working Directory
- Copy application files
- Install dependencies
- Expose required port
- Define ENTRYPOINT or CMD

Example:

```dockerfile
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

I also use multi-stage builds to reduce image size.

---

# 8. What is the difference between CMD and ENTRYPOINT in Docker?

## Answer

CMD

- Provides default command.
- Can be overridden.

Example

```
CMD ["java","-jar","app.jar"]
```

ENTRYPOINT

- Defines the main executable.
- Difficult to override.

Example

```
ENTRYPOINT ["java","-jar","app.jar"]
```

Difference:

| CMD | ENTRYPOINT |
|------|------------|
| Default command | Main executable |
| Easily overridden | Usually fixed |
| Flexible | Mandatory execution |

Production practice:

ENTRYPOINT for the application.

CMD for optional arguments.

---

# 9. What Kubernetes operations have you performed apart from deployments?

## Answer

I regularly perform:

- Pod troubleshooting
- Rollbacks
- Scaling Deployments
- Creating Namespaces
- ConfigMaps
- Secrets
- Persistent Volumes
- Persistent Volume Claims
- Ingress configuration
- Service creation
- HPA configuration
- Helm upgrades
- Pod log analysis
- Node troubleshooting
- RBAC management
- Resource optimization
- Cluster upgrades
- Monitoring and alerting

---

# 10. Have you troubleshooted CrashLoopBackOff? How?

## Answer

Yes.

My troubleshooting steps are:

```bash
kubectl get pods

kubectl describe pod

kubectl logs

kubectl logs --previous

kubectl top pod
```

Then verify:

- Image
- ConfigMaps
- Secrets
- Resource limits
- Liveness probe
- Readiness probe
- Database connectivity
- External dependencies

If caused by a deployment:

```
kubectl rollout undo deployment
```

---

# 11. Explain CrashLoopBackOff in simple terms.

## Answer

CrashLoopBackOff means:

The application inside the container starts.

↓

It crashes immediately.

↓

Kubernetes restarts it.

↓

It crashes again.

↓

After several failed attempts, Kubernetes waits longer between restart attempts.

Common reasons:

- Wrong application configuration
- Missing Secrets
- Missing ConfigMaps
- Database unavailable
- OOMKilled
- Incorrect startup command
- Probe failures

---

# 12. Explain PV and PVC in Kubernetes.

## Answer

Persistent Volume (PV)

A storage resource available in the cluster.

Persistent Volume Claim (PVC)

A request made by a Pod for storage.

Flow:

Application

↓

PVC

↓

PV

↓

AWS EBS / EFS

Difference:

| PV | PVC |
|----|-----|
| Actual storage | Storage request |
| Created by Admin/Dynamic Provisioner | Created by User |
| Supplies storage | Consumes storage |

---

# 13. How do you handle autoscaling in Kubernetes?

## Answer

I use:

Horizontal Pod Autoscaler (HPA)

- Scales Pods.

Cluster Autoscaler

- Adds worker nodes.

Vertical Pod Autoscaler (when appropriate)

- Adjusts CPU/Memory recommendations.

Metrics used:

- CPU
- Memory
- Custom Metrics
- External Metrics

This ensures applications scale automatically based on demand while optimizing infrastructure usage.


# Infosys DevOps Interview Experience – Round 1 (Part 2)

---

# 14. How do you achieve URL/Path-Based Routing in Kubernetes?

## Answer

In Kubernetes, URL or path-based routing is achieved using an **Ingress** resource along with an **Ingress Controller** (e.g., AWS Load Balancer Controller, NGINX Ingress Controller).

### Architecture

```
Internet
      │
      ▼
Application Load Balancer (ALB)
      │
      ▼
Ingress Controller
      │
 ┌────┴──────────────┐
 │                   │
 ▼                   ▼
/users          /orders
 │                   │
User Service    Order Service
 │                   │
Pods            Pods
```

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /users
        pathType: Prefix
        backend:
          service:
            name: user-service
            port:
              number: 80

      - path: /orders
        pathType: Prefix
        backend:
          service:
            name: order-service
            port:
              number: 80
```

### Benefits

- Single entry point
- Path-based routing
- Host-based routing
- SSL termination
- Lower AWS Load Balancer cost
- Easy traffic management

### Interview Answer

> In production, I use an Ingress resource with the AWS Load Balancer Controller. The ALB receives external traffic, forwards it to the Ingress Controller, and based on the URL path or hostname, the request is routed to the appropriate Kubernetes Service and Pods.

---

# 15. Are you familiar with middleware technologies like Tomcat, WebLogic, or WebSphere?

## Answer

Yes, I have worked with **Apache Tomcat** for Java-based applications.

My responsibilities included:

- Deploying WAR files
- Restarting Tomcat services
- Monitoring logs
- Troubleshooting application startup issues
- Configuring JVM options
- Managing environment variables
- Integrating Tomcat deployments into Jenkins pipelines

I have basic knowledge of WebLogic and WebSphere but my production experience is mainly with Tomcat.

### Interview Answer

> I have production experience with Apache Tomcat for deploying Java applications and troubleshooting startup issues. I also understand the basics of WebLogic and WebSphere, though my primary hands-on experience is with Tomcat.

---

# 16. How comfortable are you with Linux? What activities do you perform?

## Answer

I work with Linux daily.

Common activities include:

- User management
- File and directory permissions
- Process monitoring
- Disk usage monitoring
- Network troubleshooting
- Service management
- Log analysis
- Cron jobs
- Shell scripting

Frequently used commands:

```bash
ls
cd
pwd
cp
mv
rm
cat
grep
find
chmod
chown
ps
top
htop
free
df
du
netstat
ss
curl
wget
systemctl
journalctl
tail -f
```

### Interview Answer

> I am comfortable working with Linux and use it daily for application deployments, troubleshooting, monitoring services, analyzing logs, managing users and permissions, writing shell scripts, and performing system administration tasks.

---

# 17. Have you worked on incident management / production incidents?

## Answer

Yes.

Typical production incidents I've handled include:

- CrashLoopBackOff
- ImagePullBackOff
- High CPU usage
- High memory utilization
- Pod Pending
- Application downtime
- Jenkins pipeline failures
- Terraform deployment failures
- Ingress routing issues
- Database connectivity failures

### Incident Process

1. Receive alert from Prometheus or CloudWatch.
2. Identify impacted application.
3. Analyze logs and metrics.
4. Troubleshoot root cause.
5. Restore service (rollback if needed).
6. Validate application.
7. Conduct Root Cause Analysis (RCA).
8. Implement preventive measures.

### Interview Answer

> Yes, I regularly participate in production incident management. I investigate alerts, analyze logs and metrics, restore services quickly, communicate with stakeholders, perform RCA, and implement preventive actions to avoid future incidents.

---

# 18. Which scripting languages have you worked with?

## Answer

I have worked with:

- Bash/Shell
- Python (basic)
- Groovy (Jenkins Pipelines)

Examples:

Bash:

- Deployment automation
- Health checks
- Backup scripts
- Log cleanup

Python:

- CSV validation
- AWS automation using Boto3
- API integrations

Groovy:

- Jenkins Declarative Pipelines
- Shared Libraries

### Interview Answer

> I primarily use Bash for automation, Groovy for Jenkins pipelines, and Python for tasks such as file validation, AWS automation, and API integrations.

---

# 19. What repetitive tasks have you automated using Bash/Shell scripting?

## Answer

Examples:

- Log cleanup
- Backup scripts
- Health checks
- Service restart automation
- Docker cleanup
- Kubernetes health checks
- ECR image cleanup
- Disk usage monitoring
- Deployment validation
- User creation

Example:

```bash
docker system prune -af
```

Daily health check:

```bash
kubectl get pods

kubectl get nodes

kubectl top pods
```

### Interview Answer

> I have automated repetitive tasks such as log cleanup, service monitoring, Docker cleanup, Kubernetes health checks, backup scripts, disk monitoring, and deployment validation using Bash scripting.

---

# 20. Give a real-time example of automation you implemented.

## Answer

One automation I implemented was **Docker image cleanup** on Jenkins agents.

### Before

- Old images consumed disk space.
- Jenkins builds started failing with "No space left on device".

### Automation

Created a Bash script:

```bash
docker image prune -af

docker container prune -f

docker volume prune -f
```

Configured it as a cron job.

### Result

- Reclaimed disk space automatically.
- Prevented build failures.
- Reduced manual maintenance.

### Interview Answer

> I automated Docker cleanup on Jenkins agents using a Bash script scheduled through cron. It removed unused images, containers, and volumes, preventing disk space issues and reducing manual intervention.

---

# 21. What is Terraform State Management?

## Answer

Terraform State is a file that stores the mapping between Terraform configuration and real infrastructure.

It tracks:

- Resource IDs
- Dependencies
- Metadata
- Current infrastructure state

Example:

```
main.tf

↓

terraform apply

↓

AWS Resources

↓

terraform.tfstate
```

### Why is it important?

- Detects changes
- Plans updates
- Prevents duplicate resource creation
- Enables infrastructure tracking

### Interview Answer

> Terraform state records the current infrastructure managed by Terraform. It maps configuration to real resources, allowing Terraform to calculate changes, update existing infrastructure, and avoid recreating resources unnecessarily.

---

# 22. How do you manage Terraform State in a team environment?

## Answer

Production best practice:

Remote Backend:

- Amazon S3 (State Storage)
- DynamoDB (State Locking)

Architecture:

```
Terraform

↓

S3 Backend

↓

DynamoDB Lock

↓

AWS Infrastructure
```

Benefits:

- Shared state
- Versioning
- Encryption
- State locking
- Team collaboration

Example:

```hcl
backend "s3" {
  bucket         = "terraform-state"
  key            = "prod/terraform.tfstate"
  region         = "ap-south-1"
  dynamodb_table = "terraform-locks"
}
```

### Interview Answer

> In a team environment, I store the Terraform state in an encrypted Amazon S3 bucket with versioning enabled and use DynamoDB for state locking to prevent multiple users from modifying the infrastructure simultaneously.

---

# 23. What are Terraform Modules and why do we use them?

## Answer

A Terraform module is a reusable collection of Terraform resources.

Instead of writing EC2 creation code repeatedly, create a module once and reuse it.

Example:

```
modules/

EC2/

VPC/

EKS/

RDS/
```

Benefits:

- Reusability
- Consistency
- Easier maintenance
- Reduced code duplication
- Standardization

### Interview Answer

> Terraform modules allow us to package and reuse infrastructure code. They improve maintainability, reduce duplication, and ensure consistent infrastructure deployment across different environments.

---

# 24. What are Terraform Workspaces?

## Answer

Terraform Workspaces allow multiple environments to share the same Terraform configuration while maintaining separate state files.

Example:

```
Default

↓

Dev

↓

QA

↓

UAT

↓

Production
```

Commands:

```bash
terraform workspace list

terraform workspace new dev

terraform workspace select prod
```

Each workspace maintains its own state.

### Interview Answer

> Terraform Workspaces enable us to manage multiple environments such as Dev, QA, and Production using the same Terraform code while keeping separate state files for each environment.

---

# 25. Do you have any questions for the interviewer?

## Answer

Good questions to ask:

1. How is the DevOps team structured?

2. Which CI/CD tools and deployment strategies are currently used?

3. How do you manage Kubernetes clusters in production?

4. What monitoring and observability tools do you use?

5. How do you handle production incidents and on-call responsibilities?

6. Are you following GitOps or traditional CI/CD?

7. What are the biggest technical challenges the team is currently facing?

8. What opportunities are available for learning new cloud technologies and certifications?

9. What does success look like for this role in the first six months?

10. What are the next steps in the interview process?

### Interview Tip

Avoid saying **"No, I don't have any questions."** Asking thoughtful questions demonstrates interest in the role, the team, and the company's engineering practices.



# Infosys DevOps Interview – Round 2 (4 Years Experience)

---

## 1. Please explain your day-to-day activities as a DevOps Engineer.

### Answer

As a DevOps Engineer, my daily responsibilities include monitoring CI/CD pipelines, supporting application deployments, managing Kubernetes clusters, provisioning infrastructure using Terraform, and troubleshooting production issues.

I review Jenkins pipeline executions, resolve build failures, and ensure successful deployments to Amazon EKS using Helm and Argo CD. I monitor infrastructure and application health using Prometheus, Grafana, and CloudWatch. I also manage Docker images in Amazon ECR, optimize cloud resources, perform infrastructure changes using Terraform, and collaborate with developers to resolve deployment and application issues. Additionally, I participate in production release planning, incident management, root cause analysis (RCA), and continuously automate manual operational tasks.

---

## 2. You have cloned a Git repository. While running `git pull`, you get the error "not a git repository". How will you troubleshoot and fix it?

### Answer

First, I verify whether I'm inside the correct project directory.

```bash
pwd
ls -la
```

Then I check if the `.git` directory exists.

```bash
ls -la .git
```

If the `.git` directory is missing, it means either I'm in the wrong directory or the repository metadata has been deleted.

Next, I verify the configured remote.

```bash
git remote -v
```

If Git reports **"not a git repository"**, I navigate to the correct cloned repository.

If the repository was accidentally deleted or corrupted, I clone it again.

```bash
git clone <repository-url>
```

If necessary, I also verify the current branch.

```bash
git branch
git status
```

Once the repository is valid, I execute:

```bash
git pull origin main
```

(or the appropriate branch).

---

## 3. How do you provide access to an S3 bucket, and what permissions need to be set on the bucket?

### Answer

The recommended approach is to use **IAM Roles** instead of long-term access keys.

For EC2 instances, I attach an IAM Role containing only the required S3 permissions such as:

- s3:GetObject
- s3:PutObject
- s3:ListBucket
- s3:DeleteObject (if required)

The bucket itself can also have a Bucket Policy restricting access to specific IAM roles, AWS accounts, VPC endpoints, or IP ranges.

For Kubernetes workloads running on EKS, I use **IAM Roles for Service Accounts (IRSA)** so Pods can securely access S3 without storing AWS credentials.

Following the Principle of Least Privilege ensures applications receive only the permissions they actually require.

---

## 4. How can an application communicate with an EC2 instance that is deployed in a private subnet behind a Multi-AZ Load Balancer?

### Answer

The EC2 instances remain in private subnets without public IP addresses.

An **Application Load Balancer (ALB)** is deployed in public subnets across multiple Availability Zones.

The ALB receives incoming client requests and forwards traffic to the EC2 instances using Target Groups.

Security Groups allow inbound traffic only from the ALB Security Group rather than directly from the internet.

Applications communicate using the ALB DNS name while the backend instances remain protected inside private subnets.

This architecture provides high availability, security, and fault tolerance across multiple Availability Zones.

---

## 5. If an application is hosted in an S3 bucket and users are located in different geographic regions, how would you reduce latency?

### Answer

I would place **Amazon CloudFront** in front of the S3 bucket.

CloudFront caches static content at AWS Edge Locations located close to end users worldwide.

Instead of every request reaching the origin S3 bucket, users receive content from the nearest edge location, significantly reducing latency.

I would also enable compression, configure appropriate cache-control headers, use HTTPS, and enable Origin Access Control (OAC) so the S3 bucket remains private while CloudFront securely serves the content.

---

## 6. You encounter high latency in an application. What monitoring and troubleshooting steps would you take?

### Answer

I start by identifying whether the latency is occurring at the application, infrastructure, database, or network layer.

I review CloudWatch metrics such as CPU, Memory, Network In/Out, Disk IOPS, ALB Target Response Time, HTTP 5xx errors, and request count.

For Kubernetes workloads, I examine Prometheus metrics including Pod CPU, memory utilization, request latency, and restart count.

I inspect application logs, database performance, API response times, and distributed traces using OpenTelemetry or Jaeger if available.

I also verify Auto Scaling events, resource utilization, recent deployments, and load balancer health.

After identifying the bottleneck, I implement corrective actions such as scaling resources, optimizing queries, tuning application performance, or rolling back problematic deployments.

---

## 7. How do you manage existing (unmanaged) AWS resources using Terraform?

### Answer

Terraform can manage existing infrastructure by importing resources into its state file.

First, I define the resource block inside the Terraform configuration.

Example:

```hcl
resource "aws_instance" "web" {
}
```

Then I import the existing resource.

```bash
terraform import aws_instance.web i-0123456789abcdef
```

After importing, I execute:

```bash
terraform plan
```

Terraform compares the imported state with the configuration.

I update the Terraform code until the plan shows **No Changes**, ensuring the imported infrastructure is fully managed without recreating resources.

---

## 8. How do you import an existing VPC into Terraform?

### Answer

First, I create the VPC resource block.

```hcl
resource "aws_vpc" "main" {
}
```

Then I import the existing VPC using its VPC ID.

```bash
terraform import aws_vpc.main vpc-xxxxxxxx
```

Next, I execute:

```bash
terraform state show aws_vpc.main
```

to review the imported attributes.

Finally, I update the Terraform configuration so that it matches the actual AWS configuration.

Running:

```bash
terraform plan
```

should eventually show **No Changes**, confirming Terraform fully manages the VPC.

---

## 9. How will you upgrade a Kubernetes cluster?

### Answer

I first review the Kubernetes release notes and verify compatibility with all applications, Helm charts, CRDs, and third-party components.

I perform the upgrade in a staging environment before production.

For managed services like Amazon EKS, I upgrade the control plane first, followed by managed node groups.

Before upgrading worker nodes, I cordon and drain each node.

```bash
kubectl cordon <node>

kubectl drain <node> --ignore-daemonsets
```

Pods are automatically rescheduled onto healthy nodes.

After upgrading each node, I uncordon it.

```bash
kubectl uncordon <node>
```

Finally, I verify cluster health, application availability, monitoring dashboards, and perform post-upgrade validation before closing the maintenance window.

---

## 10. What is a Canary Deployment?

### Answer

A Canary Deployment gradually releases a new application version to a small percentage of users while the majority continue using the stable version.

For example:

- 5% Traffic → Version 2
- 95% Traffic → Version 1

If monitoring shows healthy performance, traffic is gradually increased:

- 20%
- 50%
- 100%

If issues are detected, traffic is immediately shifted back to the stable version.

Canary deployments reduce deployment risk, enable early issue detection, and provide safer production releases.

In Kubernetes, Canary deployments are commonly implemented using Argo Rollouts, Istio, Linkerd, or NGINX Ingress with weighted traffic routing.

---

## 11. During peak traffic, if the Ingress Controller fails to route requests efficiently, how would you diagnose the issue and scale the Ingress resources effectively?

### Answer

I would begin by checking the health of the Ingress Controller Pods.

```bash
kubectl get pods -n ingress-nginx
```

Then I inspect controller logs.

```bash
kubectl logs <ingress-controller-pod> -n ingress-nginx
```

Next, I verify:

- Ingress resource configuration
- Backend Service health
- Endpoint availability
- Pod readiness
- Load Balancer health
- DNS resolution
- NGINX metrics
- CPU and Memory utilization

Using Prometheus and Grafana, I monitor request rate, response time, error rate, and controller resource utilization.

If the Ingress Controller is resource-constrained, I increase its replicas using a Deployment or configure a Horizontal Pod Autoscaler (HPA) based on CPU or custom metrics.

If worker nodes become saturated, Cluster Autoscaler provisions additional nodes automatically.

Finally, I validate traffic distribution, monitor latency, and ensure the system remains stable throughout peak traffic periods.



# DevOps Engineer Interview Questions & Answers (Infosys)

## 1. In a well-designed CI/CD pipeline for a critical banking application, is it acceptable to push code directly to production without automated testing if the developer is confident and time is limited? (True/False)

**Answer:** **False**

### Explanation

No. In banking or any mission-critical application, automated testing is mandatory. Developer confidence is never a substitute for validation. Skipping testing increases the risk of production failures, security vulnerabilities, and compliance violations.

A standard CI/CD pipeline should include:

* Unit Testing
* Integration Testing
* Security Scanning
* Code Quality Checks (SonarQube)
* Approval Gates (if required)
* Automated Deployment

Only after all quality gates pass should the application be deployed to production.

---

## 2. Can adhering strictly to the Single Responsibility Principle in large distributed systems increase overall system complexity and make maintenance more difficult? (True/False)

**Answer:** **True**

### Explanation

While the Single Responsibility Principle (SRP) improves modularity and maintainability, overusing it in distributed systems can create an excessive number of microservices. This increases deployment complexity, network communication, latency, monitoring overhead, service discovery challenges, and distributed transaction management.

The goal is to find the right balance between modularity and operational simplicity.

---

## 3. As an AWS and DevOps Senior Consultant, design a secure, scalable, and highly available architecture for a global SaaS product.

### Architecture

```
Users
   │
CloudFront
   │
AWS WAF
   │
Application Load Balancer
   │
Amazon EKS Cluster
   │
Microservices
   ├── Amazon RDS Multi-AZ
   ├── Amazon ElastiCache (Redis)
   └── Amazon S3
   │
CloudWatch + Prometheus + Grafana
   │
Route 53 (Latency-Based Routing)
```

### Security

* IAM Roles
* Security Groups
* Network ACLs
* AWS Secrets Manager
* AWS KMS Encryption
* HTTPS Everywhere
* Private Subnets
* AWS Systems Manager Session Manager (or Bastion Host)

### Scalability

* Horizontal Pod Autoscaler (HPA)
* Cluster Autoscaler
* Auto Scaling Groups
* Application Load Balancer

### High Availability

* Multi-AZ Deployment
* Cross-Region Disaster Recovery
* Route 53 Failover Routing
* RDS Read Replicas
* Automated Backups and Snapshots

---

## 4. How would you structure the failover process during a regional outage?

### Architecture

```
Primary Region
      │
Route53 Health Check
      │
Region Failure Detected
      │
Traffic Redirected
      │
Secondary Region
      │
Database Replica Promotion
      │
Application Becomes Active
```

### Key Components

* Route 53 Failover Routing
* Cross-Region RDS Replication
* S3 Cross-Region Replication
* Infrastructure Provisioning using Terraform
* Automated DNS Switching
* Continuous Backup Strategy

This minimizes Recovery Time Objective (RTO) and Recovery Point Objective (RPO).

---

## 5. Your team is experiencing frequent production outages due to inconsistent environments and manual deployments. What DevOps strategy would you implement?

### Solution

Implement a complete DevOps transformation:

* Infrastructure as Code using Terraform
* Docker for consistent environments
* Kubernetes for orchestration
* Jenkins CI/CD Pipelines
* Git Branching Strategy
* Automated Unit & Integration Testing
* Blue-Green or Canary Deployments
* Configuration Management
* Continuous Monitoring with Prometheus and Grafana
* Automated Rollback Strategy

This eliminates configuration drift, reduces manual intervention, and improves deployment reliability.

---

## 6. How would you handle resistance from team members while adopting DevOps tools and practices?

### Answer

I would:

* Listen to team concerns.
* Explain the business value of DevOps.
* Start with a small pilot project.
* Provide hands-on training.
* Create proper documentation.
* Gradually introduce automation.
* Share measurable improvements such as reduced deployment time and fewer failures.
* Encourage continuous feedback.

Successful DevOps adoption is driven by collaboration and culture rather than tools alone.

---

## 7. Design an end-to-end automated deployment solution for multiple environments.

### CI/CD Flow

```
Developer
    │
Git Push
    │
Jenkins Pipeline
    │
Build Application
    │
Unit Tests
    │
SonarQube Scan
    │
Dependency Security Scan
    │
Docker Image Build
    │
Push Image to Registry
    │
Terraform Infrastructure
    │
Deploy to DEV
    │
Automated Tests
    │
Approval Gate
    │
Deploy to UAT
    │
Approval Gate
    │
Deploy to PROD
    │
Blue-Green Deployment
    │
Smoke Testing
    │
Monitoring
```

Use separate configurations for each environment through ConfigMaps, Secrets, Helm values, and Terraform Workspaces.

---

## 8. How would you measure the success of your automation initiative?

### DORA Metrics

* Deployment Frequency
* Lead Time for Changes
* Change Failure Rate
* Mean Time to Recovery (MTTR)

### Additional KPIs

* Deployment Success Rate
* Pipeline Execution Time
* Manual Effort Reduction
* Infrastructure Provisioning Time
* Rollback Frequency
* Number of Production Incidents
* Mean Time to Detect (MTTD)

---

## 9. How do Jenkins, Docker, Kubernetes, Terraform, Prometheus, and Grafana work together in a complete CI/CD pipeline?

### Workflow

```
Developer
    │
GitHub
    │
Jenkins
    │
Build & Test
    │
SonarQube
    │
Docker Build
    │
Push to Container Registry
    │
Terraform Creates Infrastructure
    │
Deploy to Kubernetes
    │
Prometheus Collects Metrics
    │
Grafana Dashboards & Alerts
```

### Tool Responsibilities

* **Jenkins:** CI/CD Automation
* **Docker:** Containerization
* **Terraform:** Infrastructure Provisioning
* **Kubernetes:** Container Orchestration
* **Prometheus:** Metrics Collection
* **Grafana:** Visualization and Alerting

---

## 10. Design the architecture of a mission-critical platform that must scale rapidly and integrate with third-party APIs.

### Architecture

```
CloudFront
      │
AWS WAF
      │
Application Load Balancer
      │
Amazon EKS
      │
API Gateway
      │
Microservices
      ├── Amazon SQS
      ├── Amazon SNS
      ├── Redis Cache
      ├── Amazon RDS
      └── Amazon S3
      │
CloudWatch
      │
Prometheus
      │
Grafana
```

### Best Practices

* Circuit Breaker Pattern
* Retry with Exponential Backoff
* API Rate Limiting
* Dead Letter Queues
* Timeout Configuration
* API Versioning

---

## 11. How would you manage data consistency and transactions across microservices deployed in multiple Availability Zones?

### Solution

Avoid distributed database transactions.

Instead use:

* Saga Pattern
* Event-Driven Architecture
* Amazon EventBridge or Kafka
* Idempotent APIs
* Retry Mechanism
* Dead Letter Queues
* Eventual Consistency
* Distributed Tracing

Each microservice should own its own database.

---

## 12. A cloud-based e-commerce application experiences unpredictable traffic spikes. How would you ensure responsiveness and reliability?

### Solution

* Application Load Balancer
* Auto Scaling Groups
* Horizontal Pod Autoscaler (HPA)
* Cluster Autoscaler
* Amazon CloudFront CDN
* Amazon ElastiCache (Redis)
* RDS Read Replicas
* Connection Pooling
* Amazon SQS for asynchronous workloads
* Circuit Breaker Pattern
* Rate Limiting
* Multi-AZ Deployment
* Continuous Monitoring with CloudWatch and Prometheus

---

## 13. Which Amazon CloudWatch metrics and alarms would you configure to detect performance bottlenecks during high-traffic periods?

### EC2 Metrics

* CPUUtilization
* MemoryUtilization (CloudWatch Agent)
* DiskReadOps
* DiskWriteOps
* DiskQueueLength
* NetworkIn
* NetworkOut
* StatusCheckFailed

### Application Load Balancer Metrics

* RequestCount
* TargetResponseTime
* HTTPCode_ELB_5XX_Count
* HTTPCode_Target_5XX_Count
* HealthyHostCount
* UnHealthyHostCount

### Amazon RDS Metrics

* CPUUtilization
* DatabaseConnections
* FreeStorageSpace
* ReadLatency
* WriteLatency
* ReadIOPS
* WriteIOPS
* ReplicaLag

### Amazon EKS / Kubernetes Metrics

* Pod CPU Usage
* Pod Memory Usage
* Pod Restarts
* Pending Pods
* Node CPU Utilization
* Node Memory Utilization
* Node Disk Pressure

### Auto Scaling Metrics

* Desired Capacity
* In-Service Instances
* Pending Instances
* Scaling Activities

### CloudWatch Alarms

Configure alarms for:

* CPU Utilization > 80%
* Memory Utilization > 80%
* ALB Response Time > 2 seconds
* HTTP 5XX Errors > Defined Threshold
* RDS CPU > 75%
* Replica Lag > Threshold
* Unhealthy Targets > 0
* Pod Restarts Increasing
* Disk Utilization > 80%
* Network Saturation
* Failed EC2 Status Checks

Integrate CloudWatch Alarms with Amazon SNS to send notifications through email, SMS, or incident management platforms such as PagerDuty or Slack for proactive monitoring.



# INFOSYS - First Round DevOps Interview Questions (4 Years Experience)

---

## 1. Customer is unable to access the application, but he has the correct credentials. How will you debug it?

### Answer

I would troubleshoot layer by layer. First, I'd verify whether the application is up and healthy. Then I'd check the authentication service logs for login failures, validate the database connection, and confirm the user's account isn't locked or disabled. Next, I'd verify IAM/LDAP/OAuth integration if used, check application and Ingress logs for authentication errors, and inspect browser/network logs. Finally, I'd reproduce the issue in a test environment to identify the root cause before implementing a fix.

---

## 2. How do you replace a string in a file in Linux?

### Answer

I use the **sed** command for in-place replacement.

```bash
sed -i 's/old_string/new_string/g' filename
```

The `-i` option updates the file directly, and `g` replaces all occurrences of the string.

---

## 3. Your pod is getting stuck in CrashLoopBackOff, but logs show no error. How will you debug the issue?

### Answer

If logs don't show any errors, I first run `kubectl describe pod` to check Events for probe failures, OOMKilled, or image issues. Then I check the **previous container logs** using `kubectl logs --previous`, verify resource limits, ConfigMaps, Secrets, mounted volumes, and environment variables. I also check node health and kubelet logs if required. In many cases, the container exits before generating logs, so Pod Events provide the actual reason.

---

## 4. Why is the Cluster Autoscaler not scaling up even though pods are in the Pending state?

### Answer

First, I'd verify whether the Pending Pods are unschedulable due to insufficient CPU or memory. Then I'd check whether the Cluster Autoscaler is running correctly, review its logs, and ensure the Auto Scaling Group has not reached its maximum node limit. I'd also verify node selectors, taints, tolerations, resource quotas, and IAM permissions because these can prevent scaling even when Pods remain Pending.

---

## 5. What is the difference between HPA and VPA?

### Answer

**HPA (Horizontal Pod Autoscaler)** scales the **number of Pods** based on metrics like CPU or memory utilization, whereas **VPA (Vertical Pod Autoscaler)** increases or decreases the **CPU and memory allocated to an individual Pod**. HPA is generally used for stateless applications to handle traffic spikes, while VPA is more suitable for applications requiring dynamic resource allocation.

---

## 6. During peak traffic, your Ingress Controller fails to route requests efficiently. How will you diagnose the issue?

### Answer

I would first check the Ingress Controller Pods for CPU and memory utilization and verify whether they are overloaded. Then I'd inspect the Ingress Controller logs for routing errors, validate Ingress rules, backend Services, and Endpoints, and ensure all backend Pods are healthy. I'd also check the Load Balancer health checks, network latency, and scaling configuration. If required, I'd increase Ingress Controller replicas using HPA to handle the traffic.

---

## 7. How will you resolve merge conflicts in Git?

### Answer

First, I'd pull the latest changes from the target branch and identify the conflicting files. Then I'd manually resolve the conflicts by reviewing both versions of the code, remove the conflict markers, test the application, stage the resolved files using `git add`, and complete the merge with `git commit`. Finally, I'd push the updated branch and create or update the Merge Request.

---

## 8. Explain how a matrix build works in GitHub Actions.

### Answer

A matrix build allows the same workflow to run multiple jobs in parallel using different configurations. For example, the application can be tested simultaneously on multiple operating systems, programming language versions, or environments. This reduces overall execution time and ensures compatibility across different platforms without duplicating workflow code.

---

## 9. How do you import an existing VPC into Terraform?

### Answer

First, I write the Terraform resource block for the existing VPC. Then I use the `terraform import` command to associate the existing AWS VPC with the Terraform state file.

```bash
terraform import aws_vpc.main vpc-xxxxxxxx
```

After importing, I run `terraform plan` and update the Terraform configuration so it matches the actual AWS resource, ensuring there are no unexpected changes.

---

## 10. How do you integrate Jenkins with a Kubernetes cluster?

### Answer

Jenkins can be integrated with Kubernetes by installing the **Kubernetes Plugin**. The plugin connects Jenkins to the Kubernetes API using a kubeconfig file or Service Account credentials. Jenkins dynamically creates Kubernetes Pods as build agents, executes the pipeline inside those Pods, and automatically deletes them after the build completes. This provides better scalability and efficient resource utilization.

---

## 11. How can you communicate with a Jenkins server running on a Kubernetes cluster?

### Answer

Jenkins can be accessed using a Kubernetes **Service** such as NodePort, LoadBalancer, or Ingress. In production, we usually expose Jenkins through an Ingress with HTTPS enabled. Internally, other Pods communicate with Jenkins using its Kubernetes Service DNS name, while external users access it through the Ingress URL.

---

## 12. Write a Jenkins Scripted Pipeline.

### Answer

```groovy
node {

    stage('Checkout') {
        git 'https://github.com/example/demo.git'
    }

    stage('Build') {
        sh 'mvn clean package'
    }

    stage('Test') {
        sh 'mvn test'
    }

    stage('Deploy') {
        sh 'kubectl apply -f deployment.yaml'
    }
}
```

This Scripted Pipeline checks out the source code, builds the application, runs tests, and deploys it to Kubernetes.

---

## 13. Write a shell script that checks CPU, memory, and disk utilization, and alerts if any of them exceed 80%.

### Answer

```bash
#!/bin/bash

CPU=$(top -bn1 | grep "Cpu(s)" | awk '{print int($2+$4)}')

MEMORY=$(free | awk '/Mem:/ {print int($3/$2 * 100)}')

DISK=$(df -h / | awk 'NR==2 {gsub("%",""); print $5}')

if [ $CPU -gt 80 ]; then
    echo "ALERT: CPU usage is ${CPU}%"
fi

if [ $MEMORY -gt 80 ]; then
    echo "ALERT: Memory usage is ${MEMORY}%"
fi

if [ $DISK -gt 80 ]; then
    echo "ALERT: Disk usage is ${DISK}%"
fi
```

This script checks CPU, memory, and disk utilization and prints an alert whenever any resource crosses the 80% threshold. In production, these alerts can be integrated with email, Slack, or monitoring tools for automated notifications.

---


# Infosys DevOps Interview Questions & Answers (4+ Years Experience)

## Q1. How would you approach migrating a monolithic application to a microservices architecture? What steps would you follow, and what key challenges might you encounter during the migration process?

Migrating a monolithic application to microservices is a gradual process and should not be done in a single release. My approach starts with understanding the existing application architecture, business domains, dependencies, database interactions, and traffic patterns. I first identify bounded contexts and separate the application into logical services such as user management, payment, notification, authentication, and reporting. Instead of rewriting everything at once, I follow the Strangler Fig Pattern where new functionality is developed as microservices while existing functionality continues to run in the monolith.

The next step is containerization using Docker and deploying services on Kubernetes or EKS. API Gateway and Ingress are introduced to manage routing, authentication, and traffic control. CI/CD pipelines are created for each microservice to enable independent deployments. Monitoring, logging, and tracing are implemented using Prometheus, Grafana, ELK, and distributed tracing tools. Database decomposition is usually the most challenging part because monoliths often share a single database. During migration, data ownership, consistency, service communication, and distributed transactions become major concerns. Other challenges include increased operational complexity, network latency, service discovery, observability, and security management. A phased migration strategy with proper testing and monitoring minimizes risk and downtime.

---

## Q2. After deploying an application, it becomes slow. How would you troubleshoot the issue and rectify it?

When an application becomes slow after deployment, my first objective is to determine whether the issue is related to infrastructure, application code, database performance, networking, or external dependencies. I start by checking monitoring dashboards such as Grafana and CloudWatch to identify spikes in CPU, memory, disk I/O, network utilization, response times, and error rates. Next, I review deployment changes and compare them with the previous stable release.

I analyze application logs, pod logs, and APM metrics to identify bottlenecks. If the application runs on Kubernetes, I verify pod health, resource requests, limits, HPA behavior, and node utilization. Database performance is checked for slow queries, connection pool exhaustion, locks, and latency. If resource utilization is normal but response times have increased, I investigate code changes, third-party API dependencies, and caching behavior. Depending on the findings, corrective actions may include scaling resources, rolling back the deployment, tuning database queries, adjusting resource limits, fixing application code, or optimizing caching layers. Once the issue is resolved, I perform a root cause analysis and implement preventive measures to avoid recurrence.

---

## Q3. What happens if the Terraform state file stored in an Amazon S3 bucket is accidentally deleted? How would you recover it and prevent such incidents in the future?

The Terraform state file is the source of truth that maps Terraform-managed resources to actual cloud infrastructure. If the state file stored in S3 is accidentally deleted, Terraform loses track of existing resources, and future deployments may attempt to recreate or modify resources incorrectly.

In production environments, I always enable S3 versioning for Terraform state storage. If the state file is deleted, I recover it by restoring the most recent version from S3 version history. After restoration, I verify the integrity of the state file and perform a terraform plan to ensure consistency. If versioning was not enabled, recovery becomes more complex and may require rebuilding the state using terraform import commands.

To prevent such incidents, I enable S3 versioning, MFA Delete where applicable, KMS encryption, IAM least-privilege access, and CloudTrail auditing. DynamoDB state locking is also configured to prevent concurrent modifications. Regular state backups and restricted access policies significantly reduce the risk of accidental deletion.

---

## Q4. What Jenkins strategy and pipeline type did you use?

In my current project, I primarily use Jenkins Declarative Pipelines because they are easier to maintain, standardize, and review across teams. The pipeline is defined as code using Jenkinsfiles stored in Git repositories. A typical pipeline includes stages such as Source Code Checkout, Build, Unit Testing, SonarQube Analysis, Security Scanning, Docker Image Build, Image Push to ECR, Terraform Validation, Deployment to Kubernetes, Smoke Testing, and Notifications.

For execution, I use a Master-Agent architecture where build workloads run on dedicated Jenkins agents instead of the controller. Agents are dynamically provisioned when required to improve scalability and resource utilization. Shared Libraries are used to standardize reusable pipeline logic across multiple projects. For production deployments, approval gates are included before release stages. This approach improves maintainability, consistency, and governance across CI/CD workflows.

---

## Q5. What Kubernetes deployment strategy did you use in your project?

The primary deployment strategy used in my project is Rolling Update because it provides zero downtime while gradually replacing old application versions with new ones. Kubernetes ensures that a minimum number of healthy pods remain available throughout the deployment process. Readiness Probes prevent traffic from reaching pods until they are fully initialized and healthy.

For business-critical services such as payment or customer-facing applications, we also use Blue-Green and Canary deployment strategies when additional risk mitigation is required. Blue-Green deployment creates a parallel environment and shifts traffic only after validation. Canary deployment releases changes to a small percentage of users before full rollout. These strategies reduce deployment risk and allow rapid rollback if issues are detected. The choice depends on application criticality, release risk, and business requirements.

---

## Q6. What is immutable infrastructure in Terraform? How does Terraform support the concept of immutability?

Immutable infrastructure is a practice where existing infrastructure is not modified after deployment. Instead of updating resources in place, new infrastructure is created and old infrastructure is replaced. This eliminates configuration drift, ensures consistency across environments, and improves deployment reliability.

Terraform supports immutability through its declarative approach and lifecycle management capabilities. Features such as create_before_destroy allow Terraform to provision replacement resources before removing existing ones, minimizing downtime. For example, instead of modifying an EC2 instance directly, Terraform can create a new instance with updated configuration and then terminate the old one. This approach ensures predictable deployments, easier rollbacks, and improved infrastructure consistency. Immutable infrastructure is commonly combined with Auto Scaling Groups, Launch Templates, and containerized workloads to achieve highly reliable deployments.

---

## Q7. Do you maintain a single CI/CD pipeline for all environments (Development, SIT, UAT, and Production), or do you use separate pipelines? How do you manage environment-specific configurations and deployments?

In my projects, I generally maintain a single reusable CI/CD pipeline with environment-specific configurations rather than creating completely separate pipelines for each environment. The pipeline logic remains the same, but deployment behavior changes based on parameters, environment variables, configuration files, and secrets.

For example, Kubernetes deployments use different values files for Development, SIT, UAT, and Production environments. Terraform uses separate backend configurations and variable files for each environment. Secrets are stored securely in AWS Secrets Manager, HashiCorp Vault, or Kubernetes Secrets rather than being hardcoded in the pipeline. Deployment approvals are required only for higher environments such as UAT and Production.

This approach reduces duplication, improves maintainability, ensures consistency across environments, and simplifies governance. At the same time, each environment remains isolated through separate infrastructure, state files, namespaces, accounts, and access controls.



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

# 31. 𝗣𝗶𝗽𝗲𝗹𝗶𝗻𝗲 𝗽𝗮𝘀𝘀𝗲𝗱. 𝗕𝘂𝘁 𝗽𝗿𝗼𝗱𝘂𝗰𝘁𝗶𝗼𝗻 𝘀𝘁𝗶𝗹𝗹 𝗵𝗮𝘀 𝗼𝗹𝗱 𝗰𝗼𝗱𝗲. 𝗘𝘅𝗽𝗹𝗮𝗶𝗻 𝘄𝗵𝘆. 𝗬𝗼𝘂 𝗵𝗮𝘃𝗲 𝟮 𝗺𝗶𝗻𝘂𝘁𝗲𝘀.

► Deployment stage skipped — pipeline has test and build but deploy step is commented out or conditional.
  Check pipeline logs — did deploy stage actually run?

► Wrong environment — pipeline deployed to staging not production.
  Check environment variable in deploy script. Which env is set?

► CDN cache — CloudFront is serving old cached version to users.
  Fix: invalidate CloudFront cache after deployment.
  aws cloudfront create-invalidation --paths "/*"

► Health check rollback — new version failed health check silently.
  Load balancer rolled back to old version automatically.
  Check target group — which version is marked healthy?

► Wrong image tag — deployment used :latest but latest was not updated in ECR.
  Always use specific tags. Never use :latest in production.

► Blue-Green switch missed — new version deployed but traffic not switched to it.
  Check ALB listener rules — still pointing to old target group.

𝗛𝗼𝘄 𝗜 𝗺𝗮𝗸𝗲 𝗱𝗲𝗽𝗹𝗼𝘆𝗺𝗲𝗻𝘁𝘀 𝗯𝘂𝗹𝗹𝗲𝘁𝗽𝗿𝗼𝗼𝗳:
• Add smoke test step after deploy — verify new version is actually live

• Always use specific image tags — never :latest

• Add deployment verification in pipeline before marking success

• Alert on deployment events in CloudWatch
