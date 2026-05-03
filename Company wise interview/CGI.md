# L1 CGI Interview Questions – DevOps (Answers)

---

## 1) Roles and responsibilities

* Build & manage CI/CD pipelines (Jenkins/GitHub Actions)
* Infrastructure provisioning using Terraform
* Dockerize applications & manage Kubernetes deployments
* Monitor systems (CPU, memory, logs)
* Troubleshoot production issues
* Ensure security best practices (IAM, secrets)
* Collaborate with dev & QA teams

---

## 2) How do you design highly scalable infrastructure in AWS

* Use **Auto Scaling Groups** for dynamic scaling
* Deploy behind **Application Load Balancer (ALB)**
* Use **Multi-AZ architecture** for high availability
* Store static content in **S3 + CloudFront**
* Use **RDS with read replicas** or **DynamoDB**
* Stateless apps (store session in Redis/DB)
* Infrastructure as Code (Terraform)

---

## 3) Deployment strategy

* Rolling deployment (default in Kubernetes)
* Blue-Green deployment
* Canary deployment
* Recreate strategy (downtime acceptable)

---

## 4) Write multi-stage docker file

```dockerfile id="t1g9df"
# Stage 1 - Build
FROM node:18 AS build
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Stage 2 - Run
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
```

---

## 5) Write python script or shell script to check CPU usage and disk usage, what is the purpose of grep command

### Shell Script:

```bash id="t7rj3d"
#!/bin/bash
echo "CPU Usage:"
top -bn1 | grep "Cpu"

echo "Disk Usage:"
df -h
```

### grep purpose:

* Used to search text/pattern in files or command output

---

## 6) How do you mention versioning of Terraform module

```hcl id="k9v1s2"
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.19.0"
}
```

---

## 7) Suppose state file is corrupted, how will you rollback

* Restore from **remote backend versioning (S3 + versioning enabled)**
* Use backup state file
* Run `terraform refresh`
* Reapply infra carefully

---

## 6) Application is not accessible, how will troubleshoot

* Check app logs
* Verify container/pod status
* Check service & ingress
* Verify ports & security groups
* Check DNS / Load balancer
* Network connectivity (curl, telnet)

---

## 7) Difference between DaemonSet and Deployment

| Feature  | Deployment   | DaemonSet                  |
| -------- | ------------ | -------------------------- |
| Purpose  | Run app pods | Run 1 pod per node         |
| Scaling  | Manual/auto  | Automatically per node     |
| Use case | Web apps     | Logging, monitoring agents |

---

## 8) How will secure the Jenkins

* Enable authentication (LDAP/OAuth)
* Role-based access control
* Use HTTPS
* Store secrets in credentials manager
* Restrict plugins
* Regular updates

---

## 9) Why do we need agents in Jenkins

* Distribute workload
* Run jobs on different environments
* Improve performance & scalability

---

## 10) Are you using GitHub Actions and Jenkins in your project?

* Yes (example answer):

  * GitHub Actions → lightweight CI (build/test)
  * Jenkins → complex pipelines, deployments

---

## 11) What will happen when you run `kubectl run`

* Creates a pod (or deployment depending on flags)
* Pulls image and starts container

---

## 12) Write a command to login pod

```bash id="2r3qhk"
kubectl exec -it <pod-name> -- /bin/bash
```

---

## 13) Write a command taint and untaint nodes in cluster

```bash id="ztrqps"
kubectl taint nodes node1 key=value:NoSchedule
kubectl taint nodes node1 key=value:NoSchedule-
```

---

## 14) What is difference between ingress and service

| Service                           | Ingress                 |
| --------------------------------- | ----------------------- |
| Exposes app internally/externally | HTTP/HTTPS routing      |
| Works at L4                       | Works at L7             |
| Types: ClusterIP, NodePort, LB    | Path/host-based routing |

---

## 15) If end users unable to access the pods, what will you check

* Pod status (`kubectl get pods`)
* Service mapping
* Ingress rules
* Network policies
* Security groups / firewall
* Logs

---

## 16) Users are reporting intermittent timeout error how will you check and How will you write RCA part?

### Troubleshooting:

* Check CPU/memory spikes
* Analyze logs
* Check network latency
* Load balancer health checks
* DB performance

### RCA:

* Issue summary
* Timeline
* Root cause (e.g., high CPU)
* Fix applied
* Preventive actions

---

## 17) Explain an issue that you solved independently recently?

**Example:**

* Issue: Pod crash due to memory limit
* Action: Analyzed logs, identified OOMKilled
* Fix: Increased memory limits & optimized app
* Result: Stable deployment

---

## 18) Can you list what are AWS service that you aware of it?

* EC2
* S3
* RDS
* IAM
* VPC
* Lambda
* CloudWatch
* Auto Scaling
* ALB
* EKS

---
