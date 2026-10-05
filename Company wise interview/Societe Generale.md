# DevOps Interview Questions & Answers (4+ Years Experience)

## 🔹 Git concepts – Git pull vs Git fetch vs Git merge

**git fetch**
- Downloads changes from the remote repository but does not merge them into the current branch.

**git merge**
- Combines changes from one branch into another branch.

**git pull**
- Combination of `git fetch` + `git merge`
- Fetches changes from remote and automatically merges them into the current branch.

Best practice in teams is to **use git fetch first to review changes before merging**.

---

## 🔹 Configuring Maven in Jenkins

1. Install **Maven Integration Plugin** in Jenkins.
2. Go to **Manage Jenkins → Global Tool Configuration**.
3. Add Maven and provide Maven home path.
4. Use it in pipeline.

Example:

```groovy
stage('Build') {
    steps {
        sh 'mvn clean package'
    }
}
```

This command compiles the code and generates artifacts like **WAR/JAR files**.

---

## 🔹 Configuring SonarQube in Jenkins pipelines

Steps:

1. Install **SonarQube Scanner plugin**.
2. Configure SonarQube server in **Manage Jenkins → Configure System**.
3. Add authentication token.
4. Use it in pipeline.

Example:

```groovy
stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('SonarServer') {
            sh 'mvn sonar:sonar'
        }
    }
}
```

---

## 🔹 Writing Jenkins pipeline stages (Build, Test, Deploy)

Example pipeline:

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }

    }
}
```

---

## 🔹 Jenkins parameters and credentials management

### Parameters

Used to pass values during runtime.

Example:

```groovy
parameters {
    string(name: 'ENV', defaultValue: 'dev')
}
```

### Credentials

Managed securely in:

```
Manage Jenkins → Credentials
```

Usage example:

```groovy
withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
    sh 'docker login'
}
```

---

## 🔹 Handling pipeline failures due to SonarQube quality gates (code coverage < 80%)

Use **Quality Gate check** in Jenkins.

Example:

```groovy
waitForQualityGate abortPipeline: true
```

If the project fails quality conditions like **coverage < 80%**, the pipeline automatically fails and stops deployment.

---

## 🔹 WAR file generation and where artifacts are stored

WAR files are generated using Maven.

Command:

```
mvn clean package
```

Generated location:

```
target/application.war
```

In Jenkins workspace:

```
/var/lib/jenkins/workspace/<job-name>
```

Artifacts can also be archived using **Archive Artifacts**.

---

## 🔹 Jenkins upgrade process

Steps:

1. Backup Jenkins data.

```
/var/lib/jenkins
```

2. Check plugin compatibility.
3. Upgrade Jenkins.

Amazon Linux:

```
sudo yum update jenkins
```

Ubuntu:

```
sudo apt update
sudo apt upgrade jenkins
```

4. Restart Jenkins.

```
sudo systemctl restart jenkins
```

---

## 🔹 Custom Quality Gates in SonarQube

Quality gates enforce code quality rules such as:

- Code coverage ≥ 80%
- No critical vulnerabilities
- Limited code smells

Steps:

1. Go to **SonarQube → Quality Gates**
2. Create a new quality gate
3. Define conditions
4. Assign it to the project

---

## 🔹 Deployment stage challenges in CI/CD pipelines

Common challenges include:

- Environment mismatch
- Dependency issues
- Database migrations
- Secrets management
- Deployment failures
- Rollback management

Solutions include:

- Blue-green deployment
- Canary deployment
- Automated rollback
- Environment parity

---

## 🔹 Write multi-stage Docker files

Example:

```dockerfile
# Build stage
FROM maven:3.8.6-openjdk-11 AS build
WORKDIR /app
COPY . .
RUN mvn clean package

# Runtime stage
FROM tomcat:9
COPY --from=build /app/target/app.war /usr/local/tomcat/webapps/
```

Benefits:

- Smaller image size
- Improved security
- Faster builds

---

## 🔹 Building Docker images and pushing them to registries

Build image:

```
docker build -t myapp:v1 .
```

Login to registry:

```
docker login
```

Push image:

```
docker push myrepo/myapp:v1
```

Images can be pushed to registries like **Docker Hub, AWS ECR, or GitHub Container Registry**.

---

## 🔹 Troubleshooting stopped containers

Steps:

Check container status:

```
docker ps -a
```

Check logs:

```
docker logs <container_id>
```

Inspect container configuration:

```
docker inspect <container_id>
```

Common causes:

- Application crash
- Incorrect environment variables
- Port conflicts

---

## 🔹 Changing Docker container port mappings

Docker does not allow modifying ports for running containers.

Steps:

Stop container:

```
docker stop container_id
```

Remove container:

```
docker rm container_id
```

Run with new port:

```
docker run -p 8081:8080 myimage
```

---

## 🔹 Kubernetes services – LoadBalancer vs Ingress

### LoadBalancer

- Creates external load balancer from cloud provider
- Each service gets its own public IP
- Simple but expensive

### Ingress

- Single entry point for multiple services
- Supports **host-based and path-based routing**
- Cost efficient and commonly used in production

---

## 🔹 Terraform state locking configuration

State locking prevents multiple users from modifying infrastructure at the same time.

Example using **S3 backend and DynamoDB**:

```hcl
terraform {
 backend "s3" {
   bucket = "terraform-state"
   key    = "dev/terraform.tfstate"
   region = "us-east-1"
   dynamodb_table = "terraform-lock"
 }
}
```

DynamoDB ensures **safe locking during Terraform execution**.

---

## 🔹 Converting shell scripts to Ansible playbooks

Shell script example:

```
yum install nginx -y
systemctl start nginx
```

Ansible equivalent:

```yaml
- hosts: servers
  become: yes

  tasks:

  - name: Install nginx
    yum:
      name: nginx
      state: present

  - name: Start nginx
    service:
      name: nginx
      state: started
```

Advantages:

- Idempotent
- Easier configuration management
- Reusable automation

---

## 🔹 Monitoring Kubernetes clusters

Common monitoring tools:

- **Prometheus** – Metrics collection
- **Grafana** – Dashboards
- **ELK / EFK stack** – Logging
- **Kubernetes Dashboard**
- **kubectl top nodes/pods**

Example command:

```
kubectl top pods
```

---

## 🔹 Shell scripting task – find files larger than 200 MB

Command:

```
find / -type f -size +200M
```

To display file sizes:

```
find / -type f -size +200M -exec ls -lh {} \;
```

---

## 🔹 Checking Jenkins system logs

Main Jenkins log:

```
/var/log/jenkins/jenkins.log
```

Using systemd:

```
journalctl -u jenkins
```

Using Jenkins UI:

```
Manage Jenkins → System Log
```

These logs help diagnose **pipeline failures, plugin errors, and system issues**.
