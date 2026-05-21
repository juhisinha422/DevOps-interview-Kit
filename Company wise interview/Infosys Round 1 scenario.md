Here’s a detailed README-style answer set for the Infosys Round 1 scenario-based interview questions.

# Infosys DevOps Interview Questions & Detailed Answers (3+ Years Scenario-Based)

---

# 1. Your Terraform state file got corrupted mid-apply. How do you recover without losing infra?

Terraform state corruption is a critical issue because Terraform uses the state file to track infrastructure resources. If corruption happens during `terraform apply`, my first step is to stop any further Terraform execution to avoid additional damage.

First, I would check whether remote backend versioning is enabled, especially if the state is stored in S3. In production, we usually enable:

* S3 versioning
* DynamoDB locking
* State backups

Recovery steps:

1. Identify the last valid state version from S3 bucket version history.
2. Restore the previous stable version of the state file.
3. Verify infrastructure manually using AWS Console or CLI.
4. Run:

```bash id="r1m8vk"
terraform refresh
```

or:

```bash id="j7w4tp"
terraform plan
```

to sync state with actual infrastructure.

If partial resources were created during failed apply:

* Import missing resources using `terraform import`
* Remove broken resources from state using:

```bash id="q3t9pl"
terraform state rm
```

Preventive measures:

* Always use remote backend
* Enable versioning
* Enable state locking
* Avoid manual state modifications

---

# 2. A pod is stuck in CrashLoopBackOff. tell me how you will debugging this ?

CrashLoopBackOff means the container repeatedly starts and crashes.

My debugging approach is systematic.

## Step 1 – Check pod status

```bash id="k2v7mp"
kubectl get pods
```

---

## Step 2 – Describe the pod

```bash id="f8q1tn"
kubectl describe pod <pod-name>
```

I check:

* Events
* Restart count
* Exit codes
* Image pull issues
* Probe failures

---

## Step 3 – Check logs

```bash id="y4p8wr"
kubectl logs <pod-name>
```

For previous crashed container:

```bash id="m1q7zk"
kubectl logs <pod-name> --previous
```

---

## Step 4 – Verify common issues

### Application issue

* Wrong startup command
* Missing dependency
* Configuration error

### Resource issue

* OOMKilled
* CPU starvation

### Probe issue

* Liveness probe failing
* Readiness probe misconfigured

### Secret/config issue

* Missing ConfigMap
* Invalid Secret

---

## Step 5 – Verify node health

```bash id="w5t2nm"
kubectl get nodes
```

---

## Step 6 – Fix and redeploy

After identifying root cause:

* Update deployment
* Rollout restart

```bash id="u6r4pk"
kubectl rollout restart deployment app
```

---

# 3. You need the same infra across Dev, QA & Prod. How do you structure your Terraform code?

I use modular and environment-based Terraform structure.

Project structure:

```text id="v3m9qx"
terraform/
├── modules/
│   ├── vpc/
│   ├── ec2/
│   └── eks/
├── dev/
├── qa/
└── prod/
```

Each environment contains:

* Backend config
* Variables
* tfvars file

Example:

```text id="p7t1wm"
dev.tfvars
qa.tfvars
prod.tfvars
```

Benefits:

* Reusable modules
* Environment isolation
* Easier maintenance
* Scalable infrastructure

I also use:

* Remote backend
* Workspaces (sometimes)
* Separate IAM permissions

Best practice:
Production and non-production should have separate state files.

---

# 4. Deployment shows 0 available pods but 3 desired. What could be wrong? How do you find out?

This means deployment wants 3 replicas but none are becoming available.

My troubleshooting steps:

## Step 1 – Check deployment

```bash id="q6n2pk"
kubectl get deployment
```

---

## Step 2 – Describe deployment

```bash id="x4v7tm"
kubectl describe deployment app
```

Check:

* Events
* ReplicaSet status
* Scheduling errors

---

## Step 3 – Check pods

```bash id="o9p1wr"
kubectl get pods
```

Possible pod states:

* Pending
* CrashLoopBackOff
* ImagePullBackOff

---

## Step 4 – Common reasons

### Image issue

Wrong image or registry access issue.

### Resource issue

Insufficient CPU/memory.

### Node issue

Node NotReady.

### Application issue

Container crashes immediately.

### Probe issue

Readiness probe failing.

### PVC issue

Storage not mounting.

---

## Step 5 – Check events

```bash id="z7m3qt"
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

## Step 6 – Verify service and endpoints

```bash id="w8q5vn"
kubectl get endpoints
```

---

# 5. A teammate manually deleted a resource managed by Terraform. What happens on the next apply? How do you handle it?

Terraform compares actual infrastructure with state file and configuration.

If resource is deleted manually:

* Terraform detects drift during `terraform plan`
* It attempts to recreate missing resource during `terraform apply`

Handling approach:

1. Verify deletion impact.
2. Run:

```bash id="l2p9tk"
terraform plan
```

3. Confirm recreation is safe.
4. Apply changes carefully.

If resource should no longer exist:

* Remove resource from code
* Apply changes properly

Prevention:

* Restrict manual console access
* Enable IAM controls
* Use drift detection
* Follow Infrastructure as Code strictly

---

# 6. A bad release went to production. How do you rollback in Kubernetes with zero downtime?

Kubernetes supports rolling updates and rollback mechanisms.

## Step 1 – Check rollout history

```bash id="g4m7vp"
kubectl rollout history deployment app
```

---

## Step 2 – Rollback deployment

```bash id="n8t3wm"
kubectl rollout undo deployment app
```

Or rollback to specific revision:

```bash id="p2q9vk"
kubectl rollout undo deployment app --to-revision=2
```

---

## Zero downtime approach

I ensure:

* Multiple replicas
* Proper readiness probes
* Rolling update strategy

Example:

```yaml id="v6p4tk"
strategy:
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

This ensures:

* Old pods remain active
* New pods come gradually
* No traffic interruption

---

# 7. You're migrating existing AWS resources to Terraform. How do you import them without recreating?

Terraform import helps bring existing infrastructure under Terraform management.

## Step 1 – Write resource block

Example:

```hcl id="m9v2qp"
resource "aws_instance" "web" {
}
```

---

## Step 2 – Import resource

```bash id="r5q1wn"
terraform import aws_instance.web i-123456789
```

---

## Step 3 – Verify state

```bash id="z4p7tm"
terraform state list
```

---

## Step 4 – Match configuration

Update Terraform code to match actual resource configuration exactly.

Otherwise:

```bash id="x8n2pk"
terraform plan
```

may show unwanted changes.

Best practice:

* Import carefully
* Test in lower environment first

---

# 8. Your service is not reachable from outside the cluster. How do you troubleshoot it end to end?

I troubleshoot layer by layer.

## Step 1 – Verify pod status

```bash id="k9w4tm"
kubectl get pods
```

---

## Step 2 – Verify service

```bash id="o2p7vk"
kubectl get svc
```

Check:

* ClusterIP
* NodePort
* LoadBalancer

---

## Step 3 – Check endpoints

```bash id="m6t1qn"
kubectl get endpoints
```

If endpoints are empty:

* Label selector mismatch

---

## Step 4 – Verify ingress

```bash id="u3w9pk"
kubectl get ingress
```

Check:

* Host rules
* Backend service
* TLS config

---

## Step 5 – Check network policies

```bash id="c7p2tm"
kubectl get networkpolicy
```

---

## Step 6 – Verify application listening port

```bash id="j4q8vn"
netstat -tulnp
```

---

## Step 7 – Check cloud load balancer

* Security groups
* Health checks
* Target groups

---

## Step 8 – Test internally

```bash id="s1m7wk"
kubectl exec -it pod -- curl service-name
```

This identifies where traffic is failing.

---

# 9. terraform plan shows a resource will be destroyed & recreated. How do you avoid downtime?

First, I identify why Terraform wants replacement.

Common reasons:

* Immutable field changed
* Resource dependency change
* Name modification
* Force replacement attribute

## Step 1 – Review plan carefully

```bash id="q9w3tm"
terraform plan
```

---

## Step 2 – Use lifecycle rules

Example:

```hcl id="r8m5pk"
lifecycle {
  create_before_destroy = true
}
```

This creates new resource before deleting old one.

---

## Step 3 – Use rolling replacement strategy

For EC2:

* Use Auto Scaling Group
* Gradual instance replacement

For Kubernetes:

* Rolling deployment

---

## Step 4 – Minimize blast radius

* Apply only targeted resources

```bash id="n1p4vq"
terraform apply -target=resource_name
```

---

## Step 5 – Backup before apply

* Snapshot databases
* Backup configurations

---

## Step 6 – Test in lower environment first

Always validate destructive changes in:

* Dev
* QA
* Staging

before production rollout.

---
