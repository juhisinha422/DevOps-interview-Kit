# PWC DevOps Engineer Interview Experience – Answers (4 Years Experience)

## Round 1: Screening (30 mins)

### Walkthrough of current project architecture and individual role
In my current project, we use a microservices-based architecture deployed on AWS. Applications are containerized using Docker and deployed on Kubernetes (EKS). Traffic flows from Route53 → ALB → Kubernetes services → pods → backend services like RDS.

My role includes:
- Designing CI/CD pipelines using Jenkins
- Managing AWS infrastructure (VPC, EC2, EKS)
- Dockerizing applications
- Deploying using Kubernetes manifests/Helm
- Monitoring using CloudWatch, Prometheus, Grafana
- Handling production issues

---

### DevOps tools used in the last couple of years
Jenkins, GitHub Actions, Docker, Kubernetes, Terraform, AWS, Prometheus, Grafana, Git

---

### AWS services used in production
EC2, S3, IAM, VPC, RDS, ALB, Auto Scaling, EKS, CloudWatch, Route53

---

### Exposing Kubernetes apps to external traffic
- NodePort for testing
- LoadBalancer for cloud-based exposure
- Ingress Controller (NGINX/ALB) for domain/path routing

---

### Purpose of NAT Gateway
Allows instances in private subnet to access the internet for updates/APIs while blocking inbound traffic for security.

---

### Linux basics (process monitoring, file handling)
- Process: top, ps, htop, kill
- Files: ls, cp, mv, rm, find
- Disk: df -h, du -sh

---

### Deployment vs StatefulSet in Kubernetes
Deployment is used for stateless applications where pods are interchangeable.
StatefulSet is used for stateful applications like databases where stable identity and storage are required.

---

### ConfigMap vs Secret
ConfigMap is used for non-sensitive configuration.
Secret is used for sensitive data like passwords, tokens (stored in base64).

---

### Network connectivity checks between servers
ping, telnet, nc, curl, traceroute

---

### CI/CD pipeline experience
Typical pipeline:
Code commit → Build → Test → Docker build → Push to registry → Deploy to Kubernetes → Verify

---

## Round 2: Technical (60 mins)

### Cross-account S3 access (Account A → Account B)
Create IAM Role in Account B, attach S3 access policy, add trust relationship with Account A, and use STS AssumeRole from Account A.

---

### Writing a multi-stage Dockerfile for Node.js
```dockerfile
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app .
CMD ["node", "app.js"]
```

---

### Handling Terraform state file corruption
Use remote backend (S3 with versioning), restore previous version, use DynamoDB for locking, run terraform refresh.

---

### Alternatives to NAT Gateway for private subnet access
NAT Instance and VPC Endpoints.

---

### Debugging exited containers
Use docker logs, docker inspect, check entrypoint, environment variables, and application errors.

---

### Importing existing AWS VPC into Terraform
terraform import aws_vpc.main vpc-xxxx

---

### Blue-green deployment in Kubernetes
Maintain two environments (blue & green), deploy new version to idle environment, switch traffic using service/ingress.

---

### Managing secrets in Terraform (without hardcoding)
Use AWS Secrets Manager or SSM Parameter Store, pass via variables, avoid committing secrets in code.

---

### COPY vs ADD in Dockerfile
COPY is used for simple file copy.
ADD supports URL and automatic extraction of archives.

---

### Cross-account provisioning using Terraform
Use provider with assume_role to access another AWS account.

---

### Handling secrets in Docker (PHP + MySQL use case)
Use environment variables or Docker/Kubernetes secrets instead of hardcoding credentials.

---

### Terraform drift (manual changes vs IaC)
Occurs when manual changes are made outside Terraform. Detect using terraform plan and fix using terraform apply.

---

### Kubernetes network policies for pod communication
Used to control traffic between pods using labels and ingress/egress rules.

---

### Python script: backup files older than 30 days
```python
import os, time

path = "/backup"
now = time.time()

for f in os.listdir(path):
    fp = os.path.join(path, f)
    if os.stat(fp).st_mtime < now - 30*86400:
        os.remove(fp)
```

---

### Cloud cost optimization strategies
Use Reserved Instances/Savings Plans, right-size resources, enable auto scaling, use spot instances, clean unused resources.

---

### Geo-location based routing using AWS
Use Route53 geo or latency-based routing to route users based on location.

---

### Troubleshooting production Kubernetes issues (ImagePull errors, pod eviction, etc.)
- ImagePullBackOff: check image name and credentials
- CrashLoopBackOff: check logs
- Pod Eviction: resource pressure
- Pending Pods: scheduling/resource issues
- Use kubectl logs, describe, get events

---

## Key Takeaway
Strong fundamentals, hands-on AWS, Kubernetes, Docker, Terraform, and troubleshooting skills are essential.

## Pro Tip (4 Years Experience)
Focus on real production scenarios, debugging approach, CI/CD design, cost optimization, and security best practices.
