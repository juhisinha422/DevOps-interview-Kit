# DevOps Interview - Real Questions Asked

save this if you're preparing.

# ECS / Containers / Deployment

## 1. Difference between ECS and EKS

| ECS | EKS |
|---|---|
| AWS managed container orchestration service | Managed Kubernetes service |
| Easier to manage | More flexible and powerful |
| AWS-native | Kubernetes ecosystem |
| Less operational overhead | More control and customization |

**Answer:**  
ECS is best for simple AWS-native container deployments with low management overhead. EKS is preferred when Kubernetes features, portability, Helm charts, and advanced orchestration are required.

---

## 2. Difference between ECS Task and Service

| ECS Task | ECS Service |
|---|---|
| Single running instance | Maintains desired number of tasks |
| Used for batch/one-time jobs | Used for long-running apps |
| Stops after completion | Automatically restarts failed tasks |

**Answer:**  
An ECS Task is a running container instance from a task definition. ECS Service manages tasks, maintains desired count, supports load balancing, and auto scaling.

---

## 3. How does ECS auto scaling work?

**Answer:**  
ECS auto scaling works using CloudWatch metrics and scaling policies.

Example:
- If CPU usage > 70%, ECS increases task count.
- If CPU usage decreases, ECS scales down tasks.

It uses:
- Target tracking scaling
- Step scaling
- Scheduled scaling

---

## 4. Write a simple Dockerfile for a Python application

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

---

## 5. Container continuously crashing — how will you troubleshoot?

**Answer:**  

Steps:
1. Check container logs
2. Verify application errors
3. Check environment variables
4. Validate image version
5. Check health checks
6. Check memory/CPU limits
7. Verify dependent services
8. Exec into container if possible

Commands:

```bash
docker logs <container-id>

kubectl logs <pod-name>

kubectl describe pod <pod-name>
```

---

## 6. Docker image size is very large — how will you reduce it?

**Answer:**  

Methods:
- Use slim/alpine base image
- Use multi-stage builds
- Remove unnecessary packages
- Use `.dockerignore`
- Remove cache files
- Combine RUN commands

Example:

```dockerfile
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
```

---

## 7. Command to push Docker image to ECR

```bash
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com

docker build -t myapp .

docker tag myapp:latest <account-id>.dkr.ecr.ap-south-1.amazonaws.com/myapp:latest

docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/myapp:latest
```

---

## 8. Difference between blue-green, rolling and canary deployment

| Deployment Type | Description |
|---|---|
| Blue-Green | Two environments, switch traffic completely |
| Rolling | Gradually replaces old version |
| Canary | Releases to small % users first |

**Answer:**  
Blue-green provides fast rollback, rolling deployment minimizes downtime gradually, and canary deployment validates new releases with limited traffic before full rollout.

---

## 9. How do you configure zero-downtime deployments?

**Answer:**  

Methods:
- Rolling updates
- Load balancer health checks
- Readiness probes
- Minimum healthy instances
- Blue-green deployment

Kubernetes example:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

---

# Git / CI-CD

## 1. What branching strategy are you using?

**Answer:**  

We use feature branching strategy:
- Developers create feature branches
- Raise pull requests
- Code review happens
- Merge into develop/main after approval

---

## 2. Common Git commands you use

```bash
git clone
git pull
git push
git checkout
git branch
git merge
git rebase
git cherry-pick
git reset
git revert
git log
```

---

## 3. Have you faced merge conflicts? How did you resolve them?

**Answer:**  

Yes. I pull latest code, identify conflicting files, manually resolve conflicts, test changes, and commit resolved code.

Commands:

```bash
git pull origin main

git status

git add .

git commit
```

---

## 4. What is cherry-picking?

**Answer:**  

Cherry-picking copies a specific commit from one branch to another.

Command:

```bash
git cherry-pick <commit-id>
```

Used for:
- Hotfix migration
- Selective commit transfer

---

## 5. Explain stages in your pipeline

**Answer:**  

Typical stages:
1. Code checkout
2. Build
3. Unit testing
4. Code scanning
5. Docker image build
6. Push image to registry
7. Deploy to staging
8. Approval
9. Production deployment

---

## 6. Pipeline not triggering on feature branch — how will you troubleshoot?

**Answer:**  

Checks:
- Branch filter configuration
- Webhook configuration
- YAML syntax
- CI trigger conditions
- SCM permissions
- Jenkinsfile/GitHub Actions workflow

---

## 7. Pipeline slowing down over time — what will you do?

**Answer:**  

Solutions:
- Enable caching
- Parallel execution
- Optimize Docker layers
- Cleanup old artifacts
- Scale runners/agents
- Remove unnecessary stages

---

# Terraform / IaC

## 1. Explain Terraform file structure

**Answer:**  

Common files:
- `main.tf` → resources
- `variables.tf` → variables
- `outputs.tf` → outputs
- `providers.tf` → provider configuration
- `terraform.tfvars` → variable values

---

## 2. Terraform commands flow

```bash
terraform init

terraform fmt

terraform validate

terraform plan

terraform apply

terraform destroy
```

---

## 3. What is Terraform state locking?

**Answer:**  

State locking prevents multiple users from modifying infrastructure simultaneously.

Usually implemented using:
- S3 backend
- DynamoDB table for locking

---

## 4. How do you perform rollback in Terraform?

**Answer:**  

Rollback steps:
1. Revert Terraform code from Git
2. Run `terraform apply`
3. Restore previous state backup if required

---

## 5. Difference between count and for_each

| count | for_each |
|---|---|
| Uses numeric index | Uses key-value pair |
| Better for identical resources | Better for unique resources |

Examples:

```hcl
count = 2
```

```hcl
for_each = {
  dev = "t2.micro"
  prod = "t3.medium"
}
```

---

## 6. What are Terraform workspaces?

**Answer:**  

Terraform workspaces help manage multiple environments using same code.

Examples:
- dev
- qa
- prod

Commands:

```bash
terraform workspace new dev

terraform workspace select prod
```

---

## 7. What happens if someone manually changes Terraform-managed infrastructure?

**Answer:**  

Terraform detects configuration drift during:

```bash
terraform plan
```

It compares actual infrastructure with Terraform state and shows differences.

---

## 8. How do you manage secrets in Terraform?

**Answer:**  

Best practices:
- AWS Secrets Manager
- HashiCorp Vault
- Environment variables
- Encrypted remote backend

Avoid storing secrets directly in `.tf` files.

---

# Kubernetes

## 1. Difference between Deployment, StatefulSet and DaemonSet

| Type | Purpose |
|---|---|
| Deployment | Stateless applications |
| StatefulSet | Stateful applications |
| DaemonSet | One pod per node |

Examples:
- Deployment → frontend app
- StatefulSet → MongoDB
- DaemonSet → Fluentd

---

## 2. Kubernetes/EKS architecture basics

**Answer:**  

Main components:
- API Server
- ETCD
- Scheduler
- Controller Manager
- Worker Nodes
- Kubelet
- Kube Proxy
- Pods

Control plane manages the cluster, and worker nodes run application workloads.

---

## 3. What is RBAC in Kubernetes?

**Answer:**  

RBAC stands for Role-Based Access Control.

Used to:
- Control user permissions
- Restrict namespace access
- Define access roles

Objects:
- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding

---

# AWS / EC2 / Networking

## 1. Explain IAM least privilege with a real example

**Answer:**  

Least privilege means giving only required permissions.

Example:
A developer needing S3 read access should only get:

```json
"s3:GetObject"
```

instead of full S3 admin permissions.

---

## 2. EC2 instance showing 1/2 health checks — how will you troubleshoot?

**Answer:**  

Checks:
- Review system logs
- Check CPU/memory/disk
- Verify networking
- Check security groups/NACL
- Validate application service

Commands:

```bash
top

df -h

systemctl status nginx
```

---

## 3. Instance still unhealthy after reboot due to OS update — how will you recover?

**Answer:**  

Recovery steps:
1. Stop instance
2. Detach root EBS volume
3. Attach to healthy EC2
4. Fix filesystem/package issue
5. Reattach volume
6. Start instance

Alternative:
- Restore from snapshot/AMI

---

## 4. What are Auto Scaling lifecycle hooks?

**Answer:**  

Lifecycle hooks pause instance launch or termination to perform custom actions.

Examples:
- Configuration setup
- Log backup
- Registration scripts

---

## 5. Difference between Security Group and NACL

| Security Group | NACL |
|---|---|
| Instance level | Subnet level |
| Stateful | Stateless |
| Allow rules only | Allow and deny rules |

---

# S3 / Lambda / Python

## 1. S3 storage classes

**Answer:**  

- Standard
- Intelligent Tiering
- Standard-IA
- One Zone-IA
- Glacier
- Glacier Deep Archive

---

## 2. S3 lifecycle management

**Answer:**  

Used to:
- Move old files to cheaper storage
- Delete old objects automatically

Example:
Move logs to Glacier after 30 days.

---

## 3. Common boto3 S3 functions

```python
import boto3

s3 = boto3.client('s3')

s3.upload_file()

s3.download_file()

s3.list_objects_v2()

s3.delete_object()
```

---

# Monitoring / Troubleshooting

## 1. Recent production incident you handled

**Answer:**  

We faced high CPU usage causing application latency. I checked CloudWatch metrics, identified sudden traffic increase, scaled ECS tasks, optimized queries, and configured alerts to avoid recurrence.

---

## 2. Application down — what's your troubleshooting approach?

**Answer:**  

Approach:
1. Check alerts/logs
2. Verify infrastructure health
3. Check application logs
4. Validate database/network
5. Check recent deployments
6. Rollback if required
7. Communicate status updates

---

## 3. Difference between metrics and traces

| Metrics | Traces |
|---|---|
| Numerical monitoring data | Request flow tracking |
| CPU, memory, latency | End-to-end request visibility |

---

## 4. What happens when a container consumes all memory?

**Answer:**  

Effects:
- OOMKilled event
- Application crash
- Node instability

Solutions:
- Configure memory requests/limits
- Optimize application memory usage
- Enable autoscaling

Example:

```yaml
resources:
  limits:
    memory: "512Mi"
```


# DevOps Interview Questions & Answers (4 Years Experience)

## 1️⃣ Your pod is running but returning 503. Is it the pod or the service? How do you find out in under 2 minutes?

### Answer:

First I check whether the issue is from the application pod or Kubernetes service routing.

#### Step 1: Check Pod Status

```bash
kubectl get pods -o wide
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

* Verify pod is Running and Ready
* Check restart count
* Check readiness/liveness probe failures

#### Step 2: Check Service Endpoints

```bash
kubectl get svc
kubectl describe svc <service-name>
kubectl get endpoints <service-name>
```

If endpoints are empty, service is not connected to pods due to label mismatch or readiness failure.

#### Step 3: Test Directly

```bash
kubectl exec -it <pod-name> -- curl localhost:<port>
```

If pod responds locally but service returns 503, issue is with service, ingress, or load balancer.

#### Step 4: Check Ingress / ALB

```bash
kubectl describe ingress
```

Main things I verify:

* Label selector mismatch
* Readiness probe failure
* Wrong target port
* Ingress backend issue

---

# 2️⃣ Terraform apply failed halfway. State file is now broken. How do you recover without destroying everything?

### Answer:

First I never directly destroy infrastructure.

#### Step 1: Check State

```bash
terraform state list
terraform plan
```

#### Step 2: Pull Backup State

```bash
terraform state pull > backup.tfstate
```

If remote backend is enabled like S3, I check versioning and restore previous state version.

#### Step 3: Refresh State

```bash
terraform refresh
```

#### Step 4: Import Missing Resources

If resources exist in AWS but not in state:

```bash
terraform import aws_instance.example i-123456
```

#### Step 5: Validate Plan

```bash
terraform plan
```

I ensure only missing resources are reconciled and no production resource deletion is planned.

Main causes:

* Interrupted apply
* State lock issue
* Manual infra changes
* Backend corruption

---

# 3️⃣ Jenkins pipeline passed. But production is still on old code. What are the first 3 things you check?

### Answer:

#### 1. Check Deployment Trigger

Verify deployment stage actually executed:

```bash
kubectl rollout history deployment <deployment-name>
```

Sometimes build passes but deployment stage is skipped.

#### 2. Check Docker Image Tag

```bash
kubectl describe pod
```

I verify:

* Latest image tag used
* ImagePullPolicy
* Correct registry image

Common issue:
Using same tag like `latest` causes old image caching.

#### 3. Check Rollout Status

```bash
kubectl rollout status deployment <deployment-name>
kubectl get rs
```

Verify new ReplicaSet was created successfully.

Also check:

* ArgoCD/Helm sync issues
* Failed rollout rollback
* Jenkins deployed to wrong namespace

---

# 4️⃣ Your Docker image is 2.1 GB. Deployment is too slow. How do you reduce it to under 300 MB?

### Answer:

#### 1. Use Lightweight Base Image

Instead of:

```dockerfile
FROM ubuntu
```

Use:

```dockerfile
FROM alpine
```

or slim images.

#### 2. Multi-Stage Build

```dockerfile
FROM maven:3.9 AS build
COPY . .
RUN mvn clean package

FROM openjdk:17-jdk-slim
COPY --from=build app.jar app.jar
CMD ["java","-jar","app.jar"]
```

#### 3. Remove Unnecessary Packages

* Remove cache
* Remove temp files
* Avoid installing debugging tools

#### 4. Optimize Layers

Combine RUN commands:

```dockerfile
RUN apt update && apt install -y curl && apt clean
```

#### 5. Use .dockerignore

Exclude:

* node_modules
* git files
* logs
* test data

#### 6. Scan Large Layers

```bash
docker history <image>
```

Tools:

* dive
* docker-slim

This usually reduces Java images from 2GB to 200-300MB.

---

# 5️⃣ AWS bill doubled this month. No new resources added. How do you find the cause in under 10 minutes?

### Answer:

#### Step 1: Check Cost Explorer

I check:

* Service-wise cost increase
* Region-wise spike
* Daily trend

#### Step 2: Identify Top Resource

Usually causes:

* Data transfer spike
* NAT Gateway cost
* Unused EBS volumes
* Load balancer traffic
* CloudWatch logs
* Auto scaling issue

#### Step 3: Use AWS CLI

```bash
aws ce get-cost-and-usage
```

#### Step 4: Check Recently Modified Resources

```bash
aws cloudtrail lookup-events
```

#### Step 5: Verify Autoscaling

Sometimes pods or EC2 scaled unexpectedly due to traffic or faulty metrics.

Main real-time issues I have seen:

* Huge NAT Gateway billing
* Infinite logging
* Cross-region transfer
* Orphan EBS snapshots

---

# 6️⃣ Node is NotReady. Pods are stuck in Pending. Walk me through your exact debug steps.

### Answer:

#### Step 1: Check Node Status

```bash
kubectl get nodes
kubectl describe node <node-name>
```

#### Step 2: Verify Kubelet

SSH into node:

```bash
systemctl status kubelet
journalctl -u kubelet
```

#### Step 3: Check Disk / Memory

```bash
df -h
free -m
```

Disk pressure or memory pressure often causes NotReady.

#### Step 4: Check Container Runtime

```bash
systemctl status docker
systemctl status containerd
```

#### Step 5: Verify Networking

Check:

* CNI plugin
* Calico/Flannel pods
* DNS
* Security groups

#### Step 6: Check Pending Pods

```bash
kubectl describe pod <pod-name>
```

Common causes:

* No resources
* Taints/tolerations mismatch
* IP exhaustion
* PVC issue

---

# 7️⃣ A secret was committed to Git 2 weeks ago. What do you do right now?

### Answer:

First priority is assuming the secret is compromised.

#### Immediate Actions:

1. Rotate the secret immediately
2. Disable old credentials
3. Check access logs

#### Remove Secret from Git History

Using:

```bash
git filter-branch
```

or

```bash
BFG Repo Cleaner
```

#### Force Push Cleaned History

```bash
git push --force
```

#### Scan Entire Repo

Tools:

* trufflehog
* git-secrets
* gitleaks

#### Prevent Future Issues

Implement:

* Secret scanning in CI/CD
* Vault/Secrets Manager
* Pre-commit hooks

Main point:
Even if repo is private, leaked credentials must always be rotated.

---

# 8️⃣ Two microservices work fine alone. Together they fail. How do you debug this in your CI/CD pipeline?

### Answer:

#### Step 1: Check Service Communication

Verify:

* DNS resolution
* Service discovery
* API endpoint URLs

```bash
kubectl exec -it <pod> -- nslookup service-name
```

#### Step 2: Check Environment Variables

Usually mismatch happens in:

* URLs
* Ports
* Secrets
* Authentication tokens

#### Step 3: Verify Network Policies

```bash
kubectl get networkpolicy
```

#### Step 4: Validate Integration Testing

I add:

* Contract testing
* API integration tests
* Smoke tests in pipeline

#### Step 5: Check Logs and Tracing

Tools:

* Grafana
* Loki
* Jaeger
* ELK

Main real-time issues:

* Timeout mismatch
* SSL/TLS mismatch
* Incorrect API response format
* Authentication failure

---

# 9️⃣ Grafana shows CPU is normal. But users say app is slow. What else do you check?

### Answer:

CPU alone is not enough.

I check:

#### Memory Usage

* Memory leaks
* High heap usage
* OOM kills

#### Disk I/O

Slow database or storage latency.

#### Network Latency

* API response time
* Packet drops
* DNS delays

#### Application Metrics

* Thread count
* Connection pool
* Garbage collection
* Request queue

#### Database Performance

* Slow queries
* Locking
* High connections

#### Kubernetes Metrics

* Pod restarts
* Readiness failures
* HPA scaling
* Node pressure

Tools I use:

* Grafana
* Prometheus
* APM tools
* Jaeger tracing

---

# 1️⃣🔟 HPA is set up but pods are not scaling during traffic spike. What could be wrong?

### Answer:

#### Step 1: Verify Metrics Server

```bash
kubectl top pods
kubectl get apiservice
```

If metrics are unavailable, HPA cannot scale.

#### Step 2: Check HPA Status

```bash
kubectl describe hpa
```

#### Step 3: Verify Resource Requests

HPA works based on CPU/memory requests.

If requests are missing:

```yaml
resources:
  requests:
    cpu: "200m"
```

HPA may not work properly.

#### Step 4: Check Max Replicas

```yaml
maxReplicas: 10
```

Maybe already reached limit.

#### Step 5: Verify Cluster Autoscaler

Sometimes HPA creates pods but nodes are unavailable.

#### Step 6: Check Cooldown Period

Scaling may delay due to stabilization window.

Common real-time issues:

* Metrics server down
* Wrong target utilization
* Missing resource requests
* Pending pods due to insufficient nodes
* HPA configured on wrong deployment

---


# DevOps Interview Questions & Answers (4+ Years Experience)

---

# 1. What are the different ways to create a pipeline?

In Jenkins, pipelines can be created using two primary approaches: Declarative Pipeline and Scripted Pipeline. Declarative Pipeline is the most commonly used approach because it follows a structured syntax, is easier to maintain, and provides built-in features such as stages, post actions, parameters, and environment variables. Scripted Pipeline provides more flexibility and is written using Groovy code, making it suitable for complex workflows.

Pipelines can also be created directly through the Jenkins UI or stored as code in a Jenkinsfile within a Git repository. Storing pipelines as code is considered a best practice because it enables version control, peer review, auditing, and easier collaboration across teams. In modern DevOps environments, most organizations follow the Pipeline as Code approach where the Jenkinsfile is maintained alongside application source code.

---

# 2. What are the different sections in Declarative Pipeline?

A Declarative Pipeline consists of several predefined sections that define the CI/CD workflow. The most commonly used sections are agent, stages, steps, environment, parameters, tools, post, options, and triggers.

The agent section specifies where the pipeline will execute. Stages represent different phases of the pipeline such as Build, Test, Security Scan, Docker Build, Push, and Deploy. Each stage contains steps that execute actual commands or scripts.

The environment section defines environment variables that can be reused throughout the pipeline. Parameters allow user inputs before pipeline execution. The post section contains actions that run after pipeline completion such as notifications, cleanup tasks, or artifact archiving. Triggers can be used to automatically start pipelines based on schedules or repository events.

A typical enterprise pipeline contains stages for source code checkout, unit testing, static code analysis, Docker image creation, image scanning, deployment, and post-deployment validation.

---

# 3. What are the different agents you used?

In Jenkins, agents define where pipeline jobs execute. The most commonly used agents are any, label-based agents, Docker agents, Kubernetes agents, and specific node agents.

The any agent allows Jenkins to run the pipeline on any available executor. Label-based agents execute jobs on nodes that match specific labels such as Linux, Docker, or Kubernetes. Docker agents run pipeline stages inside Docker containers, ensuring consistent execution environments. Kubernetes agents dynamically create pods for pipeline execution, which is very common in cloud-native CI/CD environments.

In my projects, I have primarily used Kubernetes agents integrated with Jenkins because they provide dynamic scaling, better resource utilization, isolation, and faster build execution. For specialized tasks such as Terraform deployments or Docker image creation, dedicated labeled agents are often used.

---

# 4. What are the different parameter types you used?

Jenkins supports multiple parameter types to make pipelines dynamic and reusable. The commonly used parameter types include String Parameter, Choice Parameter, Boolean Parameter, Text Parameter, Password Parameter, Active Choice Parameter, and Active Choice Reactive Parameter.

String parameters are used for inputs such as application version numbers or environment names. Choice parameters provide predefined options such as DEV, UAT, or PROD. Boolean parameters allow users to enable or disable specific stages. Password parameters securely store sensitive values. Text parameters are useful for multiline input.

In enterprise CI/CD pipelines, parameters are commonly used to select deployment environments, application versions, rollback versions, AWS accounts, Kubernetes namespaces, and infrastructure configurations.

---

# 5. How do you populate value for your parameter dynamically?

Dynamic parameter population is commonly achieved using the Active Choices Plugin in Jenkins. Instead of hardcoding parameter values, Groovy scripts are used to fetch real-time data from external systems.

For example, a deployment pipeline can dynamically retrieve available Docker image tags from ECR, artifact versions from Nexus, Git branches from GitHub, or Kubernetes namespaces from a cluster. This ensures users always see updated values without modifying pipeline code.

Dynamic parameters improve automation, reduce manual errors, and provide a better user experience by displaying only valid deployment options at runtime.

---

# 6. Which parameter you are using to populate value for your parameter dynamically?

The most commonly used parameter type for dynamic value population is the Active Choice Parameter. This parameter executes a Groovy script and displays dynamically generated values in a dropdown list.

For dependent dropdowns, Active Choice Reactive Parameters are often used. For example, if a user selects an AWS account, the second parameter can dynamically display environments associated with that account. Similarly, selecting an environment can dynamically populate available application versions.

This approach is widely used in production deployment pipelines where values need to be fetched dynamically from cloud services, repositories, or deployment platforms.

---

# 7. What is the difference between Active Choice Reactive Parameter and Active Choice Reactive Reference Parameter?

An Active Choice Reactive Parameter dynamically updates its values based on selections made in another parameter. It is typically used when one dropdown depends on another dropdown.

For example, if a user selects the PROD environment, the next parameter may automatically display only production-approved application versions.

An Active Choice Reactive Reference Parameter behaves differently because it is mainly used for displaying additional information rather than passing values into the pipeline. It can display formatted HTML, tables, descriptions, or deployment details based on previous parameter selections.

In short, Reactive Parameters are used for dynamic user input selection, while Reactive Reference Parameters are used for displaying contextual information to users.

---

# 8. What is branching strategy?

A branching strategy defines how developers manage source code changes, releases, hotfixes, and feature development in a version control system.

The most commonly used strategies include Git Flow, GitHub Flow, GitLab Flow, and Trunk-Based Development.

In enterprise environments, Git Flow is widely used. It typically contains:
- Main or Master branch for production code
- Develop branch for integration testing
- Feature branches for development
- Release branches for pre-production testing
- Hotfix branches for emergency production fixes

A well-defined branching strategy improves collaboration, reduces merge conflicts, and supports controlled software releases across multiple environments.

---

# 9. In DevOps Life Cycle, where CI and CD will fit into?

Continuous Integration (CI) and Continuous Delivery/Deployment (CD) are core components of the DevOps lifecycle.

CI starts after developers commit code to a repository. It includes code checkout, compilation, unit testing, static code analysis, security scanning, and artifact generation. The goal is to continuously validate code quality and detect issues early.

CD begins after CI successfully completes. It includes artifact promotion, deployment automation, infrastructure provisioning, environment validation, application deployment, smoke testing, and production release.

In a complete DevOps lifecycle, CI bridges development and testing, while CD bridges testing and operations, enabling faster and more reliable software delivery.

---

# 10. What are the different AWS services you used?

In my projects, I have worked extensively with AWS services across compute, networking, storage, security, monitoring, and DevOps domains.

For compute, I have used EC2, Auto Scaling Groups, ECS, EKS, Lambda, and Fargate. For storage, I have used S3, EBS, and EFS. For networking, I have worked with VPC, Route 53, NAT Gateway, Internet Gateway, Load Balancers, Security Groups, and NACLs.

For security, I have used IAM, KMS, Secrets Manager, and Parameter Store. For monitoring and logging, I have worked with CloudWatch, CloudTrail, AWS Config, and X-Ray. For DevOps automation, I have experience with CodePipeline, CodeBuild, ECR, and CloudFormation along with Terraform.

Most of my production experience involves EKS, EC2, VPC, IAM, Route 53, ALB, S3, CloudWatch, and Terraform.

---

# 11. In Route 53, what do you mean by A record and CNAME record?

An A Record (Address Record) maps a domain name directly to an IPv4 address. When a user accesses a domain, DNS resolves the domain to the configured IP address.

For example, application.example.com can point directly to an EC2 instance IP address using an A record.

A CNAME (Canonical Name) Record maps one domain name to another domain name rather than an IP address. It acts as an alias. When DNS resolves the CNAME, it performs another lookup to find the final IP address.

For example, app.example.com can point to my-alb.amazonaws.com. This is commonly used with AWS Load Balancers because their IP addresses can change over time while the DNS name remains stable.

A Records point directly to IP addresses, whereas CNAME Records point to another domain name.


# 12. What is the difference between A Record and CNAME Record?

An A Record (Address Record) maps a domain name directly to an IPv4 address. When a user accesses a website, DNS resolves the domain name directly to the specified IP address. For example, if application.company.com points to 10.0.1.15, Route 53 returns that IP address to the client.

A CNAME (Canonical Name) Record maps one domain name to another domain name instead of directly mapping to an IP address. For example, app.company.com can point to my-alb-123456.ap-south-1.elb.amazonaws.com. DNS first resolves the CNAME and then resolves the final destination domain.

The main difference is that an A Record points directly to an IP address, while a CNAME points to another DNS name. In AWS environments, CNAME records are commonly used for Load Balancers because the underlying IP addresses may change, while the DNS name remains constant. Route 53 also provides Alias Records, which behave similarly to A Records but can point directly to AWS resources such as ALBs, CloudFront distributions, and S3 static websites.

---

# 13. So suppose you have a web application running and enabled HA for that. If one zone goes down, it needs to be automatically routed to the other region or availability zone. What will you do?

To achieve high availability and automatic failover, I would deploy the application across multiple Availability Zones and place them behind an Application Load Balancer. The load balancer continuously performs health checks on backend instances or pods and automatically routes traffic only to healthy targets.

If the requirement is cross-region disaster recovery, I would use Route 53 Failover Routing Policy or Route 53 Health Checks. The primary region serves traffic during normal operations, and if Route 53 detects that the primary endpoint is unhealthy, it automatically redirects traffic to the secondary region.

In Kubernetes environments, applications are deployed with multiple replicas distributed across Availability Zones using node groups and topology spread constraints. Combined with load balancers, auto scaling, health checks, and Route 53 failover routing, this architecture ensures minimal service interruption even if an entire Availability Zone or region becomes unavailable.

---

# 14. What are the limitations of Lambda functions?

AWS Lambda is a serverless compute service that eliminates infrastructure management, but it comes with certain limitations. Lambda functions have a maximum execution time of 15 minutes, making them unsuitable for long-running workloads. Memory allocation is limited and directly impacts CPU allocation.

Lambda functions may experience cold starts when invoked after inactivity, especially for large packages or VPC-enabled functions. There are deployment package size limitations, concurrency limits, and execution environment constraints. Persistent storage is not available except for temporary storage within the execution environment.

Lambda is excellent for event-driven workloads, API backends, automation, image processing, and scheduled jobs, but it is not ideal for applications requiring long-running processes, persistent connections, large compute workloads, or high-performance stateful services.

---

# 15. Write a Lambda function for any of the use cases you want?

One common use case is automatically processing files uploaded to an S3 bucket. Whenever a file is uploaded, S3 triggers a Lambda function that validates the file and logs its details.

Example Python Lambda function:

```python
import json

def lambda_handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        file_name = record['s3']['object']['key']

        print(f"New file uploaded: {file_name} in bucket {bucket}")

    return {
        'statusCode': 200,
        'body': json.dumps('File processed successfully')
    }
```

In production, similar Lambda functions are used for image resizing, log processing, automated notifications, backup validation, security checks, and triggering CI/CD workflows.

---

# 16. What are the difference between these two private and public subnet?

A public subnet is a subnet that has a route to an Internet Gateway. Resources deployed in a public subnet can communicate directly with the internet if they have public IP addresses assigned. Common examples include Application Load Balancers, Bastion Hosts, and public-facing web servers.

A private subnet does not have a direct route to an Internet Gateway. Resources inside private subnets cannot be accessed directly from the internet. Outbound internet access is typically provided through a NAT Gateway located in a public subnet.

Private subnets are commonly used for application servers, Kubernetes worker nodes, databases, caching systems, and internal services because they provide better security by preventing direct internet exposure.

---

# 17. Where will you attach your NAT Gateway?

A NAT Gateway must always be deployed inside a public subnet because it requires internet connectivity through an Internet Gateway. The NAT Gateway is assigned an Elastic IP address, allowing resources in private subnets to access external services while remaining inaccessible from the internet.

In production environments, NAT Gateways are often deployed in multiple Availability Zones to improve availability and reduce dependency on a single zone.

---

# 18. How is the traffic from private subnet to this NAT Gateway configured?

Traffic routing is controlled using Route Tables. Private subnet route tables are configured with a default route (0.0.0.0/0) pointing to the NAT Gateway.

When a resource inside the private subnet initiates an outbound request, the traffic is forwarded to the NAT Gateway. The NAT Gateway translates the private IP address into its public Elastic IP address and forwards the traffic to the internet through the Internet Gateway. Response traffic returns through the NAT Gateway and is routed back to the originating resource.

This setup enables secure outbound internet access without exposing private resources directly to inbound internet traffic.

---

# 19. What is a blue green deployment?

Blue-Green Deployment is a deployment strategy that uses two identical production environments. One environment, called Blue, serves live production traffic, while the other environment, called Green, contains the new application version.

The new version is deployed and fully validated in the Green environment without affecting users. Once testing is complete, traffic is switched from Blue to Green using a load balancer or DNS update. If any issues are detected after deployment, traffic can quickly be redirected back to Blue.

This strategy minimizes downtime, reduces deployment risk, and provides near-instant rollback capability, making it popular for mission-critical applications.

---

# 20. How is your Auto Scaling strategy working in your EKS cluster?

In EKS, scaling typically occurs at both the pod level and node level. Horizontal Pod Autoscaler (HPA) automatically increases or decreases the number of pod replicas based on metrics such as CPU, memory, or custom application metrics.

Cluster Autoscaler monitors pending pods and automatically adds worker nodes when existing nodes lack sufficient resources. When demand decreases, unused nodes are removed to optimize costs.

In production environments, HPA handles application scaling while Cluster Autoscaler handles infrastructure scaling. Together they provide elasticity, maintain application performance during traffic spikes, and optimize resource utilization during low-demand periods.

---

# 21. What is PDB?

PDB stands for PodDisruptionBudget. It is a Kubernetes resource used to ensure a minimum number of application pods remain available during voluntary disruptions such as node maintenance, cluster upgrades, or manual pod evictions.

For example, if an application has five replicas and the PDB specifies a minimum of four available pods, Kubernetes will not allow operations that reduce availability below four running pods.

PDBs are critical for maintaining high availability during infrastructure maintenance and rolling updates in production environments.

---

# 22. What are the steps you will take in upgrading your EKS cluster?

Upgrading an EKS cluster begins with reviewing AWS and Kubernetes release notes to identify deprecated APIs and compatibility concerns. I first validate application compatibility in a staging environment before touching production.

The control plane is upgraded first because AWS manages it separately. After successful control plane upgrades, worker node groups are upgraded using rolling replacement. During the process, nodes are drained gracefully to move workloads to healthy nodes.

Throughout the upgrade, I verify PodDisruptionBudgets, readiness probes, monitoring dashboards, ingress controllers, CNI plugins, and application functionality. Once node upgrades are completed, I perform end-to-end validation, monitor production metrics, and confirm cluster stability.

A rollback strategy is always prepared before starting the upgrade process.

---

# 23. What is Ingress?

Ingress is a Kubernetes resource that manages external access to services within a cluster, typically HTTP and HTTPS traffic. Instead of exposing every application using separate load balancers, Ingress provides centralized traffic routing based on hostnames, paths, or rules.

Ingress Controllers such as NGINX Ingress Controller or AWS Load Balancer Controller process Ingress resources and configure underlying load balancers accordingly.

Ingress supports SSL termination, path-based routing, host-based routing, authentication integrations, and traffic management, making it a critical component in production Kubernetes environments.

---

# 24. In ArgoCD, what are the different components available?

ArgoCD consists of several components that work together to implement GitOps-based deployments. The API Server acts as the central management component and exposes APIs for users and UI interactions. The Repository Server manages Git repositories and generates manifests from Helm, Kustomize, or plain YAML files.

The Application Controller continuously compares the desired state stored in Git with the actual state running in Kubernetes and performs synchronization when drift is detected. Redis is used for caching and improving performance. The Web UI provides visualization and management capabilities.

Together these components enable automated, declarative, and Git-driven application deployments.

---

# 25. What are the different methods to create an application in ArgoCD?

Applications in ArgoCD can be created using multiple methods. The most common approach is using the ArgoCD Web UI, where users specify repository details, cluster information, and deployment paths.

Applications can also be created using the ArgoCD CLI, Kubernetes manifests, or GitOps automation pipelines. In enterprise environments, applications are often defined declaratively using YAML manifests stored in Git repositories and automatically deployed through ApplicationSets.

ApplicationSets are particularly useful when deploying the same application across multiple clusters or environments because they automate large-scale application management.

---

# 26. In Terraform, what is a state file?

The Terraform state file is a critical component that stores metadata about infrastructure resources managed by Terraform. It maintains mappings between Terraform configurations and actual cloud resources.

Terraform uses the state file to determine which resources already exist, which resources need modification, and which resources should be created or destroyed. Without the state file, Terraform cannot accurately track infrastructure changes.

In enterprise environments, state files are typically stored remotely in S3 buckets with DynamoDB state locking to prevent concurrent modifications and state corruption.

---

# 27. What is the use of a lifecycle block in Terraform resources?

The lifecycle block controls how Terraform manages resource creation, updates, and deletion. It provides additional behavior that helps prevent downtime or accidental resource removal.

Common lifecycle settings include create_before_destroy, prevent_destroy, and ignore_changes. The create_before_destroy option creates replacement resources before deleting old resources, reducing downtime. The prevent_destroy option protects critical resources from accidental deletion. The ignore_changes option prevents Terraform from modifying specific attributes even if changes are detected.

Lifecycle blocks are frequently used for production databases, load balancers, and mission-critical infrastructure components.

---

# 28. Can you name some of the functions you used in Terraform?

Terraform provides many built-in functions for string manipulation, collection processing, conditional logic, and data transformations. Commonly used functions include concat, merge, join, split, lookup, contains, upper, lower, replace, element, length, format, jsonencode, and file.

These functions help create dynamic and reusable infrastructure code. For example, lookup is used for retrieving values from maps, join combines strings, merge combines multiple maps, and jsonencode converts Terraform objects into JSON format.

Functions significantly improve module flexibility and reduce code duplication.

---

# 29. What is the use of element?

The element function is used to retrieve a specific item from a list based on its index position. It is commonly used when distributing resources across Availability Zones or selecting values dynamically.

For example, if a list contains multiple subnet IDs, the element function can select a subnet based on an index value. This is particularly useful in multi-AZ deployments where resources must be distributed evenly across different zones.

The function helps create scalable and reusable Terraform modules.

---

# 30. What is the difference between count and for_each?

Both count and for_each are used to create multiple instances of resources, but they work differently. Count uses numeric indexes and is suitable when resources are nearly identical. Resources are referenced using index positions.

For_each uses unique keys from maps or sets and provides more stable resource management. Because resources are tracked using keys instead of indexes, modifications are easier and less likely to trigger unnecessary resource replacement.

For_each is generally preferred in production environments because it offers better readability, flexibility, and resource tracking.

---

# 31. What is Multi-stage image?

A multi-stage Docker image uses multiple build stages within a single Dockerfile. The first stage is typically used for compiling or building the application, while the final stage contains only the runtime dependencies required to run the application.

This approach significantly reduces image size by excluding build tools, source code, and temporary files from the final image. Smaller images improve deployment speed, reduce storage consumption, and enhance security.

Multi-stage builds are considered a Docker best practice for production containerization.

---

# 32. What is the difference between ENTRYPOINT and CMD?

ENTRYPOINT defines the main executable that always runs when a container starts. CMD provides default arguments to the ENTRYPOINT or acts as the default command when no ENTRYPOINT is specified.

The key difference is that ENTRYPOINT cannot be easily overridden during container startup, while CMD can be replaced by providing alternative arguments at runtime.

ENTRYPOINT is commonly used to define the primary application, while CMD supplies configurable parameters. Together they provide flexibility and predictable container behavior.

---

# 33. In Grafana how have you created dashboards?

In Grafana, dashboards are created by connecting data sources such as Prometheus, CloudWatch, Elasticsearch, Loki, or InfluxDB. After configuring the data source, panels are created using queries that retrieve metrics relevant to the application or infrastructure.

For Kubernetes environments, I typically create dashboards that display CPU utilization, memory usage, pod status, node health, disk utilization, network traffic, container restarts, and application response times. For application monitoring, dashboards include request rates, latency percentiles, error rates, database performance, and business KPIs.

I also create variables to make dashboards dynamic, allowing users to filter by cluster, namespace, application, or environment. Alerts are integrated directly into Grafana or Prometheus Alertmanager to provide proactive notification when critical thresholds are exceeded. In production environments, dashboards are version-controlled and reused across DEV, UAT, and PROD environments to ensure consistent observability.
