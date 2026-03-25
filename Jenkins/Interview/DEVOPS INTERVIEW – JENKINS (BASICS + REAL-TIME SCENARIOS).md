# 📘 DEVOPS INTERVIEW – JENKINS (BASICS + REAL-TIME SCENARIOS)

---

## 🔹 1. JENKINS – BASICS

### In which language is Jenkins written?

Jenkins is written in **Java**.

---

### What are Jenkins project types?

* Freestyle Project
* Pipeline Project
* Multibranch Pipeline
* Folder Project

---

### What are Jenkins pipeline stages?

Common stages:

* Build
* Test
* Scan
* Deploy

---

### How do you start Jenkins manually?

```bash
sudo systemctl start jenkins
# or
java -jar jenkins.war
```

---

### Difference between a declarative and a scripted pipeline?

* **Declarative** → Simple, structured, easy to read (`pipeline {}`)
* **Scripted** → Flexible, uses Groovy scripting (`node {}`)

---

### Write a basic Jenkins pipeline

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

---

## 🔹 2. JENKINS – SCENARIO BASED

### 7) How do you design a Jenkins pipeline for dev, staging, and production?

* Use separate stages for each environment
* Use parameters or branch-based deployment
* Add approval before production

---

### 8) How do you manage environment-specific configurations in Jenkins?

* Use environment variables
* Config files per environment
* Credentials per environment

---

### 9) How do parameterized pipelines help in multi-environment deployments?

They allow selecting environment (dev/stage/prod) at runtime.

---

### 10) How do you separate deployment stages per environment?

Use conditional stages:

```groovy
when { expression { params.ENV == 'dev' } }
```

---

### 11) What happens if a Jenkins pipeline fails halfway?

Pipeline stops execution and marks build as failed.

---

### 12) How do you recover from a partially configured deployment?

* Rollback deployment
* Fix issue and redeploy
* Use versioned artifacts

---

### 13) What rollback strategies can be implemented in Jenkins?

* Previous stable build redeploy
* Blue-Green deployment
* Canary rollback

---

### 14) How can deployment state be tracked similar to Terraform state?

* Store deployment metadata in DB/S3
* Use artifact version tracking

---

### 15) How do retry mechanisms work in Jenkins pipelines?

Using retry block:

```groovy
retry(3) {
    sh 'deploy.sh'
}
```

---

### 16) How do you restart a Jenkins pipeline from a failed stage?

Use **Replay** or **Restart from Stage** option.

---

### 17) How can pipeline retries be automated?

Using retry() or post-failure triggers.

---

### 18) How do you enforce QA approval before production deployment?

Use input step:

```groovy
input "Approve deployment?"
```

---

### 19) How do you integrate Jenkins with Jira or ServiceNow?

Using plugins or API integration for ticket validation.

---

### 20) Why is ticket-based approval important in enterprises?

Ensures compliance, auditability, and controlled releases.

---

### 21) How do Jenkins pipelines securely access AWS credentials?

Using Jenkins Credentials + IAM roles.

---

### 22) How do you manage Docker Hub credentials in Jenkins?

Store in Jenkins credentials and use in pipeline.

---

### 23) Where should Jenkins credentials be stored and why?

Stored in **Jenkins Credentials Manager** for secure encryption.

---

### 24) How do you design automatic rollback for production failures?

Use health checks + auto-trigger rollback pipeline.

---

### 25) Why should build artifacts be stored after successful builds?

For reuse, rollback, and traceability.

---

### 26) How do Jenkins post actions help in rollback?

Post block can trigger rollback on failure:

```groovy
post {
  failure {
    sh 'rollback.sh'
  }
}
```

---

### 27) Why is unique Docker image tagging important?

Avoids overwriting images and ensures traceability.

---

### 28) What are different Docker image tagging strategies?

* latest
* version-based
* commit-hash

---

### 29) Why use Git commit hash instead of build number?

Ensures exact code traceability.

---

### 30) How does Jenkins fetch Git commit hash?

```groovy
env.GIT_COMMIT
```

---

### 31) How do you optimize a pipeline with long-running tests?

* Parallel execution
* Test splitting
* Caching

---

### 32) How do parallel stages improve Jenkins performance?

Run multiple tasks simultaneously → reduces build time.

---

### 33) What are risks of sequential pipeline execution?

* Slow execution
* Resource wastage
* Delayed feedback

---

### 34) What best practices are followed in enterprise Jenkins pipelines?

* Modular pipelines
* Secure credentials
* Automated testing
* Versioned artifacts

---

### 35) How do you ensure auditability and traceability in CI/CD?

* Logs
* Version control
* Artifact tracking

---

### 36) How do you balance automation and control in production pipelines?

* Automate builds/tests
* Manual approval for production
* Monitoring + rollback

---

✅ Ready to copy as README.md
Perfect for **4+ years DevOps interview preparation 🚀**
