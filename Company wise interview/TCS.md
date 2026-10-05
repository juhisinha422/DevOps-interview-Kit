````markdown id="mrxvji"
# TCS Walk-in DevOps Engineer Interview for 4 years of experience

## 1. Tell me about yourself?

I am a DevOps Engineer with around 4 years of experience in CI/CD automation, cloud infrastructure, Docker containerization, Kubernetes orchestration, and monitoring solutions. I have worked on AWS cloud, Jenkins pipelines, GitHub Actions, Terraform, and Linux administration. My daily responsibilities include automating deployments, managing Kubernetes clusters, troubleshooting production issues, monitoring applications, and improving infrastructure reliability.

---

## 2. Day-to-day activities as a DevOps engineer?

- Monitoring CI/CD pipelines
- Managing Kubernetes clusters
- Infrastructure provisioning using Terraform
- Docker image creation and optimization
- Monitoring using Prometheus and Grafana
- Troubleshooting production issues
- Managing Linux servers
- Automating deployments
- Handling alerts and incidents
- Supporting development and release teams

---

## 3. Write a Dockerfile?

```dockerfile
FROM ubuntu:24.04

RUN apt update && apt install -y nginx

COPY . /var/www/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

## 4. Difference between CMD and Entry-point?

| CMD | ENTRYPOINT |
|---|---|
| Provides default command | Defines main executable |
| Can be overridden | Less flexible to override |
| Used for optional arguments | Used for fixed behavior |

Example:

```dockerfile
ENTRYPOINT ["python3"]
CMD ["app.py"]
```

---

## 5. Write a Linux command to move a file from the var folder to the www folder?

```bash
mv /var/file.txt /www/
```

---

## 6. Difference between ALB and NLB?

| ALB | NLB |
|---|---|
| Layer 7 Load Balancer | Layer 4 Load Balancer |
| Supports HTTP/HTTPS | Supports TCP/UDP |
| Path-based routing | High performance |
| Used for web apps | Used for low latency apps |

---

## 7. Difference between Security Group and NACL?

| Security Group | NACL |
|---|---|
| Instance level | Subnet level |
| Stateful | Stateless |
| Allow rules only | Allow and deny rules |
| More secure | Additional security layer |

---

## 8. What are the pipelines available in Jenkins?

- Declarative Pipeline
- Scripted Pipeline

---

## 9. Write a declarative Pipeline?

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building application'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application'
            }
        }
    }
}
```

---

## 10. Explain Kubernetes Architecture?

### Master Components
- API Server
- Scheduler
- Controller Manager
- ETCD

### Worker Node Components
- Kubelet
- Kube Proxy
- Container Runtime

### Workflow
1. User sends request to API Server
2. Scheduler assigns pod to node
3. Kubelet creates containers
4. Kube Proxy handles networking

---

## 11. What is the crashloopBackup error?

CrashLoopBackOff occurs when a container repeatedly crashes and Kubernetes keeps restarting it.

### Causes
- Wrong application configuration
- Database connectivity issue
- Incorrect environment variables
- Port conflicts
- Application startup failure

### Troubleshooting

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

---

## 12. What is imagepullError?

ImagePullBackOff happens when Kubernetes cannot pull Docker image.

### Causes
- Wrong image name
- Invalid image tag
- Docker registry authentication failure
- Network issue

### Troubleshooting

```bash
kubectl describe pod <pod-name>
```

---

## 13. Write a Deployment file in YAML?

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 2

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
```

---

# Interview experience - Devops Engineer

---

## 1. Explain your complete CI/CD workflow used in the project

1. Developer pushes code to GitHub
2. Webhook triggers Jenkins/GitHub Actions
3. Build starts automatically
4. SonarQube performs code quality checks
5. Docker image gets created
6. Docker image pushed to Docker Hub/ECR
7. Terraform provisions infrastructure
8. Kubernetes deployment happens
9. Monitoring enabled using Prometheus and Grafana

---

## 2. How do you troubleshoot a Kubernetes CrashLoopBackOff issue?

### Steps

1. Check pod status

```bash
kubectl get pods
```

2. Describe pod

```bash
kubectl describe pod <pod-name>
```

3. Check logs

```bash
kubectl logs <pod-name>
```

4. Verify:
- Environment variables
- Database connectivity
- Startup commands
- Resource limits

---

## 3. How do you identify whether an issue is from the application, database, or network?

### Application Issue
- Check application logs
- Verify pod/container status

### Database Issue
- Verify DB connectivity
- Check DB logs

### Network Issue
- Check DNS resolution
- Verify firewall/security groups
- Test connectivity using ping/curl/telnet

---

## 4. Difference between git fetch, git pull, and git rebase

| Command | Purpose |
|---|---|
| git fetch | Downloads latest changes |
| git pull | Fetch + merge |
| git rebase | Reapplies commits on latest branch |

---

## 5. What happens internally when you run git revert?

- Git creates a new commit
- Opposite changes are applied
- Existing history is preserved

---

## 6. How is GitHub Actions integrated with AWS in your project?

GitHub Actions integrates with AWS using:
- IAM roles/users
- Access keys or OIDC
- AWS CLI configuration

Used for:
- EKS deployments
- Terraform automation
- Docker image push to ECR

---

## 7. How do you trigger workflows manually in GitHub Actions?

Using:

```yaml
workflow_dispatch:
```

Example:

```yaml
on:
  workflow_dispatch:
```

---

## 8. Explain ENTRYPOINT vs CMD in Docker with a real-time use case

### ENTRYPOINT
Defines the main executable.

### CMD
Provides default arguments.

Example:

```dockerfile
ENTRYPOINT ["python3"]
CMD ["app.py"]
```

---

## 9. When would you prefer Virtual Machines over containers?

Use VMs when:
- Strong isolation required
- Different operating systems needed
- Legacy applications
- High security requirements

Use containers when:
- Lightweight environments required
- Fast deployment needed
- Microservices architecture used

---

## 10. How do Prometheus and Grafana work together?

- Prometheus collects metrics
- Grafana visualizes metrics using dashboards
- Alertmanager sends notifications based on alerts

---

## 11. What is Terraform state file and how do you troubleshoot state mismatch issues?

Terraform state file stores infrastructure metadata and resource mappings.

### Troubleshooting

```bash
terraform refresh
terraform plan
terraform state list
```

---

## 12. Where do you store and manage secrets in your project?

- Kubernetes Secrets
- AWS Secrets Manager
- HashiCorp Vault

Secrets include:
- API keys
- Passwords
- Tokens

---

## 13. Explain how SonarQube is used in your CI/CD pipeline

SonarQube performs:
- Static code analysis
- Vulnerability scanning
- Bug detection
- Code quality checks

Integrated during build stage.

---

## 14. How do you debug when an application is not responding in production?

### Steps
1. Check monitoring dashboards
2. Verify pod health
3. Check logs
4. Verify DB connectivity
5. Check CPU and memory usage
6. Verify ingress/network configuration
7. Perform root cause analysis

---

## 15. Write a simple Terraform configuration to deploy a resource

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "server" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  tags = {
    Name = "DevOpsServer"
  }
}
```
````
