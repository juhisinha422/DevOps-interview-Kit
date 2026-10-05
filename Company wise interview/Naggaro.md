# 📘 Real Interview Questions – DevOps Engineer (4 Years Experience) | Answers

---

## 1. Design a 3-tier architecture. Explain the workflow step by step.

**Architecture:**

* **Frontend Layer (UI)** → React/Angular (served via Nginx / CDN)
* **Application Layer (Backend)** → Java/Spring Boot (on EC2/K8s)
* **Database Layer** → MySQL / RDS

**Workflow:**

1. User sends request via browser
2. Request hits Load Balancer (ALB)
3. Routed to frontend (UI)
4. UI calls backend APIs
5. Backend processes logic
6. Queries database
7. Response flows back → User

---

## 2. How does CI/CD work? What commands do you run during a Maven build?

**CI/CD Flow:**

* Code commit → Trigger pipeline
* Build → Test → Package
* Docker build → Push → Deploy

**Maven Commands:**

```bash
mvn clean
mvn compile
mvn test
mvn package
mvn install
```

---

## 3. How does GitHub work? How do you clone, pull, and commit code?

**GitHub Workflow:**

* Remote repository hosting
* Version control using Git

**Commands:**

```bash
git clone <repo-url>
git pull origin main
git add .
git commit -m "message"
git push origin main
```

---

## 4. What is PDB (Pod Disruption Budget)?

Defines minimum number of pods that must be available during disruptions.
Used to prevent downtime during maintenance or scaling.

---

## 5. What are readiness and liveness probes?

What do you add in deployment.yaml for these? Share the syntax.

* **Liveness Probe** → checks if app is alive
* **Readiness Probe** → checks if app is ready to serve traffic

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

---

## 6. After building an application in CI/CD, what practical steps do you perform next?

* Build Docker image
* Tag image with version/commit ID
* Push to registry (DockerHub/ECR)
* Deploy to Kubernetes
* Verify deployment (health checks)

---

## 7. Write a deployment.yaml manifest.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: app
        image: myrepo/app:latest
        ports:
        - containerPort: 8080
```

---

## 8. Write a multi-stage Dockerfile.

```dockerfile
# Stage 1 - Build
FROM maven:3.8.6-openjdk-11 AS build
WORKDIR /app
COPY . .
RUN mvn clean package

# Stage 2 - Run
FROM openjdk:11-jre-slim
COPY --from=build /app/target/app.jar app.jar
ENTRYPOINT ["java","-jar","app.jar"]
```

---

## 9. If a pod crashes, what changes would you make in deployment.yaml?

* Add **liveness probe**
* Increase **resources (CPU/memory)**
* Add **restart policy (default Always)**
* Check env variables/config

---

## 10. What is the difference between ADD and COPY in Docker?

* **COPY** → simple file copy
* **ADD** → supports URL + auto-extract tar

👉 Best practice: Use COPY unless extra features needed

---

## 11. Write a CI/CD pipeline for a Java application deploying to Kubernetes for Dev and Prod environments.

```groovy
pipeline {
  agent any

  environment {
    IMAGE = "myrepo/app:${BUILD_NUMBER}"
  }

  stages {

    stage('Clone') {
      steps {
        git 'https://github.com/repo.git'
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
    }

    stage('Docker Build & Push') {
      steps {
        sh "docker build -t $IMAGE ."
        sh "docker push $IMAGE"
      }
    }

    stage('Deploy to Dev') {
      steps {
        sh "kubectl apply -f k8s/dev/"
      }
    }

    stage('Approval') {
      steps {
        input message: 'Approve for Prod?'
      }
    }

    stage('Deploy to Prod') {
      steps {
        sh "kubectl apply -f k8s/prod/"
      }
    }
  }
}
```

---

✅ Answers tailored for **real 4 years DevOps experience**
📘 Practical + interview-ready
🚀 Ready to copy as **README.md**
