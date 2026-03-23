# 🚀 Xoriant Solutions Pvt Ltd – DevOps Interview Q&A (3–4 Years Experience)

---

## 1. How do you host Jenkins?

**Answer:**

* Installed Jenkins on:

  * EC2 (Linux server)
  * Docker container (recommended)

**Steps:**

* Install Java
* Install Jenkins via package or Docker
* Configure security + plugins

**Real Use:**

* Used Jenkins on EC2 with Nginx reverse proxy

---

## 2. What are environment variables?

**Answer:**

* Key-value pairs used to store configuration
* Example:

  * DB_URL
  * API_KEY

**Use:**

* Pass configs without hardcoding

---

## 3. What is parameter in Jenkins and where do you use them?

**Answer:**

* Parameters allow dynamic input at runtime

**Types:**

* String
* Choice
* Boolean

**Use:**

* Select environment (Dev/QA/Prod)
* Choose branch or version

---

## 4. What is Multi-branch pipeline and how you will use that?

**Answer:**

* Automatically creates pipelines for each branch

**Use:**

* Detects Jenkinsfile in each branch
* Useful for PR validation

---

## 5. What is cron job and how you will create that?

**Answer:**

* Scheduled task execution

**In Jenkins:**

```bash id="cr12"
H/5 * * * *
```

---

## 6. If you create the cron job and then next day I need new cron job whether you can create new or use existing one?

**Answer:**

* Modify existing cron if same job
* Create new job if requirement differs

---

## 7. Why we use WEBHOOK trigger?

**Answer:**

* To trigger pipeline instantly on code push
* Avoids polling

---

## 8. Which pipeline you used in Jenkins?

**Answer:**

* Declarative pipeline (mainly)
* Scripted for complex logic

---

## 9. How you can restrict user in GitHub?

**Answer:**

* Use:

  * Repo permissions (read/write/admin)
  * Branch protection rules
  * Teams in GitHub org

---

## 10. Why we can use PR in git?

**Answer:**

* Code review
* Maintain quality
* Prevent direct commits to main branch

---

## 11. What is namespace and how you can use that?

**Answer:**

* Logical separation in Kubernetes

**Use:**

* Dev, QA, Prod isolation

---

## 12. In pods, how many containers we can use? Why we can use multiple container?

**Answer:**

* Multiple containers allowed

**Why:**

* Sidecar pattern (logging, monitoring)
* Helper containers

---

## 13. How do you manage EKS cluster?

**Answer:**

* Using:

  * Terraform (infra setup)
  * kubectl (operations)
  * Helm (deployments)

---

## 14. What is local variables in terraform?

**Answer:**

* Used to simplify expressions

```hcl id="loc1"
locals {
  env = "dev"
}
```

---

## 15. What is modules in terraform? Are you used default module or custom?

**Answer:**

* Modules = reusable Terraform code

**Used:**

* Both:

  * Default modules (AWS)
  * Custom modules (company standard)

---

## 16. Python scripting: other than disk usage, list automation tasks

**Answer:**

* Log cleanup
* API calls
* Health checks
* File backup
* Email alerts
* Restart services

---

## 17. What is ENTRYPOINT vs CMD?

**Answer:**

**ENTRYPOINT:**

* Fixed command

**CMD:**

* Default argument (can override)

---

## 18. If a = 1,2,4 and b = 2,4 how you declare variables in Jenkins pipeline?

**Answer:**

* Declare in pipeline (not post build)

```groovy id="jen22"
environment {
  A = "1,2,4"
  B = "2,4"
}
```

---

## 19. How you use credentials in Jenkins? Where do you declare that?

**Answer:**

* Store in Jenkins Credentials Manager

**Use in pipeline:**

```groovy id="cred12"
withCredentials([string(credentialsId: 'token', variable: 'TOKEN')]) {
  sh 'echo $TOKEN'
}
```

---

## 20. Have you worked on request in python scripts?

**Answer:**

* Yes, using `requests` library

```python id="req1"
import requests
r = requests.get("https://api.example.com")
print(r.status_code)
```

---

## 21. What is your day-to-day activity and which tools you used mainly?

**Answer:**

* CI/CD pipeline maintenance
* Monitoring deployments
* Troubleshooting failures
* Infra provisioning

**Tools:**

* Jenkins, Docker, Kubernetes, Terraform, AWS, Git

---

## 22. What is RUN command in docker?

**Answer:**

* Executes commands during image build

```dockerfile id="dock1"
RUN apt-get update && apt-get install -y nginx
```

---

## 🔥 Final Tips (3–4 Years Experience)

* Focus on **real scenarios**
* Explain **tools + usage + problems solved**
* Show **debugging approach**

---

✅ Ready for Xoriant DevOps Interview
