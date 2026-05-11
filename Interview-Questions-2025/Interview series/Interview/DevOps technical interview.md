# Kubernetes

---

## 1. How do you separate environments (like Dev, Staging, Prod) within Kubernetes or across clusters?

* Using separate namespaces:

```bash id="k8sns1"
kubectl create namespace dev
kubectl create namespace prod
```

* Separate clusters for production in large organizations
* Different configs using Helm/Kustomize

---

## 2. What is the purpose of a **ServicePort** in Kubernetes?

* ServicePort exposes application internally within cluster
* Maps incoming traffic to pod target port

Example:

```yaml id="svcport1"
port: 80
targetPort: 8080
```

---

## 3. What is a **Deployment** in K8s, and how does it manage pods?

* Deployment manages ReplicaSets and Pods
* Ensures desired number of pods always running
* Supports rolling updates and rollback

---

# AWS (Amazon Web Services)

---

## 1. What are the primary differences between **AWS EC2** and **AWS Fargate**?

| EC2                     | Fargate                    |
| ----------------------- | -------------------------- |
| VM-based                | Serverless containers      |
| Manage servers manually | AWS manages infrastructure |
| More control            | Less operational overhead  |

---

## 2. Explain the different **Routing Policies** available in **Route 53**.

* Simple Routing
* Weighted Routing
* Latency Routing
* Failover Routing
* Geolocation Routing
* Multi-value Routing

---

## 3. What is the use of a **Rotation Policy** in AWS KMS (Key Management Service)?

* Automatically rotates encryption keys periodically
* Improves security and compliance

---

## 4. What are the main parameters or attributes used when configuring **Auto Scaling** on AWS?

* Minimum instances
* Maximum instances
* Desired capacity
* Scaling policies
* Health checks
* CPU/Memory thresholds

---

# Terraform (IaC)

---

## 1. What is the purpose of the **depends_on** meta-argument in Terraform?

* Creates explicit dependency between resources

Example:

```hcl id="tfdep1"
depends_on = [aws_vpc.main]
```

---

## 2. How do you view or extract **outputs** in Terraform after a deployment?

```bash id="tfout1"
terraform output
```

Specific output:

```bash id="tfout2"
terraform output instance_ip
```

---

## 3. What is the use of **terraform taint**, and when should it be applied?

* Marks resource for recreation on next apply
* Used when resource corrupted/misconfigured

```bash id="tftaint1"
terraform taint aws_instance.example
```

---

# Docker

---

## 1. What is Docker Compose, and why is it used?

* Tool for managing multi-container applications
* Uses `docker-compose.yml`

Example:

* App + DB + Redis together

---

## 2. How can you run multiple containers on the same port in Docker? (Note: Usually involves a Load Balancer or Reverse Proxy).

* Use reverse proxy/load balancer like:

  * Nginx
  * Traefik
* Internally containers run on different ports

---

## 3. What is the purpose of the **EXPOSE** command in a Dockerfile?

* Documents container listening port
* Does not publish port automatically

Example:

```dockerfile id="dockerexp1"
EXPOSE 8080
```

---

## 4. If you need to run multiple versions of Python or different base image versions, how do you handle that in a Dockerfile build?

* Use different base images/tags

Example:

```dockerfile id="dockerpy1"
FROM python:3.9
```

OR

```dockerfile id="dockerpy2"
FROM python:3.11
```

---

# Linux & Scripting

---

## 1. What does the command **chmod 777** do, and what is the meaning of the permission code **5777**?

### chmod 777

* Full permissions to:

  * Owner
  * Group
  * Others

### 5777

* `5` → Special permissions (setuid + sticky bit combination)
* `777` → Full permissions for all users

---

## 2. Which command is used to substitute a string or pattern within a particular directory in Linux?

```bash id="sed1"
sed -i 's/old/new/g' file.txt
```

---

## 3. How do you filter or search for a specific word/pattern in Linux using **grep** or **sed**?

Using grep:

```bash id="grep1"
grep "error" logfile.log
```

Using sed:

```bash id="sed2"
sed -n '/error/p' logfile.log
```

---

## 4. Write a Python script to search for a specific file within a folder or directory.

```python id="pythonfind1"
import os

filename = "test.txt"

for root, dirs, files in os.walk("/home"):
    if filename in files:
        print(os.path.join(root, filename))
```

---

# CI/CD & Git

---

## 1. How do you build **parallel stages** in a Jenkins pipeline?

```groovy id="jenkinsparallel1"
parallel {
    stage('Test') {
        steps {
            echo 'Testing'
        }
    }

    stage('Build') {
        steps {
            echo 'Building'
        }
    }
}
```

---

## 2. What is the difference between **git merge** and **git rebase**?

| Merge                     | Rebase           |
| ------------------------- | ---------------- |
| Preserves history         | Rewrites history |
| Creates merge commit      | Linear history   |
| Safer for shared branches | Cleaner history  |

---

## 3. What is meant by "**staging**" in Git?

* Intermediate area before commit
* Files added using:

```bash id="gitstage1"
git add
```

---

## 4. Explain the concept of Blue-Green Deployment.

* Two identical environments:

  * Blue (current production)
  * Green (new version)
* Traffic switched after validation
* Enables zero downtime rollback

---

## 5.What is Git stash used for.

* Temporarily saves uncommitted changes

Commands:

```bash id="gitstash1"
git stash
git stash pop
```

---

## 6. What is Git commit used for ? Are all git commits will same if we do git rebase?

* Git commit saves snapshot of code changes

```bash id="gitcommit1"
git commit -m "message"
```

* After rebase:

  * Commit hashes change
  * History rewritten
  * Content may remain same but commit IDs differ

---
