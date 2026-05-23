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
