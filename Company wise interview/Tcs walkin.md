# TCS Walk-in Interview Questions & Detailed Answers (4 Years DevOps Engineer)

---

# 1. Tell me about yourself?

I am a DevOps Engineer with around 4 years of experience in cloud infrastructure, CI/CD automation, Kubernetes, Docker, Terraform, AWS, and monitoring tools. In my current role, I mainly work on automating infrastructure provisioning, managing Kubernetes clusters, creating Jenkins pipelines, troubleshooting production issues, and implementing monitoring solutions using Prometheus and Grafana.

I have experience working with microservices-based applications where applications are containerized using Docker and deployed on Kubernetes. I also work on Infrastructure as Code using Terraform, where we manage AWS resources such as EC2, VPC, Load Balancers, IAM, and EKS clusters.

Apart from infrastructure automation, I am also involved in production support activities including incident handling, RCA preparation, log analysis, deployment troubleshooting, security hardening, and cost optimization.

---

# 2. Day-to-Day activities as DevOps Engineer?

My day-to-day activities include:

* Monitoring production environments using Grafana and Prometheus
* Managing CI/CD pipelines in Jenkins/GitHub Actions
* Deploying applications to Kubernetes clusters
* Troubleshooting pod failures and deployment issues
* Writing and maintaining Terraform modules
* Managing AWS infrastructure
* Handling incidents and preparing RCA reports
* Monitoring logs using ELK/Loki
* Coordinating with development and QA teams
* Managing Docker images and container security
* Performing backup, scaling, and optimization activities

I also work on automation scripts using Shell/Python to reduce manual operational tasks.

---

# 3. Write a Dockerfile?

Example Dockerfile for Java application:

```dockerfile
FROM openjdk:17-jdk-alpine

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

Explanation:

* `FROM` → Base image
* `WORKDIR` → Sets working directory
* `COPY` → Copies application files
* `EXPOSE` → Exposes container port
* `ENTRYPOINT` → Starts application

For production, I also prefer:

* Multi-stage builds
* Non-root users
* Minimal base images

to improve security and reduce image size.

---

# 4. Difference between CMD and ENTRYPOINT?

| CMD                        | ENTRYPOINT              |
| -------------------------- | ----------------------- |
| Provides default arguments | Defines main executable |
| Can be overridden easily   | Runs as fixed command   |
| Optional                   | Usually mandatory       |

Example:

```dockerfile
ENTRYPOINT ["java","-jar"]
CMD ["app.jar"]
```

Execution becomes:

```bash
java -jar app.jar
```

If user passes another argument:

```bash
docker run image test.jar
```

Result:

```bash
java -jar test.jar
```

ENTRYPOINT is generally used for main application execution, while CMD provides default parameters.

---

# 5. Write a Linux command to move a file from var folder to www folder?

```bash
mv /var/file.txt /www/
```

If moving directories:

```bash
mv /var/mydir /www/
```

The `mv` command is used to:

* Move files/directories
* Rename files/directories

---

# 6. Difference between ALB and NLB?

| ALB                        | NLB                          |
| -------------------------- | ---------------------------- |
| Application Load Balancer  | Network Load Balancer        |
| Layer 7                    | Layer 4                      |
| HTTP/HTTPS traffic         | TCP/UDP traffic              |
| Path-based routing         | High-performance low latency |
| Supports host/path routing | Static IP support            |

ALB is mainly used for:

* Web applications
* Microservices
* Kubernetes ingress

NLB is used for:

* High throughput
* Low latency applications
* TCP-based workloads

---

# 7. Difference between Security Group and NACL?

| Security Group                       | NACL                                      |
| ------------------------------------ | ----------------------------------------- |
| Stateful                             | Stateless                                 |
| Instance level                       | Subnet level                              |
| Allow rules only                     | Allow and Deny rules                      |
| Return traffic automatically allowed | Return traffic must be explicitly allowed |

Security Groups act as virtual firewalls for EC2 instances, while NACL acts at subnet level for additional network security.

---

# 8. What are the pipelines available in Jenkins?

There are mainly two types of pipelines in Jenkins:

## Scripted Pipeline

Written using Groovy scripting with more flexibility.

Example:

```groovy
node {
    stage('Build') {
        sh 'mvn clean install'
    }
}
```

---

## Declarative Pipeline

Structured and easier to maintain.

Example:

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}
```

Declarative pipeline is mostly preferred in production because it is cleaner and easier to standardize.

---

# 9. Write a Declarative Pipeline?

Example Jenkins Declarative Pipeline:

```groovy
pipeline {
    agent any

    environment {
        APP_NAME = "demo-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/sample/repo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t demo-app .'
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }

    post {
        success {
            echo 'Pipeline Success'
        }

        failure {
            echo 'Pipeline Failed'
        }
    }
}
```

This pipeline performs:

* Code checkout
* Build
* Docker image creation
* Kubernetes deployment

---

# 10. Explain Kubernetes Architecture?

Kubernetes architecture mainly consists of:

## Control Plane Components

### API Server

Main entry point for all Kubernetes operations.

### etcd

Distributed key-value store storing cluster state.

### Scheduler

Assigns pods to nodes.

### Controller Manager

Maintains desired cluster state.

---

## Worker Node Components

### Kubelet

Communicates with API server and manages pods.

### Container Runtime

Runs containers (containerd/docker).

### kube-proxy

Handles service networking.

---

Architecture Flow:

```text
kubectl → API Server → etcd
                    ↓
            Scheduler selects node
                    ↓
                Kubelet creates pod
```

---

# 11. What is CrashLoopBackOff error?

CrashLoopBackOff means container is repeatedly crashing and Kubernetes is continuously trying to restart it.

Common reasons:

* Application crash
* Wrong configuration
* Missing environment variables
* Database connection failure
* Resource limits exceeded

Troubleshooting steps:

```bash
kubectl logs <pod-name>
kubectl describe pod <pod-name>
```

Also verify:

* Secrets
* ConfigMaps
* Health checks
* Resource limits

---

# 12. What is ImagePullError / ImagePullBackOff?

ImagePullBackOff occurs when Kubernetes cannot pull Docker image from registry.

Common causes:

* Wrong image name/tag
* Registry authentication failure
* Image not available
* Network issue
* DockerHub rate limit

Check:

```bash
kubectl describe pod <pod-name>
```

Resolution:

* Correct image tag
* Configure imagePullSecrets
* Verify registry access

---

# 13. Write a Deployment file in YAML?

Example Kubernetes Deployment YAML:

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
            memory: "128Mi"
            cpu: "100m"

          limits:
            memory: "256Mi"
            cpu: "200m"
```

This deployment:

* Creates 3 replicas
* Runs nginx container
* Exposes port 80
* Defines resource requests and limits

Apply using:

```bash
kubectl apply -f deployment.yaml
```

---
