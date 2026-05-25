# DevOps / CI-CD Interview Questions and Answers

## How do you handle failed deployments in Jenkins?

### Answer:

When a deployment fails in Jenkins:

1. Check Jenkins console logs.
2. Identify whether failure is in build, test, Docker image creation, or deployment stage.
3. Verify application logs and Kubernetes pod logs.
4. Roll back to previous stable version if required.
5. Fix the issue and re-trigger pipeline.
6. Add proper alerts and validations to avoid future failures.

Common reasons:

* Failed unit tests
* Wrong environment variables
* Kubernetes image pull issues
* Resource limitations
* Application startup failures

---

## Have you worked on multibranch pipelines?

### Answer:

Yes, I have worked on multibranch pipelines in Jenkins/GitLab CI.

Multibranch pipeline automatically detects branches and creates separate pipelines for each branch.

Benefits:

* Separate pipeline execution for feature, develop, and main branches
* Easier pull request validation
* Better automation
* Faster development workflow

Example branches:

* feature/*
* develop
* release
* main/master

---

## How do you implement rollback strategies?

### Answer:

Rollback strategy is used to restore previous stable application version if deployment fails.

Methods used:

* Kubernetes rollout undo
* Helm rollback
* Blue-Green deployment rollback
* Canary rollback

Kubernetes rollback command:

```bash
kubectl rollout undo deployment app-name
```

Helm rollback command:

```bash
helm rollback release-name revision-number
```

Best practices:

* Keep previous stable image versions
* Use health checks
* Monitor deployments continuously

---

## Difference between Blue-Green and Canary deployments?

### Answer:

Blue-Green Deployment:

* Two environments are maintained.
* Blue = old version
* Green = new version
* Traffic switches completely after testing.
* Faster rollback.

Canary Deployment:

* New version is released to small percentage of users.
* Traffic is gradually increased.
* Lower risk deployment.
* Better for testing production behavior.

Difference:

* Blue-Green switches all traffic at once.
* Canary releases traffic gradually.

---

## How do you secure CI/CD pipelines?

### Answer:

CI/CD pipelines are secured using:

* RBAC access control
* Secret management
* Least privilege access
* Secure credentials storage
* Image scanning
* Dependency scanning
* MFA for repositories
* Approval process for production deployment

Tools used:

* SonarQube
* Trivy
* Vault
* Jenkins credentials manager

---

## How do you handle secrets in Jenkins?

### Answer:

Secrets in Jenkins are handled using Jenkins Credentials Manager.

Types of secrets:

* Username/password
* SSH keys
* Tokens
* Secret text

Secrets should never be hardcoded.

Example usage in Jenkins pipeline:

```groovy
withCredentials([string(credentialsId: 'token-id', variable: 'TOKEN')]) {
    sh 'echo Using token'
}
```

Best practices:

* Use encrypted secrets
* Restrict access
* Rotate credentials regularly
* Integrate with Vault if possible

---

## Explain your branching strategy.

### Answer:

Common branching strategy followed:

* main/master → Production code
* develop → Integration branch
* feature/* → New feature development
* release/* → Release preparation
* hotfix/* → Production fixes

Workflow:

1. Developer creates feature branch.
2. Code is committed and pushed.
3. Pull request is raised.
4. Code review happens.
5. CI pipeline validates code.
6. Merge to develop/main after approval.

Benefits:

* Better collaboration
* Controlled releases
* Easy rollback
* Cleaner code management
