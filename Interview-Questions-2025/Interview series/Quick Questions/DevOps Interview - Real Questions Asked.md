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
