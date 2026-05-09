# DevOps Interview Questions I AWS Devops Engineer role at T-Systems - Part1

These are covered:

✅ Linux & Shell Scripting
✅ GitHub Actions
✅ Terraform
✅ AWS (s3)
✅ Networking& Ports
✅ Kubernetes

Here are the questions that were asked 👇

---

## 🔹What are the projects you have worked so far?

* Worked on:

  * CI/CD pipeline automation
  * Kubernetes deployment projects
  * AWS infrastructure provisioning using Terraform
  * Docker containerization
  * Monitoring & logging setup

---

## 🔹What are your day-to-day activities ?

* Monitoring production systems
* Managing CI/CD pipelines
* Infrastructure provisioning
* Kubernetes deployments
* Troubleshooting incidents
* Cost optimization
* Security patching & automation

---

## 🔹 Have you worked with GitHub actions?

* Yes
* Used for:

  * CI/CD automation
  * Build & test workflows
  * Docker image build/push
  * Kubernetes deployments

---

## 🔹 What experience you have with IAC?

* Worked with Terraform
* Provisioned:

  * VPC
  * EC2
  * EKS
  * S3
  * IAM
* Used remote backend + modules + state locking

---

## 🔹S3 bucket created,through terraform. But you cannot upload files on it. What would be an issue?

Possible issues:

* IAM permission missing (`s3:PutObject`)
* Bucket policy deny
* Block Public Access enabled
* KMS permission issue
* Wrong region/endpoint

---

## 🔹 If your application works through your IP but not through domain, what would be the reason?

* DNS issue
* Wrong DNS records
* DNS propagation pending
* SSL certificate issue
* Load balancer/domain mapping issue

---

## 🔹 { "Version": "2012-10-17", "Statement": [ { "Effect": "Allow", "Principal": "*", "Action": "s3:GetObject", "Resource": "arn:aws:s3::: bucket-name" } ] }

## What is the issue in the above?

Issue:

* `s3:GetObject` requires object-level ARN

Correct format:

```json id="s31"
"Resource": "arn:aws:s3:::bucket-name/*"
```

---

## 🔹name: CI on: jobs: build: runs-on: ubuntu-latest steps: - uses: actions/checkout@master - run: echo Hello , Correct this .

Correct workflow:

```yaml id="gha1"
name: CI

on:
  push:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: echo "Hello"
```

---

## 🔹Users are getting access denied issue,to allow public credit access  So what can be the issue with the policy?

Possible issues:

* Explicit deny in bucket policy
* Block Public Access enabled
* Missing resource ARN
* Missing `Principal`
* Wrong IAM permissions

---

## 🔹kubectl apply -f deployment.yaml -n test ,explain this?

* `kubectl apply` → creates/updates resource
* `-f deployment.yaml` → YAML file input
* `-n test` → deploys in `test` namespace

Internally:

1. YAML sent to API Server
2. Validated
3. Stored in etcd
4. Scheduler creates pods
5. Desired state maintained

---

# Scenario-Based DevOps Interview Questions – Part 2

---

## 1️⃣ In Terraform code, scenario is five instances in US region and other 5 in Europe region. How would you manage different region in your terraform code?

* Use multiple AWS providers with aliases

```hcl id="tfmulti1"
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "europe"
  region = "eu-west-1"
}
```

### 🔹 How would your definition for AWS provider look like for this?

```hcl id="tfmulti2"
resource "aws_instance" "us" {
  provider = aws
}

resource "aws_instance" "eu" {
  provider = aws.europe
}
```

### 🔹 If the answer is multi-region provider , How can you have multiple regions in the same provider. Is it possible to have it?

* One provider block supports one region
* Multiple regions handled using provider aliases

---

## 2️⃣ Once these instances are up and running ,as there is some issue with instance number four I want to delete it and recreate it How would you do it?

```bash id="tfreplace1"
terraform taint aws_instance.example[3]
terraform apply
```

OR

```bash id="tfreplace2"
terraform apply -replace="aws_instance.example[3]"
```

---

### 🔹 Let's say in the code , I've managed all immutable instances with the state. If network changes happen, as I am managing life cycle of instances I do not want to delete my instance. So how would you tweak that in that case?

```hcl id="tflife1"
lifecycle {
  ignore_changes = [network_interface]
}
```

---

### 🔹 If there are mutable stuff, then it would delete and recreate. But I've managed those mutable instances within my terraform code. How would you do that in that case?

* Separate mutable and immutable resources
* Use lifecycle rules carefully
* Use rolling replacement strategy

---

### 🔹 Let's say if my instance number four is corrupted. And I want to just have it like recreated by some way from terraform. So what way you would use to recreate this instance number four from terraform?

```bash id="tfrecreate1"
terraform apply -replace="aws_instance.example[3]"
```

---

## 3️⃣ kubectl apply -f deployment.yaml -n test ,explain what all would happen in the backend ?

1. YAML sent to API Server
2. Authentication & validation
3. Stored in etcd
4. Deployment controller detects desired state
5. Scheduler selects node
6. Kubelet starts containers
7. Pod networking configured
8. Service discovery updated

---

### 🔹How my pods would get an IP?

* CNI plugin assigns pod IP
* Example:

  * AWS VPC CNI
  * Calico
  * Flannel

---

## 4️⃣ A pod running has 2 containers in kubernetes

### 🔹How would they talk to with each other?

* Containers inside same pod communicate via:

```text id="k8spod1"
localhost
```

* Same network namespace shared

---

### 🔹Can I somehow restrict the communication between both the containers?

* Very limited because same pod shares namespace
* Better approach:

  * Separate into different pods
  * Use Network Policies

---

## 5️⃣ I have 10 pods running in the system

### 🔹One of the nodes is highly under pressure. It wants to evict some pods out of the system .But I don't want one of the pods to be evicted .So how would you configure it under any pressure condition?

```yaml id="k8sqos1"
priorityClassName: high-priority
```

OR

```yaml id="k8sqos2"
resources:
  requests:
    memory: "1Gi"
  limits:
    memory: "1Gi"
```

* Guaranteed QoS pods are least likely to be evicted

---

## 6️⃣ Any critical incident that happened within your system related to kubernetes, And how you were able to fix it?

**Example:**

* Issue:

  * Pods continuously restarting (CrashLoopBackOff)
* Root cause:

  * Wrong environment variable after deployment
* Action:

  * Checked logs/events

```bash id="k8slogs1"
kubectl logs
kubectl describe pod
```

* Fix:

  * Corrected config and redeployed
* Result:

  * Application restored successfully

---

## 7️⃣ Can you explain how did you tune eks cluster?

* Enabled Cluster Autoscaler
* Used node groups properly
* Tuned requests/limits
* Used Spot instances
* Optimized HPA
* Enabled monitoring/logging
* Used taints/tolerations

---

## 8️⃣ How did you tune your Jenkins pipeline? ( Because there might be something that would be coming in from developers)

* Parallel stages
* Shared libraries
* Reusable pipelines
* Image caching
* Automated cleanup
* Reduced unnecessary builds

---

### 🔹How would you enforce the proper request and limits should always be there ,And then only the pods would be provisioned?

* Use:

  * Gatekeeper (OPA)
  * Kyverno
  * Admission Controllers

---

### 🔹How can you ensure that a pod without request and limits cannot exist?

* Enforce admission policy using Gatekeeper/Kyverno

---

### 🔹Have you used gate gatekeepers or jenkins approach to ensure the above?

* Yes, OPA Gatekeeper

---

### 🔹How do you use gatekeeper to enforce this and which gatekeeper have you used?

* Create ConstraintTemplate
* Define policy requiring requests/limits
* Apply constraint cluster-wide

---

## 9️⃣ What kind of cleanup have you done with the script?

* Deleted:

  * Failed pods
  * Evicted pods
  * Old images/logs
  * Unused Docker resources

---

### 🔹What advantage does it adds?What exactly the cleanup would do?

* Frees resources
* Reduces disk usage
* Improves node health

---

### 🔹If it deletes dead pods ,what do you mean by dead pods?

* Pods in:

  * Failed
  * Evicted
  * Completed
  * Unknown states

---

### 🔹One of my pod is in terminated state and it's not getting deleted.And if I execute the script, would I delete that pod from that system?

* Yes, if script targets terminated/failed pods

---

### 🔹Write down a script shell script so that it would go and identify the dead or stuck pods in the system

```bash id="shellclean1"
#!/bin/bash

kubectl get pods --all-namespaces | \
egrep 'Evicted|Error|CrashLoopBackOff|Completed' | \
awk '{print $1, $2}'
```

Delete pods:

```bash id="shellclean2"
kubectl get pods --all-namespaces | \
egrep 'Evicted|Error|CrashLoopBackOff|Completed' | \
awk '{print "kubectl delete pod -n "$1" "$2}' | sh
```

---

