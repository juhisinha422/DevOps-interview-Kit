
# 🚀 DevOps Interview Series – 20 Production Scenarios

A practical collection of **real-world DevOps interview scenarios** covering AWS, Kubernetes, Docker, Terraform, CI/CD, Linux, networking, and production troubleshooting.

The answers follow a production-oriented troubleshooting approach:

> **Check First → Logs & Metrics → Commands/Tools → Root Cause → Safe Fix → Prevention**

---

# 1. A production application suddenly becomes slow. How would you troubleshoot it?

### 🔍 What I check first

I first determine whether the slowness is:

* Application-wide or affecting specific users
* Related to a recent deployment
* Related to infrastructure resources
* Related to database or external API dependencies

I would check the application's **availability, latency, error rate, and traffic**.

### 📊 Logs & Metrics

I would check:

* CloudWatch metrics
* Application logs
* ALB/NLB metrics
* CPU and memory
* Disk I/O
* Network utilization
* Database CPU/connections/latency
* Kubernetes metrics if applicable

Important metrics:

```text
Latency ↑
Error Rate ↑
CPU ↑
Memory ↑
Database Connections ↑
Request Count ↑
```

### 🛠️ Commands / Tools

```bash
top
htop
free -m
df -h
iostat
vmstat
```

For Kubernetes:

```bash
kubectl top pods
kubectl top nodes
kubectl get pods
kubectl logs <pod>
```

### 🎯 Root Cause

Possible causes could include:

* CPU/memory exhaustion
* Database bottleneck
* Increased traffic
* Slow API dependency
* Network latency
* Memory leak
* Recent bad deployment

### ✅ Safe Fix

I would first mitigate the impact:

* Scale application instances
* Increase capacity if required
* Roll back a problematic deployment
* Optimize database queries
* Resolve unhealthy targets

I would avoid making destructive changes without identifying the cause.

### 🛡️ Prevention

* CloudWatch monitoring
* Proper alerting
* Auto Scaling
* Application Performance Monitoring
* Load testing
* Capacity planning
* Production deployment monitoring

---

# 2. An EC2 instance is running, but users cannot access the application. What would you check?

### 🔍 What I check first

I verify:

```text
EC2 Status → Application → Port → Security Group → Network → DNS
```

First, I check whether the application is actually running on the EC2 instance.

### 📊 Logs & Metrics

I check:

* EC2 system status checks
* Application/service logs
* CPU and memory
* Security Group rules
* Network ACLs
* Route tables
* Load Balancer target health
* DNS records

### 🛠️ Commands

```bash
systemctl status nginx
systemctl status <application>
ss -lntp
curl localhost:8080
curl <private-ip>:8080
```

Check listening ports:

```bash
sudo ss -lntp
```

### 🎯 Root Cause

Common causes:

* Application is stopped
* Wrong port
* Security Group doesn't allow traffic
* NACL blocks traffic
* Application listens only on `127.0.0.1`
* Load Balancer target is unhealthy
* DNS points to the wrong endpoint

### ✅ Safe Fix

I would fix only the identified issue.

For example:

```text
Application listening on 8080
Security Group allows 80
```

Then either expose the correct port or configure the Load Balancer correctly.

### 🛡️ Prevention

* Health checks
* Infrastructure monitoring
* Proper Security Group design
* Automated deployment validation
* ALB target-health monitoring

---

# 3. Explain the complete CI/CD pipeline you worked with.

### 🔍 What I check first

I explain the pipeline from **code commit to production deployment**.

A typical pipeline I have worked with is:

```text
Developer
   ↓
GitHub
   ↓
Jenkins / GitHub Actions
   ↓
Build
   ↓
Unit Tests
   ↓
SonarQube
   ↓
Docker Build
   ↓
Trivy Security Scan
   ↓
Container Registry
   ↓
Kubernetes
   ↓
Argo CD / Deployment
   ↓
Production
```

### 📊 Logs & Metrics

At every stage I monitor:

* Build logs
* Test results
* SonarQube quality gate
* Docker build logs
* Security scan results
* Kubernetes deployment status
* Application health

### 🛠️ Commands / Tools

```bash
git
docker build
docker push
kubectl get pods
kubectl rollout status deployment/<name>
kubectl logs <pod>
```

### 🎯 Root Cause

If a pipeline fails, I identify the exact failed stage rather than rerunning the complete pipeline blindly.

### ✅ Safe Fix

For example:

```text
Build failed → Fix build
Test failed → Fix code/test
Security scan failed → Fix vulnerability
Deployment failed → Investigate Kubernetes
```

### 🛡️ Prevention

* Pipeline as Code
* Automated testing
* Security scanning
* Quality gates
* Deployment approvals
* Rollback strategy
* Monitoring after deployment

---

# 4. A Kubernetes Pod is stuck in CrashLoopBackOff. How would you troubleshoot it?

### 🔍 What I check first

I first inspect the Pod:

```bash
kubectl get pods
kubectl describe pod <pod-name>
```

Then I check logs:

```bash
kubectl logs <pod-name>
```

For the previous crashed container:

```bash
kubectl logs <pod-name> --previous
```

### 📊 Logs & Metrics

I check:

* Container logs
* Pod events
* Exit code
* Restart count
* CPU/memory
* Environment variables
* ConfigMaps
* Secrets
* Probes

### 🛠️ Commands

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name> --previous
kubectl get events --sort-by=.lastTimestamp
kubectl get deployment
kubectl get configmap
kubectl get secret
```

### 🎯 Root Cause

Common causes:

* Application crashes
* Incorrect environment variables
* Missing Secret/ConfigMap
* Database unavailable
* Incorrect command/entrypoint
* Failed health check
* Permission issue
* OOMKilled

### ✅ Safe Fix

I fix the underlying configuration/application issue and monitor:

```bash
kubectl rollout status deployment/<deployment>
kubectl get pods -w
```

### 🛡️ Prevention

* Proper health probes
* Resource requests/limits
* Centralized logging
* Monitoring
* Configuration validation
* Automated deployment testing

---

# 5. Deployment succeeded, but the application returns 502 Bad Gateway. What would you check?

### 🔍 What I check first

A successful deployment does not necessarily mean the application is reachable.

I check:

```text
Load Balancer
     ↓
Target
     ↓
Service
     ↓
Pod
     ↓
Application
```

### 📊 Logs & Metrics

I check:

* ALB access logs
* ALB target health
* Kubernetes Service
* Pod status
* Application logs
* Readiness probe
* Application listening port

### 🛠️ Commands

```bash
kubectl get pods
kubectl get svc
kubectl describe svc <service>
kubectl get endpoints <service>
kubectl logs <pod>
```

Inside the Pod:

```bash
kubectl exec -it <pod> -- ss -lntp
```

### 🎯 Root Cause

Typical causes:

* Wrong Service `targetPort`
* Application listening on another port
* Pod not ready
* Failed readiness probe
* No Service endpoints
* Incorrect Ingress configuration
* Load Balancer target unhealthy

### ✅ Safe Fix

I correct the Service/Ingress/application configuration and verify:

```bash
kubectl get endpoints <service>
kubectl rollout status deployment/<deployment>
```

### 🛡️ Prevention

* Automated smoke tests
* Correct readiness probes
* Deployment validation
* Monitoring target health
* Infrastructure testing

---

# 6. Someone manually deletes a Terraform-managed EC2 instance. What happens on the next `terraform apply`?

### 🔍 What happens?

Terraform compares:

```text
Terraform State
      VS
Actual Infrastructure
```

Terraform still knows about the EC2 instance in its state, but the resource no longer exists in AWS.

During:

```bash
terraform plan
```

Terraform detects that the resource is missing.

### 📊 Check

```bash
terraform plan
```

The plan will normally show that Terraform needs to **create the missing instance again** to bring infrastructure back to the declared configuration.

### 🎯 Root Cause

Manual changes created **configuration drift**.

### ✅ Safe Fix

I would not immediately apply blindly in production.

First:

```bash
terraform plan
```

Review exactly what Terraform wants to recreate.

If correct:

```bash
terraform apply
```

### 🛡️ Prevention

* Restrict manual changes
* Infrastructure as Code
* IAM permissions
* Terraform Cloud/remote workflows
* Code review
* Drift detection
* CI/CD-based Terraform execution

---

# 7. How would you manage Terraform state when multiple engineers work on the same infrastructure?

### 🔍 What I check first

I would never use separate local state files for a shared production environment.

I would use a **remote backend** with locking.

A common AWS approach is:

```text
Terraform
    ↓
S3 Remote State
    +
State Locking
```

### 📊 What I monitor

* State access
* Locking
* State versioning
* Failed Terraform operations

### 🛠️ Example

```hcl
terraform {
  backend "s3" {
    bucket = "company-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "ap-south-1"
  }
}
```

### 🎯 Problem

Without state locking, two engineers could run:

```text
Engineer A → terraform apply
Engineer B → terraform apply
```

at the same time and potentially corrupt or conflict with state changes.

### ✅ Safe Fix

Use:

* Remote state
* State locking
* State versioning
* Separate environments
* CI/CD for production changes

### 🛡️ Prevention

* Pull requests
* Code review
* Remote backend
* State locking
* Least-privilege IAM
* No local production state

---

# 8. Terraform deployment failed halfway. How would you safely recover?

### 🔍 What I check first

I would **not immediately destroy everything**.

First:

```bash
terraform plan
terraform state list
```

I check:

* What resources were successfully created?
* Which resource failed?
* Whether the state is consistent
* The exact error message

### 📊 Logs & Output

I review:

* Terraform output
* AWS resource status
* Terraform state
* CloudTrail if necessary

### 🛠️ Commands

```bash
terraform state list
terraform plan
terraform show
```

If required:

```bash
terraform state show <resource>
```

### 🎯 Root Cause

Possible causes:

* AWS API error
* IAM permission issue
* Resource quota
* Invalid configuration
* Dependency issue
* Network failure

### ✅ Safe Fix

After fixing the actual problem:

```bash
terraform plan
terraform apply
```

Terraform is designed to reconcile the current infrastructure with the desired state.

I would use `terraform state rm` or `terraform import` only when I have confirmed that the state and real infrastructure are out of sync.

### 🛡️ Prevention

* Remote state
* State locking
* Smaller Terraform modules
* Validation
* CI/CD plan stage
* Review before apply

---

# 9. A Docker container keeps restarting. How would you troubleshoot it?

### 🔍 What I check first

I check the container status:

```bash
docker ps -a
```

Then inspect logs:

```bash
docker logs <container>
```

### 📊 Logs & Metrics

I check:

* Container logs
* Exit code
* Memory usage
* CPU
* Health check
* Environment variables
* Volumes
* Container command

### 🛠️ Commands

```bash
docker inspect <container>
docker logs --tail 100 <container>
docker stats
```

Check exit code:

```bash
docker inspect <container> \
  --format='{{.State.ExitCode}}'
```

### 🎯 Root Cause

Common reasons:

* Application crashes
* Wrong CMD/ENTRYPOINT
* Missing environment variables
* Missing files
* Permission issues
* OOMKilled
* Failed health check

### ✅ Safe Fix

Identify the application/container issue, fix it, rebuild the image if required, and redeploy.

### 🛡️ Prevention

* Proper health checks
* Resource limits
* Structured logging
* Container testing
* Image scanning
* CI/CD validation

---

# 10. CMD vs ENTRYPOINT in Docker — What's the difference?

### `CMD`

`CMD` provides the **default command or arguments**.

Example:

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

It can easily be overridden when running the container.

---

### `ENTRYPOINT`

`ENTRYPOINT` defines the main executable for the container.

Example:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Running:

```bash
docker run myimage
```

results in:

```text
python app.py
```

### Key Difference

| CMD                       | ENTRYPOINT                            |
| ------------------------- | ------------------------------------- |
| Default command/arguments | Main executable                       |
| Easily overridden         | Designed to remain fixed              |
| Often used for defaults   | Often used for application executable |

### Interview Example

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

This gives flexibility to change the Python script while keeping Python as the executable.

---

# 11. ConfigMaps vs Secrets in Kubernetes — when would you use each?

### ConfigMap

Used for **non-sensitive configuration**.

Examples:

```text
APP_ENV=production
LOG_LEVEL=INFO
API_URL=https://api.example.com
```

Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
```

---

### Secret

Used for **sensitive values**.

Examples:

```text
Database password
API credentials
Tokens
Certificates
```

Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
```

### Key Point

```text
ConfigMap → Non-sensitive configuration
Secret    → Sensitive configuration
```

Secrets should also be protected using appropriate RBAC and, for stronger security, encryption at rest and an external secret-management system such as AWS Secrets Manager or a vault solution.

---

# 12. A Kubernetes Pod was OOMKilled. How would you investigate and fix it?

### 🔍 What I check first

I check:

```bash
kubectl describe pod <pod-name>
```

Look for:

```text
Reason: OOMKilled
```

Then:

```bash
kubectl top pod <pod-name>
```

### 📊 Logs & Metrics

I check:

* Memory usage
* Memory limits
* Memory requests
* Container restart count
* Application memory behavior
* Node memory pressure

### 🛠️ Commands

```bash
kubectl describe pod <pod>
kubectl top pod <pod>
kubectl top nodes
kubectl get pod <pod> -o yaml
```

### 🎯 Root Cause

Possible causes:

* Memory limit too low
* Memory leak
* Sudden traffic increase
* Inefficient application
* Large workload
* Incorrect resource configuration

### ✅ Safe Fix

If the application legitimately requires more memory, I increase the limit appropriately.

For example:

```yaml
resources:
  requests:
    memory: "512Mi"
  limits:
    memory: "1Gi"
```

But I would **not simply increase the limit** without checking for an application memory leak.

### 🛡️ Prevention

* Memory monitoring
* Resource requests/limits
* HPA
* Load testing
* Application profiling
* Alerts on memory utilization

---

# 13. How would you deploy to Kubernetes with zero/minimal downtime?

### 🔍 What I check first

I make sure the application supports rolling deployments.

I would use:

* Multiple replicas
* Readiness probes
* RollingUpdate strategy
* Proper PodDisruptionBudget where appropriate
* Load Balancer

### 📊 Metrics

Monitor:

* Error rate
* Request latency
* Pod readiness
* CPU/memory
* HTTP 4xx/5xx

### 🛠️ Example

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

Readiness probe:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 8080
```

### 🎯 Root Cause to Avoid

Downtime commonly happens when:

* Only one replica exists
* Readiness probe is missing
* New Pods receive traffic before they are ready
* Old Pods terminate too early

### ✅ Safe Fix

Use:

```text
Old Pods → Continue serving
              ↓
New Pod → Starts
              ↓
Readiness Check
              ↓
Traffic → New Pod
              ↓
Old Pod → Terminated
```

### 🛡️ Prevention

* Rolling deployments
* Health probes
* Multiple replicas
* Automated smoke tests
* Canary/blue-green deployments for critical applications
* Automated rollback

---

# 14. A Jenkins/GitHub Actions pipeline suddenly fails. How would you troubleshoot it?

### 🔍 What I check first

I identify **which stage failed**.

For example:

```text
Checkout
Build
Test
SonarQube
Docker Build
Security Scan
Push
Deploy
```

### 📊 Logs & Metrics

I inspect:

* Pipeline logs
* Runner/agent status
* Credentials
* Environment variables
* Docker registry
* Git repository
* External service availability

### 🛠️ Commands

Depending on the failed stage:

```bash
git status
docker version
docker login
docker build .
kubectl get pods
kubectl get nodes
```

### 🎯 Root Cause

Possible causes:

* Expired credentials
* Runner/agent issue
* Dependency failure
* Docker registry unavailable
* API changes
* Broken code
* Resource exhaustion

### ✅ Safe Fix

I compare the failed run with the **last successful pipeline**.

This quickly helps identify what changed.

### 🛡️ Prevention

* Pin dependencies
* Secret management
* Pipeline monitoring
* Automated testing
* Retry only transient operations
* Keep pipeline definitions in Git
* Maintain rollback options

---

# 15. How would you securely provide AWS credentials/secrets to CI/CD?

### 🔍 What I check first

I avoid hardcoding AWS credentials in:

```text
Git repositories
Dockerfiles
Jenkinsfiles
GitHub workflow files
Environment files
```

### Recommended AWS Approach

For AWS-hosted CI/CD, I prefer **IAM roles**.

For example:

```text
CI/CD
   ↓
IAM Role
   ↓
Temporary AWS Credentials
   ↓
AWS Services
```

For GitHub Actions, I would prefer **OIDC federation** so the workflow can assume an AWS IAM role without storing long-lived AWS access keys.

For Jenkins, depending on where Jenkins runs, I can use:

* IAM instance role
* IAM role for service accounts/workload identity where supported
* Jenkins credentials integration
* AWS Secrets Manager

### 🎯 Root Cause to Avoid

Long-lived access keys create a significant security risk if leaked.

### 🛡️ Prevention

* IAM least privilege
* Short-lived credentials
* OIDC
* Secrets Manager
* Credential rotation
* Secret scanning
* Never print secrets in pipeline logs

---

# 16. Linux CPU reaches 100%. Which commands and checks would you use?

### 🔍 What I check first

I identify which process is consuming CPU.

```bash
top
```

or:

```bash
htop
```

### 🛠️ Useful Commands

```bash
uptime
top
ps aux --sort=-%cpu | head
mpstat
vmstat
pidstat
```

Check load:

```bash
uptime
```

Check CPU information:

```bash
lscpu
```

### 📊 Metrics

I check:

* CPU utilization
* Load average
* Process CPU
* Number of processes
* Context switching
* I/O wait
* Memory/swap

### 🎯 Root Cause

Possible causes:

* CPU-intensive application
* Infinite loop
* Traffic spike
* Malware/process issue
* Insufficient CPU
* Background job

### ✅ Safe Fix

I identify the process before killing it.

If a known runaway process is causing the issue, I can stop/restart it safely.

For an application workload, I may scale horizontally or vertically.

### 🛡️ Prevention

* CPU alerts
* Auto Scaling
* Resource limits
* Performance monitoring
* Capacity planning

---

# 17. Production disk usage reaches 100%. What would you investigate before deleting anything?

### 🔍 What I check first

I **do not immediately delete files**.

First:

```bash
df -h
```

Then identify which filesystem is full.

```bash
du -sh /*
```

For a specific directory:

```bash
du -sh /var/*
```

Find large files:

```bash
find /var -type f -size +1G -ls
```

### 📊 What I investigate

* Application logs
* Docker images
* Docker containers
* Temporary files
* Core dumps
* Database files
* Deleted-but-open files
* Log rotation
* Backups

Check deleted files still held open:

```bash
lsof +L1
```

### 🎯 Root Cause

Common causes:

* Logs growing continuously
* Docker image/container buildup
* Missing log rotation
* Temporary files
* Application generating large files

### ✅ Safe Fix

I first determine whether the data is required.

Then:

* Rotate/compress logs
* Clean unused Docker resources
* Increase disk capacity
* Configure retention
* Move data to appropriate storage

### 🛡️ Prevention

* Log rotation
* Disk alerts
* Storage monitoring
* Retention policies
* Automated cleanup
* Capacity planning

---

# 18. What happens after a user enters a URL until the application responds?

### 🔍 Complete Request Flow

Suppose the user enters:

```text
https://example.com
```

The request generally follows:

```text
User
 ↓
DNS Resolution
 ↓
Route to Server/CDN
 ↓
Load Balancer
 ↓
Application Server
 ↓
Database / External Services
 ↓
Application Response
 ↓
Load Balancer
 ↓
User
```

### Step 1 – DNS

The browser resolves the domain using DNS.

```text
example.com
     ↓
IP Address
```

### Step 2 – Connection

The client establishes the network connection and, for HTTPS, performs TLS negotiation.

### Step 3 – Load Balancer

The request reaches a CDN and/or Load Balancer.

### Step 4 – Application

The Load Balancer forwards the request to a healthy application instance or Kubernetes Pod.

### Step 5 – Backend

The application may communicate with:

* Database
* Cache
* S3
* External APIs

### Step 6 – Response

The application generates the response and sends it back through the network to the user.

### 🛠️ Useful Tools

```bash
nslookup example.com
dig example.com
curl -v https://example.com
traceroute example.com
```

### 🛡️ Prevention / Observability

Monitor the entire request path:

```text
DNS → CDN → LB → Application → Database
```

Using metrics, logs, traces, and distributed tracing helps identify where latency or failures occur.

---

# 19. An EC2 application needs S3 access without storing AWS keys. How would you design it?

### 🔍 What I check first

I would **not create access keys and store them on the EC2 instance**.

Instead, I would attach an **IAM Role** to the EC2 instance through an instance profile.

### Recommended Architecture

```text
EC2 Application
      |
      ↓
IAM Role
      |
      ↓
Temporary Credentials
      |
      ↓
Amazon S3
```

### 🎯 IAM Policy

I would follow the principle of least privilege.

Instead of:

```text
s3:*
```

I would provide only the required permissions.

Example:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject",
    "s3:PutObject"
  ],
  "Resource": "arn:aws:s3:::my-app-bucket/*"
}
```

### 🛠️ Application

The AWS SDK automatically obtains temporary credentials from the EC2 instance's role.

No static AWS keys are required inside the application.

### 🛡️ Prevention

* IAM least privilege
* Bucket policies
* Block public access
* Encryption
* CloudTrail auditing
* Avoid long-lived credentials

---

# 20. A new deployment causes production errors. What rollback strategy would you use?

### 🔍 What I check first

I first determine whether the errors started immediately after the deployment.

I compare:

```text
Before Deployment
       VS
After Deployment
```

I check:

* Error rate
* HTTP 5xx
* Application logs
* Pod health
* CPU/memory
* Database compatibility
* Recent code/configuration changes

### 📊 Metrics & Logs

I monitor:

```text
Error Rate ↑
Latency ↑
5xx ↑
Healthy Pods ↓
```

### 🛠️ Kubernetes Rollback

First check rollout history:

```bash
kubectl rollout history deployment/<deployment>
```

Rollback:

```bash
kubectl rollout undo deployment/<deployment>
```

Monitor:

```bash
kubectl rollout status deployment/<deployment>
```

### 🎯 Root Cause

Possible causes:

* Application bug
* Configuration change
* Incompatible database migration
* Dependency issue
* Incorrect environment variable
* Infrastructure change

### ✅ Safe Fix

If the deployment is clearly responsible and production impact is significant, I would **rollback quickly first** to restore service.

Then investigate the failed release separately.

For larger systems, I would consider:

```text
Blue-Green Deployment
Canary Deployment
Rolling Deployment
```

### Example – Canary

```text
                 Load Balancer
                      |
              ┌───────┴───────┐
              ↓               ↓
          Version 1        Version 2
          95% traffic       5% traffic
                             
                       Monitor metrics
                              |
                    If healthy → increase
                    If unhealthy → rollback
```

### 🛡️ Prevention

* Automated smoke tests
* Canary deployments
* Blue-green deployments
* Health checks
* Automated rollback
* Deployment approvals
* Database backward-compatible migrations
* Monitoring and alerting

---

# 🎯 Production Troubleshooting Framework

When facing almost any DevOps production issue, I follow a structured approach:

```text
1. Understand the Impact
          ↓
2. Check What Changed
          ↓
3. Check Metrics
          ↓
4. Check Logs
          ↓
5. Reproduce / Isolate
          ↓
6. Identify Root Cause
          ↓
7. Apply the Safest Fix
          ↓
8. Verify Recovery
          ↓
9. Prevent Recurrence
```

## ⭐ Interview Tip

Instead of saying:

> "I will restart the server."

A stronger production answer is:

> "First, I would identify the scope and impact of the issue, check recent changes, review metrics and logs, isolate the root cause, and then apply the safest mitigation. After confirming recovery, I would implement monitoring or architectural changes to prevent the issue from recurring."

---

# 🧰 Important Commands Cheat Sheet

## Linux

```bash
top
htop
ps aux --sort=-%cpu
free -m
df -h
du -sh
iostat
vmstat
ss -lntp
lsof +L1
```

## Docker

```bash
docker ps
docker ps -a
docker logs <container>
docker inspect <container>
docker stats
docker images
```

## Kubernetes

```bash
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> --previous
kubectl get events
kubectl top pods
kubectl top nodes
kubectl get svc
kubectl get endpoints
kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>
kubectl rollout undo deployment/<name>
```

## Terraform

```bash
terraform init
terraform validate
terraform plan
terraform apply
terraform state list
terraform state show <resource>
terraform show
```

## AWS

```text
CloudWatch
CloudTrail
EC2
VPC
ALB/NLB
IAM
S3
RDS
Cost Explorer
Trusted Advisor
Systems Manager
AWS Config
```

---

# 🏆 Key DevOps Principle

> **Don't just fix the symptom. Find the root cause, restore service safely, and put controls in place so the same problem doesn't happen again.**

This mindset is especially important when answering **production scenario-based DevOps interviews**.



# ☁️ AWS & Terraform – Cloud Infrastructure Interview Scenarios

A collection of **real-world Cloud/DevOps interview scenarios** focused on AWS infrastructure, cost optimization, high availability, troubleshooting, and Terraform.

---

## 1. Your cloud infrastructure is becoming very expensive. How would you identify the cause?

### Answer

I would first identify **where the cost is coming from** instead of immediately reducing resources.

My approach would be:

1. Use **AWS Cost Explorer** to analyze costs by:

   * AWS Service
   * Region
   * Account
   * Environment
   * Usage Type
2. Use **Cost Allocation Tags** to identify which application or team is generating the cost.
3. Check common cost drivers:

   * Over-provisioned EC2 instances
   * Unused EBS volumes and snapshots
   * NAT Gateway data-processing charges
   * Excessive data transfer
   * Idle Load Balancers
   * Oversized RDS instances
   * Unused Elastic IPs
   * Excessive CloudWatch logs
4. Use **AWS Trusted Advisor** to identify idle or underutilized resources.
5. Review **CloudWatch metrics** such as CPU, memory, network, and request utilization.
6. After identifying the root cause, optimize using:

   * Right-sizing
   * Auto Scaling
   * Savings Plans / Reserved Instances where appropriate
   * Spot Instances for suitable workloads
   * Storage lifecycle policies
   * Removing unused resources

### Example

If EC2 costs have increased significantly, I would check whether instances are actually being utilized.

For example:

```text
EC2 Instance: m5.2xlarge
CPU Utilization: 5–10%
```

This could indicate that the instance is oversized and can potentially be moved to a smaller instance type.

### Key Point

> **First identify the cost source, then optimize it based on actual utilization.**

---

## 2. An AWS service you need is not available in your selected Region. What would you do?

### Answer

I would first verify the service's **regional availability** and determine whether the application has a strict regional dependency.

My approach would be:

1. Check whether the service is available in another AWS Region.
2. Evaluate whether we can deploy the service in another Region.
3. Check:

   * Data residency requirements
   * Compliance requirements
   * Latency
   * Data transfer costs
   * Service dependencies
4. If necessary, use a **multi-Region architecture**.
5. If the service must remain in the selected Region, evaluate an alternative AWS service or architecture.

### Example

Suppose an AWS service is unavailable in `ap-south-1`, but available in `us-east-1`.

I could:

```text
Application
    |
    +---- ap-south-1
    |
    +---- us-east-1
             |
             +---- Required AWS Service
```

I would then evaluate latency, compliance, and cross-region data-transfer costs before choosing this architecture.

### Key Point

> **I would not blindly change the Region. I would evaluate availability, compliance, latency, dependencies, and cost first.**

---

## 3. Your application has high latency for users in another country. What AWS infrastructure factors would you investigate?

### Answer

I would investigate the complete request path from the user to the application.

I would check:

1. **User location vs application Region**

   * Is the application deployed far away from the users?

2. **Amazon CloudFront**

   * Is CDN being used?
   * Are static assets cached at edge locations?

3. **Route 53**

   * Check DNS routing strategy.
   * Consider latency-based or geolocation routing if appropriate.

4. **Load Balancer**

   * Check ALB/NLB configuration and target health.

5. **Network latency**

   * Check VPC routing, NAT Gateway, Transit Gateway, VPN, etc.

6. **Backend services**

   * RDS latency
   * ElastiCache
   * External APIs
   * Cross-region calls

7. **Application architecture**

   * Check whether requests are making unnecessary cross-region calls.

### Example

If users in Europe are accessing an application hosted only in India:

```text
Europe User
     |
     | High network latency
     ↓
CloudFront
     |
     ↓
AWS Region: India
     |
     ↓
Application
```

I would consider **CloudFront** for static content and, depending on the application requirements, a multi-Region deployment.

### Key Point

> **I would identify where the latency occurs instead of assuming that the AWS Region alone is the problem.**

---

## 4. Your application is available in one AZ, but fails when that AZ becomes unavailable. What architectural problem does this indicate?

### Answer

This indicates a **single point of failure** and lack of **high availability**.

The application is dependent on only one Availability Zone.

A highly available architecture should distribute critical resources across multiple AZs.

### Current Architecture

```text
                Load Balancer
                     |
                     ↓
                  AZ-1
                     |
                Application
```

If AZ-1 fails, the application becomes unavailable.

### Improved Architecture

```text
                  ALB
                 /   \
                /     \
              AZ-1    AZ-2
               |        |
              EC2      EC2
```

I would use:

* Multi-AZ deployment
* Application Load Balancer
* Auto Scaling Group
* Multi-AZ RDS where applicable
* Replicated storage/services where required

### Key Point

> **The root problem is lack of redundancy across Availability Zones.**

---

## 5. Users report intermittent application failures. How would you determine whether the issue is infrastructure-related or application-related?

### Answer

I would troubleshoot systematically by checking the entire request path.

### Step 1 – Check Application Health

I would check:

```bash
kubectl get pods
kubectl get events
kubectl logs <pod-name>
```

For EC2-based applications, I would check application logs and service status.

### Step 2 – Check Infrastructure

I would investigate:

* EC2 health
* CPU and memory
* Disk utilization
* Network metrics
* Load Balancer health checks
* Target health
* Security Groups
* NACLs
* DNS
* Database connectivity

### Step 3 – Check AWS Monitoring

I would use:

* CloudWatch Metrics
* CloudWatch Logs
* CloudTrail
* ALB access logs
* VPC Flow Logs

### Step 4 – Correlate the Time

For example:

```text
10:00 AM → Application errors increase
10:00 AM → EC2 CPU = 95%
10:01 AM → ALB target failures increase
```

This strongly indicates an infrastructure/resource issue.

Alternatively:

```text
EC2 CPU = normal
Network = normal
ALB = healthy
Application logs = NullPointerException
```

This points more toward an application issue.

### Key Point

> **I would correlate application logs, infrastructure metrics, and timestamps before deciding where the problem exists.**

---

## 6. Your organization migrated to AWS, but infrastructure costs are higher than expected. What would you investigate?

### Answer

I would compare the **actual AWS architecture and usage** against the original migration estimates.

I would investigate:

### Compute

* EC2 instance sizing
* Idle instances
* Auto Scaling configuration
* On-demand vs Savings Plans
* Spot opportunities

### Storage

* Unused EBS volumes
* Old snapshots
* S3 storage classes
* Data retention policies

### Networking

This is an important area because unexpected costs can come from:

* NAT Gateways
* Cross-AZ traffic
* Cross-Region traffic
* Internet data transfer
* Transit Gateway

### Databases

I would check:

* RDS instance size
* Multi-AZ requirements
* Storage
* Read replicas
* Backup retention

### Monitoring

I would also check:

* CloudWatch Logs
* Metrics
* Log retention
* High-volume application logs

### Governance

I would implement:

* Cost Allocation Tags
* AWS Budgets
* Cost alerts
* Resource tagging standards
* Regular cost reviews

### Key Point

> **Migration to AWS does not automatically guarantee lower cost. The architecture and consumption model determine the actual cost.**

---

## 7. A developer says, “Cloud computing is always cheaper than on-premises infrastructure.” How would you evaluate this claim?

### Answer

I would explain that this statement is **not always true**.

Cloud provides benefits such as:

* Pay-as-you-go pricing
* Elastic scaling
* Reduced hardware management
* Faster provisioning
* Global infrastructure
* Managed services

However, cloud can become expensive if resources are poorly managed.

For example:

```text
On-Premises
    |
Fixed infrastructure cost
    |
Predictable workloads
```

versus:

```text
Cloud
    |
Pay for usage
    |
Can scale up/down
```

For a workload that runs continuously at high utilization for many years, properly optimized on-premises infrastructure could potentially be cheaper.

For highly variable workloads, cloud can provide significant economic benefits because we can scale resources according to demand.

### I would evaluate:

* Workload utilization
* Traffic patterns
* Infrastructure lifecycle
* Licensing
* Data transfer
* Storage
* Operational costs
* Staff requirements
* Availability requirements
* Long-term commitment

### Key Point

> **Cloud is not automatically cheaper; it provides flexibility and operational benefits, and cost depends on workload and architecture.**

---

# Terraform Interview Scenarios

## 8. What is the difference between `count` and `for_each` in Terraform?

### Answer

Both `count` and `for_each` are used to create multiple instances of a resource.

### `count`

`count` uses an integer value.

Example:

```hcl
resource "aws_instance" "web" {
  count = 3

  ami           = var.ami_id
  instance_type = "t3.micro"
}
```

Terraform creates:

```text
aws_instance.web[0]
aws_instance.web[1]
aws_instance.web[2]
```

It is useful when resources are essentially identical and can be managed by numeric indexes.

---

### `for_each`

`for_each` works with a map or set.

Example:

```hcl
variable "instances" {
  type = map(string)

  default = {
    dev  = "t3.micro"
    test = "t3.small"
    prod = "t3.medium"
  }
}

resource "aws_instance" "web" {
  for_each = var.instances

  ami           = var.ami_id
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

Terraform creates:

```text
aws_instance.web["dev"]
aws_instance.web["test"]
aws_instance.web["prod"]
```

### Main Difference

| Feature             | `count`           | `for_each`               |
| ------------------- | ----------------- | ------------------------ |
| Input               | Number            | Map / Set                |
| Resource addressing | Index             | Key                      |
| Example             | `[0]`, `[1]`      | `["dev"]`, `["prod"]`    |
| Best for            | Similar resources | Distinct named resources |
| Stability           | Index-based       | Key-based                |

### Interview Tip

I generally prefer **`for_each` when resources have meaningful identities**, because adding or removing an item doesn't cause the same index-shifting problems that can occur with `count`.

---

## 9. You have 100 instances in your network. How would you install or update software on all of them using Terraform/AWS automation?

### Answer

I would **not use Terraform to directly manage software installation on 100 running servers**.

Terraform is primarily an **Infrastructure as Code** tool.

For operating-system-level software management, I would use **AWS Systems Manager (SSM)**.

### Recommended Architecture

```text
                 Terraform
                     |
                     ↓
          AWS Infrastructure
                     |
                     ↓
            EC2 Instances
                     |
                     ↓
             AWS Systems Manager
                     |
          ┌──────────┴──────────┐
          ↓                     ↓
     Run Command           State Manager
          ↓                     ↓
    Install Software       Maintain State
```

### Option 1 – AWS Systems Manager Run Command

I can execute commands across multiple EC2 instances.

Example:

```bash
sudo yum update -y
sudo yum install nginx -y
```

I can target instances using:

* Instance IDs
* Tags
* Resource groups

For example:

```text
Environment = Production
```

Then SSM can target all instances with that tag.

---

### Option 2 – SSM State Manager

If I need the software to **remain installed and continuously managed**, I would use SSM State Manager.

For example:

```text
Desired State:
nginx = installed
nginx = running
```

SSM can periodically ensure that the instances remain in the desired state.

---

### Option 3 – Terraform + SSM

Terraform can provision the infrastructure and configure SSM-related resources.

For example:

```hcl
resource "aws_ssm_document" "install_nginx" {
  name          = "InstallNginx"
  document_type = "Command"

  content = <<DOC
{
  "schemaVersion": "2.2",
  "description": "Install Nginx",
  "mainSteps": [
    {
      "action": "aws:runShellScript",
      "name": "installNginx",
      "inputs": {
        "runCommand": [
          "sudo yum install -y nginx",
          "sudo systemctl enable nginx",
          "sudo systemctl start nginx"
        ]
      }
    }
  ]
}
DOC
}
```

Then I can execute the SSM document against the required instances.

### Important Consideration

All EC2 instances should have:

* SSM Agent installed
* IAM permissions through an instance profile
* Network connectivity to SSM endpoints

### Production Approach

For a large fleet, I would preferably use:

```text
Terraform
   ↓
Infrastructure
   ↓
Launch Templates / AMIs
   ↓
Auto Scaling Group
   ↓
AWS Systems Manager
   ↓
Software Configuration / Patching
```

For immutable infrastructure, I would also consider building a **golden AMI** with tools such as **EC2 Image Builder** or Packer and then rolling out the new AMI through an Auto Scaling Group.

### Key Point

> **Terraform should manage infrastructure; AWS Systems Manager, AMIs, or configuration-management tools should manage software on the instances.**



# 🚀 20 DevOps Interview Questions & Answers

## Kubernetes & Containers

### 1. Pod is Running but returning 503 — how do you debug at network, service, and ingress level?

If a Pod is in the `Running` state but the application is returning `503`, I first verify whether the application inside the Pod is actually healthy. I check the Pod logs using `kubectl logs` and test the application endpoint from inside the Pod using `curl`. Then I verify the Service configuration using `kubectl get svc` and `kubectl describe svc` and check whether the Service has healthy endpoints using `kubectl get endpoints` or `kubectl get endpointslices`. I make sure the Service selector matches the Pod labels and that the target port matches the container port. If the Service is working, I then check the Ingress configuration, ingress controller logs, backend service mapping, DNS, TLS configuration, and network policies. A 503 can occur when the ingress controller cannot reach a healthy backend, so I troubleshoot layer by layer: **Pod → Service → Endpoint → Ingress → Load Balancer → Client**.

### 2. How does Kubernetes scheduling work internally, and what are common causes of scheduling failures?

Kubernetes scheduling is mainly handled by the `kube-scheduler`. When a Pod is created, the API Server stores its desired state, and if the Pod does not have a node assigned, the scheduler evaluates available nodes. It first filters nodes that cannot run the Pod based on requirements such as CPU, memory, taints, affinity rules, and resource availability, and then scores the remaining nodes to select the most suitable one. Common scheduling failures include insufficient CPU or memory, node taints without matching tolerations, node selectors that do not match node labels, affinity or anti-affinity rules, unavailable persistent volumes, and resource quotas. I normally troubleshoot with `kubectl describe pod`, because the scheduler events usually explain why the Pod could not be scheduled.

### 3. How do you design zero-downtime deployments for stateful applications in Kubernetes?

For stateful applications, zero-downtime deployment requires more planning than a normal stateless Deployment because data consistency and application state must be protected. I generally use StatefulSets when appropriate, multiple replicas where the application supports replication, persistent volumes for durable data, readiness probes to prevent traffic from reaching an unready instance, and PodDisruptionBudgets to maintain availability during planned disruptions. For databases, I don't simply increase replicas because the database must support replication and consistency. I also use rolling or controlled upgrades, backup and restore procedures, proper storage configuration, and application-level health checks. Before production changes, I validate the upgrade in a lower environment and make sure that one healthy instance remains available while another instance is being updated.

### 4. Explain how CNI plugins work and how cross-node Pod communication happens.

A Kubernetes CNI plugin is responsible for providing network connectivity to Pods. When a Pod is created, the kubelet asks the container runtime to create the Pod sandbox, and the CNI plugin configures the Pod's network interface, IP address, routes, and connectivity according to the networking implementation. For communication between Pods on the same node, traffic can generally travel through the node's local networking components. For cross-node communication, the CNI provides the required routing or encapsulation mechanism so that traffic can move between nodes. In Amazon EKS, for example, the AWS VPC CNI assigns Pod IP addresses from the VPC networking model. When troubleshooting networking, I check Pod IPs, node routes, CNI Pods and logs, security groups, network policies, and connectivity using tools such as `kubectl exec` and `curl`.

### 5. How do you debug intermittent Pod restarts with no clear logs?

When a Pod restarts intermittently, I first check the restart count and Pod events using `kubectl get pods` and `kubectl describe pod`. I then check the previous container instance logs using `kubectl logs --previous`, because the current logs may not contain the information from the crashed container. I also check the container's termination reason and exit code to determine whether it was an application crash, `OOMKilled`, probe failure, or node-related issue. I review CPU and memory usage, resource limits, liveness and readiness probes, node conditions, and kubelet events. If the problem is still unclear, I correlate the restart time with application logs, infrastructure metrics, and monitoring data. This helps determine whether the root cause is application-level, resource-related, health-check-related, or infrastructure-related.

---

# CI/CD & Performance

### 6. CI pipeline builds 50 images and takes 20 minutes — how do you reduce it to less than 5 minutes?

If a CI pipeline builds 50 Docker images and takes around 20 minutes, I would first identify where the time is being spent rather than immediately changing the pipeline. I would enable parallel builds so independent images can be built simultaneously instead of sequentially. I would optimize Dockerfiles using multi-stage builds, smaller build contexts, proper layer ordering, and BuildKit caching. I would also reuse dependencies and Docker build cache between pipeline executions where possible. If the images are independent, I can split them into parallel jobs and only rebuild images whose source code has changed. I would also use appropriate CI runners with sufficient CPU and memory. With parallelization, caching, incremental builds, and optimized Dockerfiles, a significant reduction from 20 minutes toward the target can usually be achieved.

### 7. How do you design a secure CI/CD pipeline with integrated DevSecOps practices?

I design a secure CI/CD pipeline by adding security checks throughout the software delivery lifecycle rather than performing security only at the end. The pipeline starts with secure source-code practices and branch protection, followed by dependency and SAST scanning during the build. Docker images are scanned for vulnerabilities before being pushed to the registry, and infrastructure code such as Terraform is scanned for security misconfigurations. Secrets should never be hardcoded in Git or pipeline files; instead, I use a secure secrets-management solution and short-lived credentials wherever possible. Before deployment, I perform appropriate security and policy checks, and production deployment should require proper approvals. After deployment, I continue monitoring the application, infrastructure, and security events. The overall approach is **shift-left security combined with runtime security and continuous monitoring**.

### 8. Pipeline works in staging but fails in production — how do you systematically debug?

When a pipeline succeeds in staging but fails in production, I first compare the two environments instead of assuming the application code is the problem. I check the exact Git commit, Docker image, configuration, environment variables, secrets, IAM permissions, network connectivity, Kubernetes resources, and infrastructure differences. I verify that the same artifact tested in staging is being promoted to production rather than rebuilding it differently. I then check pipeline logs, Kubernetes events, application logs, and deployment status to identify the exact failure point. If the issue is configuration-related, I compare the relevant configuration values without exposing secrets. I also check production-specific dependencies such as databases, DNS, load balancers, security groups, and IAM permissions. Once the root cause is identified, I fix the configuration or infrastructure issue and improve the pipeline so that the same problem can be detected earlier.

---

# Cloud & Infrastructure

### 9. Design a multi-region, highly available architecture with failover and data consistency.

For a multi-region highly available architecture, I would deploy the application across at least two AWS regions and distribute traffic using a global DNS or traffic-management solution. Each region would have multiple Availability Zones, private subnets, load balancers, auto-scaling, and containerized or compute workloads. For data, the design depends on the application's consistency requirements. For example, a database can use an appropriate cross-region replication strategy, while object storage can use cross-region replication. DNS health checks or global traffic routing can redirect users from an unhealthy region to a healthy region. I would also define RPO and RTO requirements because there is always a trade-off between availability, consistency, complexity, and cost. The architecture should include automated backups, monitoring, disaster-recovery testing, and documented failover procedures rather than relying only on infrastructure redundancy.

### 10. Your AWS bill increased 3x overnight — how do you identify the root cause?

If the AWS bill suddenly increases three times overnight, I first determine which service, account, region, or resource caused the increase using AWS Cost Explorer and billing reports. I compare the current usage with the previous day's and look for unusual changes in services such as EC2, EKS, NAT Gateway, data transfer, S3, RDS, or load balancers. I then identify the specific resources responsible for the increased usage and check recent deployments, Terraform changes, autoscaling activity, and application traffic. For example, an incorrect auto-scaling configuration or large increase in NAT Gateway data processing could create unexpected costs. I also check CloudTrail and infrastructure changes to determine who or what introduced the change. After identifying the cause, I stop unnecessary resources if safe, correct the configuration, and introduce AWS Budgets, cost alerts, tagging, and monitoring to prevent similar incidents.

### 11. How do you handle infrastructure drift in Terraform across multiple teams?

I handle Terraform drift by making Terraform the source of truth for infrastructure and controlling how changes are made. I use remote state, state locking, Git-based workflows, pull requests, and CI/CD pipelines so that infrastructure changes are reviewed before being applied. I periodically run `terraform plan` to detect differences between the Terraform configuration, state, and actual infrastructure. If someone manually changes an AWS resource, Terraform should detect the difference during the next plan. Depending on the situation, I either revert the manual change through Terraform or intentionally update the Terraform configuration to represent the desired state. For multiple teams, I also separate ownership using modules, environments, and state files so that one team does not accidentally modify infrastructure owned by another team.

### 12. Terraform apply failed midway — how do you recover without corrupting state?

If `terraform apply` fails midway, I don't manually modify the state file or immediately run destructive commands. First, I identify which resources were successfully created or modified and inspect the Terraform state using `terraform state list` and `terraform show`. Terraform normally updates state as resources are successfully processed, so the state should represent the resources it knows about. I then run `terraform plan` to understand what Terraform currently thinks is different from the desired configuration. If a resource exists in AWS but is missing from state, I may need to import it using `terraform import` depending on the situation. I also verify the remote backend and locking status before retrying. After resolving the actual failure, I run `terraform plan` again and only then perform another `terraform apply`. I avoid manually editing the state file because that can introduce serious inconsistencies.

---

# Security & Secrets

### 13. How do you design secure secrets management across 100+ microservices?

For hundreds of microservices, I avoid storing secrets directly in source code, Docker images, Git repositories, or plain-text Kubernetes manifests. I would centralize secrets using a dedicated secrets-management solution such as AWS Secrets Manager or another enterprise-approved solution. Access should follow the principle of least privilege, where each workload receives only the secrets it actually requires. In Kubernetes, secrets can be integrated into workloads using appropriate mechanisms such as CSI-based secret integrations or Kubernetes Secrets with strong access controls and encryption. I also use IAM roles for workloads instead of embedding AWS access keys inside containers. Secrets should be rotated regularly, access should be audited, and production and non-production secrets should be separated. The objective is to make secrets secure, centrally managed, auditable, and automatically rotatable.

### 14. Your system passed all scans but got compromised — what security layers were missing?

If a system passes vulnerability scans but still gets compromised, I would assume that vulnerability scanning covered only part of the security model. Security requires multiple layers, including identity security, network controls, secrets protection, runtime security, access management, configuration security, monitoring, and incident response. The missing layer could be an overly permissive IAM policy, leaked credentials, an exposed service, weak authentication, compromised dependency, misconfigured cloud resource, or an attack that was not detectable through static vulnerability scanning. I would investigate logs, CloudTrail, application access logs, network activity, container activity, and authentication events to identify the attack path. After containment, I would rotate compromised credentials, patch the vulnerability if applicable, tighten IAM and network controls, improve runtime detection, and perform a post-incident review. The key lesson is that **passing a vulnerability scan does not mean the entire system is secure**.

---

# Observability & Reliability

### 15. How do you design observability — logs, metrics, and traces — for a distributed system?

For a distributed system, I design observability around three major signals: **logs, metrics, and traces**. Metrics provide numerical information about system health, such as CPU, memory, request rate, latency, and error rate. Logs provide detailed information about what happened inside an application or infrastructure component. Distributed tracing helps follow a request across multiple microservices and identify where latency or failures occur. In Kubernetes environments, I can use Prometheus for metrics, Grafana for dashboards, a centralized logging solution for logs, and OpenTelemetry-compatible tooling for traces. I also ensure that services have consistent labels, correlation IDs, structured logs, and meaningful dashboards. The objective is not simply to collect data but to quickly answer: **What is broken, where is it broken, why is it broken, and which users are affected?**

### 16. How do you implement SLO-based alerting without alert fatigue?

I start by defining meaningful Service Level Objectives based on user experience, such as availability, latency, or successful request percentage. Instead of creating alerts for every infrastructure metric, I focus alerts on conditions that indicate a real impact to the service or a high risk of violating the SLO. I use error budgets to determine how much unreliability is acceptable and prioritize alerts based on severity. For example, a temporary increase in CPU may not require an immediate page if the application is healthy, whereas a rapidly increasing error rate or SLO burn rate should trigger an alert. I also use appropriate warning and critical thresholds, deduplicate alerts, route them to the correct team, and regularly review noisy alerts. This approach keeps alerting focused on **actionable user-impacting problems rather than every metric fluctuation**.

### 17. Service latency spikes every 60 seconds — how do you debug the root cause?

If latency spikes exactly every 60 seconds, the regular interval itself is an important clue. I would first correlate the spike with application logs, metrics, traces, infrastructure metrics, and scheduled jobs. I would investigate whether a cron job, health check, database connection pool, cache refresh, garbage collection, DNS lookup, token refresh, or external API call is occurring every minute. I would also check CPU and memory usage during the spike and examine network and load-balancer metrics. Distributed tracing can help identify which downstream dependency becomes slow at that exact time. I would compare the timestamps across application and infrastructure logs to establish a clear correlation. Once the responsible component is identified, I would fix the underlying issue rather than simply increasing resources.

---

# Production & Incident Handling

### 18. Production is down — what are your first 5 steps under pressure?

When production is down, my first priority is to restore service safely while simultaneously identifying the root cause. First, I confirm the incident and determine the scope, such as whether all users or only a specific service are affected. Second, I check monitoring dashboards, alerts, load balancer health, Kubernetes status, and recent deployments to quickly identify obvious failures. Third, I communicate with the incident-response team and establish a clear owner and communication channel. Fourth, if a recent deployment is strongly correlated with the outage and rollback is safe, I consider rolling back to the last known good version. Fifth, I continue investigating logs, metrics, events, infrastructure, and dependencies while monitoring recovery. After service restoration, I perform a detailed root-cause analysis and document preventive actions so the same incident is less likely to happen again.

### 19. How do you design systems for graceful degradation during failures?

Graceful degradation means that when one component fails, the entire application should not necessarily become unavailable. I design systems with redundancy, timeouts, retries with backoff, circuit breakers, caching, queue-based processing, health checks, and appropriate fallback behavior. For example, if a recommendation service becomes unavailable, the main application can still display the core product information instead of returning an error to the user. In Kubernetes, I use multiple replicas, proper readiness and liveness probes, PodDisruptionBudgets, and appropriate autoscaling. For external dependencies, I use reasonable timeout values and avoid uncontrolled retries that can create cascading failures. The objective is to maintain the most important functionality even when non-critical components are degraded.

### 20. How do you ensure zero-downtime cluster upgrades in Kubernetes?

For zero-downtime Kubernetes upgrades, I first review the upgrade path and confirm compatibility of applications, APIs, Helm charts, controllers, CNI, CSI drivers, and other add-ons. I make sure applications have multiple replicas, correct readiness probes, appropriate PodDisruptionBudgets, and sufficient capacity to tolerate node replacement. I upgrade the control plane according to the platform's supported procedure and then upgrade worker nodes gradually rather than all at once. During node upgrades, Pods are safely drained and rescheduled onto healthy nodes. I continuously monitor node status, Pod health, application error rates, latency, and traffic. After the upgrade, I validate system components, workloads, services, ingress, networking, storage, and monitoring. The key principle is to maintain enough healthy capacity throughout the process so that application traffic continues without interruption



# Senior DevOps / SRE Interview Questions & Answers

## 𝗟𝗜𝗡𝗨𝗫 & 𝗦𝗬𝗦𝗧𝗘𝗠𝗦

### 1. How do you prove Linux OOM-killed a process?

I would first check the kernel logs because an OOM kill is recorded by the Linux kernel. Commands such as `dmesg -T | grep -i -E "oom|out of memory|killed process"` or `journalctl -k | grep -i oom` can show whether the kernel invoked the Out-Of-Memory killer and which process was terminated. I would also check memory usage using `free -h`, `vmstat`, `top`, or `ps` and compare it with historical monitoring data. In a containerized environment, I would additionally check whether the container or Pod was terminated with an `OOMKilled` reason. I would then identify whether the issue was caused by insufficient host memory, an application memory leak, excessive workload growth, or an incorrectly configured container memory limit.

---

### 2. Load avg 20 but CPU 25% — what’s the bottleneck?

A high load average with relatively low CPU utilization usually indicates that processes are waiting for something other than CPU, commonly disk I/O, network I/O, or other uninterruptible waits. I would check `top`, `vmstat`, `iostat`, and process states to determine whether many processes are in the `D` state. I would also examine disk latency, IOPS, filesystem performance, network connectivity, NFS/EBS performance, and database dependencies. A load average of 20 does not automatically mean the CPU is overloaded; it represents the number of runnable or uninterruptible tasks. Therefore, I would correlate load average with CPU utilization, I/O wait, memory pressure, and application behavior before identifying the actual bottleneck.

---

### 3. Memory leak vs cache/workload growth — how do you identify it?

I would monitor memory usage over time rather than looking at a single point in time. A memory leak generally shows continuously increasing memory consumption that does not decrease even when workload decreases or memory is released. Cache growth, on the other hand, may increase memory usage but should stabilize or be reclaimable when memory pressure occurs. I would compare application memory metrics with workload volume, request rates, cache size, garbage collection metrics, and process-level RSS usage. Tools such as `top`, `ps`, `/proc/<pid>/status`, application profilers, and APM tools can help identify the process responsible. I would also perform controlled testing by reducing traffic and observing whether memory is released. If memory continues increasing independently of workload, I would investigate a potential application memory leak.

---

### 4. What happens when file-descriptor limits are reached?

When a process reaches its file-descriptor limit, it cannot open additional files, sockets, pipes, or other file-descriptor-based resources. Applications may start producing errors such as `Too many open files`, and network connections may fail because sockets also consume file descriptors. I would verify the current limits using `ulimit -n` and inspect process usage through `/proc/<pid>/fd` or commands such as `lsof`. I would determine whether the issue is caused by an application leak, too many concurrent connections, or an unnecessarily low operating-system limit. The correct solution is not simply increasing the limit; I would first fix the underlying resource leak and then increase the appropriate system and service limits if the workload legitimately requires more descriptors.

---

### 5. IOPS normal but latency high — what do you check?

I would not assume that normal IOPS means the storage system is healthy because latency can increase even when the number of operations is within expected limits. I would check storage latency metrics, throughput, queue depth, burst credits where applicable, filesystem behavior, and individual request sizes. I would also investigate whether the application is performing synchronous or random I/O and whether there are downstream dependencies causing apparent storage latency. For AWS workloads, I would examine EBS volume metrics and instance-level limits, along with CloudWatch metrics. I would compare the affected resource against a healthy resource and correlate latency spikes with deployments, traffic patterns, database activity, or backup jobs.

---

# 𝗡𝗘𝗧𝗪𝗢𝗥𝗞𝗜𝗡𝗚

### 6. Service works in one AZ but fails in another — how do you isolate it?

I would compare the complete network path between the working and failing Availability Zones. I would check subnet route tables, security groups, Network ACLs, NAT gateways, VPC endpoints, load balancer targets, DNS resolution, and application configuration. I would test connectivity from resources in both AZs using tools such as `curl`, `nc`, `ping` where supported, `traceroute`, and DNS utilities. I would also compare VPC Flow Logs and application logs to identify whether packets are being rejected or whether the request reaches the application but fails later. If the issue is specific to one AZ, I would inspect AZ-specific resources such as NAT gateways, routes, targets, or infrastructure capacity rather than treating it as a global networking issue.

---

### 7. TCP connects but HTTP hangs — what can fail after the handshake?

A successful TCP connection only proves that the TCP connection was established; it does not prove that the HTTP application is functioning correctly. After the handshake, the server may fail to process the request because of application thread exhaustion, connection-pool exhaustion, TLS issues, proxy behavior, slow downstream services, database latency, or application-level deadlocks. I would use `curl -v`, application logs, reverse-proxy logs, packet captures where appropriate, and monitoring metrics to determine where the request stops progressing. I would also check whether the client is waiting for response headers, response data, or a downstream dependency.

---

### 8. How do you locate intermittent packet loss?

I would first establish whether packet loss occurs consistently between specific endpoints or only intermittently. I would use tools such as `ping`, `mtr`, `traceroute`, and application-level health checks from multiple locations. I would correlate packet loss with timestamps and inspect VPC Flow Logs, network-device metrics, load balancer metrics, and interface-level statistics. I would compare multiple AZs, subnets, instances, and network paths to determine whether the issue is endpoint-specific, AZ-specific, or network-path-specific. If packet loss occurs only for a particular application, I would also investigate security groups, Network ACLs, MTU mismatches, connection tracking, and application behavior.

---

### 9. Connection vs read timeout? How can retries cause a storm?

A connection timeout occurs when a client cannot establish a connection to the destination within the configured time. A read timeout occurs after the connection has been established but the client does not receive the expected response within the configured period. If many clients experience timeouts and automatically retry, the retries can increase traffic against an already unhealthy service. This creates a retry storm, which can consume threads, connections, CPU, database connections, and network resources and make the original problem worse. I would use appropriate timeout values, exponential backoff, jitter, retry limits, and circuit breakers to prevent uncontrolled retries.

---

### 10. DNS works in one region but fails in another — how do you debug?

I would first compare DNS resolution from both regions using tools such as `dig` and `nslookup`. I would check whether the records are different because of Route 53 routing policies such as latency-based, weighted, failover, or geolocation routing. I would verify Route 53 health checks, hosted-zone configuration, TTLs, resolver behavior, and regional endpoints. In private environments, I would also check Route 53 Resolver endpoints, forwarding rules, VPC DNS settings, and connectivity between the VPC and DNS infrastructure. I would compare the exact DNS response, nameserver response, and resolved IP address from both regions before investigating the application itself.

---

# 𝗞𝗨𝗕𝗘𝗥𝗡𝗘𝗧𝗘𝗦

### 11. Pods are Ready, but users see failures — where do you start?

I would start from the user-facing layer and work inward. First, I would check the load balancer, Ingress, Service, and endpoint configuration. I would verify whether the Service selector correctly matches the intended Pods and whether the Service has healthy endpoints. Then I would test the Service directly from inside the cluster and compare it with direct Pod connectivity. I would inspect HTTP status codes, application logs, Ingress/load-balancer logs, latency, and downstream dependencies. A Pod being Ready only means that its readiness condition passed; it does not guarantee that the complete user request path is functioning correctly.

---

### 12. Traffic increases 5× but HPA reacts late — why and how do you improve it?

HPA may react late because it depends on metrics collection and evaluation intervals, and CPU-based scaling may not immediately reflect request traffic. There can also be application startup time, image-pull time, scheduling delays, or insufficient cluster capacity. I would check the HPA configuration, target metrics, metrics-server availability, stabilization windows, scaling policies, Pod startup time, and Cluster Autoscaler behavior. For request-driven workloads, I could use custom or external metrics such as requests per second, queue depth, or active connections instead of relying only on CPU. I would also maintain an appropriate minimum replica count and configure scale-up policies to respond faster to large traffic increases.

---

### 13. What happens when a Kubernetes node fails?

When a node fails, the Kubernetes control plane detects that the node is no longer healthy and eventually marks it `NotReady`. Pods running on that node become unavailable. For workloads managed by Deployments, ReplicaSets, or StatefulSets, Kubernetes attempts to create replacement Pods on healthy nodes according to scheduling constraints. A Service stops routing traffic to endpoints that are no longer healthy. The exact recovery behavior depends on Pod disruption handling, workload controllers, storage, scheduling constraints, and cluster capacity. I would check node health, replacement Pod scheduling, persistent volumes, application availability, and whether sufficient capacity exists on the remaining nodes.

---

### 14. How can readiness-probe failures cause a partial outage?

A readiness probe determines whether a Pod should receive traffic. If the probe starts failing, Kubernetes removes that Pod from the Service endpoints even though the container may still be running. If enough Pods fail readiness simultaneously, the Service may have too few healthy endpoints or potentially no endpoints at all, resulting in partial or complete application failure. I would investigate the readiness endpoint, probe timeout, failure threshold, application startup behavior, dependencies, and resource pressure. I would ensure the readiness probe accurately represents the application's ability to serve traffic and avoid overly aggressive probe settings that can unnecessarily remove healthy Pods.

---

### 15. How do you reduce blast radius in a large multi-team cluster?

I would use strong workload isolation and controlled access. Namespaces can separate teams and environments, while RBAC ensures teams can access only the resources they own. ResourceQuotas and LimitRanges prevent one team from consuming excessive cluster resources. NetworkPolicies restrict unnecessary communication between workloads. Taints, tolerations, node affinity, and topology spread constraints can isolate critical workloads. PodDisruptionBudgets help protect availability during voluntary disruptions. I would also use separate node groups for workloads with different reliability or security requirements and apply admission policies to enforce standards. For highly sensitive or critical workloads, separate clusters may be preferable.

---

# 𝗖𝗜/𝗖𝗗 & 𝗥𝗘𝗟𝗘𝗔𝗦𝗘𝗦

### 16. Pipeline succeeds but users get 500s — how?

A successful pipeline only confirms that the defined CI/CD stages completed; it does not necessarily prove that the application is functioning correctly in production. I would first check whether the correct application version was deployed and whether the Kubernetes Deployment, Pods, Service, Ingress, and load balancer are healthy. I would inspect application logs and HTTP 5xx metrics and compare the failure timestamp with the deployment. I would also verify environment variables, ConfigMaps, Secrets, database connectivity, downstream APIs, and application dependencies. If the issue started immediately after deployment, I would compare the new version with the previous version and consider rolling back. I would then add post-deployment smoke tests and health checks so similar failures are detected automatically before the pipeline is marked successful.

---

### 17. Release fails during peak traffic — rollback, roll-forward, flag, or hotfix?

The decision depends on the failure type, business impact, and rollback safety. If the new release is clearly causing severe production impact and the previous version is known to be healthy, I would normally prioritize rollback. If the database schema or infrastructure changes are not backward compatible, rollback may not be safe and a roll-forward or hotfix may be required. Feature flags are useful when the problematic functionality can be disabled without reverting the entire release. During peak traffic, I would prioritize restoring service and minimizing user impact while communicating clearly with stakeholders. After stabilization, I would perform RCA and improve the deployment strategy.

---

### 18. CI fails 1/20 runs — how do you find the cause?

I would first identify whether the failures correlate with a particular runner, node, branch, dependency, time period, or stage. I would collect logs from successful and failed executions and compare them. I would investigate flaky tests, network dependencies, external services, resource exhaustion, race conditions, cache corruption, package repository availability, and runner health. If the same commit succeeds repeatedly but occasionally fails, that strongly suggests nondeterministic behavior rather than a code defect alone. I would reproduce the failure, isolate the unreliable component, and fix the underlying cause instead of simply adding automatic retries. Retries can be used carefully as a temporary mitigation for genuinely transient failures.

---

### 19. How would you design multi-region CI/CD with minimal impact?

I would keep the build process consistent while allowing deployment to each region independently. Application artifacts should be immutable and stored in a reliable artifact repository or replicated registry. The pipeline would deploy first to a lower-risk region or a small percentage of traffic, perform automated validation, and then progressively deploy to other regions. Infrastructure would be managed through Terraform or another IaC solution with separate state and environment boundaries. The deployment strategy should support rollback at the regional level so a failed deployment does not affect all regions. Health checks, observability, DNS or global load-balancing controls, and automated rollback would be integrated into the pipeline.

---

### 20. How would you validate an untested rollback?

I would never rely on a rollback procedure that has never been tested in an environment where it can be safely validated. I would reproduce the production architecture in a lower environment and intentionally deploy a known-bad version. Then I would execute the rollback procedure and verify application functionality, database compatibility, traffic routing, configuration, and monitoring. For Kubernetes, I would validate Deployment or Helm rollback behavior and ensure the previous image remains available. I would also test database rollback compatibility because application rollback can fail if schema changes are irreversible. Afterward, I would document the procedure and periodically test it through controlled production exercises or disaster-recovery drills.

---

# 𝗧𝗘𝗥𝗥𝗔𝗙𝗢𝗥𝗠 / 𝗜𝗮𝗖

### 21. How can Terraform plan succeed but apply cause an outage?

`terraform plan` is based on the configuration, current state, and information available during planning, but the real infrastructure can change between plan and apply. External changes, provider behavior, unknown values, race conditions, dependencies, or changes made by other systems can cause the actual apply to behave differently. A plan can also show a technically valid change that is operationally risky, such as replacing a production resource. I would use a reviewed and saved plan for production, protect critical resources, validate dependencies, and use appropriate lifecycle rules. I would also perform changes in stages and monitor the infrastructure during the apply.

---

### 22. How do you safely recover corrupted Terraform state?

I would first stop all Terraform operations to prevent further state changes. Then I would identify whether the state is actually corrupted or simply out of sync with the infrastructure. If using a remote backend, I would check state versioning and backups and restore the most recent known-good state. I would inspect the restored state and compare it with the real infrastructure before running another apply. If necessary, resources can be re-imported into Terraform state. I would avoid manually editing state unless absolutely necessary and would use Terraform state commands carefully. After recovery, I would investigate why the corruption occurred and improve backend access controls, locking, backup, and versioning.

---

### 23. How do you handle Terraform drift?

Terraform drift occurs when infrastructure changes outside Terraform and the real infrastructure no longer matches the Terraform state/configuration. I would detect drift through `terraform plan`, refresh-related operations, monitoring, or scheduled drift detection. I would then determine whether the manual change was intentional or unauthorized. If Terraform should remain the source of truth, I would update the Terraform configuration to represent the desired state and apply it. If the external change should be preserved, I may update the Terraform configuration or import the resource state as appropriate. For production environments, I would reduce manual changes through access controls, IaC policies, and automated drift detection.

---

### 24. How do you safely break-change a shared Terraform module?

I would avoid directly changing the module in a way that unexpectedly breaks existing consumers. First, I would identify all module consumers and understand the impact of the proposed change. I would introduce a new module version with clear versioning and document the breaking changes. Existing environments would continue using the current version while environments are migrated gradually. I would test the new version in development and staging before production. I would also use automated Terraform validation, plan reviews, and CI checks. Once all consumers are migrated successfully, the old version can eventually be deprecated.

---

### 25. How do you prevent accidental destruction of critical resources?

I would use multiple layers of protection. Terraform lifecycle rules such as `prevent_destroy = true` can protect especially critical resources. Production plans should require peer review and approval before apply. Remote state should have locking, versioning, and access controls. I would separate production state from lower environments and restrict who can execute production changes. Before applying destructive



# 🔍𝗗𝗲𝘃𝗢𝗽𝘀 𝗜𝗻𝘁𝗲𝗿𝘃𝗶𝗲𝘄 𝗤𝘂𝗲𝘀𝘁𝗶𝗼𝗻𝘀 𝗘𝘅𝗽𝗹𝗮𝗶𝗻𝗲𝗱 — 𝗢𝗻𝗲 𝗮𝘁 𝗮 𝗧𝗶𝗺𝗲!

𝗦𝗰𝗲𝗻𝗮𝗿𝗶𝗼 : 𝗔 𝘁𝗲𝗮𝗺𝗺𝗮𝘁𝗲 𝗮𝗰𝗰𝗶𝗱𝗲𝗻𝘁𝗮𝗹𝗹𝘆 𝗽𝘂𝘀𝗵𝗲𝗱 𝗞𝘂𝗯𝗲𝗿𝗻𝗲𝘁𝗲𝘀 𝘀𝗲𝗰𝗿𝗲𝘁𝘀 𝘁𝗼 𝗚𝗶𝘁 ? 𝗛𝗼𝘄 𝘄𝗶𝗹𝗹 𝘆𝗼𝘂 𝘁𝗮𝗰𝗸𝗹𝗲 𝘁𝗵𝗶𝘀 𝗶𝘀𝘀𝘂𝗲 ?

 ```
𝗦𝘁𝗲𝗽 𝟭: 𝗔𝘀𝘀𝗲𝘀𝘀 𝘁𝗵𝗲 𝗘𝘅𝗽𝗼𝘀𝘂𝗿𝗲 :
 1. Was the repository public or private?
 2. What kind of secret was exposed?
 • AWS Access Key
 • Kubernetes Secret manifest
 • Database password
 • API token
 • SSH private key
 3. Has anyone cloned or forked the repository?
 4. How long has the secret been exposed?
This helps determine the severity of the incident.

𝗦𝘁𝗲𝗽 𝟮: 𝗥𝗼𝘁𝗮𝘁𝗲 𝘁𝗵𝗲 𝗦𝗲𝗰𝗿𝗲𝘁 𝗜𝗺𝗺𝗲𝗱𝗶𝗮𝘁𝗲𝗹𝘆 (𝗛𝗶𝗴𝗵𝗲𝘀𝘁 𝗣𝗿𝗶𝗼𝗿𝗶𝘁𝘆)
* If an AWS Access Key was exposed:
 • Create a new Access Key.
 • Update applications to use the new key.
 • Disable and delete the compromised key.
* If it's a Kubernetes Secret containing API credentials:
 • Generate new credentials.
 • Update the Secret object.
 • Redeploy workloads if required.

𝗦𝘁𝗲𝗽 𝟯: 𝗥𝗲𝗺𝗼𝘃𝗲 𝘁𝗵𝗲 𝗦𝗲𝗰𝗿𝗲𝘁 𝗳𝗿𝗼𝗺 𝗚𝗶𝘁 :
If the secret has already been pushed, the secret should be removed from the repository history using tools designed for history rewriting, such as:
𝗴𝗶𝘁 𝗳𝗶𝗹𝘁𝗲𝗿-𝗿𝗲𝗽𝗼 (𝗿𝗲𝗰𝗼𝗺𝗺𝗲𝗻𝗱𝗲𝗱)
𝗕𝗙𝗚 𝗥𝗲𝗽𝗼-𝗖𝗹𝗲𝗮𝗻𝗲𝗿
After rewriting history: 𝗴𝗶𝘁 𝗽𝘂𝘀𝗵 --𝗳𝗼𝗿𝗰𝗲 (Be aware that collaborators will need to synchronize their local clones because history has changed.)

𝗦𝘁𝗲𝗽 𝟰: 𝗖𝗵𝗲𝗰𝗸 𝗥𝗲𝗽𝗼𝘀𝗶𝘁𝗼𝗿𝘆 𝗛𝗶𝘀𝘁𝗼𝗿𝘆 : 
Search the repository to ensure the secret doesn't exist elsewhere by using 𝗴𝗶𝘁 𝗹𝗼𝗴 command.

𝗦𝘁𝗲𝗽 𝟱: 𝗡𝗼𝘁𝗶𝗳𝘆 𝘁𝗵𝗲 𝗧𝗲𝗮𝗺

𝗦𝘁𝗲𝗽 𝟲: 𝗣𝗿𝗲𝘃𝗲𝗻𝘁 𝗜𝘁 𝗳𝗿𝗼𝗺 𝗛𝗮𝗽𝗽𝗲𝗻𝗶𝗻𝗴 𝗔𝗴𝗮𝗶𝗻 (𝗠𝗼𝘀𝘁 𝗜𝗺𝗽𝗼𝗿𝘁𝗮𝗻𝘁)
* 𝗨𝘀𝗲 𝗚𝗶𝘁 𝗽𝗿𝗲-𝗰𝗼𝗺𝗺𝗶𝘁 𝗵𝗼𝗼𝗸𝘀 and tools like 𝗚𝗶𝘁𝗹𝗲𝗮𝗸𝘀, 𝗚𝗶𝘁 𝗦𝗲𝗰𝗿𝗲𝘁𝘀 to block commits containing secrets.
* 𝗨𝘀𝗲 𝗮 𝗦𝗲𝗰𝗿𝗲𝘁 𝗠𝗮𝗻𝗮𝗴𝗲𝗿 : Instead of storing secrets in Git,use AWS Secrets Manager , AWS Systems Manager Parameter Store,HashiCorp Vault or External Secrets Operator for Kubernetes
* 𝗖𝗜/𝗖𝗗 𝗦𝗲𝗰𝗿𝗲𝘁 𝗦𝗰𝗮𝗻𝗻𝗶𝗻𝗴 :Run secret scanning during pull requests.
```

# Docker, Kubernetes, Terraform, CI/CD, Linux & Networking Interview Questions
## Senior DevOps Interview Preparation (Part 1)

---

# 🐳 DOCKER & CONTAINERS

# 1. What is the actual difference between ADD and COPY in a Dockerfile?

## Answer

Both `ADD` and `COPY` are Dockerfile instructions used to copy files from the host into the Docker image, but `ADD` provides additional functionality.

### COPY

- Simply copies files or directories from the local system to the image.
- Preferred for most use cases because it is predictable and secure.
- Does **not** extract archives or download files from URLs.

Example:

```dockerfile
COPY app.jar /app/
```

---

### ADD

In addition to copying files, `ADD` can:

- Automatically extract local `.tar` archives.
- Download files from remote URLs (although this is generally discouraged).

Example:

```dockerfile
ADD app.tar.gz /opt/
```

Docker automatically extracts `app.tar.gz` into `/opt`.

---

### Comparison

| Feature | COPY | ADD |
|----------|------|-----|
| Copy local files | ✅ | ✅ |
| Extract local tar files | ❌ | ✅ |
| Download remote URLs | ❌ | ✅ |
| Recommended for most cases | ✅ | ❌ |

---

### Best Practice

Use **COPY** unless you specifically need automatic archive extraction.

---

### Production Interview Answer

> I prefer `COPY` because it performs only file copying, making Dockerfiles more predictable and secure. I use `ADD` only when I intentionally need automatic extraction of local tar archives. Downloading files with `ADD` is generally avoided in production because it reduces build reproducibility.

---

# 2. How do multi-stage builds help reduce production image sizes?

## Answer

Multi-stage builds allow us to separate the build environment from the runtime environment.

Instead of shipping the entire build toolchain (Maven, Gradle, Node.js, compilers), we copy only the compiled application into a lightweight runtime image.

---

### Without Multi-Stage Build

```
Ubuntu
↓

JDK

↓

Maven

↓

Source Code

↓

Dependencies

↓

Application
```

Final image may exceed **800 MB**.

---

### With Multi-Stage Build

```
Stage 1

Maven

↓

Compile Application

↓

Generate JAR

------------------------

Stage 2

OpenJDK Slim

↓

Copy JAR Only

↓

Run Application
```

Final image may be **150–250 MB**.

---

### Benefits

- Smaller image size
- Faster deployments
- Reduced attack surface
- Faster image pull times
- Lower storage costs

---

### Example

```dockerfile
# Build Stage
FROM maven:3.9-eclipse-temurin-17 AS builder

WORKDIR /app

COPY . .

RUN mvn clean package

# Runtime Stage
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY --from=builder /app/target/app.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
```

---

### Production Interview Answer

> Multi-stage builds separate the build and runtime environments. I compile the application in the first stage using build tools such as Maven, then copy only the generated artifact into a lightweight runtime image. This significantly reduces image size, improves security, and speeds up deployments.

---

# 3. Which two Linux kernel mechanisms are responsible for container isolation and resource limits?

## Answer

Containers rely primarily on two Linux kernel features:

### 1. Namespaces

Namespaces provide **isolation**.

Each container gets its own:

- Process IDs (PID)
- Network stack
- Mount points
- Hostname
- IPC
- Users

Example:

```
Container A

PID 1

Container B

PID 1
```

Both containers think they are running their own PID 1 process.

---

### 2. Control Groups (cgroups)

cgroups control **resource allocation**.

They limit:

- CPU
- Memory
- Disk I/O
- Network bandwidth
- Processes

Example:

```yaml
resources:
  limits:
    cpu: "2"
    memory: "4Gi"
```

The kernel enforces these limits.

---

### Production Interview Answer

> Containers use Linux namespaces for process and network isolation, while cgroups enforce resource limits such as CPU and memory. Together, they provide secure isolation and controlled resource usage without requiring full virtual machines.

---

# 4. What is the difference between CMD and ENTRYPOINT?

## Answer

Both define the default command executed when a container starts.

---

### CMD

Provides a default command that users can easily override.

Example:

```dockerfile
CMD ["nginx","-g","daemon off;"]
```

Override:

```bash
docker run image ls
```

The `ls` command replaces the CMD.

---

### ENTRYPOINT

Defines the main executable.

Example:

```dockerfile
ENTRYPOINT ["python"]
```

Running:

```bash
docker run image app.py
```

Executes:

```
python app.py
```

---

### CMD + ENTRYPOINT Together

```dockerfile
ENTRYPOINT ["python"]

CMD ["app.py"]
```

Default:

```
python app.py
```

Override:

```bash
docker run image test.py
```

Executes:

```
python test.py
```

---

### Comparison

| Feature | CMD | ENTRYPOINT |
|----------|-----|------------|
| Default command | ✅ | ❌ |
| Main executable | ❌ | ✅ |
| Easily overridden | ✅ | ❌ |

---

### Production Interview Answer

> `ENTRYPOINT` defines the main executable that always runs, while `CMD` provides default arguments or commands that can be overridden. In production, I often use `ENTRYPOINT` for the application binary and `CMD` for default runtime arguments.

---

# ☸️ KUBERNETES

# 5. What is the core difference between a ClusterIP and a NodePort Service?

## Answer

### ClusterIP

- Default Service type.
- Accessible only within the Kubernetes cluster.
- Used for internal service-to-service communication.

Example:

```
Frontend

↓

Backend Service

↓

Database
```

---

### NodePort

- Exposes the Service on a static port (30000–32767) on every worker node.
- Accessible from outside the cluster.

Example:

```
Client

↓

NodeIP:30080

↓

Service

↓

Pods
```

---

### Comparison

| ClusterIP | NodePort |
|------------|----------|
| Internal only | External access |
| Default Service | Opens a port on each node |
| More secure | Primarily for development/testing |

---

### Production Interview Answer

> ClusterIP is used for internal communication between services inside the cluster, while NodePort exposes the service externally through a static port on every worker node. In production, I typically use ClusterIP with an Ingress Controller rather than exposing services directly through NodePort.

---

# 6. Why would you use a StatefulSet over a standard Deployment?

## Answer

A StatefulSet is designed for applications that require:

- Stable network identities
- Persistent storage
- Ordered deployment and scaling

Examples:

- PostgreSQL
- MySQL
- MongoDB
- Kafka

A Deployment is intended for stateless applications where Pods are interchangeable.

---

### Comparison

| Deployment | StatefulSet |
|------------|-------------|
| Stateless | Stateful |
| Random Pod names | Stable Pod names |
| Ephemeral storage | Persistent Volumes |
| Suitable for web apps | Suitable for databases |

---

### Production Interview Answer

> I use Deployments for stateless applications such as APIs and web services, and StatefulSets for databases or messaging systems that require stable identities and persistent storage.

# Kubernetes, Terraform & IaC Interview Questions
## Senior DevOps Interview Preparation (Part 2)

---

# ☸️ KUBERNETES

# 7. What is the function of the mutation phase in a Kubernetes Admission Controller?

## Answer

The **Mutation Admission Controller** intercepts API requests **before objects are stored in etcd** and can automatically modify or inject additional configurations into Kubernetes resources.

It runs **after authentication and authorization** but **before validation**.

### Request Flow

```
kubectl apply
      │
      ▼
Authentication
      │
      ▼
Authorization
      │
      ▼
Mutating Admission Controller
      │
      ▼
Validating Admission Controller
      │
      ▼
etcd
```

---

### Common Use Cases

- Inject sidecar containers (Istio, Linkerd)
- Add default labels and annotations
- Automatically inject resource requests/limits
- Add imagePullSecrets
- Enforce security settings
- Add tolerations or node selectors
- Inject environment variables

---

### Example

A user creates a Pod without CPU and memory limits:

```yaml
containers:
- name: app
  image: nginx
```

The Mutating Admission Controller automatically adds:

```yaml
resources:
  requests:
    cpu: "200m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

---

### Popular Mutating Admission Controllers

- MutatingAdmissionWebhook
- Istio Sidecar Injector
- Kyverno
- Open Policy Agent (OPA)
- Gatekeeper

---

### Production Interview Answer

> The Mutation Admission Controller modifies Kubernetes objects before they are persisted in etcd. It is commonly used to inject sidecars, resource limits, labels, annotations, security policies, and default configurations automatically, ensuring consistency across production workloads.

---

# 8. What is the exact difference between livenessProbe and readinessProbe?

## Answer

Both probes monitor container health but serve different purposes.

---

## Liveness Probe

Checks whether the application is **still running correctly**.

If the liveness probe fails:

- Kubernetes assumes the application is unhealthy.
- The container is restarted automatically.

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
```

---

## Readiness Probe

Checks whether the application is **ready to serve traffic**.

If the readiness probe fails:

- The Pod is removed from the Service endpoints.
- No traffic is sent to the Pod.
- The container is **not restarted**.

Example:

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
```

---

## Real Production Scenario

During application startup:

```
Pod Created

↓

Application Initializing

↓

Readiness = Failed

↓

Traffic NOT Sent

↓

Initialization Complete

↓

Readiness = Success

↓

Traffic Starts
```

---

If later the application hangs:

```
Application Frozen

↓

Liveness Fails

↓

Container Restarted
```

---

## Comparison

| Liveness Probe | Readiness Probe |
|----------------|-----------------|
| Detects unhealthy containers | Detects if application is ready |
| Restarts container | Stops traffic only |
| Used for recovery | Used for load balancing |
| Prevents hung applications | Prevents failed requests |

---

### Production Interview Answer

> Liveness probes determine whether a container should be restarted, while readiness probes determine whether a Pod should receive traffic. In production, readiness probes prevent users from hitting unready applications, and liveness probes recover applications that become unhealthy or unresponsive.

---

# 🌍 TERRAFORM / IaC

# 9. What is the primary purpose of the terraform.tfstate file?

## Answer

The **terraform.tfstate** file is Terraform's source of truth. It stores the current state of infrastructure so Terraform knows what resources already exist and how they map to the configuration.

Without the state file, Terraform cannot accurately determine which resources need to be created, updated, or destroyed.

---

## State File Contains

- Resource IDs
- Resource attributes
- Metadata
- Dependency information
- Outputs
- Module information

---

## Why is it Important?

Terraform compares:

```
Terraform Code

VS

terraform.tfstate

↓

Execution Plan
```

Without the state file, Terraform may attempt to recreate existing resources or fail to manage them correctly.

---

## Where Should It Be Stored?

For team environments:

- AWS S3 (Remote Backend)
- Azure Storage Account
- Google Cloud Storage
- Terraform Cloud

Use **DynamoDB** (AWS) for state locking.

---

## Best Practices

- Never commit the state file to Git.
- Enable S3 versioning.
- Encrypt the state file.
- Restrict IAM access.
- Enable state locking.

---

### Production Interview Answer

> The terraform.tfstate file is Terraform's source of truth. It records the actual infrastructure state, allowing Terraform to compare it with the desired configuration. In production, I store it remotely in an encrypted S3 bucket with versioning enabled and use DynamoDB for state locking.

---

# 10. When should you use count versus for_each to loop through resources?

## Answer

Both create multiple resource instances but differ in how they identify those instances.

---

## count

Uses numeric indexes.

Example:

```hcl
count = 3
```

Creates:

```
server[0]
server[1]
server[2]
```

---

### Best For

- Identical resources
- Simple scaling
- Fixed number of resources

---

## for_each

Uses unique keys.

Example:

```hcl
for_each = {
  dev = "t3.micro"
  prod = "t3.large"
}
```

Creates:

```
server["dev"]
server["prod"]
```

---

### Best For

- Resources with unique names
- Maps
- Sets
- Environment-specific resources

---

## Why count Can Become a Problem

Suppose:

```
count = 3
```

Resources:

```
server[0]

server[1]

server[2]
```

If you remove the second resource, indexes shift:

```
server[0]

server[1]
```

Terraform may destroy and recreate resources unnecessarily.

With **for_each**, keys remain stable.

---

## Comparison

| count | for_each |
|--------|----------|
| Index-based | Key-based |
| Better for identical resources | Better for unique resources |
| Index changes can recreate resources | Stable resource identity |

---

### Production Interview Answer

> I use `count` for creating identical resources, such as multiple EC2 instances with the same configuration. I use `for_each` when resources have unique names or configurations because stable keys prevent unnecessary resource recreation during updates.

---

# 11. How does state locking work, and why is it crucial in a team environment?

## Answer

State locking prevents multiple users or CI/CD pipelines from modifying the same Terraform state simultaneously.

Without locking:

```
Engineer A

terraform apply

↓

Engineer B

terraform apply

↓

State Corruption
```

---

### AWS Implementation

```
Terraform

↓

S3 Backend

↓

DynamoDB Lock Table
```

Terraform acquires a lock before making changes. Other users must wait until the lock is released.

---

### Benefits

- Prevents race conditions
- Prevents state corruption
- Ensures infrastructure consistency
- Safe collaboration

---

### Production Interview Answer

> State locking ensures only one Terraform operation can modify the state at a time. In AWS, I use an S3 backend for remote state storage and a DynamoDB table for state locking to prevent concurrent updates and state corruption.

---

# 12. What is the difference between `terraform plan` and `terraform apply -refresh-only`?

## Answer

### terraform plan

- Compares Terraform configuration with the current infrastructure.
- Shows proposed changes.
- Does not modify infrastructure or state.

Example:

```bash
terraform plan
```

---

### terraform apply -refresh-only

- Refreshes the Terraform state with the actual infrastructure.
- Updates only the state file.
- Does not create, modify, or destroy resources.

Example:

```bash
terraform apply -refresh-only
```

---

### When to Use refresh-only

- Manual infrastructure changes occurred.
- State drift is suspected.
- Need to synchronize state without applying configuration changes.

---

## Comparison

| terraform plan | terraform apply -refresh-only |
|----------------|-------------------------------|
| Shows planned changes | Updates state only |
| No changes applied | No infrastructure changes |
| Detects drift | Synchronizes state with reality |

---

### Production Interview Answer

> `terraform plan` previews the changes required to align infrastructure with the configuration, while `terraform apply -refresh-only` updates the Terraform state to reflect the current infrastructure without making any changes. I use `refresh-only` when I need to synchronize the state after manual changes or investigate state drift.


# CI/CD, Observability, Linux & Networking Interview Questions
## Senior DevOps Interview Preparation (Part 3)

---

# 🚀 CI/CD & OBSERVABILITY

# 13. What is the technical difference between Continuous Delivery and Continuous Deployment?

## Answer

Both Continuous Delivery and Continuous Deployment automate software delivery, but they differ in the final deployment step.

### Continuous Delivery

Every code change automatically goes through:

- Build
- Unit Testing
- Security Scanning
- Integration Testing
- Packaging

The application is **ready for production**, but deployment requires **manual approval**.

```
Developer

↓

Git Push

↓

CI Pipeline

↓

Tests

↓

Build

↓

Manual Approval

↓

Production
```

**Example:** Jenkins pipeline waits for manager approval before production deployment.

---

### Continuous Deployment

Everything is automated.

If all tests pass, the application is automatically deployed to production without human intervention.

```
Developer

↓

Git Push

↓

CI Pipeline

↓

Tests

↓

Production
```

---

### Comparison

| Continuous Delivery | Continuous Deployment |
|----------------------|------------------------|
| Manual production approval | Fully automated |
| Production-ready artifacts | Automatically deployed |
| More control | Faster releases |
| Common in enterprises | Common in SaaS companies |

---

### Production Interview Answer

> Continuous Delivery automates building, testing, and packaging but requires manual approval before production deployment. Continuous Deployment extends this by automatically deploying every successful build to production without human intervention. In enterprise environments, Continuous Delivery is more common because it provides better release control.

---

# 14. How do you efficiently use caching in a CI pipeline to speed up build times?

## Answer

Caching avoids downloading the same dependencies repeatedly, significantly reducing pipeline execution time.

---

### Common Cache Types

- Maven Repository (`~/.m2`)
- Gradle Cache
- npm Cache
- pip Packages
- Docker Layers
- Terraform Providers
- Sonar Scanner Cache

---

### Example (GitHub Actions)

```yaml
- uses: actions/cache@v3
  with:
    path: ~/.m2
    key: maven-cache
```

---

### Jenkins Example

Cache:

```
~/.m2

Docker Layers

Node Modules
```

instead of downloading every build.

---

### Docker Layer Cache

Without Cache:

```
Download Dependencies

↓

Compile

↓

Package
```

Every build repeats all steps.

With Cache:

```
Reuse Existing Layers

↓

Build Only Changed Layers
```

---

### Benefits

- Faster builds
- Reduced network traffic
- Lower cloud costs
- Faster developer feedback

---

### Best Practices

- Cache dependencies only
- Avoid caching build artifacts
- Use checksum-based cache keys
- Invalidate cache when dependency files change

---

### Production Interview Answer

> I cache dependency repositories such as Maven, npm, Docker layers, and Terraform plugins to reduce build time. Cache keys are tied to dependency files like `pom.xml` or `package-lock.json`, ensuring caches are refreshed only when dependencies change.

---

# 15. What is the difference between structured logging and unstructured plain-text logging?

## Answer

### Unstructured Logging

Logs are plain text.

Example:

```
User login failed for admin from IP 10.0.0.5
```

Problems:

- Difficult to search
- Difficult to filter
- Hard to analyze automatically

---

### Structured Logging

Logs are stored in JSON or key-value format.

Example:

```json
{
  "timestamp":"2026-08-05T10:15:20Z",
  "level":"ERROR",
  "user":"admin",
  "ip":"10.0.0.5",
  "service":"login-api",
  "message":"Authentication failed"
}
```

---

### Benefits

- Easy filtering
- Better querying
- Faster troubleshooting
- Machine-readable
- Works well with ELK/OpenSearch

---

### Comparison

| Structured | Unstructured |
|------------|--------------|
| JSON format | Plain text |
| Searchable | Difficult to search |
| Better analytics | Limited analysis |
| Production recommended | Small applications |

---

### Production Interview Answer

> Structured logging stores logs in JSON format with consistent fields such as timestamp, service, request ID, and log level. This makes searching and correlation much easier in tools like ELK or OpenSearch. Plain-text logging is harder to query and is generally avoided in large production environments.

---

# 16. In Prometheus, what is the difference between a Gauge and a Counter metric type?

## Answer

Prometheus provides different metric types for different monitoring use cases.

---

## Counter

A Counter only increases.

It can only:

- Increase
- Reset to zero after restart

Examples:

- HTTP requests
- Login attempts
- Errors
- API calls

Example:

```
100

↓

150

↓

210
```

Never decreases.

---

## Gauge

A Gauge can increase or decrease.

Examples:

- CPU usage
- Memory usage
- Active users
- Queue length
- Temperature

Example:

```
65%

↓

40%

↓

82%

↓

55%
```

---

### Comparison

| Counter | Gauge |
|----------|-------|
| Only increases | Increases & decreases |
| Request count | CPU usage |
| Error count | Memory usage |
| Login count | Queue size |

---

### Production Interview Answer

> I use Counters for cumulative values such as total requests or errors because they only increase. Gauges are used for values that fluctuate, such as CPU utilization, memory consumption, active sessions, or queue lengths.

---

# 🐧 LINUX & NETWORKING

# 17. What is the difference between a hard link and a soft (symbolic) link in Linux?

## Answer

A **hard link** points directly to the file's inode, while a **soft link** points to the file's path.

---

### Hard Link

- Shares the same inode
- Remains valid even if the original filename is deleted
- Cannot span different filesystems
- Cannot link directories

Example:

```bash
ln file.txt hardlink.txt
```

---

### Soft Link

- Points to the file path
- Breaks if the original file is removed
- Can cross filesystems
- Can link directories

Example:

```bash
ln -s file.txt softlink.txt
```

---

### Comparison

| Hard Link | Soft Link |
|------------|-----------|
| Same inode | Different inode |
| Survives original deletion | Breaks if target is deleted |
| Same filesystem only | Cross-filesystem supported |
| Files only | Files and directories |

---

### Production Interview Answer

> A hard link references the same inode as the original file, so deleting the original filename does not remove the data until all hard links are deleted. A symbolic link simply points to the file path and becomes invalid if the target file is removed.

---

# 18. How do you debug a network connectivity issue between two microservices using CLI tools?

## Answer

I follow a structured troubleshooting process.

### Step 1: Verify Pod Status

```bash
kubectl get pods
```

---

### Step 2: Verify Service

```bash
kubectl get svc
```

---

### Step 3: Verify Endpoints

```bash
kubectl get endpoints
```

Ensure the Service has healthy Pod endpoints.

---

### Step 4: Test DNS

```bash
nslookup service-name

dig service-name
```

---

### Step 5: Test Connectivity

```bash
curl http://service-name

wget

telnet

nc
```

---

### Step 6: Check Network Policies

```bash
kubectl get networkpolicy
```

---

### Step 7: Verify Logs

```bash
kubectl logs pod-name
```

---

### Step 8: Check Kubernetes Events

```bash
kubectl get events
```

---

### Production Interview Answer

> I verify Pod health, Service configuration, Endpoints, DNS resolution, and network policies before testing connectivity with tools like `curl`, `telnet`, or `nc`. I then review application logs and Kubernetes events to isolate whether the issue is related to networking, service discovery, or the application itself.

---

# 19. What does a 502 Bad Gateway error usually mean in an Nginx reverse proxy setup?

## Answer

A **502 Bad Gateway** means **Nginx successfully received the client's request but failed to obtain a valid response from the upstream backend server**.

---

### Common Causes

- Backend application is down
- Application crashed
- Wrong upstream IP or port
- Backend timeout
- Kubernetes Pods unhealthy
- Service has no endpoints
- Firewall or Security Group issue

---

### Troubleshooting Steps

Check Nginx logs:

```bash
tail -f /var/log/nginx/error.log
```

Check backend:

```bash
curl backend-service
```

Check Kubernetes:

```bash
kubectl get endpoints

kubectl describe svc
```

Check Pod health:

```bash
kubectl get pods
```

---

### Production Interview Answer

> A 502 Bad Gateway indicates that Nginx could not get a valid response from the upstream application. I check backend application health, Service endpoints, Pod readiness, Nginx logs, network connectivity, and timeout settings to identify the root cause.

---

# 20. Which DNS record type is used to map an alias name to a canonical domain name?

## Answer

The **CNAME (Canonical Name)** record maps one domain name to another.

Example:

```
api.example.com

↓

backend.example.com
```

DNS resolves:

```
api.example.com

↓

backend.example.com

↓

A Record

↓

IP Address
```

---

### Common DNS Records

| Record | Purpose |
|---------|----------|
| A | Maps domain to IPv4 address |
| AAAA | Maps domain to IPv6 address |
| CNAME | Maps one hostname to another |
| MX | Mail server |
| TXT | Verification, SPF, DKIM |
| NS | Name server |
| PTR | Reverse DNS lookup |

---

### Production Interview Answer

> A CNAME record maps one hostname to another canonical hostname instead of directly to an IP address. It is commonly used for aliases such as `www.example.com` pointing to `example.com` or application endpoints that resolve through cloud-managed DNS.


# DevOps Engineer – Scenario-Based Interview Questions
## Production Operations & Incident Management

---

# 1. How do you know your production environment still matches the operational standards that your team expects?

## Answer

In production, we continuously validate the environment against predefined operational standards rather than assuming it remains healthy. We rely on monitoring, automated compliance checks, Infrastructure as Code (IaC), configuration management, and regular audits.

### My Approach

### 1. Monitoring & Alerting

I monitor infrastructure and applications using tools like:

- Prometheus
- Grafana
- CloudWatch
- ELK/OpenSearch

Key metrics include:

- CPU & Memory utilization
- Disk usage
- Network latency
- Pod health
- Application response time
- Error rate
- Availability
- Throughput

Alerts are configured based on defined SLOs.

---

### 2. Infrastructure as Code Validation

Since infrastructure is managed using Terraform, I regularly run:

```bash
terraform plan
```

This helps detect infrastructure drift and verifies that production matches the desired configuration stored in Git.

---

### 3. Kubernetes Health Checks

I verify:

```bash
kubectl get nodes

kubectl get pods -A

kubectl get deployments

kubectl get events
```

I also check:

- Node health
- Pod readiness
- Replica availability
- Failed scheduling events

---

### 4. Security & Compliance

Regularly validate:

- IAM permissions
- RBAC roles
- Security Groups
- Network Policies
- Vulnerability scan reports
- Image scanning (Trivy)

---

### 5. Observability

I ensure dashboards display:

- Application availability
- API latency
- Error rates
- Pod restarts
- Deployment success rate
- Infrastructure health

---

### 6. Log Reviews

Using ELK/OpenSearch, I monitor:

- Application errors
- Authentication failures
- Database errors
- Container crashes
- JVM exceptions

---

### 7. Incident Review

After every production incident:

- Perform RCA
- Update monitoring rules
- Improve runbooks
- Add missing alerts
- Automate repetitive recovery tasks

---

## Production Interview Answer

> I ensure production continues to meet operational standards by combining proactive monitoring, Infrastructure as Code validation, Kubernetes health checks, security audits, observability dashboards, and periodic reviews. I also compare system performance against predefined SLIs and SLOs, perform infrastructure drift detection, and continuously improve monitoring based on production incidents.

---

# 2. Have you ever had multiple dashboards all telling different stories during the same incident?

## Answer

Yes. This is common in distributed systems because different monitoring tools collect different types of data at different intervals.

For example:

- Grafana shows high CPU usage.
- CloudWatch indicates healthy EC2 instances.
- Prometheus reports Pod restarts.
- ELK displays database timeout exceptions.
- AWS ALB shows increased HTTP 503 errors.

Each dashboard provides only part of the overall picture.

---

## How I Handle It

### Step 1: Identify the Source of Truth

I first determine which metric directly represents customer impact.

Examples:

- HTTP 5xx errors
- API response time
- Application availability
- Failed transactions

These metrics take priority over infrastructure metrics.

---

### Step 2: Correlate Data

I correlate:

```
Logs
↓

Metrics
↓

Events
↓

Traces
```

For example:

- Prometheus → CPU spike
- Grafana → Memory stable
- ELK → Database timeout
- CloudWatch → Increased RDS latency

The combined evidence identifies the root cause.

---

### Step 3: Check Timeline

Different tools refresh at different intervals.

Example:

```
CloudWatch

1 minute

Prometheus

15 seconds

Grafana

30 seconds
```

This timing difference can explain conflicting dashboards.

---

### Step 4: Validate Kubernetes Events

```bash
kubectl get events

kubectl describe pod
```

Events often reveal issues before dashboards update.

---

### Step 5: Verify Application Logs

Application logs usually confirm whether the issue is:

- Database
- Network
- Authentication
- Memory
- Deployment
- Configuration

---

## Real Example

During one incident:

- CPU usage was normal.
- Memory usage was normal.
- Pods were Running.
- Users still received HTTP 503 errors.

Investigation revealed that the Readiness Probe was failing because the application couldn't connect to the database. The Pods remained running but were removed from Service endpoints, causing the ALB to return 503 errors.

This showed why correlating multiple monitoring sources is essential.

---

## Production Interview Answer

> Yes, I've seen situations where different dashboards showed different symptoms of the same incident. Instead of relying on a single dashboard, I correlate metrics, logs, traces, Kubernetes events, and infrastructure data. My primary focus is always on customer-impact metrics such as availability, latency, and error rates before investigating underlying infrastructure issues.

---

# 3. During incidents, which sources of information do you trust the most?

## Answer

I don't rely on a single monitoring tool. Instead, I correlate multiple sources of information because each provides a different perspective.

### Priority Order

### 1. Application Logs ⭐⭐⭐⭐⭐

Application logs usually provide the most direct evidence.

Examples:

- Stack traces
- SQL errors
- Null pointer exceptions
- Authentication failures
- API failures

Tools:

- ELK
- OpenSearch
- CloudWatch Logs

---

### 2. Kubernetes Events ⭐⭐⭐⭐⭐

```bash
kubectl get events

kubectl describe pod
```

These reveal:

- Scheduling failures
- Image pull errors
- Probe failures
- Volume mount issues

---

### 3. Metrics ⭐⭐⭐⭐

Metrics help identify:

- CPU
- Memory
- Network
- Request rate
- Error rate
- Latency

Tools:

- Prometheus
- Grafana
- CloudWatch

---

### 4. Distributed Tracing ⭐⭐⭐⭐

For microservices:

- AWS X-Ray
- Jaeger
- OpenTelemetry

Tracing identifies which service introduces latency or failures.

---

### 5. Infrastructure Monitoring ⭐⭐⭐

Check:

- EC2 health
- RDS metrics
- EBS latency
- ALB Target Health
- Auto Scaling activity

---

### 6. CI/CD History ⭐⭐⭐

Review:

- Recent deployments
- Jenkins build history
- ArgoCD sync history
- Terraform changes

Many incidents are triggered immediately after deployments.

---

### 7. Cloud Audit Logs ⭐⭐⭐

CloudTrail helps determine:

- Infrastructure changes
- IAM modifications
- Security policy updates
- Manual production changes

---

## Production Incident Workflow

```
User Reports Issue
          │
          ▼
Application Logs
          │
          ▼
Kubernetes Events
          │
          ▼
Metrics (Prometheus/Grafana)
          │
          ▼
Tracing (X-Ray/Jaeger)
          │
          ▼
Infrastructure Health
          │
          ▼
CI/CD Changes
          │
          ▼
Root Cause Analysis
```

---

## Production Interview Answer

> During incidents, I trust application logs and Kubernetes events the most because they usually provide the earliest and most accurate indication of the root cause. I then correlate logs with metrics, distributed traces, infrastructure health, and deployment history. Rather than relying on a single dashboard, I combine multiple sources of information to accurately identify and resolve the issue while minimizing customer impact.



# DevOps Engineer – 2nd Round Interview (Scenario-Based)
## Part 1 (Questions 1–5)

---

# 1. Your EC2 instance suddenly stops responding to SSH. How do you debug it without a reboot?

## Answer

When an EC2 instance becomes unreachable over SSH, I avoid rebooting immediately because it may erase valuable diagnostic information. Instead, I troubleshoot systematically.

### Step 1: Verify EC2 Instance Status

Check:

- Instance State (Running/Stopped)
- System Status Checks
- Instance Status Checks

AWS Console:

```
EC2 → Instances → Status Checks
```

If either status check fails, it indicates an infrastructure or OS issue.

---

### Step 2: Verify Security Group

Ensure Security Group allows:

```
Inbound Rule

Port: 22

Source: My Public IP
```

Verify your current public IP hasn't changed.

---

### Step 3: Check Network ACL

Ensure:

- Port 22 inbound allowed
- Ephemeral ports (1024–65535) allowed outbound
- No DENY rule blocking traffic

---

### Step 4: Verify Route Table

Confirm the subnet has a valid route:

```
0.0.0.0/0

↓

Internet Gateway
```

For private subnets, verify VPN, Direct Connect, or Bastion Host connectivity.

---

### Step 5: Use EC2 Serial Console or SSM Session Manager

If enabled:

- AWS Systems Manager Session Manager
- EC2 Serial Console

These allow shell access without SSH.

---

### Step 6: Check Disk Space

If the root filesystem is full, SSH daemon may fail.

Commands:

```bash
df -h

du -sh /*
```

---

### Step 7: Check SSH Service

```bash
sudo systemctl status sshd

sudo journalctl -u sshd
```

Restart if required:

```bash
sudo systemctl restart sshd
```

---

### Step 8: Verify CPU & Memory

High CPU or memory exhaustion may make SSH unresponsive.

Check CloudWatch metrics:

- CPUUtilization
- Memory (if CloudWatch Agent installed)
- Disk I/O
- Network

---

### Step 9: Review System Logs

AWS Console:

```
Get System Log
```

Look for:

- Kernel panic
- Filesystem errors
- OOMKilled
- Boot issues

---

### Production Interview Answer

> I first verify instance health, Security Groups, NACLs, and routing. Then I use AWS Systems Manager Session Manager or the EC2 Serial Console for access without SSH. I inspect disk usage, SSH service status, CloudWatch metrics, and system logs. I only reboot after collecting enough evidence to identify the root cause.

---

# 2. A Docker container works locally but crashes immediately in production. What's your debugging approach?

## Answer

### Step 1: Check Container Status

```bash
docker ps -a
```

---

### Step 2: View Logs

```bash
docker logs <container-id>
```

Check for:

- Missing environment variables
- Database connection failures
- Missing files
- Startup exceptions

---

### Step 3: Verify Image Version

Ensure the correct image tag was deployed.

```bash
docker images
```

---

### Step 4: Inspect Environment Variables

```bash
docker inspect <container-id>
```

Compare production variables with local development.

---

### Step 5: Verify Mounted Volumes

Incorrect volume mounts often cause startup failures.

```bash
docker inspect
```

---

### Step 6: Test the Image Locally

Run the same image used in production:

```bash
docker run -it image:tag
```

---

### Step 7: Check Resource Limits

Container may be OOMKilled.

```bash
docker stats
```

---

### Step 8: Verify Network Connectivity

Check access to:

- Database
- Redis
- Kafka
- APIs

---

### Step 9: Compare Production Configuration

Validate:

- Secrets
- ConfigMaps
- Environment variables
- File permissions

---

### Production Interview Answer

> I inspect container logs, verify the deployed image version, compare production environment variables with local settings, validate mounted volumes and external dependencies, check resource usage for OOM issues, and reproduce the problem using the same production image locally. Most production failures are caused by configuration differences rather than application code.

---

# 3. Explain how a Kubernetes Service routes traffic to Pods. What happens if labels don't match?

## Answer

A Kubernetes Service provides a stable endpoint to access a dynamic set of Pods.

### Traffic Flow

```
Client
   │
   ▼
Service (ClusterIP)
   │
   ▼
Endpoints
   │
   ▼
Pods
```

The Service identifies Pods using **label selectors**.

Example:

Pod labels:

```yaml
labels:
  app: payment
```

Service:

```yaml
selector:
  app: payment
```

Because the labels match, Kubernetes automatically creates endpoints and routes traffic.

### If Labels Don't Match

If the Service selector doesn't match any Pod labels:

- No endpoints are created.
- Requests to the Service fail.
- Users may receive HTTP 503 or connection refused.

Check:

```bash
kubectl get endpoints
```

If you see:

```
ENDPOINTS: <none>
```

it usually indicates a selector mismatch.

### Production Interview Answer

> A Kubernetes Service uses label selectors to identify backend Pods and routes traffic through endpoints managed by kube-proxy. If the Service selector doesn't match Pod labels, no endpoints are created, so the Service cannot forward requests.

---

# 4. Your S3 bucket costs suddenly spiked 5x overnight. How do you investigate?

## Answer

### Step 1: Review AWS Cost Explorer

Identify:

- Which bucket
- Which storage class
- Which API operations
- Time of increase

---

### Step 2: Check S3 Storage Metrics

Review:

- Bucket size
- Object count
- Storage class distribution

---

### Step 3: Analyze Access Logs

Enable or inspect:

- S3 Server Access Logs
- CloudTrail Data Events

Look for:

- Excessive GET requests
- PUT requests
- LIST operations

---

### Step 4: Check Lifecycle Policies

Confirm objects are transitioning to cheaper storage classes (e.g., Glacier) as expected.

---

### Step 5: Investigate Large Uploads

Determine whether a backup job or application uploaded unexpected data.

---

### Step 6: Review Replication

Verify that Cross-Region Replication isn't duplicating excessive data.

---

### Step 7: Check Public Access

A publicly accessible bucket may experience unexpected downloads.

---

### Step 8: Enable AWS Budgets

Configure cost alerts to detect future anomalies early.

### Production Interview Answer

> I analyze Cost Explorer to identify the cost driver, review S3 storage metrics, inspect CloudTrail and access logs for unusual API activity, verify lifecycle policies and replication settings, and check for unexpected uploads or public access. I then implement budgets and alerts to prevent similar spikes.

---

# 5. What causes a Kubernetes node to go NotReady, and how do you recover it?

## Answer

A node enters the **NotReady** state when the control plane determines it cannot reliably schedule or run workloads.

### Common Causes

- Kubelet stopped
- Network failure
- Disk pressure
- Memory pressure
- PID pressure
- CNI plugin failure
- Container runtime failure
- Node disconnected from API Server

---

### Step 1: Verify Node Status

```bash
kubectl get nodes
```

---

### Step 2: Describe the Node

```bash
kubectl describe node <node-name>
```

Check:

- Conditions
- Events
- Resource pressure

---

### Step 3: Verify Kubelet

```bash
sudo systemctl status kubelet

sudo journalctl -u kubelet
```

---

### Step 4: Check Container Runtime

For containerd:

```bash
sudo systemctl status containerd
```

---

### Step 5: Verify Disk Usage

```bash
df -h
```

---

### Step 6: Check Memory

```bash
free -m
```

---

### Step 7: Verify CNI Plugin

Ensure the networking plugin (AWS VPC CNI, Calico, Cilium, etc.) is healthy.

```bash
kubectl get pods -n kube-system
```

---

### Step 8: Recover the Node

Depending on the cause:

- Restart kubelet
- Restart container runtime
- Free disk space
- Resolve network issues
- Replace or drain the node if necessary

```bash
kubectl drain <node-name>

kubectl delete node <node-name>
```

Cluster Autoscaler can provision a replacement node if configured.

### Production Interview Answer

> I inspect the node status and events, verify kubelet and the container runtime, check for disk, memory, or network issues, and ensure the CNI plugin is healthy. After resolving the root cause, I restore the node or replace it by draining and removing it, allowing the Cluster Autoscaler to provision a new one if enabled.

# DevOps Engineer – 2nd Round Interview (Scenario-Based)
## Part 2 (Questions 6–10)

---

# 6. Your IAM role has correct permissions but the API call still fails. What do you check?

## Answer

Even if the IAM role appears to have the required permissions, several other factors can cause API failures.

### Step 1: Verify the IAM Policy

Ensure the policy explicitly allows the required action.

Example:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "*"
}
```

Look for:

- Explicit Deny statements
- Missing actions
- Incorrect resource ARNs

---

### Step 2: Check Resource-Based Policies

Some AWS services (S3, KMS, SNS, SQS, Lambda) also require resource policies.

Example:

- Bucket Policy
- KMS Key Policy
- Lambda Resource Policy

Even with IAM permissions, the resource policy can deny access.

---

### Step 3: Verify Trust Relationship

For assumed roles:

```
IAM Role
↓

Trust Policy

↓

EC2 / Lambda / EKS
```

Ensure the role trusts the service using it.

---

### Step 4: Check Service Control Policies (SCP)

If AWS Organizations is used:

```
Organization

↓

SCP

↓

Account

↓

IAM Role
```

An SCP can override IAM permissions.

---

### Step 5: Verify Region

Resources may exist in another region.

Example:

```
Bucket

ap-south-1

Application

us-east-1
```

---

### Step 6: Check Credentials

Verify:

```bash
aws sts get-caller-identity
```

This confirms which IAM role or user is making the request.

---

### Step 7: Review CloudTrail

CloudTrail shows:

- Which API failed
- Error code
- Principal
- Reason for denial

---

### Step 8: Validate Temporary Credentials

For STS AssumeRole:

- Session not expired
- Correct session token
- Proper role assumption

---

## Production Interview Answer

> I verify IAM policies, resource-based policies, trust relationships, SCPs, region configuration, and temporary credentials. I also use `aws sts get-caller-identity` to confirm the active identity and review CloudTrail logs to identify the exact authorization failure.

---

# 7. Explain how Terraform decides what to destroy and recreate vs update in-place.

## Answer

Terraform compares three things:

```
Terraform Configuration

↓

Terraform State

↓

Actual Infrastructure
```

This comparison creates an execution plan.

---

### Update In-Place

Terraform updates existing resources when supported.

Example:

```hcl
instance_type = "t3.medium"
```

Changing to

```hcl
instance_type = "t3.large"
```

results in:

```
Modify Existing EC2
```

No replacement required.

---

### Destroy and Recreate

Some attributes are immutable.

Examples:

- AMI ID
- Certain subnet changes
- Resource name (depending on provider)
- Availability Zone (for some resources)

Terraform output:

```
-/+

Destroy

Create
```

---

### Force Replacement

Terraform indicates:

```
forces replacement
```

Example:

```
~ ami = ami-123

→ ami-456

Forces replacement
```

---

### Dependencies

Terraform builds a dependency graph.

```
VPC

↓

Subnet

↓

EC2

↓

ALB
```

Resources are created and destroyed in dependency order.

---

## Production Interview Answer

> Terraform compares the desired configuration, the state file, and the actual infrastructure. It updates resources in place whenever possible, but if an immutable attribute changes, Terraform destroys and recreates the resource. The dependency graph ensures resources are processed in the correct order.

---

# 8. Two team members ran terraform apply at the same time. What happens, and how do you prevent it?

## Answer

Without state locking:

```
Engineer A

↓

terraform apply

↓

terraform.tfstate

↑

Engineer B

↓

terraform apply
```

Both users attempt to modify the same state file.

Possible outcomes:

- State corruption
- Duplicate resources
- Partial infrastructure
- Failed deployments

---

### Best Practice

Use a remote backend.

```
Terraform

↓

S3 Backend

↓

DynamoDB Lock Table
```

When Engineer A runs:

```
terraform apply
```

Terraform acquires a lock.

Engineer B receives:

```
Error acquiring state lock
```

and must wait until the first operation completes.

---

### Additional Protection

- CI/CD pipelines instead of manual applies
- Branch protection
- Pull request approvals
- Separate workspaces
- Least privilege IAM access

---

## Production Interview Answer

> In production, I use an S3 backend with DynamoDB state locking. If two engineers run `terraform apply` simultaneously, the second operation is blocked until the first releases the lock, preventing state corruption and conflicting infrastructure changes.

---

# 9. Explain the difference between Readiness and Liveness Probes with a real failure scenario.

## Answer

## Readiness Probe

Determines whether a Pod is ready to receive traffic.

If it fails:

- Pod continues running.
- Removed from Service endpoints.
- No traffic is sent.

---

### Example

Application startup:

```
Application

↓

Database Migration

↓

Warm Cache

↓

Ready
```

During initialization:

```
Readiness = Failed
```

Users are not routed to the Pod.

---

## Liveness Probe

Determines whether the application is healthy.

If it fails:

```
Restart Container
```

---

### Example

Application enters deadlock.

The process is still running but no longer responds.

```
Liveness Failed

↓

Kubelet Restarts Container
```

---

### Real Production Scenario

A Spring Boot application requires 60 seconds to initialize.

Without a readiness probe:

```
ALB

↓

Routes traffic

↓

Application still starting

↓

HTTP 500
```

With a readiness probe:

```
Traffic waits

↓

Application Ready

↓

Users receive successful responses
```

Later, the application hangs due to a memory leak.

```
Liveness Probe

↓

Restart Container
```

---

## Production Interview Answer

> Readiness probes determine whether a Pod can receive traffic, while liveness probes determine whether the application should be restarted. For example, during application startup, the readiness probe prevents traffic from reaching the Pod until initialization is complete. If the application later hangs because of a deadlock or memory leak, the liveness probe detects the failure and restarts the container automatically.

---

# 10. Your Jenkins pipeline works for one branch but fails for another with the same code. Why?

## Answer

Even if the application code is identical, branch-specific configurations often differ.

### Step 1: Compare Jenkinsfile

Check whether each branch has the same:

- Pipeline stages
- Environment variables
- Credentials
- Shared Libraries

---

### Step 2: Verify Branch Configuration

Multibranch Pipeline:

```
main

↓

feature/login

↓

release
```

Different branches may trigger different logic.

Example:

```groovy
if (env.BRANCH_NAME == "main")
```

---

### Step 3: Check Environment Variables

Missing variables:

```
AWS_REGION

DOCKER_TAG

ECR_REPOSITORY

JAVA_HOME
```

---

### Step 4: Verify Credentials

Ensure:

- AWS credentials
- Docker registry credentials
- Git credentials
- Kubernetes kubeconfig

are available to all branches.

---

### Step 5: Compare Build Agents

Different branches may run on different Jenkins agents.

Check:

- Installed tools
- Java version
- Docker version
- Available disk space

---

### Step 6: Review Shared Libraries

A branch may reference a different library version.

---

### Step 7: Check Branch Protection

Verify:

- Webhooks
- Git permissions
- Pipeline triggers

---

### Step 8: Review Build Logs

Compare:

```
Successful Branch

↓

Failed Branch
```

Identify differences in:

- Build stage
- Test stage
- Docker build
- Deployment

---

## Production Interview Answer

> I compare the Jenkinsfile, environment variables, credentials, shared libraries, and agent configurations across branches. I also verify branch-specific conditions in the pipeline and compare build logs. In most cases, the issue is caused by configuration differences rather than differences in the application code.


# DevOps Engineer – 2nd Round Interview (Scenario-Based)
## Part 3 (Questions 11–15)

---

# 11. Explain how Kubernetes handles a Rolling Update if the new Pod keeps crashing.

## Answer

A Rolling Update allows Kubernetes to replace old Pods with new ones gradually while minimizing downtime. It is managed by the **Deployment** controller.

### Normal Rolling Update Flow

```
Old Pods (v1)

↓

Create New Pod (v2)

↓

Readiness Probe Success

↓

Traffic Shifted

↓

Old Pod Deleted

↓

Repeat Until Complete
```

---

### If the New Pod Keeps Crashing

Suppose the new version has an application bug.

```
Deployment

↓

New Pod Created

↓

CrashLoopBackOff

↓

Readiness Probe Fails

↓

Pod Never Added to Service

↓

Old Pods Continue Serving Traffic
```

Since the new Pod never becomes **Ready**, Kubernetes does **not** send production traffic to it.

---

### How Kubernetes Detects the Failure

Deployment monitors:

- Readiness Probe
- Liveness Probe
- Pod Status
- Replica availability

Commands:

```bash
kubectl get pods

kubectl describe pod <pod-name>

kubectl logs <pod-name>
```

---

### Deployment Status

```bash
kubectl rollout status deployment myapp
```

Possible output:

```
Waiting for deployment to finish...
```

or

```
ProgressDeadlineExceeded
```

---

### Rollback

```bash
kubectl rollout undo deployment myapp
```

or

```bash
kubectl rollout undo deployment myapp --to-revision=3
```

---

### Production Best Practices

Configure:

```yaml
strategy:
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

Use:

- Readiness Probes
- Liveness Probes
- Startup Probes
- Canary Deployments
- Blue-Green Deployments

---

## Production Interview Answer

> During a Rolling Update, Kubernetes gradually creates new Pods and waits for them to become Ready before routing traffic. If the new Pods continuously crash or fail readiness checks, Kubernetes keeps serving traffic through the existing healthy Pods. I monitor the rollout using `kubectl rollout status`, inspect logs and events, identify the root cause, and roll back using `kubectl rollout undo` if required.

---

# 12. Your RDS database is hitting connection limits during peak traffic. How do you fix it?

## Answer

This usually indicates that the application is opening more database connections than the RDS instance can handle.

---

### Step 1: Confirm the Issue

Check CloudWatch Metrics:

- DatabaseConnections
- CPUUtilization
- FreeableMemory
- ReadIOPS
- WriteIOPS

---

### Step 2: Identify Connection Sources

Check:

- Application
- Lambda Functions
- Kubernetes Pods
- EC2 Instances

Look for connection leaks.

---

### Step 3: Use Connection Pooling

Instead of creating a new database connection for every request, configure connection pools.

Examples:

- HikariCP
- PgBouncer
- ProxySQL

---

### Step 4: Enable RDS Proxy

Architecture

```
Application

↓

RDS Proxy

↓

Amazon RDS
```

Benefits:

- Connection reuse
- Faster failover
- Better scalability

---

### Step 5: Scale the Database

Increase:

- Instance class
- CPU
- Memory

Example:

```
db.t3.medium

↓

db.r6g.large
```

---

### Step 6: Add Read Replicas

For read-heavy workloads:

```
Primary

↓

Read Replica 1

↓

Read Replica 2
```

---

### Step 7: Optimize Queries

Identify slow queries using:

- Performance Insights
- Slow Query Log

---

## Production Interview Answer

> I first verify CloudWatch metrics to confirm connection exhaustion, then investigate connection leaks in the application. I implement connection pooling or RDS Proxy, optimize slow queries, add read replicas for read-heavy workloads, and scale the database if necessary.

---

# 13. Explain the difference between a Kubernetes Deployment and a StatefulSet, with a real use case.

## Answer

## Deployment

Used for **stateless applications**.

Characteristics:

- Pods are interchangeable.
- Pod names are random.
- No stable storage.
- Easy horizontal scaling.

Examples:

- React
- Spring Boot APIs
- Node.js
- NGINX

Example:

```
Frontend

↓

Deployment

↓

ReplicaSet

↓

Pods
```

---

## StatefulSet

Used for **stateful applications**.

Characteristics:

- Stable Pod names
- Stable network identity
- Persistent storage
- Ordered deployment
- Ordered scaling

Examples:

- PostgreSQL
- MySQL
- MongoDB
- Kafka
- Elasticsearch

---

### Example

```
postgres-0

↓

PVC-0

↓

EBS Volume
```

Even after restart:

```
postgres-0

↓

Same Storage

↓

Same Network Identity
```

---

### Real Production Example

Microservices

```
API

↓

Deployment
```

Database

```
PostgreSQL

↓

StatefulSet

↓

Persistent Volume
```

---

## Production Interview Answer

> Deployments are designed for stateless applications where Pods are interchangeable, while StatefulSets provide stable identities and persistent storage for stateful workloads such as databases. In production, I deploy APIs using Deployments and databases like PostgreSQL using StatefulSets.

---

# 14. Your CloudWatch alarms didn't trigger during an actual outage. How do you investigate?

## Answer

### Step 1: Verify Alarm State

Check:

```
CloudWatch

↓

Alarms

↓

History
```

---

### Step 2: Verify Metrics

Ensure the monitored metric exists.

Example:

```
CPUUtilization

NetworkIn

5XXError

HealthyHostCount
```

---

### Step 3: Verify Threshold

Example:

Alarm:

```
CPU > 90%
```

Actual:

```
CPU = 85%
```

Alarm will never trigger.

---

### Step 4: Verify Evaluation Period

Example:

```
Threshold

90%

Evaluation Period

5 Minutes

```

If the outage lasted only 2 minutes, the alarm won't fire.

---

### Step 5: Check Missing Data Configuration

CloudWatch options:

- Treat as Missing
- Ignore
- Breaching
- Not Breaching

Incorrect settings can suppress alarms.

---

### Step 6: Verify SNS Notifications

Check:

- SNS Topic
- Email Subscription
- Slack Integration
- PagerDuty
- OpsGenie

---

### Step 7: Review IAM Permissions

Ensure CloudWatch can publish notifications.

---

## Production Interview Answer

> I review the alarm configuration, threshold, evaluation period, monitored metric, missing data handling, and alarm history. I also verify SNS delivery and notification integrations to ensure alerts reach the operations team.

---

# 15. Explain how DNS resolution fails inside a VPC with a private subnet setup.

## Answer

DNS failures inside a private subnet are usually related to networking or VPC DNS configuration.

---

### Normal Flow

```
Application

↓

CoreDNS

↓

Amazon Route53 Resolver

↓

Private DNS

↓

Target Resource
```

---

### Possible Causes

### 1. DNS Resolution Disabled

Check:

```
enableDnsSupport

enableDnsHostnames
```

Both should be enabled.

---

### 2. CoreDNS Failure

```bash
kubectl get pods -n kube-system
```

Check:

```
coredns

Running
```

---

### 3. Security Groups

Ensure DNS traffic is allowed:

```
UDP 53

TCP 53
```

---

### 4. Route Tables

Private subnet should reach:

- Route53 Resolver
- NAT Gateway (if Internet access is required)

---

### 5. DHCP Options Set

Incorrect DHCP configuration may point to invalid DNS servers.

---

### 6. Network ACL

Allow:

```
UDP 53

TCP 53
```

---

### Debug Commands

Inside a Pod:

```bash
nslookup kubernetes.default

dig google.com

cat /etc/resolv.conf
```

---

## Production Interview Answer

> I verify that DNS support and hostnames are enabled for the VPC, ensure CoreDNS is healthy, check security groups, NACLs, route tables, and DHCP options, and test DNS resolution using `nslookup`, `dig`, and `/etc/resolv.conf` from inside a Pod. This helps isolate whether the issue is with Kubernetes DNS, the VPC, or the application.


# DevOps Engineer – 2nd Round Interview (Scenario-Based)
## Part 4 (Questions 16–20)

---

# 16. A Terraform module works in Dev but fails in Prod with the same variables. What's your approach?

## Answer

Even if the Terraform code and variables are the same, differences in the production environment can cause failures. I follow a structured troubleshooting approach.

### Step 1: Review the Error Message

Run:

```bash
terraform plan

terraform apply
```

Check whether the failure is due to:

- Permission denied
- Resource already exists
- Dependency failure
- Quota exceeded
- Invalid configuration

---

### Step 2: Compare Environment Configurations

Verify:

- AWS Account ID
- AWS Region
- Backend Configuration
- Terraform Workspace
- Provider Version

Commands:

```bash
terraform workspace show

terraform providers
```

---

### Step 3: Check IAM Permissions

Production often has stricter IAM policies than Dev.

Verify:

- EC2 permissions
- VPC permissions
- IAM role permissions
- S3 backend access
- DynamoDB lock table access

---

### Step 4: Compare Existing Infrastructure

Production may already contain:

- VPC
- Subnets
- Security Groups
- IAM Roles

Terraform may attempt to recreate existing resources.

Use:

```bash
terraform state list

terraform import
```

if required.

---

### Step 5: Validate Module Inputs

Check:

```bash
terraform validate

terraform plan
```

Ensure all required variables are supplied.

---

### Step 6: Check Resource Limits

Production may have reached AWS service quotas.

Examples:

- VPC Limits
- EIP Limits
- EC2 Limits
- EBS Limits

---

### Step 7: Compare Terraform Versions

```bash
terraform version
```

Different Terraform or provider versions may behave differently.

---

### Step 8: Review Logs

Enable debugging:

```bash
export TF_LOG=DEBUG
terraform apply
```

---

## Production Interview Answer

> I first review the Terraform error message, compare Dev and Prod configurations, verify IAM permissions, ensure the correct workspace and backend are being used, check for existing resources that may require import, validate module inputs, review AWS service quotas, and enable Terraform debug logs if necessary. In production, environment differences are often the root cause rather than the Terraform module itself.

---

# 17. Explain how Kubernetes Secrets are stored, and why that matters for security.

## Answer

Kubernetes Secrets store sensitive information such as:

- Database passwords
- API Keys
- OAuth Tokens
- TLS Certificates
- SSH Keys

---

### Storage Flow

```
Secret YAML

↓

API Server

↓

etcd

↓

Pod
```

Secrets are stored inside **etcd**, the Kubernetes key-value database.

---

### Important Security Fact

Secrets are **Base64 encoded**, **not encrypted** by default.

Example:

```yaml
data:
  password: YWRtaW4xMjM=
```

Anyone with access to etcd can decode the value unless encryption at rest is enabled.

---

### Production Best Practices

✔ Enable Encryption at Rest

```text
API Server
↓

Encryption Provider
↓

Encrypted etcd
```

---

✔ Restrict access using RBAC

```yaml
Role

RoleBinding

ServiceAccount
```

---

✔ Never store secrets in Git

Instead use:

- HashiCorp Vault
- AWS Secrets Manager
- External Secrets Operator
- Azure Key Vault

---

✔ Rotate secrets regularly

---

### How Pods Consume Secrets

As Environment Variables

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
```

Or

Mounted as files

```
/etc/secrets
```

---

## Production Interview Answer

> Kubernetes Secrets are stored in etcd and are Base64 encoded by default, which is not encryption. In production, I enable Encryption at Rest, enforce RBAC, avoid storing secrets in Git, integrate with AWS Secrets Manager or HashiCorp Vault, and rotate credentials regularly to ensure sensitive data remains protected.

---

# 18. Your Lambda function times out intermittently. How do you find the root cause?

## Answer

Intermittent Lambda timeouts usually indicate dependency, networking, or resource issues rather than problems with the Lambda service itself.

### Step 1: Review CloudWatch Logs

Check:

```
START

END

REPORT
```

Look for:

```
Task timed out after 30 seconds
```

---

### Step 2: Check CloudWatch Metrics

Review:

- Duration
- Errors
- Throttles
- Concurrent Executions

---

### Step 3: Verify Timeout Configuration

Example:

```
Lambda Timeout

30 seconds

Application Execution

35 seconds

↓

Timeout
```

Increase timeout if justified.

---

### Step 4: Check VPC Networking

If Lambda runs inside a VPC:

Verify:

- NAT Gateway
- Route Tables
- Security Groups
- Subnets

---

### Step 5: Investigate Downstream Services

Slow responses from:

- RDS
- DynamoDB
- External APIs
- Redis
- S3

can delay Lambda execution.

---

### Step 6: Check Cold Starts

Large deployment packages or VPC-enabled Lambdas may experience cold start delays.

Mitigation:

- Provisioned Concurrency
- Smaller packages
- Dependency optimization

---

### Step 7: Optimize Code

Identify:

- Infinite loops
- Long-running queries
- Unnecessary retries

Use AWS X-Ray for tracing.

---

## Production Interview Answer

> I analyze CloudWatch logs and metrics, verify timeout settings, inspect downstream service latency, review VPC networking, investigate cold starts, and use AWS X-Ray to trace the request path. Most intermittent timeouts are caused by slow dependencies or networking issues rather than Lambda itself.

---

# 19. Explain the tradeoffs between Self-Managed Kubernetes and Amazon EKS.

## Answer

| Feature | Self-Managed Kubernetes | Amazon EKS |
|----------|-------------------------|------------|
| Control Plane | Managed by you | Managed by AWS |
| Upgrades | Manual | AWS Managed |
| High Availability | Configure yourself | Built-in |
| Maintenance | High | Low |
| Cost | Lower infrastructure cost but higher operational effort | EKS control plane cost + reduced operational effort |
| Security | Fully managed by your team | AWS handles control plane security |
| Scaling | Manual configuration | Integrated with Cluster Autoscaler, Managed Node Groups, Fargate |
| Monitoring | Manual setup | Easy integration with CloudWatch and AWS services |

### When to Use Self-Managed Kubernetes

- On-premises deployments
- Full control over Kubernetes components
- Highly customized environments

### When to Use Amazon EKS

- Production workloads on AWS
- Reduced operational overhead
- Managed control plane
- Faster upgrades
- Better integration with AWS services

---

## Production Interview Answer

> Self-managed Kubernetes provides maximum flexibility but requires the team to manage the control plane, upgrades, security, backups, and high availability. Amazon EKS manages the control plane, reduces operational effort, integrates seamlessly with AWS services, and is generally my preferred choice for production workloads running on AWS.

---

# 20. Your CI/CD pipeline deploys successfully but users still see the old version of the application. How do you troubleshoot?

## Answer

A successful deployment doesn't always mean users are accessing the latest version. I investigate the entire deployment path.

### Step 1: Verify Deployment

```bash
kubectl rollout status deployment my-app

kubectl get pods
```

Ensure new Pods are running.

---

### Step 2: Verify Image Version

```bash
kubectl describe pod

kubectl get pods -o wide
```

Check the image tag.

Avoid using:

```
latest
```

Use immutable version tags:

```
v1.2.5

Build-125
```

---

### Step 3: Verify Rolling Update

```bash
kubectl rollout history deployment my-app
```

Confirm the new ReplicaSet is active.

---

### Step 4: Check Service Endpoints

```bash
kubectl get endpoints
```

Ensure traffic points to the new Pods.

---

### Step 5: Verify Ingress / Load Balancer

Check:

- ALB Target Groups
- NGINX Ingress
- AWS Load Balancer Controller

Healthy targets should point to the new Pods.

---

### Step 6: Clear Application Cache

Possible caches:

- Browser Cache
- CloudFront CDN
- Redis Cache
- API Gateway Cache

Invalidate cache if necessary.

---

### Step 7: Verify DNS

Ensure DNS still isn't pointing to an old environment.

---

### Step 8: Check Application Version

Expose a health endpoint:

```
/version
```

Example response:

```json
{
  "version":"1.2.5",
  "build":"125"
}
```

This confirms what version is actually serving traffic.

---

### Production Interview Answer

> I verify the deployment rollout, confirm the correct image tag is running, inspect ReplicaSets and Service endpoints, validate Ingress and Load Balancer routing, check for CDN or browser caching, verify DNS, and confirm the application version through a health endpoint. Successful deployment does not always guarantee users are receiving traffic from the latest application version.

---

# ⭐ Interview Tip

For scenario-based questions, interviewers expect you to follow a structured troubleshooting methodology:

1. **Identify the issue** using logs, metrics, and alerts.
2. **Validate the infrastructure** (network, IAM, storage, compute).
3. **Inspect the application** (logs, health checks, configuration).
4. **Implement the fix** with minimal downtime.
5. **Verify recovery** using monitoring and user validation.
6. **Perform RCA (Root Cause Analysis)** and implement preventive measures.

Following this approach demonstrates strong production experience and systematic problem-solving skills.



# DevOps Interview Questions & Answers (4 Years Experience)

---

# Kubernetes

## 1. Difference between a Deployment and a StatefulSet?

### Answer

A **Deployment** is used for **stateless applications** where Pods are interchangeable and do not require persistent identities. Examples include web applications, APIs, and microservices. Deployments support rolling updates, rollbacks, and automatically manage ReplicaSets.

A **StatefulSet** is used for **stateful applications** such as PostgreSQL, MySQL, MongoDB, Kafka, or Elasticsearch. Each Pod has a stable hostname, a unique network identity, and its own Persistent Volume that remains attached even if the Pod is recreated. StatefulSets also support ordered deployment and termination, which is important for clustered databases.

In production, I use Deployments for stateless workloads and StatefulSets for applications that require persistent storage and stable identities.

---

## 2. How does a Service discover and route traffic to Pods?

### Answer

A Kubernetes Service uses **labels and selectors** to identify the Pods that belong to it. When a Pod is created with matching labels, it is automatically added to the Service's Endpoints.

When a client sends a request to the Service, **kube-proxy** uses iptables or IPVS rules to distribute traffic across all healthy Pods associated with that Service. Pods are automatically added or removed from the endpoint list as they become ready or terminate.

For external traffic, requests typically follow this flow:

**Client → Load Balancer/Ingress → Service → Pod**

This provides a stable endpoint even though Pod IPs change over time.

---

## 3. How would you troubleshoot a Pod stuck in CrashLoopBackOff?

### Answer

I start by describing the Pod using:

```bash
kubectl describe pod <pod-name>
```

to review events and identify restart reasons.

Next, I inspect the application logs:

```bash
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
```

I then verify ConfigMaps, Secrets, environment variables, mounted volumes, image versions, startup commands, liveness and readiness probes, and resource limits.

I also check whether the application can connect to required services such as databases or APIs.

If the issue started after a deployment, I compare the current release with the previous working version and roll back if necessary.

---

# GitHub

## 4. Difference between Merge and Rebase?

### Answer

A **Merge** combines two branches by creating a new merge commit. It preserves the complete commit history and clearly shows when branches were integrated.

A **Rebase** moves commits from one branch onto another, creating a cleaner and more linear history by rewriting commit hashes.

In production, I generally use **Merge** for shared branches because it preserves history. I use **Rebase** on my local feature branch before creating a Pull Request to keep the commit history clean, but I avoid rebasing branches that others are already using.

---

## 5. How do you handle merge conflicts in a team workflow?

### Answer

When a merge conflict occurs, I first pull the latest changes from the target branch and attempt the merge or rebase.

Git highlights the conflicting files, and I manually review each conflict, discuss any business logic conflicts with the development team if needed, and resolve them carefully.

After resolving the conflicts, I test the application, commit the resolved changes, and push the updated branch.

To reduce future conflicts, I encourage developers to create small Pull Requests, rebase frequently, and integrate changes regularly.

---

## 6. GitFlow vs Trunk-Based Development — what's your branching strategy?

### Answer

**GitFlow** uses long-lived branches such as `main`, `develop`, `feature`, `release`, and `hotfix`. It is suitable for large projects with scheduled releases and strict release management.

**Trunk-Based Development** encourages developers to commit small changes frequently to a single main branch or very short-lived feature branches. It supports faster CI/CD and continuous delivery.

In my projects, I primarily follow a GitFlow-style workflow with feature branches, Pull Requests, code reviews, and release branches because it aligns well with controlled production deployments.

---

# Jenkins

## 7. Declarative vs Scripted Pipeline — how do you structure each?

### Answer

A **Declarative Pipeline** uses a structured syntax with predefined sections such as `pipeline`, `agent`, `stages`, and `steps`. It is easier to read, maintain, and is the preferred choice for most CI/CD pipelines.

A **Scripted Pipeline** is written entirely in Groovy and provides greater flexibility for complex workflows, conditional logic, loops, and dynamic behavior.

In production, I generally use Declarative Pipelines because they are standardized and easier for teams to maintain, while using Scripted syntax only for advanced use cases that require more flexibility.

---

## 8. How do you manage secrets/credentials in a pipeline?

### Answer

I never hardcode passwords, API keys, or tokens in Jenkinsfiles.

Instead, I store them securely in the Jenkins Credentials Store as Secret Text, Username/Password, SSH Keys, or AWS credentials.

The pipeline retrieves credentials using the `credentials()` function or the `withCredentials` block, ensuring secrets are injected only during execution and are masked in console logs.

For cloud-native deployments, I also integrate with AWS Secrets Manager or HashiCorp Vault where appropriate.

---

## 9. How do you trigger a pipeline automatically on a Git push?

### Answer

I configure a **Git Webhook** in GitHub or GitLab that sends an HTTP POST request to Jenkins whenever code is pushed.

Jenkins listens for the webhook, detects the repository event, and automatically triggers the appropriate pipeline.

This eliminates manual builds and enables fully automated Continuous Integration.

---

# AWS Infrastructure

## 10. How would you design a VPC for a 3-tier application?

### Answer

I would create a VPC spanning at least two or three Availability Zones for high availability.

Each Availability Zone contains:

- Public Subnet
- Private Application Subnet
- Private Database Subnet

The Application Load Balancer resides in the public subnets, application servers or EKS worker nodes run in private subnets, and the database resides in isolated private database subnets.

Internet access for private instances is provided through NAT Gateways, while Security Groups and Network ACLs restrict traffic between layers.

This architecture improves security, scalability, and fault tolerance.

---

## 11. ALB vs NLB — when do you use each?

### Answer

I use an **Application Load Balancer (ALB)** for HTTP and HTTPS applications because it supports Layer 7 features such as path-based routing, host-based routing, SSL termination, authentication, and AWS WAF integration.

I use a **Network Load Balancer (NLB)** for high-performance TCP or UDP workloads that require ultra-low latency, static IP addresses, or millions of concurrent connections.

Most web applications use ALB, while gaming servers, Kafka, VoIP, and financial applications often benefit from NLB.

---

## 12. IAM Roles vs Policies — how do you enforce least privilege?

### Answer

An **IAM Policy** defines what actions are allowed or denied on AWS resources.

An **IAM Role** is an identity that applications, EC2 instances, Lambda functions, or EKS Pods can assume to obtain temporary credentials.

To enforce least privilege, I create narrowly scoped policies granting only the permissions required for a specific workload and attach them to IAM Roles instead of users. This eliminates long-term credentials and minimizes security risks.

---

# Helm

## 13. Difference between a Helm Chart and a Helm Release?

### Answer

A **Helm Chart** is a reusable package containing Kubernetes manifests, templates, default values, and metadata required to deploy an application.

A **Helm Release** is a running instance of that chart installed in a Kubernetes cluster.

One chart can be installed multiple times as different releases with different configurations.

---

## 14. How do you manage environment-specific values (Dev/Stage/Prod)?

### Answer

I maintain separate values files for each environment.

Example:

```
values-dev.yaml

values-stage.yaml

values-prod.yaml
```

During deployment, I specify the appropriate values file:

```bash
helm upgrade --install app ./chart \
-f values-prod.yaml
```

This allows the same chart to be reused across environments while customizing replicas, resource limits, image tags, and environment-specific configurations.

---

## 15. How do you roll back a failed Helm deployment?

### Answer

First, I review the release history:

```bash
helm history <release-name>
```

Then I roll back to the previous stable revision:

```bash
helm rollback <release-name> <revision-number>
```

After rollback, I verify Pod health, application functionality, and Kubernetes events before investigating the root cause.

---

# Docker

## 16. COPY vs ADD in a Dockerfile — what's the difference?

### Answer

`COPY` simply copies files and directories from the local system into the Docker image.

`ADD` provides additional functionality such as automatically extracting local compressed archives and downloading files from URLs.

Since these extra features can introduce unexpected behavior, I use **COPY** in most cases and reserve **ADD** only when archive extraction is genuinely required.

---

## 17. How do you reduce Docker image size?

### Answer

I use **multi-stage builds** to separate the build environment from the runtime image.

Other optimizations include:

- Using lightweight base images such as Alpine or Distroless.
- Removing unnecessary build dependencies.
- Combining RUN commands to reduce layers.
- Using `.dockerignore` to exclude unnecessary files.
- Cleaning package caches after installation.

These practices improve deployment speed, reduce storage costs, and lower the attack surface.

---

## 18. CMD vs ENTRYPOINT — when do you use each?

### Answer

`ENTRYPOINT` defines the main executable that always runs when the container starts.

`CMD` provides default arguments to the ENTRYPOINT or specifies the default command if no ENTRYPOINT exists.

In production, I use ENTRYPOINT for the application executable and CMD for configurable default parameters, allowing users to override arguments without replacing the executable.

---

# Terraform

## 19. Do you know Terraform, and have you used it?

### Answer

Yes. I have used Terraform extensively to provision and manage AWS infrastructure including VPCs, subnets, EC2 instances, IAM roles, EKS clusters, security groups, Route 53 records, Application Load Balancers, and Amazon ECR repositories.

I organize Terraform code using reusable modules, store the remote state in Amazon S3 with DynamoDB state locking, and execute Terraform through Jenkins CI/CD pipelines. I also manage multiple environments using environment-specific variable files and follow Infrastructure as Code best practices to ensure consistent, repeatable, and version-controlled deployments.




# DevOps Interview Questions & Answers

# Ansible

## → Is Ansible inventory static or dynamic?

Ansible inventory is the list of managed hosts on which playbooks execute. It can be either **static** or **dynamic**.

A **static inventory** is a manually maintained file (INI or YAML format) containing server IPs, hostnames, and groups. It is suitable for environments where servers rarely change.

Example:
```ini
[web]
192.168.1.10
192.168.1.11

[db]
192.168.1.20
```

A **dynamic inventory** automatically retrieves hosts from cloud providers or external systems such as AWS, Azure, GCP, VMware, Kubernetes, or CMDBs. It is ideal for cloud environments where instances are frequently created or terminated.

For example, in AWS EC2, Ansible can automatically discover instances based on tags without manually updating the inventory file.

**Difference**

| Static Inventory | Dynamic Inventory |
|------------------|-------------------|
| Manually maintained | Automatically generated |
| Best for on-premises | Best for cloud environments |
| Requires manual updates | Updates automatically |
| Simple configuration | Requires inventory plugins or scripts |

---

## → Difference between shell module and command module?

Both modules execute commands on remote hosts, but they behave differently.

### Command Module

The `command` module executes commands directly without invoking a shell.

Example:

```yaml
- name: Check disk usage
  command: df -h
```

Characteristics:
- More secure
- Does not understand shell operators
- Cannot use pipes (`|`), redirects (`>`), variables (`$HOME`), or wildcards (`*`)

Example that will fail:

```yaml
command: cat file.txt | grep error
```

---

### Shell Module

The `shell` module executes commands through `/bin/sh`.

Example:

```yaml
- name: Find error logs
  shell: cat /var/log/app.log | grep ERROR
```

Characteristics:
- Supports pipes
- Supports redirection
- Supports environment variables
- Supports shell scripting

Example:

```yaml
shell: echo "Backup" > backup.txt
```

### Difference

| Command Module | Shell Module |
|---------------|--------------|
| Executes directly | Executes through shell |
| More secure | Less secure |
| No shell features | Supports shell features |
| Faster | Slightly slower |
| Preferred whenever possible | Use only when shell functionality is needed |

---

## → Write a playbook to copy a file and start a service.

```yaml
---
- name: Copy configuration file and restart nginx
  hosts: web
  become: yes

  tasks:

    - name: Copy nginx configuration
      copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf
        owner: root
        group: root
        mode: '0644'

    - name: Ensure nginx service is running
      service:
        name: nginx
        state: started
        enabled: yes
```

**Explanation**

- `hosts` specifies target machines.
- `become: yes` executes tasks with sudo privileges.
- `copy` module copies files from the control node to managed nodes.
- `service` module starts and enables the service.

---

## → What is a module in Ansible?

A module is a reusable unit of work in Ansible that performs a specific task on managed nodes. Instead of writing shell scripts, Ansible modules execute predefined operations such as installing packages, copying files, managing users, starting services, creating directories, or interacting with cloud resources.

Examples:

Install package

```yaml
- yum:
    name: httpd
    state: present
```

Copy file

```yaml
- copy:
    src: app.conf
    dest: /etc/app.conf
```

Create user

```yaml
- user:
    name: devops
    state: present
```

Start service

```yaml
- service:
    name: nginx
    state: started
```

Common modules include:

- copy
- file
- package
- yum
- apt
- service
- user
- group
- command
- shell
- git
- template
- cron

---

# Linux Scenarios

## → Scenario based questions on the find command.

The `find` command searches files and directories based on various criteria.

### Find a file by name

```bash
find /home -name "config.yaml"
```

---

### Find all log files

```bash
find /var/log -name "*.log"
```

---

### Find files modified in last 2 days

```bash
find /var/log -mtime -2
```

---

### Find files larger than 500 MB

```bash
find / -size +500M
```

---

### Find empty files

```bash
find /tmp -type f -empty
```

---

### Find directories only

```bash
find /opt -type d
```

---

### Delete files older than 30 days

```bash
find /backup -type f -mtime +30 -delete
```

---

### Change permissions of all shell scripts

```bash
find . -name "*.sh" -exec chmod +x {} \;
```

---

### Find files owned by a specific user

```bash
find /home -user ubuntu
```

---

### Find files containing a specific text

```bash
find /var/log -name "*.log" -exec grep -H "ERROR" {} \;
```

---

## → How do you check listening ports?

To identify which ports are listening for incoming connections:

Using ss (recommended)

```bash
ss -tuln
```

With process information

```bash
ss -tulpn
```

Using netstat

```bash
netstat -tulpn
```

Using lsof

```bash
lsof -i -P -n
```

To check a specific port:

```bash
ss -tulpn | grep 8080
```

---

## → How do you check running processes?

View all running processes

```bash
ps -ef
```

Interactive process monitor

```bash
top
```

Enhanced process monitor

```bash
htop
```

Search for a process

```bash
ps -ef | grep nginx
```

Using pgrep

```bash
pgrep nginx
```

Check CPU and memory usage

```bash
top
```

Kill a process

```bash
kill -9 PID
```

---

# F5 Load Balancer

## → What is an iRule?

An iRule is a TCL-based scripting language used in F5 BIG-IP to inspect, modify, and control traffic passing through the load balancer. It allows administrators to implement advanced traffic management logic beyond standard load balancing.

Common use cases include:
- URL redirection
- HTTP header manipulation
- Session persistence
- Traffic routing based on URI or client IP
- Blocking malicious requests
- Custom authentication

Example:

```tcl
when HTTP_REQUEST {
    if { [HTTP::uri] starts_with "/admin" } {
        pool admin_pool
    }
}
```

---

## → Types of load balancing methods and how they work.

### Round Robin

Requests are distributed equally among all servers in sequence.

Example:

```
Request1 → Server1
Request2 → Server2
Request3 → Server3
```

---

### Least Connections

New requests are sent to the server with the fewest active connections.

Useful when request durations vary.

---

### Ratio (Weighted Round Robin)

Servers receive traffic based on assigned weights.

Example:

```
Server1 Weight=3
Server2 Weight=1

Traffic:
75% → Server1
25% → Server2
```

---

### Fastest Response

Traffic is directed to the server responding the fastest.

---

### Observed

Considers both response time and active connections before selecting a server.

---

### Predictive

Uses historical performance data to predict which server will perform best.

---

### Dynamic Ratio

Server load is monitored dynamically (CPU, memory, etc.), and traffic is distributed accordingly.

---

## → Difference between Virtual Server, Pool, Pool Member and Node.

| Component | Description |
|-----------|-------------|
| **Virtual Server** | The client-facing IP address and port where incoming traffic arrives. |
| **Pool** | A logical collection of backend servers that provide the same application. |
| **Pool Member** | An individual server and port within a pool (for example, `10.0.0.10:80`). |
| **Node** | The actual backend server identified by its IP address. A node can belong to multiple pools. |

Flow:

```
Client
   |
Virtual Server
   |
Pool
   |
Pool Members
   |
Backend Nodes
```

---

# Jenkins

## → Freestyle vs Pipeline — what is the difference?

A **Freestyle Project** is the traditional Jenkins job where build steps are configured through the Jenkins UI. It is simple to create and suitable for basic automation, but it becomes difficult to manage as the CI/CD process grows. Configuration is stored in Jenkins rather than version control, making it harder to track changes and reproduce builds.

A **Pipeline** defines the entire CI/CD workflow as code using a `Jenkinsfile`. It supports multiple stages such as build, test, scan, package, and deployment. Pipelines are version-controlled, reusable, easier to maintain, and better suited for modern DevOps practices. They also support advanced features like parallel execution, conditional stages, shared libraries, and integration with Git.

| Freestyle | Pipeline |
|------------|----------|
| GUI-based configuration | Pipeline as Code |
| Limited flexibility | Highly flexible |
| Hard to version control | Stored in Git |
| Best for simple jobs | Best for complex CI/CD |
| Less reusable | Highly reusable |
| Limited stage visibility | Clear stage-wise visualization |

---

## → What are plugins and what types exist?

Plugins extend Jenkins functionality and allow integration with various DevOps tools and platforms. Jenkins has thousands of plugins that enable source code management, build automation, testing, security, notifications, cloud deployments, and reporting.

Common plugin categories include:

- **SCM Plugins:** Git, GitHub, GitLab, Bitbucket
- **Build Tools:** Maven, Gradle, Ant
- **Pipeline Plugins:** Pipeline, Blue Ocean, Shared Libraries
- **Cloud Plugins:** AWS, Azure, Kubernetes, Docker
- **Notification Plugins:** Slack, Microsoft Teams, Email Extension
- **Security Plugins:** Role-Based Authorization, LDAP, Active Directory, Credentials
- **Testing Plugins:** JUnit, TestNG, Cucumber
- **Code Quality Plugins:** SonarQube, Checkstyle, PMD
- **Artifact Repository Plugins:** Nexus Repository, JFrog Artifactory
- **Monitoring Plugins:** Prometheus Metrics, Monitoring Plugin

Plugins make Jenkins highly extensible, allowing organizations to integrate their complete DevOps toolchain into a single CI/CD platform.

---

# Scenario Question

## → A client is observing latency in an application. What steps do you take to identify the root cause?

When users report application latency, I follow a structured troubleshooting approach to isolate the bottleneck rather than making assumptions.

First, I verify whether the issue is widespread or limited to specific users, regions, APIs, or environments. I review monitoring dashboards such as Prometheus, Grafana, CloudWatch, or Datadog to check response time, request rate, error rate, CPU, memory, disk I/O, and network latency.

Next, I identify where the delay occurs:
- Check the load balancer for backend health, connection counts, and response times.
- Review application logs for slow requests, exceptions, or timeout errors.
- Analyze web server logs (Nginx/Apache) and application logs.
- Examine database performance by checking slow query logs, locks, CPU utilization, and connection pool usage.
- Verify Kubernetes or server health using `kubectl top`, `kubectl describe`, and pod logs to identify resource constraints or restarts.
- Check network connectivity, DNS resolution, firewall rules, and latency using tools such as `ping`, `traceroute`, `mtr`, and `curl`.

If infrastructure resources are saturated, I evaluate scaling options such as increasing pod replicas with HPA, adding EC2 instances, or resizing compute resources. If the issue is code-related, I work with developers to profile the application and optimize inefficient queries or business logic.

Finally, after implementing the fix, I validate that response times have returned to normal, continue monitoring to ensure stability, perform a root cause analysis (RCA), document the incident, and recommend preventive measures such as better monitoring, alerting, performance testing, or auto-scaling.


# DevOps Engineer Interview Questions (4 Years Experience)

---

# 1. How does a request flow from a browser to a Kubernetes Pod?

### Answer

When a user enters a URL, the browser queries DNS (Route 53) to resolve the domain name. The request reaches the Application Load Balancer (ALB), which forwards it to the Kubernetes Ingress Controller. The Ingress matches the host or path rules and routes the request to the appropriate Kubernetes Service. The Service uses kube-proxy to load balance traffic to one of the healthy Pods. The Pod processes the request, communicates with backend services if needed, and returns the response back through the same path.

---

# 2. What happens internally when you run a Docker container?

### Answer

When `docker run` is executed, Docker checks if the image exists locally. If not, it pulls it from the registry. Docker creates a writable container layer on top of the image, sets up namespaces and cgroups for isolation, configures networking and volumes, executes the ENTRYPOINT and CMD instructions, and starts the application process. Once the main process starts successfully, the container enters the Running state.

---

# 3. How does Kubernetes decide which node should run a Pod?

### Answer

The Kubernetes Scheduler evaluates all worker nodes and selects the most suitable one based on available CPU and memory, resource requests, node labels, taints and tolerations, node affinity, pod affinity/anti-affinity, and scheduling policies. After selecting a node, kubelet receives the Pod specification, pulls the image, and starts the container.

---

# 4. Difference between Readiness, Liveness, and Startup Probes.

### Answer

**Readiness Probe** checks whether the application is ready to receive traffic. If it fails, the Pod is removed from the Service endpoints.

**Liveness Probe** checks whether the application is still healthy. If it fails repeatedly, Kubernetes restarts the container.

**Startup Probe** is used for slow-starting applications. It disables Readiness and Liveness checks until the application has fully started, preventing unnecessary restarts.

---

# 5. Why would a Pod be Running but the application still be unavailable?

### Answer

A Pod can be Running while the application is unavailable due to failed Readiness Probes, incorrect Service selectors, missing Service Endpoints, application startup failures, database connectivity issues, ConfigMap or Secret misconfiguration, Ingress routing problems, or Load Balancer health check failures. I verify each layer systematically to identify the root cause.

---

# 6. Explain the complete lifecycle of a CI/CD pipeline from commit to production.

### Answer

Developers commit code to GitHub, triggering Jenkins via webhook. Jenkins checks out the code, builds it using Maven, runs unit tests, performs SonarQube analysis, builds a Docker image, scans it for vulnerabilities, pushes it to Amazon ECR, and deploys it to Amazon EKS using Helm or Kubernetes manifests. Post-deployment, health checks and smoke tests are executed. If successful, the deployment is completed; otherwise, the pipeline stops and alerts the team.

---

# 7. How does Terraform build and execute its dependency graph?

### Answer

Terraform analyzes resource references to automatically create a dependency graph. Resources without dependencies are created in parallel, while dependent resources are created in the correct order. If required, explicit dependencies can be defined using the `depends_on` argument.

---

# 8. What is Terraform Drift, and how do you handle it?

### Answer

Terraform Drift occurs when infrastructure is modified manually outside Terraform. I detect drift using `terraform plan`, which compares the Terraform state with the actual infrastructure. If the manual change is valid, I update the Terraform code. Otherwise, I run `terraform apply` to restore the desired state.

---

# 9. How does Terraform state locking work with S3 and DynamoDB?

### Answer

Terraform stores the state file in an Amazon S3 bucket. Before modifying the infrastructure, Terraform creates a lock entry in DynamoDB. While the lock exists, no other user or pipeline can update the same state file. Once the operation completes successfully, the lock is released automatically, preventing concurrent updates and state corruption.

---

# 10. How do you design reusable Terraform modules for enterprise projects?

### Answer

I create separate modules for resources such as VPC, EC2, IAM, Security Groups, EKS, and RDS. Common logic is placed inside modules, while environment-specific values are passed through variables or `.tfvars` files. Each environment maintains its own backend and state file. This approach reduces code duplication, improves consistency, and simplifies maintenance across Development, QA, UAT, and Production.

---

# 11. Explain the difference between Rolling, Blue-Green, and Canary deployments.

### Answer

**Rolling Deployment** gradually replaces old Pods with new ones without downtime. It is simple and is Kubernetes' default deployment strategy.

**Blue-Green Deployment** maintains two identical environments. The new version is deployed to the Green environment while Blue serves production traffic. Once validated, traffic is switched to Green, providing instant rollback if needed.

**Canary Deployment** releases the new version to a small percentage of users first (e.g., 5–10%). After monitoring for issues, traffic is gradually increased until all users are on the new version.

I generally use Rolling Deployments for regular releases, Blue-Green for critical production deployments requiring instant rollback, and Canary for high-risk changes where gradual exposure reduces business impact.

---

# 12. How would you implement a zero-downtime deployment?

### Answer

I ensure multiple replicas of the application are running and configure proper Readiness and Liveness Probes. During deployment, Kubernetes waits for new Pods to become Ready before terminating old Pods. For critical applications, I prefer Blue-Green deployment, where the new environment is fully validated before switching traffic through the Load Balancer or Kubernetes Service. If any issue occurs, traffic can immediately be redirected to the previous version without downtime.

---

# 13. How do you secure secrets across CI/CD, Kubernetes, and cloud services?

### Answer

Secrets are never hardcoded in source code or pipeline scripts. In Jenkins, I use the Credentials Store. In Kubernetes, sensitive data is stored as Kubernetes Secrets or integrated with AWS Secrets Manager or HashiCorp Vault. IAM Roles control access to cloud resources, and only authorized applications or users can retrieve secrets. I also enable Git secret scanning and rotate credentials periodically to improve security.

---

# 14. Explain IAM Roles vs IAM Policies with a real-world scenario.

### Answer

An **IAM Policy** defines permissions, such as allowing access to Amazon S3 or EC2. An **IAM Role** is an identity that can assume one or more policies and provides temporary credentials.

For example, an EC2 instance running an application needs to access an S3 bucket. Instead of storing AWS access keys on the server, I attach an IAM Role with an S3 access policy to the EC2 instance. The application automatically receives temporary credentials, improving security and eliminating long-term access keys.

---

# 15. How do you troubleshoot intermittent 503 errors in Kubernetes?

### Answer

I first check whether the Pods are Ready using `kubectl get pods`. Then I verify the Service endpoints, Service selectors, and Ingress configuration. I review application logs, Kubernetes Events, and Load Balancer target health. I also check whether backend services such as databases are available and whether resource limits are causing application instability. By validating each layer, I can quickly determine whether the issue lies in Kubernetes, networking, or the application itself.

---

# 16. How do you identify the bottleneck when an application becomes slow in production?

### Answer

I begin by reviewing Grafana dashboards for CPU, memory, network, and response time metrics. Then I analyze application logs in ELK, inspect Kubernetes Pods for resource utilization, verify database performance, check Load Balancer metrics, and review distributed traces if available. This systematic approach helps determine whether the bottleneck is in the infrastructure, network, application, or database.

---

# 17. Difference between logs, metrics, and traces. When do you use each?

### Answer

**Logs** provide detailed event information for debugging errors and application behavior.

**Metrics** are numerical values such as CPU usage, memory utilization, request rate, and latency, which are used for monitoring system health and triggering alerts.

**Traces** track a request across multiple services in a distributed application, helping identify where delays occur.

In production, I use metrics for monitoring, logs for troubleshooting, and traces for debugging microservices performance.

---

# 18. How do you design monitoring and alerting to reduce alert fatigue?

### Answer

I monitor only critical business and infrastructure metrics instead of generating alerts for every event. Alert thresholds are carefully tuned to minimize false positives. Related alerts are grouped, duplicate notifications are suppressed, and severity levels are defined for warning and critical conditions. Dashboards provide overall health visibility, while alerts are sent only for actionable issues through email, Slack, or Microsoft Teams. This ensures the team focuses on genuine production problems.

---

# 19. Explain a production incident you resolved and your RCA approach.

### Answer

During a production deployment, users started receiving **503 Service Unavailable** errors. I checked the Pods and found the readiness probes were failing. Application logs showed an incorrect database connection string in the ConfigMap. After updating the configuration and restarting the deployment, the Pods became Ready and traffic was restored. During the Root Cause Analysis (RCA), we identified a missing configuration validation step in the CI/CD pipeline. We added automated configuration checks before deployment to prevent similar incidents in the future.

---

# 20. If a deployment fails midway in production, how do you recover safely?

### Answer

I first stop the deployment to prevent further impact. I review deployment logs, Kubernetes Events, and monitoring dashboards to identify the failure. If the application is unhealthy, I immediately roll back using `kubectl rollout undo` or switch traffic back to the previous environment in a Blue-Green deployment. After service is restored, I perform RCA, fix the issue in a lower environment, validate the changes, and then redeploy following the standard release process. This approach minimizes downtime while ensuring production stability.

---


# BNP Paribas DevOps Interview Questions (4 Years Experience)

---

# Kafka

## 1. What would be your day-to-day role when it comes to Kafka?

### Answer

In my day-to-day activities, I monitor Kafka cluster health, broker availability, consumer lag, topic performance, and replication status. I troubleshoot producer or consumer issues, manage topic creation and configuration, monitor disk usage, ensure high availability, and work with development teams to resolve messaging-related production issues. I also use Grafana and Prometheus to monitor Kafka metrics and respond to alerts.

---

## 2. Can you tell me the core components of Kafka and what each component does?

### Answer

Kafka consists of **Producer, Broker, Consumer, Topic, Partition, ZooKeeper/KRaft, and Consumer Groups**. Producers publish messages to Topics. Topics are divided into Partitions for scalability. Brokers store the data. Consumers read messages from Topics. Consumer Groups enable load balancing across multiple consumers. ZooKeeper (or KRaft in newer versions) manages cluster metadata and leader election.

---

## 3. Do you have working experience on the ELK Stack?

### Answer

Yes. I have worked with the ELK Stack for centralized log management. Application and system logs were collected using Filebeat, processed through Logstash, stored in Elasticsearch, and visualized using Kibana. This helped us quickly troubleshoot production issues and analyze logs from multiple servers in one place.

---

## 4. What sort of improvements did you make in your real-time ELK Stack project?

### Answer

I optimized Logstash pipelines by reducing unnecessary filters, configured Index Lifecycle Management (ILM) to automatically archive old logs, improved Elasticsearch indexing performance, created Kibana dashboards for faster troubleshooting, and configured alerts for critical application errors. These improvements reduced storage costs and improved search performance.

---

## 5. How good are you in development?

### Answer

I am not a full-time developer, but I am comfortable with Python, Bash, and Shell scripting. I have written automation scripts for deployments, monitoring, log cleanup, health checks, backup automation, and CI/CD pipeline tasks. I can also understand application code sufficiently to troubleshoot deployment and infrastructure issues.

---

## 6. What kind of automation have you done?

### Answer

I have automated infrastructure provisioning using Terraform, application deployments using Jenkins and Kubernetes, configuration management using Ansible, Docker image creation, monitoring alerts, backup scripts, log rotation, server health monitoring, and scheduled maintenance tasks using Shell and Python scripts.

---

## 7. Can you explain analyzers, tokenizers, and token filters in Elasticsearch?

### Answer

An **Analyzer** processes text before indexing. It consists of a **Tokenizer**, which breaks text into individual tokens or words, and **Token Filters**, which modify those tokens by converting them to lowercase, removing stop words, or applying stemming. Together, they improve search accuracy.

---

## 8. How does Kafka handle data durability?

### Answer

Kafka ensures durability by writing messages to disk and replicating them across multiple brokers based on the configured replication factor. Even if one broker fails, another replica becomes the leader, ensuring that data is not lost.

---

## 9. When should an application consider using Kafka?

### Answer

Kafka should be used when applications require high-throughput, real-time messaging, event streaming, asynchronous communication, log aggregation, microservices communication, or processing large volumes of data with high reliability.

---

## 10. What is the purpose of the replication factor in Kafka?

### Answer

The replication factor determines how many copies of each partition are maintained across different brokers. Higher replication improves fault tolerance because data remains available even if one or more brokers fail.

---

# Terraform

## 11. What is Terraform?

### Answer

Terraform is an Infrastructure as Code (IaC) tool that allows us to provision and manage cloud infrastructure using declarative configuration files. It automates infrastructure deployment, ensures consistency, and supports multiple cloud providers.

---

## 12. Why do we use Terraform?

### Answer

Terraform eliminates manual infrastructure creation, improves consistency, enables version control, supports automation through CI/CD pipelines, and makes infrastructure repeatable across Development, QA, UAT, and Production environments.

---

## 13. Explain the Terraform workflow.

### Answer

The standard workflow is:

**Write Configuration → terraform init → terraform validate → terraform plan → terraform apply → terraform destroy (if required).**

---

## 14. What is a Terraform state file?

### Answer

The Terraform state file stores the mapping between Terraform configuration and the actual infrastructure. It enables Terraform to track existing resources and perform incremental updates instead of recreating everything.

---

## 15. What is remote state?

### Answer

Remote state stores the Terraform state file in a shared backend such as Amazon S3 instead of the local machine. This allows multiple team members to collaborate safely while maintaining a single source of truth.

---

## 16. Why do we use an S3 bucket and DynamoDB for Terraform state?

### Answer

Amazon S3 stores the remote Terraform state file, while DynamoDB provides state locking. State locking prevents multiple users from modifying the same infrastructure simultaneously and protects the state from corruption.

---

## 17. What is state locking in Terraform?

### Answer

State locking prevents concurrent Terraform operations on the same infrastructure. When one user runs `terraform apply`, Terraform locks the state so that no other user can modify it until the operation completes.

---

## 18. What is a Terraform provider?

### Answer

A provider is a plugin that enables Terraform to interact with a specific platform such as AWS, Azure, GCP, or Kubernetes. It exposes the resources and services that Terraform can manage.

---

## 19. What is a Terraform resource?

### Answer

A resource represents an infrastructure component managed by Terraform, such as an EC2 instance, VPC, S3 bucket, Security Group, or Kubernetes deployment.

---

## 20. What are variables and outputs in Terraform?

### Answer

Variables allow dynamic values to be passed into Terraform configurations, making the code reusable. Outputs expose resource information such as EC2 public IPs or VPC IDs so they can be referenced by other modules or displayed after deployment.

---

## 21. What are Terraform modules?

### Answer

Modules are reusable collections of Terraform resources. They reduce code duplication, improve maintainability, and enable the same infrastructure code to be reused across multiple environments.

---

## 22. What is the difference between terraform plan and terraform apply?

### Answer

`terraform plan` shows the changes Terraform intends to make without modifying infrastructure. `terraform apply` executes those changes and updates the actual infrastructure.

---

## 23. What is terraform init?

### Answer

`terraform init` initializes the Terraform working directory by downloading providers, configuring the backend, and preparing the environment before any other Terraform command is executed.

---

## 24. What is Terraform drift?

### Answer

Terraform drift occurs when infrastructure is manually modified outside Terraform, causing the actual infrastructure to differ from the Terraform state. Drift is detected during `terraform plan`.

---

## 25. How do you import an existing AWS resource into Terraform?

### Answer

I first define the resource in the Terraform configuration and then use the `terraform import` command to associate the existing AWS resource with the Terraform state. After importing, I run `terraform plan` to verify that the configuration matches the existing infrastructure.

---

# Ansible

## 26. Explain the structure of an Ansible playbook.

### Answer

An Ansible playbook consists of **Hosts**, **Variables**, **Tasks**, **Handlers**, and optionally **Roles**. Tasks define the actions to perform, handlers execute only when notified, and roles organize reusable automation code.

---

## 27. What is the ansible.cfg file?

### Answer

The `ansible.cfg` file is the main Ansible configuration file. It defines settings such as the inventory location, SSH user, private key path, timeout values, privilege escalation, and default behavior for Ansible execution.

---

## 28. How do you integrate Ansible with the ELK Stack?

### Answer

Ansible automates the installation and configuration of Elasticsearch, Logstash, Kibana, and Filebeat across multiple servers. It ensures consistent configurations, simplifies upgrades, and enables repeatable deployments.

---

# Monitoring

## 29. What are the components of the ELK Stack?

### Answer

The ELK Stack consists of **Elasticsearch** for storing and searching logs, **Logstash** for collecting and processing logs, and **Kibana** for visualizing and analyzing log data. Filebeat is commonly used to ship logs from servers to Logstash.

---

## 30. What is the difference between Grafana and the ELK Stack?

### Answer

Grafana is primarily used for monitoring and visualizing metrics from tools like Prometheus and CloudWatch. ELK focuses on centralized log management and log analysis. Grafana answers **"What is happening?"**, while ELK helps answer **"Why did it happen?"**. Together, they provide complete observability.

---


# Scenario-Based DevOps Interview Questions (4 Years Experience)

---

## 1. Your pod is running but returning 503. Is it the Pod or the Service? How do you tell in under 2 minutes?

### Answer

I troubleshoot layer by layer.

1. First, I check whether the Pod is actually Ready using `kubectl get pods`.
2. Then I verify the readiness probe status and application logs.
3. Next, I check the Service Endpoints using `kubectl get endpoints <service-name>`. If there are no endpoints, the Service isn't routing traffic to the Pods.
4. If endpoints exist, I test the Service internally using a temporary Pod or `kubectl port-forward`.
5. Finally, I check the Ingress or Load Balancer configuration.

If the Pod is healthy but the Service has no endpoints, it's a Service issue. If the Service has healthy endpoints but the application returns 503, it's an application or Pod issue.

---

## 2. Terraform apply failed halfway. State file is now out of sync. What is your exact recovery plan?

### Answer

First, I stop all further Terraform operations to avoid additional changes. I verify whether the state file is locked and release the lock only if no Terraform process is running. Next, I compare the Terraform state with the actual AWS resources using `terraform plan` and the AWS Console. If resources were created successfully but are missing from the state, I import them using `terraform import`. If the state is corrupted, I restore it from the latest S3 version backup. After confirming that the state matches the infrastructure, I run `terraform plan` again to ensure no unexpected changes exist before executing `terraform apply`.

---

## 3. Pipeline passed. Production still has old code. Name every possible reason and how you confirm each one.

### Answer

I verify the deployment step by step.

- Check whether Jenkins deployed the latest artifact.
- Verify the latest Docker image exists in ECR.
- Confirm Kubernetes pulled the latest image instead of using a cached image.
- Check whether the Deployment rollout completed successfully.
- Verify the image tag running inside the Pods.
- Check whether the Service is routing traffic to the new Pods.
- Verify Ingress and Load Balancer configuration.
- Check browser or CDN cache.
- Verify the application version directly inside the running container.

By checking each layer sequentially, I can quickly identify where the deployment failed.

---

## 4. EC2 is unreachable. You cannot SSH. Walk me through every single step you take.

### Answer

First, I verify that the EC2 instance is running and both system status checks are passing. Then I check the Security Group to ensure port 22 is allowed from my IP. Next, I verify the Network ACL, Route Table, Internet Gateway, and public IP assignment. If SSH is intentionally disabled, I connect using AWS Systems Manager Session Manager. If the instance is completely unreachable, I review the EC2 system logs from the AWS Console, check CloudWatch metrics, and, if necessary, detach the root volume and inspect it from another EC2 instance for further investigation.

---

## 5. Docker image is 2.1 GB. Deployment is too slow. How do you bring it under 300 MB without breaking the app?

### Answer

I would use a multi-stage Docker build so that build tools are excluded from the final image. I'd switch to a lightweight base image such as Alpine or a slim JRE, remove unnecessary packages and temporary files, use `.dockerignore` to exclude unwanted files, combine RUN commands to reduce layers, and copy only the final application artifact instead of the entire project. I would also scan the image for unused dependencies. These optimizations usually reduce the image size significantly while maintaining functionality.

---

## 6. AWS bill doubled this month. No new resources were added. How do you find the exact cause in under 10 minutes?

### Answer

I first open AWS Cost Explorer and compare this month's costs with the previous month. Then I group costs by Service, Region, and Usage Type to identify the source of the increase. I review CloudWatch metrics for abnormal traffic, verify Auto Scaling activity, inspect data transfer costs, check EBS snapshots, NAT Gateway usage, Load Balancer traffic, and S3 storage growth. Finally, I use Cost and Usage Reports to identify the exact resource responsible for the increased billing.

---

## 7. HPA is configured. Traffic spiked. Pods are not scaling. What are the 4 possible reasons?

### Answer

The most common reasons are:

1. Metrics Server is not running or not reporting metrics.
2. CPU or memory requests are not defined in the Pod specification.
3. HPA target thresholds have not been reached or are configured incorrectly.
4. Cluster Autoscaler cannot provision additional worker nodes due to resource limits or Auto Scaling constraints.

I verify each of these before concluding the issue.

---

## 8. A developer committed secret keys to GitHub at 2 PM. What do you do in the next 5 minutes?

### Answer

My immediate priority is to minimize the security risk.

1. Revoke or rotate the exposed AWS access keys immediately.
2. Remove the secret from Git history and the repository.
3. Force push the cleaned repository if approved.
4. Review CloudTrail logs for any unauthorized usage of the compromised credentials.
5. Inform the security team and document the incident.
6. Enable Git secret scanning and pre-commit hooks to prevent future secret leaks.

---

## 9. Your Grafana dashboard shows everything is green. But users are flooding support with complaints. What is missing in your observability setup?

### Answer

Infrastructure metrics alone are not enough. We also need **Application Performance Monitoring (APM)**, distributed tracing, synthetic monitoring, real user monitoring (RUM), business metrics, centralized logging, and user experience monitoring. CPU and memory can appear healthy while users experience API failures, slow transactions, or frontend issues. Complete observability requires metrics, logs, traces, and user experience data together.

---

## 10. You need to deploy to production with zero downtime. Your team has never done Blue-Green before. How do you design it?

### Answer

I would create two identical production environments: **Blue** (current production) and **Green** (new version). The new application is deployed to Green while Blue continues serving users. After validating Green through smoke tests, health checks, and monitoring, I switch the Load Balancer or Kubernetes Service to route traffic to Green. I continue monitoring the application, and if any issue occurs, I immediately switch traffic back to Blue. This approach provides zero downtime and instant rollback capability.

---


# Linux, Docker, Kubernetes & Client Communication Interview Questions (4 Years Experience)

---

# Linux & Troubleshooting

## 1. What is Exit Status 2 in Kubernetes?

### Answer

Exit Code **2** usually indicates that the application terminated due to an incorrect command, invalid arguments, or a shell/script execution error. I first check the container logs, startup command, entrypoint, environment variables, and Pod Events to identify the exact reason before redeploying.

---

## 2. What is Exit Status 143 in Kubernetes?

### Answer

Exit Code **143** means the container received a **SIGTERM** signal and shut down gracefully. This commonly happens during rolling updates, Pod deletion, node draining, or scaling events. It is usually expected behavior unless the application exits unexpectedly during normal operation.

---

## 3. If you installed a package on Linux and it worked yesterday but failed today, which logs would you check?

### Answer

I would first check the application logs and then review **system logs** using `journalctl`, `/var/log/messages`, `/var/log/syslog`, or service-specific logs. I would also verify whether any recent OS updates, permission changes, dependency updates, or disk space issues caused the package to stop working.

---

## 4. For a memory or CPU-related issue, what would be shown in logs or events?

### Answer

For high memory usage, I would check for **OOMKilled** events in Kubernetes or Out of Memory messages in Linux logs. For CPU issues, I would observe high CPU utilization in monitoring tools such as Prometheus, Grafana, or CloudWatch. In Kubernetes, Pod Events and `kubectl describe pod` often indicate resource-related problems.

---

## 5. A tool installed on a Linux server is running slowly. How would you troubleshoot and improve its performance?

### Answer

I would first check CPU, memory, disk I/O, and network utilization. Then I'd verify whether the server is running out of resources, inspect application logs for errors, identify long-running processes, check disk space, and review recent configuration changes. Based on the findings, I may increase resources, optimize the application configuration, or remove unnecessary background processes.

---

## 6. What is systemd?

### Answer

**systemd** is the Linux system and service manager responsible for starting, stopping, monitoring, and managing system services during boot and runtime. It uses **systemctl** commands to control services and **journalctl** to view service logs.

---

## 7. Which production issues have you faced, and how did you troubleshoot them?

### Answer

One production issue I handled involved users receiving **503 Service Unavailable** errors after deployment. I checked Kubernetes Pod status, reviewed readiness probe failures, inspected application logs, and identified an incorrect database configuration in the ConfigMap. After correcting the configuration and redeploying the application, all Pods became healthy and traffic was restored. We later added automated validation in the CI/CD pipeline to prevent similar issues.

---

# Docker & Containers

## 8. Explain the Docker container lifecycle.

### Answer

The Docker container lifecycle consists of **Created → Running → Paused (optional) → Stopped → Removed**. A container is created from an image, started to run the application, may be paused if required, stopped when the application exits or is terminated, and finally removed when it is no longer needed.

---

## 9. A Docker container is consuming high CPU and memory. How would you check and troubleshoot it?

### Answer

I would first use `docker stats` to monitor CPU and memory usage. Then I'd inspect application logs, verify resource limits, identify expensive processes inside the container, review recent deployments, and analyze application performance. If required, I would optimize the application or increase resource limits.

---

## 10. How do you use docker stats during troubleshooting?

### Answer

`docker stats` provides real-time CPU, memory, network, and disk I/O usage for running containers. During troubleshooting, I use it to quickly identify containers consuming excessive resources and determine whether performance issues are caused by resource exhaustion.

---

## 11. What is the purpose of a Docker image?

### Answer

A Docker image is a read-only template containing the application, runtime, libraries, and dependencies required to run a container. It ensures the application behaves consistently across Development, QA, UAT, and Production environments.

---

## 12. What is the Docker daemon?

### Answer

The Docker daemon (**dockerd**) is the background service responsible for building images, creating containers, managing networks, volumes, and communicating with the Docker CLI. All Docker commands are executed through the daemon.

---

## 13. What are Docker volumes used for?

### Answer

Docker volumes provide persistent storage for containers. Data stored in a volume remains available even if the container is deleted or recreated. They are commonly used for databases, log files, uploaded files, and application data.

---

# Kubernetes

## 14. Explain ClusterIP and NodePort. What is the difference between them?

### Answer

**ClusterIP** exposes a Service only within the Kubernetes cluster and is mainly used for internal communication between microservices. **NodePort** exposes the Service on a specific port of every worker node, allowing external access using the node's IP address and port.

---

## 15. What is a Deployment in Kubernetes?

### Answer

A Deployment manages stateless applications by maintaining the desired number of Pod replicas. It supports rolling updates, automatic rollback, self-healing, and scaling, ensuring high availability of the application.

---

## 16. What is a Pod Disruption Budget (PDB), and why is it important for production workloads?

### Answer

A Pod Disruption Budget specifies the minimum number of Pods that must remain available during voluntary disruptions such as node maintenance or cluster upgrades. It prevents too many Pods from being unavailable simultaneously, helping maintain application availability in production.

---

## 17. Explain PersistentVolume (PV) and PersistentVolumeClaim (PVC).

### Answer

A **PersistentVolume (PV)** is storage provisioned for Kubernetes clusters, while a **PersistentVolumeClaim (PVC)** is a request made by a Pod to use that storage. Kubernetes automatically binds the PVC to a matching PV, allowing data to persist even if the Pod is recreated.

---

## 18. Explain Kubernetes RBAC and how it controls access to cluster resources.

### Answer

RBAC (Role-Based Access Control) controls who can perform specific actions within a Kubernetes cluster. Permissions are defined using **Roles** or **ClusterRoles** and assigned to users or Service Accounts through **RoleBindings** or **ClusterRoleBindings**. This ensures users have only the permissions required for their responsibilities.

---

# Communication & Client Handling

## 19. Explain the email and enterprise involvement/escalation flow followed during a production issue.

### Answer

When a production issue occurs, the monitoring system generates an alert, and the incident is assigned to the DevOps or Operations team. After initial investigation, stakeholders including developers, project managers, support teams, and clients are informed based on the severity. Regular status updates are shared until the issue is resolved. Once service is restored, a Root Cause Analysis (RCA) is prepared and shared along with preventive actions to avoid recurrence.

---

## 20. Write an email to a client explaining the issue faced and the solution provided.

### Answer

**Subject:** Production Issue Resolved – Application Service Restored

Dear Team,

We identified an issue that affected application availability due to a configuration mismatch introduced during the recent deployment. Our team immediately investigated the issue, corrected the configuration, and successfully redeployed the application.

The application has now been fully restored, and all services are functioning normally. We have completed validation checks to ensure stability and are continuously monitoring the environment. Additionally, preventive validation has been added to our deployment pipeline to avoid similar issues in the future.

We apologize for the inconvenience caused and appreciate your patience. Please let us know if you observe any further issues.

Regards,

**DevOps Team**

---


# DevOps Interview Questions & Answers (4 Years Experience)

## 1. What are policies in Auto Scaling?

Amazon EC2 Auto Scaling uses scaling policies to automatically increase or decrease the number of EC2 instances based on application demand. The most commonly used policies are **Target Tracking Scaling**, **Step Scaling**, **Simple Scaling**, and **Predictive Scaling**. In my experience, I mostly use **Target Tracking Scaling**, where the Auto Scaling Group automatically maintains a target metric such as 60% CPU utilization. For applications with predictable traffic patterns, Predictive Scaling can be used, while Step Scaling is suitable when different scaling actions are required at different metric thresholds. These policies help maintain application availability while optimizing infrastructure costs.

---

## 2. How do path-based routing and host-based routing work in ALB, and which has priority?

Application Load Balancer (ALB) supports routing traffic based on host names and URL paths. **Host-based routing** directs requests based on the domain name, for example `api.example.com` and `app.example.com` can route to different target groups. **Path-based routing** directs requests based on the URL path such as `/api/*` or `/images/*`. ALB evaluates listener rules based on **priority numbers**, where a lower priority number is evaluated first. If multiple rules match, the rule with the highest priority (lowest number) is executed before the default rule. In production, I have used both host-based and path-based routing together to serve multiple microservices through a single ALB.

---

## 3. How can you give SSH access to an EC2 instance without sharing the PEM key?

Instead of sharing the PEM key, I prefer secure methods such as **AWS Systems Manager Session Manager**, **EC2 Instance Connect**, or creating individual SSH key pairs for each user. Session Manager is the most secure option because it provides shell access through the AWS Console or CLI without opening port 22 or distributing private keys. Another common approach is adding the user's public key to the `authorized_keys` file or integrating EC2 with IAM and Active Directory for centralized access management.

---

## 4. What is the Terraform lifecycle?

Terraform lifecycle is a meta-argument used to control how Terraform manages resources during creation, update, and deletion. The commonly used lifecycle rules are:

- **create_before_destroy** – Creates the new resource before deleting the old one, avoiding downtime.
- **prevent_destroy** – Prevents accidental deletion of critical resources like production databases.
- **ignore_changes** – Ignores specific attribute changes made outside Terraform.
- **replace_triggered_by** – Forces replacement when another dependent resource changes.

In production, I have frequently used `create_before_destroy` for zero-downtime deployments and `prevent_destroy` for critical infrastructure.

---

## 5. Difference between count and for_each in Terraform?

Both `count` and `for_each` are used to create multiple resource instances, but they work differently. `count` creates resources based on an integer value and identifies them using numeric indexes. It is suitable when all resources are similar. However, if an item is removed from the list, Terraform may recreate other resources because indexes change. `for_each` works with maps or sets and uses unique keys instead of indexes, making it more stable and easier to manage. For production infrastructure, I generally prefer `for_each` because resource identities remain consistent even when adding or removing items.

---

## 6. What is a data block, and how is it different from a dynamic block?

A **data block** is used to fetch information about existing infrastructure instead of creating new resources. For example, retrieving the latest AMI ID or existing VPC details. A **dynamic block** is used to generate nested configuration blocks inside a resource dynamically using loops. In simple terms, data blocks read existing resources, while dynamic blocks help write repetitive configurations more efficiently.

---

## 7. What are provisioners in Terraform?

Provisioners execute scripts or commands after a resource is created or before it is destroyed. The commonly used provisioners are `local-exec`, which runs commands on the local machine, and `remote-exec`, which executes commands on the remote server through SSH. Although provisioners are available, HashiCorp recommends avoiding them whenever possible because they are not idempotent. In my projects, I prefer using cloud-init, user data scripts, Ansible, or configuration management tools instead of provisioners.

---

## 8. What are the parameters of SonarQube and how do you test an application?

SonarQube analyzes code quality using parameters such as Project Key, Project Name, Source Directory, Language, Sonar Host URL, Authentication Token, Coverage Reports, Exclusions, and Quality Gate settings. During CI/CD, I integrate SonarQube after the build stage. The application is tested using unit tests (JUnit), code coverage (JaCoCo), static code analysis through SonarQube, dependency vulnerability scanning, and functional testing before deployment. The pipeline proceeds only if the Quality Gate passes successfully.

---

## 9. If production keys are exposed, how do you troubleshoot and resolve the issue?

If production keys are exposed, the first priority is containment. I immediately revoke or rotate the compromised credentials, disable access if necessary, and generate new keys. Next, I identify where the exposure occurred, such as GitHub, logs, or configuration files. I review AWS CloudTrail logs and application logs to check whether the keys were misused. After replacing the credentials, I update all dependent services, verify application functionality, and perform a security audit. Finally, I implement preventive measures such as AWS Secrets Manager, IAM roles, secret scanning, and CI/CD secret management to avoid similar incidents.

---

## 10. How do you upgrade a Kubernetes cluster without downtime?

A Kubernetes cluster can be upgraded without downtime by following a rolling upgrade strategy. First, verify compatibility between the current and target Kubernetes versions and take backups of etcd. Upgrade the control plane components first, followed by worker nodes one at a time. Before upgrading each worker node, use `kubectl drain` to safely evict workloads while respecting Pod Disruption Budgets. Upgrade kubelet and kubectl, restart the node, and then uncordon it using `kubectl uncordon`. Since Deployments use rolling updates and replicas are distributed across multiple nodes, applications remain available throughout the upgrade.

---

## 11. Which Kubernetes version have you used?

I have primarily worked with Kubernetes versions **1.26, 1.27, and 1.28** in production environments. My responsibilities included deploying applications using Deployments and Helm charts, managing Ingress controllers, ConfigMaps, Secrets, Horizontal Pod Autoscalers, monitoring with Prometheus and Grafana, and performing cluster upgrades while ensuring minimal downtime.

---

## 12. What are liveness and readiness probes?

Liveness and readiness probes are Kubernetes health checks. A **liveness probe** determines whether a container is alive. If the probe fails repeatedly, Kubernetes restarts the container automatically. A **readiness probe** determines whether the application is ready to receive traffic. If it fails, Kubernetes removes the pod from the service endpoints without restarting it. Using both probes ensures application reliability by preventing unhealthy or uninitialized pods from receiving production traffic.

---

## 13. What are static pods and how do you handle them?

Static pods are pods managed directly by the kubelet instead of the Kubernetes API server. Their manifests are stored on the node, usually under `/etc/kubernetes/manifests`. The kubelet continuously monitors this directory and automatically creates or recreates the pods if the manifest exists. Core Kubernetes components such as kube-apiserver, kube-controller-manager, kube-scheduler, and etcd are commonly deployed as static pods in kubeadm clusters. To manage static pods, I update or modify the manifest files on the node, and the kubelet automatically applies the changes.

---

## 14. Explain NGINX pod structure.

An NGINX pod typically consists of one or more containers. The main NGINX container serves HTTP requests on port 80 or 443. Configuration is usually provided through a ConfigMap and mounted as a volume. Logs are written to stdout and stderr, allowing Kubernetes logging systems to collect them. Persistent storage can be attached if required, although most NGINX pods are stateless. Liveness and readiness probes monitor the health of the web server, while Services expose the pod internally or externally. Multiple NGINX pods are managed through a Deployment for high availability and rolling updates.

---

## 15. Explain Kubernetes Deployment YAML file.

A Kubernetes Deployment YAML defines the desired state of an application. It starts with the API version and resource kind, followed by metadata containing the deployment name and labels. The `spec` section defines the number of replicas, selector labels, and pod template. The pod template specifies container images, ports, environment variables, resource requests and limits, ConfigMaps, Secrets, volume mounts, and health probes. The Deployment controller continuously ensures that the desired number of healthy pods are running and supports rolling updates and rollback features. This makes Deployments the preferred method for managing stateless applications in Kubernetes.

Example structure:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:latest

        ports:
        - containerPort: 80

        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"

          limits:
            cpu: "500m"
            memory: "512Mi"

        readinessProbe:
          httpGet:
            path: /
            port: 80

        livenessProbe:
          httpGet:
            path: /
            port: 80
```

### Important sections:

- **apiVersion** – Kubernetes API version.
- **kind** – Resource type (Deployment).
- **metadata** – Name and labels.
- **replicas** – Number of pod replicas.
- **selector** – Matches pods managed by the deployment.
- **template** – Pod specification.
- **containers** – Container details.
- **image** – Docker image.
- **ports** – Exposed container ports.
- **resources** – CPU and memory requests/limits.
- **readinessProbe** – Checks if the application is ready for traffic.
- **livenessProbe** – Restarts unhealthy containers automatically.



# DevOps Interview Questions & Answers (4 Years Experience)

## GitLab CI/CD

---

## 1. Difference between Continuous Integration, Continuous Delivery, and Continuous Deployment?

### Answer

Continuous Integration (CI), Continuous Delivery (CD), and Continuous Deployment are three important practices in a DevOps lifecycle that help automate software delivery while improving quality and reducing deployment risks.

**Continuous Integration (CI)** is the process of frequently integrating developers' code changes into a shared repository. In my current project, whenever a developer raises a Merge Request or pushes code to the feature branch, GitLab automatically triggers a pipeline. The pipeline performs several activities such as code checkout, dependency installation, unit testing, static code analysis using SonarQube, security scanning, Docker image creation, and artifact generation. The primary objective of CI is to detect integration issues early instead of discovering them during release. Since multiple developers work on the same microservices, CI helps us identify compilation errors, dependency conflicts, or test failures immediately after code is committed.

**Continuous Delivery** is the next stage after Continuous Integration. Once the application has been successfully built and tested, it is automatically deployed to environments such as Development or UAT. However, deployment to Production still requires a manual approval from the Release Manager or Product Owner. In our project, after all automated quality gates pass, GitLab pauses the pipeline at the Production stage and waits for an authorized approver. This gives the business team an opportunity to validate the release, schedule deployments during maintenance windows, and ensure all compliance requirements are met before releasing the application to customers.

**Continuous Deployment** goes one step further by eliminating the manual approval process. Once all automated validations such as unit tests, integration tests, security scans, performance checks, and deployment verification succeed, the application is automatically deployed to Production without human intervention. Continuous Deployment is commonly used in organizations with highly mature DevOps practices and comprehensive automated testing. Although our Production deployments require manual approval due to business compliance requirements, our Development and UAT environments follow an automated Continuous Deployment approach where every successful pipeline automatically deploys the latest version.

In simple terms, Continuous Integration focuses on automatically building and testing code whenever changes are committed. Continuous Delivery ensures the application is always in a deployable state while requiring manual approval before Production deployment. Continuous Deployment automates the complete software release process, including deployment to Production, without any manual intervention.

---

## 2. Explain your current CI/CD pipeline flow.

### Answer

In my current project, we use **GitLab CI/CD** to automate the complete application delivery process from source code commit to deployment on Amazon EKS. The objective of the pipeline is to reduce manual effort, improve deployment consistency, and ensure every release passes predefined quality and security checks before reaching Production.

The pipeline starts when a developer creates a Merge Request or pushes code to the feature branch. GitLab automatically triggers the pipeline based on the `.gitlab-ci.yml` configuration file. During the first stage, the pipeline checks out the source code from the Git repository and installs the required dependencies. The application is then compiled and unit tests are executed to validate that the new code does not introduce compilation or functional issues.

After successful compilation, the pipeline performs static code analysis using SonarQube. We have configured Quality Gates to verify code coverage, code duplication, security vulnerabilities, bugs, and code smells. If the Quality Gate fails, the pipeline immediately stops, preventing low-quality code from moving to the next stage.

Once the quality checks pass, the application is packaged, and a Docker image is built using a multi-stage Dockerfile. The Docker image is then scanned for vulnerabilities before being pushed to Amazon Elastic Container Registry (ECR). This ensures that vulnerable operating system packages or libraries are identified before deployment.

The deployment stage uses Kubernetes manifests or Helm charts to deploy the application into the Development environment running on Amazon EKS. After deployment, automated smoke tests and health checks verify that the application is functioning correctly. If all validations succeed, the pipeline automatically proceeds to deploy the application to the UAT environment.

Production deployment is controlled through a manual approval stage. Once business approval is received, the same tested Docker image is deployed to the Production EKS cluster using a rolling deployment strategy. During deployment, Kubernetes gradually replaces old Pods with new ones while continuously monitoring health probes. This approach minimizes downtime and allows users to continue accessing the application during the deployment process.

After deployment, application health is continuously monitored using Prometheus, Grafana, and AWS CloudWatch. If abnormal error rates, Pod failures, or resource utilization issues are detected, Kubernetes automatically restarts unhealthy Pods, and if required, we can quickly perform a rollback to the previous stable ReplicaSet.

This automated CI/CD pipeline has significantly reduced manual deployment effort, improved release consistency, minimized production failures, and enabled multiple deployments per week while maintaining high application availability.

---

## 3. What are the stages in your CI/CD pipeline?

### Answer

Our GitLab CI/CD pipeline consists of multiple stages, each designed to validate a specific aspect of the application before allowing it to move to the next phase. The pipeline follows a quality-first approach where each stage acts as a checkpoint, ensuring only stable and secure code progresses through the deployment lifecycle.

The first stage is **Source Checkout**, where GitLab retrieves the latest code from the repository based on the branch or Merge Request that triggered the pipeline. This ensures the pipeline always works with the most recent version of the application.

The second stage is **Build**, where the application is compiled and all project dependencies are downloaded. For Java applications, we use Maven or Gradle, while Node.js applications use npm or yarn. This stage ensures the application can be successfully compiled without dependency issues.

The third stage is **Unit Testing**, where automated unit tests verify the functionality of individual components. Running unit tests during every pipeline execution helps us identify coding errors early in the development lifecycle.

Next comes **Static Code Analysis** using SonarQube. During this stage, the code is analyzed for bugs, vulnerabilities, code smells, duplicated code, and test coverage. We have configured SonarQube Quality Gates so that any critical issues automatically fail the pipeline.

The fifth stage is **Security Scanning**, where we scan dependencies and Docker images for known vulnerabilities. This helps ensure that insecure libraries or outdated packages do not reach production.

After successful validation, the pipeline enters the **Docker Build** stage. Here, a Docker image is created using a multi-stage Dockerfile to optimize image size and improve security. Once built, the image is tagged using the Git commit ID or release version and pushed to Amazon Elastic Container Registry (ECR).

The next stage is **Deployment to Development**, where Kubernetes deploys the latest Docker image into the Development EKS cluster. Automated smoke tests verify that the application starts successfully, APIs respond correctly, and basic functionality works as expected.

Once Development validation succeeds, the pipeline deploys the application to the **UAT environment**, where testers and business users perform functional and user acceptance testing. This environment closely mirrors Production, allowing comprehensive validation before release.

The final stage is **Production Deployment**. This stage requires manual approval from the Release Manager. Once approved, Kubernetes performs a rolling deployment, gradually replacing existing Pods with the new version while continuously monitoring application health. After deployment, monitoring tools such as Prometheus, Grafana, and CloudWatch confirm that the application is healthy and performing as expected.

By organizing the pipeline into clearly defined stages, we ensure that code quality, security, testing, and deployment validations are completed before any release reaches Production, significantly reducing deployment risks and improving software reliability.



---

## 4. What problems does your CI/CD pipeline solve?

### Answer

Before implementing CI/CD, our development and deployment process involved several manual activities. Developers would manually build the application on their local systems, operations teams would manually copy artifacts to servers, execute deployment scripts, and verify the application after deployment. This process was time-consuming, inconsistent, and highly prone to human error. Different environments often had configuration differences, resulting in "it works on my machine" issues. Deployments also required significant coordination between development, QA, and operations teams, making releases slow and increasing the risk of production failures.

Our GitLab CI/CD pipeline solved these challenges by automating the entire software delivery lifecycle. Whenever a developer pushes code or raises a Merge Request, the pipeline automatically builds the application, executes unit tests, performs static code analysis using SonarQube, scans dependencies and Docker images for vulnerabilities, builds a Docker image, pushes it to Amazon ECR, and deploys it to the Development environment. This ensures that every code change goes through the same standardized process, eliminating inconsistencies caused by manual deployments.

The pipeline also significantly improved code quality. Earlier, developers sometimes merged code without running sufficient tests, leading to production defects. Now, every commit must pass automated quality gates before it can be merged. SonarQube checks for bugs, code smells, security vulnerabilities, duplicated code, and code coverage. If any critical issue is detected, the pipeline fails immediately, preventing low-quality code from progressing further.

Another major improvement was deployment consistency. Since all environments use the same Docker image and Kubernetes manifests, we no longer face configuration mismatches between Development, UAT, and Production. Every deployment is repeatable, version-controlled, and fully traceable. If an issue occurs, we can quickly identify the exact commit, pipeline, Docker image, and deployment responsible for the release.

The CI/CD pipeline has also reduced deployment time significantly. Previously, deployments could take several hours because of manual approvals, artifact copying, and verification. Today, most deployments complete within minutes, allowing the team to release features more frequently while maintaining high quality. Automated rollback capabilities and continuous monitoring further reduce the impact of production issues, enabling faster recovery whenever incidents occur.

Overall, the CI/CD pipeline has improved development speed, software quality, deployment reliability, collaboration between teams, and application availability while reducing manual effort and operational risks.

---

## 5. How do you troubleshoot and resolve pipeline failures?

### Answer

Whenever a CI/CD pipeline fails, I follow a structured troubleshooting approach instead of rerunning the pipeline immediately. The first step is to identify the exact stage where the failure occurred because different stages indicate different categories of problems. For example, failures during the build stage usually indicate compilation issues or dependency conflicts, whereas failures during deployment often point to Kubernetes configuration problems, infrastructure issues, or application startup failures.

I begin by reviewing the GitLab pipeline logs to understand the exact error message. The logs usually indicate whether the issue occurred during dependency installation, code compilation, unit testing, SonarQube analysis, Docker image creation, security scanning, image push to Amazon ECR, or Kubernetes deployment. Rather than focusing only on the final error message, I carefully examine the complete log because the root cause often appears several lines before the actual failure.

If the failure occurs during the build stage, I verify whether any recent code changes introduced compilation errors or dependency issues. I also check whether external package repositories are available and whether build dependencies are compatible. For test failures, I analyze the failed test cases, reproduce the issue locally if required, and determine whether the failure is caused by application logic, test data, or environmental changes.

If the Docker build fails, I review the Dockerfile for syntax errors, invalid base images, permission issues, or missing application artifacts. When the pipeline cannot push the image to Amazon ECR, I verify IAM permissions, authentication tokens, repository availability, and network connectivity between GitLab runners and AWS.

Deployment failures require additional investigation. I check the Kubernetes deployment status using tools such as `kubectl describe`, Kubernetes Events, Pod logs, and ReplicaSet status. Common issues include incorrect image tags, failed health probes, missing ConfigMaps or Secrets, insufficient cluster resources, or networking problems. I also review Prometheus and Grafana dashboards to determine whether the failure is related to infrastructure or application behavior.

If the failure is caused by temporary external issues such as network interruptions or AWS service availability, I validate the environment before restarting the pipeline. However, I avoid repeatedly rerunning failed pipelines without understanding the root cause because this can waste build resources and delay releases.

After resolving the issue, I document the root cause, update the team's knowledge base, and implement preventive measures wherever possible. For example, if a dependency issue caused the failure, I may introduce dependency version locking. If a deployment failed due to missing configuration, I update validation checks to detect the issue earlier in the pipeline. This continuous improvement approach reduces recurring failures and makes the CI/CD process more reliable over time.

---

## 6. Describe a GitLab pipeline failure and how you fixed it.

### Answer

One production issue I encountered involved a GitLab deployment pipeline that consistently failed during the Kubernetes deployment stage, even though the application had compiled successfully, passed all unit tests, cleared SonarQube Quality Gates, and the Docker image had been pushed successfully to Amazon ECR. Initially, the pipeline appeared healthy until the deployment stage, where Kubernetes reported that the Deployment had exceeded its progress deadline.

Instead of rerunning the pipeline, I investigated the Kubernetes cluster. I checked the Deployment status, ReplicaSets, and Pod Events. I discovered that the newly created Pods were repeatedly entering the **CrashLoopBackOff** state. The application itself was failing during startup because it could not locate one of the required environment variables that was supposed to be provided through a Kubernetes Secret.

After reviewing the deployment manifest, I noticed that the Secret had been created in the Development namespace but was missing from the UAT namespace. Since the deployment pipeline promoted the same Docker image across multiple environments, the application started successfully in Development but failed immediately in UAT because the required Secret did not exist.

To resolve the issue, I created the missing Kubernetes Secret in the correct namespace and redeployed the application. The Pods started successfully, passed readiness and liveness probes, and the pipeline completed without further issues.

To prevent similar incidents, we improved our deployment process by introducing a validation stage before deployment. The pipeline now automatically verifies the existence of required ConfigMaps, Secrets, namespaces, and other Kubernetes resources before deploying the application. If any required resource is missing, the pipeline fails immediately with a clear error message, preventing failed deployments and reducing troubleshooting time.

This incident reinforced the importance of validating environment-specific configurations before deployment rather than assuming all Kubernetes resources are identical across environments.

---

## 7. Which Git branching strategy do you use?

### Answer

In my current project, we primarily follow a modified **GitFlow branching strategy** because it provides a structured approach for managing feature development, testing, releases, and production support. Since multiple developers work simultaneously on different microservices and features, having a well-defined branching strategy helps avoid conflicts and ensures that production code remains stable.

The `main` branch always represents the production-ready version of the application. Direct commits to this branch are not permitted. Every production release is deployed from the `main` branch after passing all quality checks and obtaining the required approvals.

Developers create individual **feature branches** from the `develop` branch for each new feature, enhancement, or bug fix. They perform development, testing, and code commits within their feature branch. Once development is complete, they raise a Merge Request to merge their changes into the `develop` branch. The Merge Request automatically triggers the GitLab CI/CD pipeline, which executes unit tests, static code analysis, security scanning, and build validation.

For production releases, we create a **release branch** from the `develop` branch. QA teams perform final testing in this branch, and only critical bug fixes are permitted. Once testing is completed successfully, the release branch is merged into both `main` and `develop` to ensure that production changes are synchronized with ongoing development.

If a critical production issue occurs, we create a **hotfix branch** directly from `main`. After resolving and validating the issue, the hotfix is merged back into both `main` and `develop` so that future releases also contain the fix.

This branching strategy provides better isolation between development and production, supports parallel feature development, simplifies release management, and ensures that only thoroughly tested code reaches production. It also integrates well with GitLab Merge Requests, mandatory code reviews, and automated CI/CD pipelines, making the overall software delivery process more reliable and predictable.


```markdown id="8fw3kd"
```
---

## 8. How do you manage Git branches?

### Answer

In my current project, we follow a structured Git branching strategy to ensure smooth collaboration among developers and maintain code stability across different environments. Since multiple developers work on different features, bug fixes, and production issues simultaneously, proper branch management is essential to avoid merge conflicts and ensure that only tested code reaches production.

Every new feature or bug fix begins with creating a dedicated feature branch from the `develop` branch. Developers work independently on their assigned branches without affecting the code being developed by others. We follow a naming convention such as `feature/login-enhancement`, `bugfix/payment-timeout`, or `hotfix/api-failure`, making it easy to identify the purpose of each branch.

Developers commit code frequently with meaningful commit messages rather than large, infrequent commits. Before raising a Merge Request, they synchronize their branch with the latest changes from the `develop` branch to minimize merge conflicts. If conflicts occur, they resolve them locally, test the application thoroughly, and only then push the updated branch.

Once development is complete, a Merge Request is created. GitLab automatically triggers the CI/CD pipeline, which performs code compilation, unit testing, SonarQube analysis, security scanning, and Docker image creation. The Merge Request cannot be merged unless all pipeline stages pass successfully. This ensures that only validated code progresses to the shared branch.

Code reviews are mandatory in our project. At least one or two senior developers review the code for functionality, coding standards, security, maintainability, and performance before approving the merge. Direct commits to protected branches such as `main` and `develop` are not allowed, ensuring that every change is reviewed and validated through the CI/CD process.

After successful deployment and release, feature branches are deleted to keep the repository clean. However, release tags remain available, allowing us to identify the exact version deployed to Production whenever rollback or auditing is required. This structured branch management process improves collaboration, reduces integration issues, and maintains a clean and organized Git repository.

---

## 9. Do you use any backup/recovery before deleting stale branches?

### Answer

Yes. Although deleting stale branches helps keep the repository clean and improves maintainability, we never delete branches without ensuring that the required code has been preserved. Before deleting any branch, we first verify whether its changes have already been merged into the appropriate target branch, such as `develop` or `main`. GitLab provides merge history, which allows us to confirm that all commits are safely included in the main codebase.

For release branches and production deployments, we always create Git tags before deletion. Tags serve as permanent references to important releases and allow us to recreate the exact source code corresponding to any production deployment. This is extremely useful during production rollbacks, audits, and troubleshooting because we can identify the precise version that was deployed.

For long-running feature branches, we review open Merge Requests and pending work before deletion. If the feature is incomplete or may be required later, we communicate with the development team before taking any action. In some cases, branches are archived instead of being deleted immediately, especially if they contain work that may be resumed in the future.

Git itself also provides an additional safety mechanism through commit history and reflog. Even if a branch is accidentally deleted, it can often be recovered if the commits still exist in the repository history. However, we do not rely solely on recovery mechanisms. Our standard practice is to verify merge status, maintain release tags, and obtain team confirmation before deleting stale branches.

By following this controlled approach, we maintain a clean repository while ensuring that valuable development work is never lost.

---

## 10. Explain your code review and merge approval process.

### Answer

In my current project, every code change follows a structured review and approval process before it can be merged into the shared branch. This process improves code quality, ensures compliance with development standards, and reduces the risk of introducing defects into production.

Once a developer completes a feature or bug fix, they push their changes to their feature branch and create a Merge Request in GitLab. Creating the Merge Request automatically triggers the GitLab CI/CD pipeline. The pipeline performs code compilation, unit testing, static code analysis using SonarQube, security scanning, and Docker image creation. If any stage fails, the Merge Request cannot proceed until the issues are resolved.

After the pipeline completes successfully, the Merge Request is assigned to one or more reviewers, typically senior developers or technical leads. During the review, they examine the code for correctness, readability, maintainability, adherence to coding standards, performance considerations, and security best practices. They also verify that the implementation aligns with business requirements and architectural guidelines.

If reviewers identify any issues, they provide comments directly within the Merge Request. The developer addresses the feedback, updates the code, and pushes the revised changes. The CI/CD pipeline executes again automatically to validate the updated implementation. This review cycle continues until all concerns are resolved.

Once all required reviewers approve the Merge Request and all pipeline stages pass successfully, the code is merged into the target branch. Protected branch policies prevent direct commits to important branches such as `main` and `develop`, ensuring that every code change undergoes the same validation and approval process.

This structured workflow has significantly improved collaboration within the team, reduced production defects, increased code consistency, and ensured that every deployment is based on thoroughly reviewed and tested code.

---

## 11. Difference between Git Merge and Git Rebase? When do you use each?

### Answer

Both Git Merge and Git Rebase are used to integrate changes from one branch into another, but they achieve this in different ways and serve different purposes.

**Git Merge** combines the histories of two branches by creating a new merge commit. The complete development history is preserved, making it easy to understand when different branches were integrated. Since Merge does not rewrite commit history, it is considered safer for shared branches that multiple developers are using. In our project, Merge is primarily used when integrating feature branches into the `develop` branch through Merge Requests because it maintains a clear history of collaboration.

**Git Rebase**, on the other hand, rewrites commit history by moving commits from one branch onto the latest commit of another branch. Instead of creating a merge commit, Rebase creates a linear project history that is easier to read. Before creating a Merge Request, developers often rebase their feature branch onto the latest `develop` branch to incorporate recent changes and reduce merge conflicts. Since Rebase modifies commit history, it should only be performed on local or private branches that are not shared with other developers.

In my project, I typically use Rebase during active development to keep my feature branch updated with the latest code from `develop`. Once the feature is complete, I raise a Merge Request, where GitLab performs the final merge after successful review and pipeline validation. This approach combines the benefits of a clean development history while preserving collaboration history in the shared repository.

---

## 12. What is Git?

### Answer

Git is a distributed version control system that helps developers manage source code efficiently while enabling multiple team members to work on the same project simultaneously. Unlike traditional version control systems that rely on a centralized server, Git allows every developer to have a complete copy of the repository, including its full commit history. This distributed architecture enables developers to work offline, create branches independently, and synchronize their changes whenever required.

In my current project, Git is an essential part of our DevOps workflow. Every application, infrastructure configuration, Kubernetes manifest, Terraform module, and CI/CD pipeline definition is maintained in Git repositories. Developers create feature branches for new development, commit changes with meaningful messages, and raise Merge Requests for review. GitLab CI/CD automatically builds, tests, and validates the application whenever new commits are pushed, ensuring that every code change follows the same quality assurance process.

Git also provides complete traceability. Every commit records who made the change, when it was made, and why it was introduced. If an issue is discovered in Production, we can quickly identify the responsible commit, compare changes between releases, or revert problematic commits if necessary. This significantly improves troubleshooting and accountability.

Another major advantage of Git is its powerful branching and merging capabilities. Multiple developers can work on different features simultaneously without affecting each other's work. Once development is complete and the code has passed reviews and automated testing, the changes are merged into the main development branch. This enables parallel development while maintaining code stability and reducing integration conflicts.

Overall, Git serves as the foundation of our software development lifecycle by providing version control, collaboration, change tracking, release management, and seamless integration with automated CI/CD pipelines.

---

## 13. Difference between Declarative and Scripted Pipelines in Jenkins?

### Answer

Jenkins supports two primary pipeline syntaxes: **Declarative Pipeline** and **Scripted Pipeline**. Both achieve the same objective of automating CI/CD workflows, but they differ in syntax, flexibility, and complexity.

A **Declarative Pipeline** follows a structured and predefined syntax. It is easier to read, maintain, and understand because the pipeline stages, environment variables, agents, post-build actions, and conditions are organized in a standardized format. Since the syntax is more restrictive, it reduces the chances of programming errors and improves consistency across projects. In my experience, Declarative Pipelines are ideal for most enterprise CI/CD workflows because they are easier for teams to maintain and onboard new engineers.

A **Scripted Pipeline** is written using the full Groovy programming language and provides much greater flexibility. It allows developers to implement complex logic, loops, conditional execution, exception handling, dynamic stage generation, and reusable functions. However, because it behaves like a programming language, Scripted Pipelines are generally more difficult to understand, debug, and maintain. Small syntax mistakes can also make troubleshooting more challenging.

In my projects, we primarily use **Declarative Pipelines** because our CI/CD process follows a predictable sequence of stages such as source checkout, build, unit testing, SonarQube analysis, security scanning, Docker image creation, image push to Amazon ECR, Kubernetes deployment, and post-deployment verification. Declarative syntax makes these stages easy to visualize and maintain. However, when implementing highly customized workflows, dynamic deployments, or advanced automation logic, I have used Scripted Pipeline features within Declarative Pipelines through Groovy scripting.

From an interview perspective, I usually explain that Declarative Pipelines are preferred for standard enterprise CI/CD implementations because of their readability and maintainability, while Scripted Pipelines are chosen only when advanced programming logic or dynamic behavior is required.


# Terraform

---

## 1. Why is Terraform used?

### Answer

In my current project, Terraform is our primary **Infrastructure as Code (IaC)** tool used to provision, configure, and manage AWS infrastructure in a consistent, repeatable, and automated manner. Instead of manually creating resources through the AWS Management Console, we define the entire infrastructure using Terraform code. This includes resources such as VPCs, Subnets, Internet Gateways, NAT Gateways, Route Tables, Security Groups, EC2 instances, IAM Roles, Application Load Balancers, Auto Scaling Groups, Amazon EKS clusters, Amazon RDS databases, and S3 buckets.

Before Terraform was introduced, infrastructure provisioning was largely manual. Engineers had to create resources one by one through the AWS Console, which was time-consuming and often resulted in inconsistencies between Development, UAT, and Production environments. Manual provisioning also increased the chances of configuration errors, missing security rules, and undocumented infrastructure changes.

Terraform solved these challenges by allowing us to define infrastructure declaratively using HCL (HashiCorp Configuration Language). Every infrastructure change is stored in GitLab, reviewed through Merge Requests, and deployed through the CI/CD pipeline. This provides version control, peer review, auditability, and consistency across environments. If a new environment needs to be created, Terraform can provision the complete infrastructure within minutes using the same codebase, ensuring that every environment is identical.

Another significant advantage is idempotency. Terraform compares the desired state defined in the code with the existing infrastructure and modifies only the resources that require changes. This minimizes unnecessary updates and reduces deployment risks. Terraform also supports modular design, allowing us to reuse infrastructure components across multiple projects, which improves maintainability and reduces code duplication.

Overall, Terraform has significantly improved deployment consistency, reduced manual effort, accelerated environment provisioning, and enabled our infrastructure to be managed using the same DevOps practices as application code.

---

## 2. Advantages over manual infrastructure provisioning?

### Answer

Terraform provides several advantages over manual infrastructure provisioning, especially in large-scale cloud environments where consistency, automation, and reliability are essential. In my experience, one of the biggest benefits is **consistency**. When infrastructure is created manually through the AWS Console, different engineers may configure resources differently, leading to configuration drift between environments. Terraform eliminates this problem because every environment is created using the same code.

Another major advantage is **automation**. Previously, provisioning a complete environment could take several hours or even days because engineers had to manually create networking components, IAM roles, compute instances, databases, and security configurations. With Terraform, the same infrastructure can be provisioned automatically within minutes using a single pipeline execution. This significantly reduces deployment time and improves operational efficiency.

Terraform also provides **version control**. Since all infrastructure code is stored in GitLab, every change is tracked, reviewed, and approved before being applied. We always know who modified the infrastructure, when the change was made, and why it was introduced. If required, we can review previous versions or revert changes using Git history.

One of the most valuable features is **state management**. Terraform maintains a state file that records the current infrastructure. During every execution, Terraform compares the desired configuration with the existing infrastructure and performs only the necessary modifications. This prevents unnecessary resource recreation and minimizes downtime during infrastructure updates.

Terraform also improves disaster recovery. If an environment is accidentally deleted or a new environment is required, we do not need to recreate resources manually. We simply execute the Terraform code, and the complete infrastructure is recreated consistently. This capability has greatly simplified environment provisioning for development, testing, and production.

Finally, Terraform integrates seamlessly with GitLab CI/CD, allowing infrastructure deployments to follow the same automated review and approval process as application deployments. This reduces manual intervention, improves governance, and ensures that infrastructure changes are implemented safely and consistently.

---

## 3. Experience with Terraform modules?

### Answer

Yes. In my current project, we extensively use Terraform Modules to improve code reusability, maintainability, and standardization. Instead of writing the same infrastructure code repeatedly for every environment or application, we created reusable modules that encapsulate common infrastructure components. These modules are then invoked multiple times with different input variables depending on the environment or application requirements.

For example, we have separate modules for VPC creation, Security Groups, IAM Roles, EC2 instances, Amazon EKS node groups, Application Load Balancers, Auto Scaling Groups, Amazon RDS databases, and Amazon S3 buckets. Each module contains all the required resource definitions, while environment-specific values such as CIDR ranges, instance types, subnet IDs, security group rules, and tags are passed through variables.

This modular approach significantly reduced code duplication and made our infrastructure easier to maintain. If a security enhancement or configuration change is required, we update the module once, and the change can be applied consistently across all environments after proper testing. This also improves standardization because every team provisions infrastructure using the same approved modules instead of writing custom Terraform code.

Modules also simplified onboarding new team members. Instead of understanding hundreds of Terraform resource definitions, engineers only needed to understand how to consume existing modules by providing the required variables. This reduced development effort and improved deployment consistency across multiple AWS accounts and environments.

In addition, our modules are version-controlled through GitLab repositories. Before upgrading a module version, we validate the changes in lower environments to ensure there are no unexpected impacts. Using reusable Terraform modules has significantly improved scalability, maintainability, and governance of our Infrastructure as Code implementation.

---



---

## 4. How do you manage Terraform state files?

### Answer

Managing the Terraform state file is one of the most critical aspects of working with Terraform because the state file acts as the source of truth for all infrastructure managed by Terraform. It maintains the mapping between the infrastructure defined in the Terraform configuration and the actual resources running in AWS. Without a properly managed state file, Terraform cannot determine which resources need to be created, updated, or deleted, increasing the risk of infrastructure inconsistencies.

In my current project, we never store the Terraform state file (`terraform.tfstate`) locally because multiple DevOps engineers work on the same infrastructure. Instead, we use a **remote backend** with an **Amazon S3 bucket** to store the state file centrally. This allows all team members and GitLab CI/CD pipelines to work with the same state, ensuring consistency across deployments. Since the state file contains sensitive information such as resource IDs, ARNs, IP addresses, and sometimes metadata about infrastructure, the S3 bucket is configured with server-side encryption using AWS KMS, versioning enabled, and restricted IAM permissions so that only authorized users and CI/CD pipelines can access it.

To prevent multiple engineers from modifying the infrastructure simultaneously, we use **Amazon DynamoDB** for state locking. Whenever a Terraform operation such as `terraform apply` starts, Terraform automatically acquires a lock in the DynamoDB table. This prevents another user or pipeline from executing Terraform against the same state file until the first operation completes. This mechanism eliminates race conditions and protects the infrastructure from corruption caused by concurrent updates.

We also separate infrastructure for different environments using **Terraform Workspaces** or separate backend configurations, depending on the project. Development, UAT, and Production environments maintain independent state files, ensuring that changes made to one environment cannot accidentally impact another. This isolation improves safety and simplifies environment-specific deployments.

As part of our GitLab CI/CD process, every infrastructure change follows a controlled workflow. Developers first execute `terraform fmt` and `terraform validate` to verify formatting and syntax. The pipeline then runs `terraform plan`, allowing reviewers to see exactly which infrastructure changes will occur. Only after the Merge Request is approved does the pipeline execute `terraform apply`. This review process minimizes the risk of unintended infrastructure modifications.

Another important practice is enabling **S3 bucket versioning**. If a state file is accidentally overwritten or corrupted, previous versions can be restored quickly without rebuilding the infrastructure. We also perform regular backups and restrict direct manual editing of the state file because modifying it outside Terraform can introduce inconsistencies.

Overall, using a remote S3 backend, DynamoDB state locking, encrypted storage, versioning, isolated environments, and CI/CD-based deployments has enabled us to manage Terraform state securely and reliably while supporting multiple engineers working on the same infrastructure.

---

## 5. Challenges faced in Terraform and how you resolved them?

### Answer

During my experience with Terraform, I have encountered several real-world challenges while managing AWS infrastructure. One of the most common issues was **Terraform drift**, where infrastructure was manually modified through the AWS Management Console instead of Terraform. For example, a support engineer temporarily opened an additional Security Group rule to troubleshoot an application issue. Since this change was not reflected in the Terraform code, the next `terraform plan` detected it as drift and attempted to revert the manual modification.

To resolve this, we first verified whether the manual change was intended to become permanent. If it was a valid infrastructure change, we updated the Terraform configuration accordingly and applied the new configuration so that the code remained the single source of truth. If the manual modification was temporary or unauthorized, Terraform safely restored the infrastructure to its expected state. We also educated teams to avoid manual production changes and introduced stricter IAM permissions so that infrastructure modifications were performed only through Terraform.

Another challenge involved **state lock conflicts**. Occasionally, a GitLab pipeline would be interrupted due to network issues or manual cancellation while Terraform was executing. Since Terraform had already acquired the DynamoDB lock, subsequent pipeline executions failed with a "State Locked" error. Before removing the lock, we always verified that no Terraform process was still running. Once confirmed, we safely released the stale lock using `terraform force-unlock` and reran the pipeline. This prevented accidental corruption of the shared state file.

I also encountered dependency-related challenges when provisioning complex AWS resources. For example, an Application Load Balancer depended on Security Groups, Subnets, Target Groups, and VPC resources. During one deployment, Terraform attempted to create resources before their dependencies were fully available, causing intermittent failures. To resolve this, we used explicit `depends_on` relationships where necessary and designed our Terraform modules to expose required outputs so that dependent resources were created in the correct order. This significantly improved deployment reliability.

Another challenge involved managing infrastructure across multiple environments. Initially, environment-specific values such as CIDR blocks, instance sizes, and subnet IDs were hardcoded, making maintenance difficult. We refactored the code to use reusable Terraform modules and environment-specific variable files. This reduced duplication, simplified deployments, and ensured that Development, UAT, and Production environments followed the same architecture while still allowing environment-specific customization.

We also experienced issues when team members were using different Terraform versions. Differences in provider versions occasionally caused unexpected plan outputs or compatibility issues. To solve this, we standardized Terraform and provider versions using version constraints in the configuration and ensured that all GitLab runners used the same Terraform version. This eliminated inconsistencies between local development environments and CI/CD pipelines.

From these experiences, I learned that successful Terraform implementation is not only about writing Infrastructure as Code but also about following operational best practices such as centralized state management, version control, module reuse, peer reviews, automated CI/CD deployments, state locking, and minimizing manual infrastructure changes. These practices have made our infrastructure deployments more reliable, repeatable, and easier to maintain.

---


# Docker

---

## 1. What is Docker and why is it used?

### Answer

Docker is an open-source containerization platform that enables developers to package an application along with all its dependencies, libraries, runtime, and configuration files into a lightweight, portable container. The main advantage of Docker is that the application behaves consistently across different environments, whether it is running on a developer's laptop, a testing server, or a production Kubernetes cluster.

In my current project, we use Docker extensively as part of our CI/CD pipeline. Whenever developers commit code to GitLab, the pipeline automatically builds the application, creates a Docker image, scans it for vulnerabilities, pushes it to Amazon Elastic Container Registry (ECR), and deploys it to Amazon EKS. This ensures that the exact same Docker image tested in the Development environment is promoted to UAT and eventually Production, eliminating environment-specific issues.

Before adopting Docker, applications were deployed directly on virtual machines. Different servers often had different versions of Java, Node.js, Python, or operating system libraries, resulting in compatibility issues and deployment failures. Docker solved this problem by packaging the application together with its runtime environment. Since the container includes everything the application needs to run, it behaves identically across all environments.

Another major advantage is resource efficiency. Unlike virtual machines, Docker containers share the host operating system kernel, making them significantly lighter and faster to start. A single server can run multiple isolated containers with lower CPU and memory overhead. This enables better resource utilization and faster application scaling, especially in Kubernetes environments.

Docker also simplifies deployments and rollback procedures. Every image is versioned using tags, allowing us to deploy a specific application version whenever required. If a deployment introduces an issue, Kubernetes can quickly roll back to the previous stable Docker image without rebuilding the application. This has significantly improved release reliability and reduced deployment risks in our production environment.

Overall, Docker has become a fundamental component of our DevOps workflow by improving application portability, deployment consistency, scalability, resource utilization, and integration with Kubernetes and CI/CD pipelines.

---

## 2. Difference between ENTRYPOINT and CMD?

### Answer

Both **ENTRYPOINT** and **CMD** are Dockerfile instructions that define what happens when a container starts, but they serve different purposes and are often used together.

The **ENTRYPOINT** instruction specifies the main executable that should always run when the container starts. It defines the primary purpose of the container and cannot be easily overridden unless explicitly specified using the `--entrypoint` option during container execution. In production environments, ENTRYPOINT is commonly used to start the application itself, ensuring that the container consistently performs its intended function.

The **CMD** instruction, on the other hand, provides default arguments for the ENTRYPOINT command or specifies a default command if ENTRYPOINT is not defined. Unlike ENTRYPOINT, CMD can easily be overridden when starting the container by providing a different command. This makes CMD useful for supplying configurable runtime parameters while keeping the main application unchanged.

In my projects, we usually combine both instructions. For example, the ENTRYPOINT starts the Java application, while CMD supplies the default JVM options or application parameters. This provides flexibility because operations teams can override only the runtime arguments without modifying the Docker image itself.

A practical example would be a Spring Boot application. The ENTRYPOINT launches the Java runtime and application JAR, while CMD specifies default JVM memory settings. If we need different memory allocation for Production compared to Development, we can override the CMD values without rebuilding the Docker image.

Using ENTRYPOINT and CMD correctly makes Docker images more reusable, configurable, and easier to manage across different deployment environments.

---

## 3. Difference between Docker images and containers?

### Answer

A Docker **Image** is a read-only template that contains everything required to run an application, including the operating system libraries, runtime, application code, dependencies, environment configuration, and startup instructions. Images are created from Dockerfiles and stored in container registries such as Amazon Elastic Container Registry (ECR) or Docker Hub. Once an image is built, it remains unchanged unless a new version is created.

A Docker **Container** is a running instance of a Docker image. When a container starts, Docker creates a writable layer on top of the image where temporary files, logs, and runtime changes are stored. Multiple containers can be created from the same image, each running independently with its own processes, networking, and filesystem.

In our project, every successful GitLab pipeline builds a Docker image and pushes it to Amazon ECR with a unique version tag based on the Git commit ID. Kubernetes then pulls that image from ECR and creates multiple container instances depending on the required number of replicas. For example, a Deployment configured with three replicas creates three separate containers from the same Docker image. Although all three containers originate from the same image, each container operates independently and can be restarted or replaced without affecting the others.

One important difference is persistence. Docker images are immutable and serve as reusable templates, whereas containers are temporary runtime instances. If a container is deleted, any changes made inside the container are lost unless external persistent storage such as Kubernetes Persistent Volumes or Amazon EFS is used.

Understanding this distinction is important because CI/CD pipelines primarily produce Docker images, while Kubernetes manages the lifecycle of Docker containers during application deployment and scaling.

---

## 4. What are multi-stage builds?

### Answer

Multi-stage builds are a Docker feature that allows multiple build stages within a single Dockerfile. The primary objective is to create smaller, more secure, and production-ready Docker images by separating the build environment from the runtime environment.

In traditional Docker builds, all build tools, compilers, package managers, source code, and temporary files remain inside the final Docker image. This increases the image size, introduces unnecessary security vulnerabilities, and slows down image downloads during deployments. Multi-stage builds solve this problem by performing the application compilation in one stage and copying only the required runtime artifacts into the final production image.

In my current project, our Java Spring Boot applications are built using Maven. The first stage of the Dockerfile uses a Maven image to download dependencies and compile the application. After the build completes, only the generated JAR file is copied into a lightweight OpenJDK runtime image. Since Maven, source code, build caches, and temporary files are excluded from the final image, the production image becomes significantly smaller and more secure.

This approach provides several advantages. Smaller Docker images are transferred faster between GitLab runners, Amazon ECR, and Kubernetes nodes, reducing deployment time. Fewer installed packages also reduce the attack surface because unnecessary build tools are not included in production containers. Additionally, smaller images consume less storage and improve Kubernetes startup times.

Multi-stage builds have become one of our standard Docker best practices because they improve performance, reduce security risks, and produce clean production images suitable for enterprise deployments.

---


---

## 5. How do you reduce Docker image size and why?

### Answer

Reducing Docker image size is an important best practice because smaller images are faster to build, transfer, store, and deploy. In our production environment, every successful GitLab CI/CD pipeline builds a Docker image, pushes it to Amazon Elastic Container Registry (ECR), and Kubernetes pulls the image before creating Pods. If the image size is unnecessarily large, deployments become slower, consume more network bandwidth, occupy additional storage in the container registry, and increase application startup time. Therefore, image optimization is a regular part of our Docker development process.

The first optimization technique we use is **multi-stage builds**. During the build stage, all development tools such as Maven, Gradle, Node.js, or build dependencies are available to compile the application. Once the build is complete, only the final executable artifact, such as a JAR file or compiled application, is copied into a lightweight runtime image. This removes unnecessary source code, build tools, caches, and temporary files from the final image, significantly reducing its size.

Another important practice is selecting **lightweight base images**. Instead of using large operating system images, we prefer minimal runtime images such as Alpine Linux or slim variants whenever they are compatible with the application. These images contain only the essential packages required to run the application, reducing both image size and the attack surface.

We also optimize the Dockerfile by combining related commands into fewer layers. Every instruction in a Dockerfile creates a new layer, so minimizing unnecessary layers helps reduce the overall image size. Temporary files, package manager caches, and unnecessary installation artifacts are removed within the same layer to prevent them from remaining in the final image.

Another optimization involves using a `.dockerignore` file. Similar to `.gitignore`, this file prevents unnecessary files such as Git repositories, local IDE configurations, documentation, logs, temporary files, and test reports from being copied into the Docker build context. Excluding these files reduces both build time and image size.

Finally, we regularly scan our Docker images using vulnerability scanning tools integrated into the CI/CD pipeline. During this process, we also identify unnecessary packages and outdated libraries that can be removed or upgraded. Smaller images not only improve deployment speed but also reduce the number of installed packages, thereby lowering the potential attack surface.

By following these optimization techniques, we have reduced image sizes significantly, resulting in faster Kubernetes deployments, lower storage costs, quicker image downloads, and improved overall application performance.

---

## 6. How do you optimize Docker images for performance and security?

### Answer

Optimizing Docker images involves improving both runtime performance and container security because production containers should be efficient, lightweight, and resistant to security vulnerabilities. In my current project, Docker image optimization is integrated into our DevOps pipeline and follows multiple best practices before an image is deployed to Amazon EKS.

From a performance perspective, we first use **multi-stage Docker builds** so that only the application runtime artifacts are included in the final image. Build tools such as Maven, Gradle, source code, and temporary files remain in the build stage and are excluded from the production image. This significantly reduces image size, allowing Kubernetes nodes to download images faster and start Pods more quickly.

We also choose lightweight base images wherever possible. Smaller runtime images consume fewer system resources and reduce startup times. Additionally, we carefully structure the Dockerfile to maximize layer caching. Instructions that change infrequently, such as dependency installation, are placed before frequently changing application source code. This enables Docker to reuse cached layers during subsequent builds, reducing pipeline execution time.

From a security perspective, one of our key practices is avoiding running containers as the root user. Instead, we create a dedicated non-root user inside the Docker image and configure the application to run with minimal privileges. This significantly reduces the impact of potential container compromises.

We also ensure that only required packages are installed inside the image. Unnecessary utilities, editors, debugging tools, and package managers are excluded from the final runtime image. Fewer installed packages mean fewer potential vulnerabilities and a smaller attack surface.

Every Docker image is scanned automatically during the GitLab CI/CD pipeline using container vulnerability scanning tools. The scan identifies outdated operating system packages, vulnerable libraries, and known CVEs. If critical vulnerabilities are detected, the pipeline fails, preventing insecure images from reaching Production.

Sensitive information such as database passwords, API keys, or AWS credentials is never embedded inside Docker images. Instead, Kubernetes Secrets, AWS Secrets Manager, or environment variables are used to inject configuration securely at runtime. This ensures that images remain generic and reusable while protecting confidential information.

Finally, we regularly update base images to include the latest security patches and operating system updates. Even if the application code has not changed, rebuilding Docker images periodically ensures that newly discovered vulnerabilities are addressed before deployment.

By combining lightweight images, efficient layer caching, non-root execution, vulnerability scanning, secret management, and regular updates, we create Docker images that are both performant and secure for enterprise production environments.

---

## 7. Explain the Dockerfile creation process.

### Answer

A Dockerfile is a text file containing a sequence of instructions that Docker follows to build a container image. In my current project, every microservice has its own Dockerfile, which is maintained alongside the application source code in GitLab. During the CI/CD pipeline, GitLab automatically reads the Dockerfile, builds the image, scans it for vulnerabilities, and pushes it to Amazon Elastic Container Registry (ECR) before deployment to Amazon EKS.

The Dockerfile creation process begins by selecting an appropriate **base image**. The choice depends on the application's technology stack. For example, Java applications use OpenJDK runtime images, while Node.js applications use official Node runtime images. Choosing a minimal and secure base image helps improve performance and reduce vulnerabilities.

The next step is configuring the application environment. This includes defining the working directory, copying dependency files, installing required packages, and downloading application dependencies. To improve Docker layer caching, dependency installation is performed before copying the complete application source code whenever possible. This allows Docker to reuse cached dependency layers if only the application code changes.

After dependencies are installed, the application source code is copied into the image, and the application is compiled if necessary. For Java applications, we commonly use multi-stage builds where Maven compiles the project in the first stage, and only the generated JAR file is copied into the final runtime image.

The Dockerfile also specifies runtime configuration such as environment variables, exposed ports, health checks where appropriate, and the command used to start the application. We generally use ENTRYPOINT to define the main application executable and CMD to provide configurable runtime parameters.

Before finalizing the Dockerfile, we review it against security best practices. We avoid running containers as the root user, remove unnecessary packages, clean temporary files, minimize image layers, and ensure that sensitive information is never hardcoded inside the image.

Once the Dockerfile is committed to GitLab, the CI/CD pipeline automatically builds the Docker image. After successful vulnerability scanning, the image is tagged using the application version or Git commit ID and pushed to Amazon ECR. Kubernetes then pulls the same immutable image for deployment across Development, UAT, and Production environments.

By following this standardized Dockerfile creation process, we ensure that every application is packaged consistently, securely, and efficiently, making deployments reliable and repeatable across all environments.

---


# Kubernetes (K8s)

---

## 1. What is Kubernetes and why is it used?

### Answer

Kubernetes, commonly referred to as K8s, is an open-source container orchestration platform used to automate the deployment, scaling, management, and monitoring of containerized applications. While Docker is responsible for creating and packaging applications into containers, Kubernetes manages those containers in production environments. It ensures that applications remain highly available, scalable, and self-healing without requiring manual intervention.

In my current project, we use **Amazon Elastic Kubernetes Service (EKS)** to host our microservices. Every successful GitLab CI/CD pipeline builds a Docker image, pushes it to Amazon Elastic Container Registry (ECR), and deploys the latest version to the EKS cluster. Kubernetes manages the complete lifecycle of these containers, including scheduling Pods on worker nodes, monitoring their health, restarting failed containers, scaling applications based on workload, and performing rolling deployments with minimal downtime.

One of the primary reasons we use Kubernetes is its **self-healing capability**. If a Pod crashes due to an application failure or node issue, Kubernetes automatically creates a new Pod to replace it. This happens without any manual intervention, ensuring that the desired number of application instances is always maintained. This significantly improves application availability and reduces operational effort.

Another important feature is **automatic scaling**. During periods of high user traffic, Kubernetes can increase the number of running Pods using the Horizontal Pod Autoscaler (HPA). When traffic decreases, it automatically reduces the number of Pods to optimize resource utilization. Combined with the Cluster Autoscaler, Kubernetes can also add or remove worker nodes based on resource requirements, making the platform highly scalable.

Kubernetes also simplifies application updates. Instead of shutting down the application during deployment, it performs rolling updates by gradually replacing old Pods with new ones while continuously monitoring health checks. If the new version fails, Kubernetes supports automatic rollback to the previous stable version, minimizing downtime and reducing deployment risks.

Overall, Kubernetes has become a critical component of our DevOps architecture because it provides container orchestration, high availability, automatic scaling, self-healing, service discovery, load balancing, and seamless integration with CI/CD pipelines, enabling us to manage production workloads efficiently.

---

## 2. Explain Kubernetes architecture.

### Answer

Kubernetes follows a **master-worker architecture**, where the Control Plane manages the cluster, and Worker Nodes run the actual application workloads. Understanding this architecture is important because every Kubernetes operation involves interaction between these components.

The **Control Plane** is responsible for managing the entire Kubernetes cluster. It contains several core components. The **API Server** acts as the central entry point for all cluster operations. Whenever we execute commands using `kubectl` or when GitLab deploys an application, the request is sent to the API Server, which validates and processes it.

The **etcd** database stores the complete state of the Kubernetes cluster, including information about Pods, Deployments, Services, ConfigMaps, Secrets, Nodes, and other Kubernetes resources. Since etcd contains the cluster configuration, its availability and backup are critical for cluster recovery.

Another important component is the **Scheduler**, which determines the most suitable worker node for every newly created Pod. It considers factors such as CPU availability, memory utilization, node affinity, taints, tolerations, and resource requests before assigning a Pod to a node.

The **Controller Manager** continuously monitors the desired state of the cluster and compares it with the actual state. If a Pod crashes or a node becomes unavailable, the Controller Manager automatically creates replacement Pods to restore the desired number of replicas. This is one of the reasons Kubernetes is considered self-healing.

The **Worker Nodes** execute the application workloads. Every worker node contains the **Kubelet**, which communicates with the Control Plane and ensures that containers are running as instructed. The **Container Runtime**, such as containerd, is responsible for pulling Docker images from Amazon ECR and running the containers. **Kube Proxy** manages networking and load balancing between Pods by maintaining network rules for Services.

In our Amazon EKS environment, AWS manages the Control Plane, while we manage the worker nodes using managed node groups. Applications are deployed through Kubernetes Deployments, and traffic is routed through Kubernetes Services and AWS Application Load Balancers. Prometheus and Grafana continuously monitor the cluster, while Cluster Autoscaler automatically adjusts node capacity based on workload.

This architecture enables Kubernetes to provide scalability, high availability, fault tolerance, and automated container management, making it suitable for enterprise production environments.

---

## 3. Difference between ConfigMap and Secret?

### Answer

Both ConfigMaps and Secrets are Kubernetes resources used to externalize application configuration, but they are designed for different types of data. Understanding the distinction is important because it directly affects application security and configuration management.

A **ConfigMap** is used to store non-sensitive configuration data such as application properties, environment variables, feature flags, URLs, log levels, timeout values, or configuration files. By storing configuration outside the application image, the same Docker image can be deployed across Development, UAT, and Production while using different configuration values for each environment. This improves flexibility and reduces the need to rebuild Docker images whenever configuration changes.

A **Secret**, on the other hand, is specifically designed to store sensitive information such as database passwords, API keys, authentication tokens, SSL certificates, or cloud credentials. Although Kubernetes Secrets are encoded using Base64 by default, in production environments we integrate them with AWS Secrets Manager and enable encryption at rest using AWS KMS to provide stronger security. Access to Secrets is also restricted using Kubernetes RBAC policies so that only authorized Pods can retrieve them.

In my project, ConfigMaps are used to store configuration such as application endpoints, logging configurations, JVM options, and environment-specific settings. Secrets are used for database credentials, third-party API tokens, OAuth client secrets, and SSL certificates. During deployment, Kubernetes injects these values into the application as environment variables or mounted files, allowing the application to retrieve configuration securely at runtime.

Another advantage of separating configuration from application code is operational flexibility. If a configuration value changes, we can update the ConfigMap or Secret and restart the affected Pods without rebuilding or modifying the Docker image. This simplifies configuration management across multiple environments.

From a security perspective, we follow the principle that **sensitive information should never be stored in ConfigMaps or hardcoded inside Docker images**. Instead, all confidential information is stored in Secrets or external secret management systems, ensuring that application credentials remain protected throughout the deployment lifecycle.

---


---

## 4. Explain Kubernetes configuration changes/deployments you handled.

### Answer

In my current project, I am responsible for deploying and managing multiple microservices running on Amazon EKS. Most configuration changes are performed through Kubernetes manifests or Helm charts and are deployed using our GitLab CI/CD pipeline. Since all Kubernetes configurations are stored in Git repositories, every change goes through code review, approval, and automated deployment, ensuring consistency across Development, UAT, and Production environments.

One of the common activities I handle is updating **Deployments** whenever a new application version is released. After the GitLab pipeline builds the Docker image and pushes it to Amazon ECR, the Kubernetes Deployment manifest is updated with the new image tag. Kubernetes performs a rolling update, gradually replacing old Pods with new ones while continuously monitoring readiness and liveness probes. This approach ensures that users experience minimal or no downtime during deployments.

I also frequently manage **ConfigMaps** and **Secrets**. For example, when application properties such as API endpoints, logging levels, or feature flags need to change, I update the ConfigMap rather than rebuilding the Docker image. Similarly, when database passwords or API credentials are rotated, I update the Kubernetes Secret or AWS Secrets Manager integration. After these changes, the affected Pods are restarted so they can consume the updated configuration.

Another area I regularly work on is resource optimization. Based on Prometheus and Grafana monitoring data, I update CPU and memory requests and limits to ensure applications have sufficient resources without overprovisioning the cluster. I have also configured Horizontal Pod Autoscaler (HPA) for several services to automatically increase or decrease the number of Pods based on CPU utilization and application traffic.

Health probe configuration is another important responsibility. During one deployment, users experienced intermittent request failures because traffic was reaching the application before it had fully initialized. I resolved this by configuring appropriate readiness probes and startup probes so Kubernetes would route traffic only after the application was completely ready to serve requests. This significantly improved deployment stability.

I have also managed Kubernetes Ingress resources integrated with the AWS Application Load Balancer. When new APIs or services are introduced, I update Ingress rules, SSL certificates, and routing configurations to ensure secure HTTPS communication. Throughout all configuration changes, we validate updates in Development and UAT before promoting them to Production, minimizing deployment risks.

Overall, my Kubernetes responsibilities include application deployments, configuration management, scaling adjustments, resource optimization, health check tuning, Ingress updates, and production troubleshooting while following GitOps and CI/CD best practices.

---

## 5. How do you troubleshoot Pod issues?

### Answer

When a Kubernetes Pod encounters an issue, I follow a systematic troubleshooting approach rather than making assumptions. My objective is first to identify whether the problem is related to the application, Kubernetes configuration, networking, storage, or the underlying infrastructure. A structured approach helps minimize downtime and ensures the root cause is identified quickly.

The first step is to check the Pod status using Kubernetes. States such as **Pending**, **CrashLoopBackOff**, **ImagePullBackOff**, **ErrImagePull**, **OOMKilled**, or **ContainerCreating** provide an initial indication of the problem. I then examine the Pod events to identify scheduling failures, image pull issues, insufficient resources, or probe failures.

If the Pod is running but the application is not functioning correctly, I review the container logs. The logs often reveal application startup failures, missing environment variables, configuration issues, database connectivity problems, or runtime exceptions. If the application terminates before logs are generated, I inspect the previous container logs to understand why the container exited unexpectedly.

For Pods stuck in the **Pending** state, I verify whether sufficient CPU and memory are available on the worker nodes. I also check node taints, tolerations, node selectors, resource quotas, and Persistent Volume availability. In some cases, the Cluster Autoscaler may need to provision additional worker nodes before the Pod can be scheduled.

If the Pod cannot pull its Docker image, I verify that the image exists in Amazon ECR, confirm the image tag is correct, and ensure the worker nodes have the required IAM permissions to access the registry. I also check network connectivity between the EKS cluster and Amazon ECR.

Networking issues require validating Kubernetes Services, Endpoints, DNS resolution, Ingress resources, and Network Policies. If an application cannot communicate with another service, I verify that the Service selector matches the Pod labels and confirm that the endpoints have been created correctly.

Resource-related issues are another common cause of Pod failures. If the Pod is repeatedly being terminated due to insufficient memory, Kubernetes marks it as **OOMKilled**. In such cases, I review Prometheus and Grafana metrics, adjust resource requests and limits, and work with the development team to optimize application memory consumption if necessary.

If the issue is not immediately apparent, I investigate the worker node itself by reviewing kubelet logs, node conditions, disk utilization, CPU usage, and system events. Sometimes infrastructure issues such as node failures, disk pressure, or network problems can indirectly affect Pod health.

Throughout the troubleshooting process, I rely on Kubernetes Events, Prometheus, Grafana, CloudWatch logs, and centralized ELK logging to correlate application behavior with infrastructure events. This structured methodology enables me to diagnose and resolve Pod issues efficiently while minimizing production downtime.

---

## 6. Difference between Deployment, StatefulSet, and DaemonSet?

### Answer

Deployment, StatefulSet, and DaemonSet are Kubernetes workload controllers, but they are designed for different types of applications and operational requirements. Choosing the appropriate controller depends on whether the application is stateless, stateful, or infrastructure-related.

A **Deployment** is used for **stateless applications** where individual Pods are interchangeable. Examples include web applications, REST APIs, frontend services, and microservices. Deployments support rolling updates, automatic rollbacks, scaling, and self-healing. In my project, almost all Spring Boot microservices are deployed using Deployments because each Pod is identical and any Pod can handle incoming requests without maintaining user-specific state.

A **StatefulSet** is designed for **stateful applications** that require stable network identities, persistent storage, and predictable Pod naming. Unlike Deployments, StatefulSets create Pods in a specific order and maintain the same identity even after restarts. They are commonly used for databases such as MySQL, PostgreSQL, MongoDB, Cassandra, Kafka, or Elasticsearch. Each Pod receives its own Persistent Volume, ensuring that application data is preserved even if the Pod is recreated. Although our production databases are managed using Amazon RDS, I have worked with StatefulSets while deploying stateful applications in Kubernetes test environments.

A **DaemonSet** ensures that exactly one Pod runs on every worker node in the cluster. Whenever a new node joins the cluster, Kubernetes automatically schedules a DaemonSet Pod on that node. DaemonSets are typically used for infrastructure components rather than business applications. Examples include Fluentd or Fluent Bit for log collection, Prometheus Node Exporter for infrastructure monitoring, security agents, and networking plugins. In our EKS cluster, Node Exporter and Fluent Bit are deployed as DaemonSets so that every worker node continuously exports system metrics and forwards logs to centralized logging systems.

In summary, I use **Deployments** for stateless business applications, **StatefulSets** for applications requiring persistent identity and storage, and **DaemonSets** for cluster-wide infrastructure services that must run on every node.

---

## 7. How do you manage scaling and high availability?

### Answer

Ensuring scalability and high availability is one of the primary reasons we use Kubernetes in production. In my current project, we achieve this through a combination of Kubernetes features, AWS infrastructure, and continuous monitoring.

For application scaling, we use the **Horizontal Pod Autoscaler (HPA)**. Based on metrics such as CPU utilization, HPA automatically increases or decreases the number of application Pods. For example, if CPU utilization exceeds our configured threshold during periods of high traffic, Kubernetes automatically creates additional Pods to distribute the workload. Once traffic decreases, the extra Pods are removed to optimize resource utilization.

At the infrastructure level, we use the **Cluster Autoscaler** with Amazon EKS. If HPA creates additional Pods but the cluster lacks sufficient CPU or memory to schedule them, the Cluster Autoscaler automatically provisions new EC2 worker nodes. Similarly, when cluster utilization decreases, unnecessary nodes are removed to reduce infrastructure costs.

To ensure application availability, every Deployment is configured with multiple replicas distributed across multiple Availability Zones. If a worker node fails, Kubernetes automatically reschedules the affected Pods onto healthy nodes. We also configure Pod Anti-Affinity rules where appropriate to prevent multiple replicas from running on the same node, reducing the impact of individual node failures.

Health monitoring plays a significant role in maintaining availability. We configure **liveness probes**, **readiness probes**, and **startup probes** for all critical applications. Readiness probes ensure traffic is routed only to healthy Pods, while liveness probes automatically restart unhealthy containers. Startup probes provide additional time for applications with longer initialization periods before Kubernetes begins health monitoring.

Deployments use a **rolling update strategy**, allowing Kubernetes to gradually replace existing Pods with new versions while maintaining application availability. If any issues are detected during deployment, Kubernetes can roll back to the previous ReplicaSet, minimizing user impact.

Continuous monitoring is implemented using Prometheus, Grafana, and AWS CloudWatch. We monitor CPU usage, memory consumption, Pod restarts, response times, request rates, and application error rates. Alerts are configured so that the operations team is notified immediately whenever predefined thresholds are exceeded.

By combining Horizontal Pod Autoscaler, Cluster Autoscaler, multi-replica deployments, Multi-AZ worker nodes, health probes, rolling updates, automatic rollbacks, and proactive monitoring, we maintain a highly available and scalable Kubernetes platform capable of handling production workloads efficiently while minimizing downtime.

---


# AWS

---

## 1. Which AWS services have you worked with?

### Answer

During my 4 years as a DevOps Engineer, I have worked extensively with AWS services to build, deploy, secure, monitor, and maintain cloud infrastructure for enterprise applications. My primary responsibility has been automating infrastructure provisioning using Terraform, deploying containerized applications on Amazon EKS, implementing CI/CD pipelines, monitoring production workloads, and ensuring high availability and security.

One of the core services I use is **Amazon EC2**, where I have provisioned Linux instances for Jenkins servers, self-hosted GitLab runners, monitoring tools, and utility servers. I have configured Auto Scaling Groups to automatically adjust the number of EC2 instances based on workload and integrated them with Application Load Balancers to distribute traffic efficiently.

For containerized workloads, I have extensive experience with **Amazon EKS (Elastic Kubernetes Service)**. Our microservices run on EKS clusters, where I manage Deployments, Services, Ingress resources, ConfigMaps, Secrets, Horizontal Pod Autoscaler (HPA), and Cluster Autoscaler. Docker images are stored in **Amazon Elastic Container Registry (ECR)** and are automatically deployed through GitLab CI/CD pipelines.

I have worked extensively with **Amazon S3** for storing Terraform remote state files, application artifacts, deployment packages, backup files, and log archives. S3 bucket versioning, lifecycle policies, server-side encryption, and IAM access controls are configured to improve security and manage storage efficiently.

Networking is another major area of my experience. I have created and managed **Amazon VPCs**, public and private subnets, Internet Gateways, NAT Gateways, Route Tables, Security Groups, Network ACLs, and VPC Endpoints. These components ensure secure communication between applications while preventing unnecessary internet exposure.

For identity and access management, I regularly configure **AWS IAM** users, roles, instance profiles, policies, and cross-service permissions. We follow the principle of least privilege by granting only the minimum permissions required for applications and CI/CD pipelines.

Our production databases are hosted on **Amazon RDS**, primarily using MySQL and PostgreSQL. I have worked on database connectivity, backup configuration, Multi-AZ deployments, security group management, and credential management through AWS Secrets Manager.

Monitoring and logging are handled using **Amazon CloudWatch**, Prometheus, Grafana, and the ELK Stack. CloudWatch collects EC2 metrics, application logs, alarms, and dashboard metrics, while Prometheus and Grafana provide detailed Kubernetes monitoring.

I have also worked with **Route 53** for DNS management, **AWS Secrets Manager** for secure credential storage, **AWS KMS** for encryption, **Application Load Balancer (ALB)** for traffic distribution, **Auto Scaling Groups**, and **CloudTrail** for auditing infrastructure changes.

Overall, my AWS experience covers infrastructure provisioning, container orchestration, networking, security, monitoring, automation, storage, database management, and CI/CD integration across Development, UAT, and Production environments.

---

## 2. What AWS improvements or changes have you implemented?

### Answer

During my current project, I have implemented several AWS improvements focused on automation, security, performance, scalability, and operational efficiency. Most of these improvements were introduced to reduce manual effort, improve deployment reliability, and optimize cloud infrastructure costs.

One of the biggest improvements was migrating manual infrastructure provisioning to **Terraform**. Previously, engineers created AWS resources manually using the AWS Management Console, resulting in inconsistent configurations across environments. By implementing Infrastructure as Code, we standardized infrastructure provisioning, introduced version control, enabled peer reviews through GitLab Merge Requests, and significantly reduced deployment time.

I also contributed to migrating several applications from traditional EC2 deployments to **Amazon EKS**. Containerizing applications with Docker and deploying them on Kubernetes improved resource utilization, enabled automatic scaling, simplified rolling deployments, and reduced operational overhead. The migration also improved deployment consistency because every environment used the same Docker images.

To improve deployment automation, I enhanced our **GitLab CI/CD pipelines** by integrating Docker image creation, vulnerability scanning, SonarQube code analysis, Terraform validation, Kubernetes deployment, and post-deployment health verification. These changes reduced manual deployments, minimized production errors, and accelerated release cycles.

On the infrastructure side, I implemented **Horizontal Pod Autoscaler (HPA)** and **Cluster Autoscaler** in Amazon EKS. Applications now automatically scale based on CPU utilization, while worker nodes are added or removed dynamically depending on workload. This improved application availability during traffic spikes while reducing unnecessary infrastructure costs during low-traffic periods.

I also strengthened cloud security by replacing hardcoded credentials with **AWS Secrets Manager** and Kubernetes Secrets. IAM policies were reviewed and refined to enforce the principle of least privilege. Sensitive S3 buckets were encrypted using AWS KMS, and unnecessary public access was removed.

Monitoring was another area of improvement. I configured CloudWatch dashboards, Prometheus metrics, Grafana dashboards, and alerting rules to proactively monitor application health, Kubernetes cluster performance, resource utilization, and infrastructure availability. This significantly reduced Mean Time to Detect (MTTD) and improved incident response times.

Additionally, I optimized storage costs by implementing S3 lifecycle policies that automatically moved older log files and backups to lower-cost storage classes. Regular cleanup of unused EBS volumes, snapshots, and obsolete Docker images further reduced AWS operational costs.

These improvements collectively enhanced deployment speed, infrastructure consistency, security, monitoring capabilities, scalability, and overall operational efficiency within our AWS environment.

---

## 3. Explain your experience with EC2, S3, IAM, and VPC.

### Answer

I have hands-on experience with Amazon EC2, S3, IAM, and VPC, as these services form the foundation of our AWS infrastructure.

For **Amazon EC2**, I have provisioned Linux instances using Terraform for Jenkins servers, GitLab runners, monitoring tools, bastion hosts, and utility servers. My responsibilities include selecting appropriate instance types, configuring security groups, attaching IAM roles, managing EBS volumes, enabling CloudWatch monitoring, and integrating instances with Auto Scaling Groups and Application Load Balancers. I have also performed operating system patching, troubleshooting, and performance monitoring of EC2 instances.

With **Amazon S3**, I primarily use it for storing Terraform remote state files, CI/CD build artifacts, application backups, log archives, and static content. I have configured bucket versioning, lifecycle policies, server-side encryption using AWS KMS, access logging, and IAM-based access controls. S3 versioning has been especially useful for recovering previous Terraform state files whenever accidental modifications occurred.

Regarding **AWS IAM**, I regularly create and manage IAM users, groups, roles, and policies. Rather than embedding AWS credentials inside applications, we assign IAM Roles to EC2 instances, EKS worker nodes, and Kubernetes service accounts. This allows applications to securely access AWS services without exposing long-term credentials. I also review IAM policies to ensure least privilege access and periodically remove unused permissions to strengthen security.

Networking is an area where I have substantial practical experience. I have created and managed **Amazon VPCs** with public and private subnets distributed across multiple Availability Zones. I configure Internet Gateways, NAT Gateways, Route Tables, Security Groups, Network ACLs, and VPC Endpoints to ensure secure communication between application components. Our EKS worker nodes and Amazon RDS databases are deployed within private subnets, while Application Load Balancers are deployed in public subnets to receive internet traffic securely.

These AWS services work together to provide a secure, scalable, and highly available cloud infrastructure that supports our containerized applications and automated DevOps workflows.

---



# AWS (Continued)

---

## 4. How do you monitor applications and infrastructure?

### Answer

Monitoring applications and infrastructure is a critical part of my responsibilities as a DevOps Engineer because proactive monitoring helps identify issues before they impact end users. In my current project, we use a combination of **Amazon CloudWatch, Prometheus, Grafana, and the ELK Stack** to monitor infrastructure, Kubernetes clusters, applications, and logs. This layered monitoring approach provides complete visibility into the health and performance of our production environment.

For AWS infrastructure, **Amazon CloudWatch** is our primary monitoring service. It collects metrics from EC2 instances, Application Load Balancers, Auto Scaling Groups, RDS databases, and EKS worker nodes. We monitor CPU utilization, memory usage (through CloudWatch Agent), disk utilization, network throughput, EBS performance, and instance health. CloudWatch Alarms are configured with thresholds for critical metrics, such as high CPU utilization, low disk space, or unhealthy instances. When these thresholds are exceeded, notifications are sent to the operations team through Amazon SNS, enabling rapid incident response.

For Kubernetes monitoring, we use **Prometheus** to collect metrics from Pods, Deployments, Nodes, Services, and other Kubernetes resources. Prometheus continuously scrapes metrics exposed by applications and infrastructure components. These metrics are then visualized using **Grafana dashboards**, which provide real-time insights into application performance, Pod health, CPU and memory utilization, request rates, response times, container restarts, and resource consumption. Grafana dashboards help us quickly identify performance bottlenecks and capacity issues.

Log management is handled using the **ELK Stack (Elasticsearch, Logstash, and Kibana)**. Application logs, Kubernetes logs, and system logs are centralized into Elasticsearch through Fluent Bit or Logstash. Kibana provides powerful search and visualization capabilities, allowing us to troubleshoot production issues by filtering logs based on timestamps, services, error messages, or transaction IDs. Centralized logging eliminates the need to log in to individual servers, making troubleshooting much faster.

We also implement **health checks** within Kubernetes using liveness, readiness, and startup probes. These probes continuously monitor application health and ensure that only healthy Pods receive production traffic. If a Pod becomes unhealthy, Kubernetes automatically restarts it, reducing manual intervention and improving application availability.

For production incident management, monitoring tools are integrated with alerting mechanisms that notify the DevOps team whenever predefined thresholds are exceeded. Alerts are prioritized based on severity so that critical production issues receive immediate attention. During incidents, we correlate CloudWatch metrics, Prometheus dashboards, and ELK logs to quickly identify the root cause and implement corrective actions.

Overall, our monitoring strategy combines infrastructure metrics, Kubernetes metrics, centralized logging, health checks, and automated alerting to ensure high application availability, faster troubleshooting, and proactive infrastructure management.

---

## 5. Have you worked on AWS cost optimization?

### Answer

Yes. AWS cost optimization has been an important part of my role because cloud resources can become expensive if they are not monitored and managed properly. In my current project, we regularly review infrastructure utilization and implement several optimization strategies to reduce unnecessary cloud costs while maintaining application performance and availability.

One of the first improvements we implemented was enabling **Horizontal Pod Autoscaler (HPA)** and **Cluster Autoscaler** in Amazon EKS. Instead of running a fixed number of Pods and worker nodes throughout the day, Kubernetes now automatically scales applications based on CPU utilization and workload. During low-traffic periods, unused Pods and worker nodes are removed automatically, significantly reducing EC2 infrastructure costs.

We also reviewed our EC2 instances regularly using CloudWatch metrics. Several servers were consistently underutilized, with CPU utilization remaining below 15%. Based on this data, we downsized certain EC2 instance types to more appropriate configurations without affecting application performance. We also terminated unused development and testing instances that were no longer required.

For storage optimization, we implemented **Amazon S3 Lifecycle Policies**. Older log files, backups, and archived deployment artifacts were automatically moved from the Standard storage class to lower-cost storage classes such as S3 Standard-IA or Glacier. This reduced long-term storage costs while still meeting compliance and retention requirements.

Another area of optimization involved cleaning up unused resources. We periodically identified and removed unattached EBS volumes, obsolete EBS snapshots, unused Elastic IP addresses, outdated Amazon Machine Images (AMIs), and old Docker images stored in Amazon ECR. These resources often continue generating costs even though they are no longer actively used.

Database optimization was also part of our cost management efforts. We monitored Amazon RDS utilization and selected appropriate instance sizes based on workload patterns. Multi-AZ deployments were enabled only for production workloads that required high availability, while development environments used smaller and more cost-effective database instances.

From an infrastructure automation perspective, Terraform helped reduce costs by ensuring resources were provisioned consistently and preventing duplicate or unnecessary infrastructure. Infrastructure changes went through peer review, reducing the likelihood of accidentally creating expensive resources.

We also monitored AWS billing reports and CloudWatch metrics regularly to identify unusual spending trends. Whenever unexpected cost increases occurred, we investigated the affected services, identified the root cause, and implemented corrective actions before the costs continued to grow.

Overall, my AWS cost optimization experience includes rightsizing EC2 instances, implementing Kubernetes auto-scaling, optimizing S3 storage, cleaning unused cloud resources, managing RDS efficiently, automating infrastructure with Terraform, and continuously monitoring resource utilization to balance performance with operational costs.

---


# Monitoring & Ticketing

---

## 1. Which monitoring tools do you use?

### Answer

In my current project, we use a combination of **Prometheus, Grafana, Amazon CloudWatch, and the ELK Stack** to monitor applications, Kubernetes clusters, AWS infrastructure, and logs. Since our applications run on Amazon EKS, no single monitoring tool provides complete visibility, so we use different tools for different purposes and integrate them to achieve end-to-end monitoring.

For Kubernetes monitoring, **Prometheus** is responsible for collecting metrics from Pods, Nodes, Deployments, Services, kube-state-metrics, and Node Exporter. It continuously scrapes metrics from the Kubernetes cluster and stores them in its time-series database.

To visualize these metrics, we use **Grafana**. Grafana dashboards display important production metrics such as CPU utilization, memory consumption, Pod restarts, request rates, response times, error percentages, node health, and application availability. These dashboards help both the DevOps and development teams quickly understand the health of the production environment.

For AWS infrastructure monitoring, we rely on **Amazon CloudWatch**. It collects metrics from EC2 instances, EKS worker nodes, Application Load Balancers, Auto Scaling Groups, RDS databases, and EBS volumes. CloudWatch Alarms notify our operations team whenever predefined thresholds are exceeded, allowing us to respond before users are affected.

For centralized log management, we use the **ELK Stack (Elasticsearch, Logstash, and Kibana)**. Application logs, Kubernetes logs, and system logs are forwarded to Elasticsearch, while Kibana provides powerful search and visualization capabilities. During production incidents, Kibana allows us to quickly locate error messages, stack traces, failed API requests, and application exceptions without logging into individual servers.

Together, these monitoring tools provide complete visibility into infrastructure, applications, containers, Kubernetes clusters, and production logs, enabling proactive monitoring and faster incident resolution.

---

## 2. How do you monitor application and infrastructure health?

### Answer

Monitoring application and infrastructure health is a continuous process in our production environment. We use multiple monitoring layers to ensure that issues are detected before they impact end users. Rather than relying on a single tool, we combine infrastructure monitoring, Kubernetes monitoring, application monitoring, centralized logging, and automated alerting.

At the infrastructure level, **Amazon CloudWatch** continuously monitors EC2 instances, EBS volumes, Application Load Balancers, Auto Scaling Groups, Amazon RDS, and EKS worker nodes. Important metrics include CPU utilization, memory usage, disk utilization, network throughput, instance health, and database performance. CloudWatch Alarms automatically notify the operations team whenever any metric crosses predefined thresholds.

Within Kubernetes, **Prometheus** collects detailed metrics from Pods, Deployments, Nodes, Services, kube-state-metrics, and Node Exporter. These metrics are visualized using **Grafana**, where we monitor application response time, request throughput, container CPU and memory usage, Pod restart counts, error rates, and Kubernetes resource utilization. This helps us identify performance bottlenecks before they become production issues.

We also configure **liveness probes**, **readiness probes**, and **startup probes** for every critical application. Readiness probes ensure that traffic is sent only to healthy Pods, while liveness probes automatically restart unhealthy containers. Startup probes allow applications with longer initialization times to start successfully before Kubernetes begins health monitoring.

Centralized logging using the ELK Stack provides another important layer of monitoring. Instead of checking logs on individual servers, all application and system logs are collected in Elasticsearch and analyzed through Kibana. During production incidents, this significantly reduces troubleshooting time because logs from all services are available in a single location.

In addition to automated monitoring, we review dashboards regularly to identify resource utilization trends, unusual traffic patterns, or increasing error rates. Combining infrastructure metrics, application metrics, health checks, centralized logging, and alerting enables us to maintain high application availability while reducing Mean Time to Detect (MTTD) and Mean Time to Resolve (MTTR).

---

## 3. Experience with Prometheus, Grafana, CloudWatch, or ELK Stack?

### Answer

Yes. I have practical experience working with all four tools as part of our production monitoring solution.

For Kubernetes monitoring, I have worked extensively with **Prometheus**. It collects metrics from Kubernetes components, Node Exporter, kube-state-metrics, and application endpoints exposed through Prometheus metrics. These metrics include CPU usage, memory consumption, network traffic, Pod status, deployment health, container restarts, request latency, and application-specific business metrics. Prometheus serves as the primary monitoring backend for our Kubernetes clusters.

We use **Grafana** to visualize Prometheus metrics through interactive dashboards. I have configured dashboards for Kubernetes clusters, EC2 instances, application performance, JVM metrics, and infrastructure health. These dashboards display important production indicators such as response times, request rates, CPU utilization, memory usage, Pod health, and resource consumption. Grafana also supports alerting, allowing us to notify the operations team whenever important metrics exceed predefined thresholds.

For AWS infrastructure, we use **Amazon CloudWatch**. It monitors EC2 instances, Application Load Balancers, Amazon RDS, Auto Scaling Groups, EKS worker nodes, and other AWS resources. I have configured CloudWatch dashboards, alarms, log groups, and custom metrics. CloudWatch integrates with Amazon SNS to send email notifications whenever production infrastructure experiences high resource utilization or service degradation.

For centralized logging, we use the **ELK Stack**. Fluent Bit forwards Kubernetes and application logs to Elasticsearch, where they are indexed and stored. Kibana provides a centralized interface for searching logs, filtering by application, namespace, timestamp, or error message, and visualizing production events. During production incidents, Kibana helps identify application exceptions, failed API requests, authentication failures, and infrastructure errors much faster than manually checking server logs.

By integrating Prometheus, Grafana, CloudWatch, and the ELK Stack, we achieve complete visibility into infrastructure health, Kubernetes performance, application metrics, and production logs, enabling faster troubleshooting and proactive monitoring.

---

## 4. Which ticketing tool do you use?

### Answer

In my current project, we primarily use **Jira** as our ticketing and project management tool. Jira is used to track development tasks, production incidents, infrastructure changes, service requests, bug fixes, and enhancement requests throughout the software development lifecycle.

Whenever a production issue occurs, a Jira incident ticket is created containing information such as the affected application, severity level, business impact, error description, and supporting logs or screenshots. The ticket is assigned to the appropriate team based on the nature of the issue. As a DevOps Engineer, my responsibility is to investigate infrastructure-related incidents, Kubernetes issues, CI/CD failures, deployment problems, cloud resource issues, or monitoring alerts.

For infrastructure changes such as Terraform updates, Kubernetes configuration modifications, IAM policy updates, or production deployments, change request tickets are created and linked to the corresponding GitLab Merge Requests. This provides complete traceability from business requirements to infrastructure changes and production deployments.

During production incidents, Jira is updated continuously with investigation findings, root cause analysis, mitigation steps, deployment status, and final resolution details. Once the issue is resolved, we perform a Root Cause Analysis (RCA) and document preventive measures to reduce the likelihood of similar incidents occurring in the future.

Using Jira helps improve collaboration between development, DevOps, QA, and business teams while providing complete visibility into ongoing work, incident management, and deployment history.

---

## 5. How do you handle production incidents and escalations?

### Answer

Handling production incidents requires a structured and methodical approach because production systems directly impact business operations and end users. Whenever a critical production alert is received through CloudWatch, Grafana, or the monitoring system, my first priority is to assess the severity of the incident and understand its business impact.

The investigation begins by reviewing monitoring dashboards to determine whether the issue is related to infrastructure, Kubernetes, networking, databases, or the application itself. I correlate metrics from CloudWatch, Prometheus, and Grafana with application logs available in Kibana to identify the root cause as quickly as possible.

If the issue is infrastructure-related, I verify EC2 health, EKS node status, Auto Scaling Groups, Application Load Balancers, database connectivity, storage utilization, and network configuration. For Kubernetes-related issues, I examine Pod status, deployment health, events, logs, resource utilization, readiness probes, and service endpoints.

Throughout the incident, I maintain continuous communication with developers, QA engineers, project managers, and business stakeholders. Regular updates are provided through the incident bridge or communication channels so that everyone remains informed about the investigation progress, estimated resolution time, and current system status.

If the issue was introduced by a recent deployment, we immediately evaluate rollback options. Since our deployments use Kubernetes rolling updates and versioned Docker images, we can quickly roll back to the previous stable release if necessary, minimizing business impact.

After service restoration, we conduct a detailed Root Cause Analysis (RCA). The RCA documents the timeline of events, root cause, corrective actions, and preventive measures. Based on the findings, we may improve monitoring alerts, optimize resource configurations, strengthen CI/CD validation, or update operational runbooks to prevent similar incidents in the future.

By following a structured incident management process, maintaining clear communication, using centralized monitoring, and documenting lessons learned, we ensure rapid recovery while continuously improving the stability and reliability of our production environment.

---


# Shell Scripting & Linux

---

## 1. Which Linux commands do you use regularly?

### Answer

Linux is the primary operating system in our production environment, so I use Linux commands daily for server administration, application deployment, troubleshooting, log analysis, process management, and automation. Rather than memorizing commands, I use them as part of routine operational tasks while supporting CI/CD pipelines, Kubernetes clusters, and AWS infrastructure.

For file and directory management, I regularly use commands such as `ls`, `cd`, `pwd`, `cp`, `mv`, `rm`, `mkdir`, `find`, `locate`, and `du`. These help me navigate the file system, manage deployment artifacts, identify large files consuming disk space, and organize application directories.

For viewing and analyzing logs, I frequently use `cat`, `less`, `more`, `head`, `tail`, and especially `tail -f` to monitor application logs in real time during deployments or production incidents. I also use `grep`, `egrep`, `awk`, `sed`, `sort`, `uniq`, and `cut` to filter logs, extract useful information, and analyze application errors efficiently.

To monitor server performance, I regularly use commands such as `top`, `htop`, `ps`, `free -h`, `vmstat`, `iostat`, `sar`, `df -h`, and `du -sh`. These commands help identify high CPU usage, memory exhaustion, disk utilization issues, and abnormal resource consumption that may affect application performance.

For networking and connectivity troubleshooting, I commonly use `ping`, `curl`, `wget`, `netstat`, `ss`, `nslookup`, `dig`, `traceroute`, `telnet`, and `nc`. These commands help verify DNS resolution, API connectivity, open ports, firewall behavior, and communication between application components.

For process management, I use `ps`, `kill`, `kill -9`, `systemctl`, `service`, and `journalctl` to manage services, restart applications, review system logs, and terminate unresponsive processes.

I also use commands related to permissions and security, including `chmod`, `chown`, `id`, `whoami`, `sudo`, and `passwd` to manage file permissions and user access.

In my daily work as a DevOps Engineer, these Linux commands are essential for troubleshooting production issues, supporting deployments, monitoring servers, managing applications, and performing routine administrative tasks efficiently.

---

## 2. What automation tasks have you implemented using shell scripting?

### Answer

Shell scripting has helped automate many repetitive operational tasks in my current project, reducing manual effort and improving consistency across environments. Instead of performing routine administrative tasks manually, we use Bash scripts that can be executed independently or integrated into GitLab CI/CD pipelines.

One common automation script I developed performs **application health verification after deployment**. Once Kubernetes deployment completes, the script continuously checks the application's health endpoint until it returns a successful response or a configurable timeout is reached. If the health check fails, the script immediately reports the deployment failure and stops the pipeline, preventing unstable releases from progressing further.

I have also created scripts to automate **log collection during production incidents**. Instead of manually collecting logs from multiple servers or Kubernetes Pods, the script gathers application logs, system logs, Kubernetes events, resource utilization, and diagnostic information into a single compressed archive. This significantly reduces troubleshooting time during production outages.

Another useful automation involves **backup and cleanup activities**. We use shell scripts to archive old log files, remove obsolete temporary files, clean application caches, rotate logs, and monitor disk utilization. These scripts are scheduled through cron jobs, ensuring that routine maintenance occurs automatically without manual intervention.

For infrastructure operations, I have developed scripts that validate environment variables, verify AWS credentials, check Kubernetes cluster connectivity, and confirm that required resources exist before Terraform or deployment pipelines begin execution. These pre-validation checks reduce deployment failures caused by configuration issues.

I have also written scripts to automate Docker image cleanup on build servers by removing unused images, stopped containers, and dangling volumes. This helps prevent disk space exhaustion on Jenkins and GitLab runners while maintaining efficient CI/CD execution.

Overall, shell scripting has enabled us to automate repetitive administrative tasks, reduce operational errors, improve deployment reliability, and increase team productivity by minimizing manual intervention.

---

## 3. Are your scripts used by the team? Explain their impact.

### Answer

Yes. Several shell scripts that I developed are actively used by the entire DevOps team and have become part of our standard operational processes. Instead of individual engineers performing repetitive manual tasks, these scripts provide standardized automation that improves consistency, reduces human error, and saves significant operational time.

One example is our **post-deployment validation script**. After every GitLab CI/CD deployment, the script automatically verifies Kubernetes Pod status, application health endpoints, service availability, and deployment rollout status. If any validation fails, the deployment is immediately flagged, allowing the team to investigate before users experience issues. This automation has significantly improved deployment confidence and reduced production incidents caused by incomplete deployments.

Another widely used script automates **Kubernetes log collection** during incident investigations. Previously, engineers manually executed multiple kubectl commands to collect logs from different Pods. The automated script retrieves logs, Kubernetes events, Pod descriptions, resource utilization, and cluster diagnostics with a single command. This standardization has reduced troubleshooting time and simplified incident investigations, especially during critical production outages.

Our team also uses automated **server health check scripts** that verify CPU usage, memory consumption, disk utilization, running services, network connectivity, and critical application processes. These scripts are executed before maintenance activities and after deployments to ensure infrastructure stability.

Several maintenance scripts have also been integrated into scheduled cron jobs. These scripts automate log rotation, backup verification, cleanup of temporary files, and monitoring of available disk space. By automating these routine tasks, we have reduced the likelihood of operational issues caused by neglected server maintenance.

The biggest impact of these scripts has been improved operational efficiency, faster incident resolution, consistent execution of standard procedures, reduced manual effort, and lower risk of human error. Because the scripts are version-controlled in GitLab, improvements and updates are shared across the team, ensuring that everyone benefits from the latest automation enhancements.

---

## 4. How do you troubleshoot Linux server issues?

### Answer

When troubleshooting Linux servers, I always follow a structured approach instead of making assumptions. My objective is to identify the root cause as quickly as possible while minimizing the impact on production services. The first step is understanding the symptoms, such as application downtime, high response times, deployment failures, or monitoring alerts.

I begin by checking overall system health, including CPU utilization, memory usage, disk space, load average, and running processes. High CPU utilization may indicate runaway processes or application loops, while memory exhaustion can lead to Out Of Memory (OOM) events. Disk utilization is also important because full file systems can prevent applications from writing logs or temporary files.

Next, I examine system and application logs using tools such as `journalctl`, `/var/log/messages`, application-specific log files, and Kubernetes logs where applicable. Error messages often reveal issues related to configuration changes, failed services, permission problems, or network connectivity.

If the application cannot communicate with external systems, I verify network connectivity using tools such as `ping`, `curl`, `ss`, `netstat`, `dig`, and `nslookup`. These checks help identify DNS resolution failures, blocked ports, firewall issues, or routing problems.

I also verify whether essential services are running correctly using `systemctl` or `service`. If a service has stopped unexpectedly, I review recent configuration changes, restart the service if appropriate, and continue monitoring to ensure it remains stable.

For performance-related issues, I analyze CPU, memory, disk I/O, and network statistics using monitoring tools such as CloudWatch, Prometheus, and Grafana in addition to Linux utilities. This helps determine whether the problem originates from the operating system, infrastructure, or application layer.

Throughout the troubleshooting process, I document observations, correlate monitoring metrics with logs, communicate updates to stakeholders, and perform root cause analysis after resolving the issue. This systematic approach enables faster diagnosis while reducing the likelihood of recurring production problems.

---

## 5. Explain a shell script you developed for a real-time problem.

### Answer

One shell script that had a significant impact in our production environment was an automated **Kubernetes deployment validation script** integrated into our GitLab CI/CD pipeline.

Previously, after every deployment, engineers manually checked whether Pods were running successfully, verified rollout status, tested application health endpoints, and confirmed service availability. This manual validation was time-consuming, inconsistent, and occasionally resulted in deployments being marked successful even though some Pods had not started correctly.

To address this issue, I developed a Bash script that automatically performs several validation steps immediately after deployment. The script first checks whether the Kubernetes Deployment has completed successfully using rollout status verification. It then validates that all expected Pods are in the Running state and confirms that readiness probes have passed. After the infrastructure checks, the script sends HTTP requests to the application's health endpoint to ensure the service is responding correctly.

If any validation step fails, the script immediately exits with a non-zero status, causing the GitLab pipeline to fail automatically. This prevents faulty deployments from progressing further and alerts the DevOps team before end users are affected. When validation succeeds, the deployment proceeds to completion without requiring manual intervention.

I also included detailed logging within the script so that any failure clearly identifies which validation step failed. This greatly simplifies troubleshooting because engineers can immediately determine whether the issue involves Kubernetes scheduling, application startup, networking, or health checks.

After implementing this automation, deployment verification became faster, more reliable, and fully standardized across all environments. It reduced manual effort, improved deployment quality, minimized production incidents caused by incomplete deployments, and increased confidence in our CI/CD release process. The script is now used as a standard post-deployment validation step across multiple applications in our project.

---



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

