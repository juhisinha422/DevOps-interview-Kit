# Wipro DevOps/SRE Interview Questions & Detailed Answers (4+ Years Experience)

---

# Kubernetes

## Explain Blue-Green deployment strategy and how to achieve zero downtime in Kubernetes

Blue-Green deployment is a release strategy where we maintain two identical environments:

* Blue → Current production environment
* Green → New version environment

Initially, all production traffic goes to Blue environment. The new application version is deployed in Green environment and tested completely. Once validation is successful, traffic is switched from Blue to Green with minimal interruption.

In Kubernetes, this is achieved using:

* Separate deployments
* Kubernetes services
* Ingress/load balancer switching

Example:

```text
User → Service → Blue Pods
```

After deployment:

```text
User → Service → Green Pods
```

Steps:

1. Deploy new version as Green deployment
2. Verify health checks
3. Run smoke testing
4. Switch service selector to Green
5. Monitor application
6. Rollback quickly if issues occur

Advantages:

* Near zero downtime
* Fast rollback
* Safer deployments

Challenges:

* Double infrastructure cost
* Database schema compatibility

For true zero downtime:

* Readiness probes must be configured
* Graceful shutdown should exist
* Session persistence handled properly
* Database migrations should be backward compatible

---

## What are taints and tolerations, and why are they important?

Taints and tolerations control pod scheduling on Kubernetes nodes.

### Taints

Applied on nodes to repel pods.

Example:

```bash
kubectl taint nodes node1 env=prod:NoSchedule
```

This prevents pods from scheduling unless they tolerate the taint.

### Tolerations

Applied on pods to allow scheduling onto tainted nodes.

Example:

```yaml
tolerations:
- key: "env"
  operator: "Equal"
  value: "prod"
  effect: "NoSchedule"
```

Importance:

* Dedicated nodes for workloads
* Isolation of production workloads
* GPU node management
* Preventing system pods on worker nodes
* Improved cluster resource control

---

## What is the role of ConfigMaps and Secrets in Kubernetes?

### ConfigMaps

Used to store non-sensitive configuration data such as:

* Application properties
* URLs
* Environment variables
* Feature flags

Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
```

---

### Secrets

Used for sensitive information such as:

* Passwords
* API keys
* Tokens
* Certificates

Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: YWRtaW4=
```

Secrets are base64 encoded and can integrate with external secret managers.

Benefits:

* Separation of config from application
* Better security
* Easier environment management

---

# CI/CD & DevSecOps

## How do you integrate code quality and security tools in CI/CD pipelines?

In CI/CD pipelines, security and quality checks are integrated at multiple stages.

Typical pipeline flow:

```text
Code Commit → Build → Unit Test → SonarQube → SAST → Dependency Scan → Docker Build → Image Scan → Deploy
```

Tools used:

* SonarQube → Code quality
* Trivy/Anchore → Container scanning
* OWASP Dependency Check → Dependency vulnerabilities
* Snyk → Security scanning
* Checkov/Tfsec → Terraform scanning

Best practices:

* Shift-left security
* Fail pipeline on critical vulnerabilities
* Automate quality gates
* Integrate reports into dashboards

---

## How did you integrate SonarQube with Jenkins pipelines?

I integrated SonarQube in Jenkins using SonarScanner plugin.

Pipeline example:

```groovy
stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('SonarQube') {
            sh 'mvn sonar:sonar'
        }
    }
}
```

Then quality gate validation:

```groovy
waitForQualityGate abortPipeline: true
```

This ensures:

* Code smells detected
* Bugs identified
* Security vulnerabilities scanned
* Coverage validation

Pipeline fails automatically if quality gate conditions are not met.

---

## How do you handle failures detected during automated scans?

When vulnerabilities or quality issues are detected:

1. Analyze severity
2. Block deployment if critical
3. Notify development team
4. Create remediation ticket
5. Fix issue and rerun pipeline

For critical security findings:

* Deployment halted immediately
* RCA initiated if production affected
* Temporary mitigation applied if needed

Best practice:

* Define severity thresholds
* Automate notifications
* Integrate with Jira/Slack

---

## What is artifact management in CI/CD?

Artifact management refers to storing and managing build outputs such as:

* JAR files
* WAR files
* Docker images
* Helm charts

Tools:

* Nexus
* JFrog Artifactory
* Docker Registry
* ECR

Benefits:

* Version control for artifacts
* Reproducible deployments
* Centralized storage
* Rollback capability

Example:

```text
Build → Store Artifact → Deploy Artifact
```

---

## How do you ensure reproducible builds in DevOps workflows?

Reproducible builds ensure same source code always produces same artifact.

Approaches:

* Pin dependency versions
* Use immutable Docker images
* Lock package versions
* Use Infrastructure as Code
* Standardize build environments
* Use CI/CD automation

Example:

```dockerfile
FROM python:3.11.4-alpine
```

instead of:

```dockerfile
FROM python:latest
```

Benefits:

* Predictable deployments
* Easier debugging
* Consistent testing

---

# Docker

## How do you tag Docker images for different environments?

Docker image tagging strategy generally includes:

* Application version
* Environment
* Build number

Examples:

```bash
docker build -t app:1.0-dev .
docker build -t app:1.0-qa .
docker build -t app:1.0-prod .
```

Best practices:

* Semantic versioning
* Immutable tags
* Avoid using only `latest`
* Include Git commit hash if needed

Example:

```bash
app:v1.2.3-commitid
```

---

# Terraform

## Have you collaborated on Terraform codebases with multiple team members?

Yes, Terraform collaboration is very common in enterprise projects.

Best practices followed:

* Remote backend in S3
* DynamoDB locking
* Git branching strategy
* Pull request reviews
* Modular Terraform code
* Environment segregation

Workflow:

```text
Developer → Git PR → Review → Merge → CI/CD → Terraform Apply
```

This avoids:

* State corruption
* Concurrent modification
* Untracked infra changes

---

## How did you manage Terraform state and locking?

Terraform state was stored remotely in AWS S3.

Example backend:

```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
  }
}
```

### S3 used for:

* Centralized state storage
* Versioning
* Backup

### DynamoDB used for:

* State locking
* Prevent concurrent terraform apply

This prevents state corruption when multiple engineers work simultaneously.

---

## Create Terraform configuration for Azure SQL Database with cross-region resiliency

Example:

```hcl
resource "azurerm_sql_server" "primary" {
  name                         = "primarysqlserver"
  resource_group_name          = "prod-rg"
  location                     = "East US"
  version                      = "12.0"
  administrator_login          = "adminuser"
  administrator_login_password = "Password123!"
}

resource "azurerm_sql_database" "db" {
  name                = "prod-db"
  resource_group_name = "prod-rg"
  location            = "East US"
  server_name         = azurerm_sql_server.primary.name
}

resource "azurerm_sql_failover_group" "failover" {
  name      = "sql-failover-group"
  server_name = azurerm_sql_server.primary.name

  partner_servers {
    id = azurerm_sql_server.secondary.id
  }
}
```

This provides:

* Cross-region failover
* High availability
* Disaster recovery

---

# Git & Collaboration

## How do you revert a buggy commit in a shared branch?

For shared branches, safest approach is:

```bash
git revert <commit-id>
```

This creates a new commit that reverses previous changes without rewriting Git history.

Avoid force push on shared branches unless absolutely necessary.

---

## How do you respond when pull request reviews request changes?

I carefully review comments, understand concerns, and update code accordingly.

Best practices:

* Never take feedback personally
* Clarify doubts politely
* Implement requested improvements
* Re-test changes
* Respond professionally

PR reviews improve:

* Code quality
* Security
* Maintainability
* Team collaboration

---

# Problem Solving & and Programming

## What is recursion base case and why is it important?

Base case is the stopping condition in recursion.

Without base case:

* Infinite recursion occurs
* Stack overflow happens

Example:

```python
def factorial(n):
    if n == 1:
        return 1
    return n * factorial(n-1)
```

Here:

```python
if n == 1
```

is the base case.

---

## Palindrome validation considering only alphanumeric characters and ignoring case

Example Python solution:

```python
import re

def is_palindrome(s):
    s = re.sub(r'[^a-zA-Z0-9]', '', s).lower()
    return s == s[::-1]

print(is_palindrome("A man, a plan, a canal: Panama"))
```

This:

* Removes special characters
* Ignores case
* Checks palindrome

---

## Standardizing different user inputs into a common format

Example:

* Convert to lowercase
* Trim spaces
* Remove special characters
* Apply formatting rules

Python example:

```python
name = input().strip().lower()
```

Used in:

* Data normalization
* User registration systems
* API validation

---

## Rewrite an informal/slang paragraph into professional English

Example:

Informal:

```text
Hey dude, app is kinda broken and stuff isn't working.
```

Professional:

```text
The application is currently experiencing issues, and certain functionalities are not operating as expected.
```

---

# Scenario-Based Questions

## Explain a challenging technical issue you faced and how you solved it

One critical production issue I handled was repeated pod crashes after deployment in Kubernetes.

Symptoms:

* High application downtime
* CrashLoopBackOff state
* Increased user errors

Troubleshooting steps:

1. Checked pod logs
2. Verified events
3. Compared old vs new deployment
4. Identified memory leak issue
5. Increased memory limits temporarily
6. Rolled back faulty deployment
7. Coordinated with developers for fix

After resolution:

* Added better monitoring
* Implemented memory alerts
* Improved pre-production testing

---

## Brief introduction and project experience discussion

I explained my experience working on cloud-native applications using:

* AWS
* Kubernetes
* Docker
* Terraform
* Jenkins
* GitHub Actions
* Prometheus/Grafana
* ELK/Loki

My responsibilities included:

* Infrastructure provisioning
* CI/CD automation
* Kubernetes management
* Production support
* Monitoring
* Security hardening
* Incident response
* Cost optimization

I also discussed architecture design, deployment strategies, scalability, high availability, and observability practices followed in projects.

---
