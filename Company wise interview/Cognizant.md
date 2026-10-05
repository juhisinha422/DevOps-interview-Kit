# 📘 Cognizant Azure DevOps – Scenario & Concept-Based Questions (4 Years Experience)

---

## 1. CI/CD Pipeline Design

● How would you design a CI/CD pipeline in Azure DevOps for a microservices-based application?

**Answer:**

* CI Stage:

  * Trigger on PR & commits
  * Build each microservice independently
  * Run unit tests & SonarQube scan
  * Build Docker images
  * Push to ACR
  * Publish artifacts

* CD Stage:

  * Dev → QA → Prod environments
  * Deploy using Kubernetes (Helm/Manifests)
  * Use environment-specific configs
  * Approval before production

* Best Practices:

  * YAML templates for reuse
  * Parallel builds
  * Artifact versioning

---

## 2. YAML vs Classic Pipelines

● What are the advantages of YAML pipelines over Classic pipelines? When would you still use Classic?

**Answer:**

**YAML Advantages:**

* Stored in repo (version controlled)
* Reusable templates
* Infra-as-Code approach
* Easy to replicate across projects

**When to use Classic:**

* Beginner-friendly UI
* Quick PoC setups
* Legacy pipelines

---

## 3. Branching Strategy

● Which branching strategy have you used (GitFlow, trunk-based)? Why?

**Answer:**

* Used **GitFlow** in enterprise:

  * feature → develop → release → main
* Used **Trunk-based** for faster delivery:

  * Short-lived branches
  * Faster CI/CD

**Why:**

* GitFlow for structured releases
* Trunk-based for speed & automation

---

## 4. Pipeline Failure Debugging

● A build pipeline suddenly starts failing without code changes. How will you troubleshoot?

**Answer:**

* Check pipeline logs
* Verify agent health
* Check dependency/version changes
* Validate service connections
* Check expired credentials/tokens
* Review recent infra/environment changes

---

## 5. Self-hosted vs Microsoft-hosted Agents

● Difference between self-hosted and Microsoft-hosted agents in Azure DevOps? When to use each?

**Answer:**

**Microsoft-hosted:**

* Managed by Azure
* Quick setup
* Clean environment every run

**Self-hosted:**

* Custom tools installed
* Faster builds (no setup each time)
* More control

**Use:**

* Hosted → general workloads
* Self-hosted → custom tools, private network access

---

## 6. Secrets Management

● How do you securely store and use secrets in pipelines?

**Answer:**

* Azure Key Vault integration
* Variable groups with secrets
* Secret masking in logs
* Use service connections
* Avoid hardcoding credentials

---

## 7. Deployment Strategies

● Explain Blue-Green and Canary deployments. Which one have you used?

**Answer:**

**Blue-Green:**

* Two environments (Blue = old, Green = new)
* Switch traffic instantly

**Canary:**

* Release to small % users first
* Gradually increase traffic

**Used:**

* Canary in Kubernetes using ingress/traffic split

---

## 8. Infrastructure as Code

● How have you used Terraform or ARM templates with Azure DevOps?

**Answer:**

* Terraform used for provisioning:

  * VMs, AKS, Storage, Networking
* Integrated in pipeline:

  * terraform init
  * terraform plan
  * terraform apply

**State Management:**

* Remote backend (Azure Storage)
* State locking enabled

---

## 9. Environment Approvals

● How do you implement manual approvals before production deployment?

**Answer:**

* Use Azure DevOps Environments
* Configure pre-deployment approvals
* Assign approvers (e.g., manager/lead)
* Pipeline pauses until approval

---

## 10. Artifact Management

● What are artifacts? How do you version and store?

**Answer:**

* Artifacts = build outputs (binaries, packages, images)
* Stored in:

  * Azure Artifacts
  * ACR (for Docker images)

**Versioning:**

* Use build ID / semantic versioning
* Example: v1.0.0, build-123

---

## 11. Production Issue

● Deployment succeeded but application is not working in production. What will you do?

**Answer:**

* Check application logs
* Validate configs (env variables)
* Check service health endpoints
* Verify DB/API connectivity
* Rollback to previous stable version
* Monitor metrics (CPU, memory)

---

## 12. Pipeline Optimization

● Your pipeline takes 30 minutes. How will you reduce execution time?

**Answer:**

* Enable parallel jobs
* Use caching (dependencies, Docker layers)
* Incremental builds
* Use self-hosted agents
* Skip unnecessary steps
* Optimize test execution

---

## 13. Multi-Environment Deployment

● How do you manage Dev, QA, and Prod environments in pipeline?

**Answer:**

* Use multi-stage YAML pipeline
* Separate variable groups per environment
* Use environment-specific configs
* Approval gates for higher environments

---

## 14. Access Control

● How do you manage role-based access in Azure DevOps?

**Answer:**

* Use RBAC:

  * Project-level permissions
  * Repo-level access
* Assign roles:

  * Reader, Contributor, Admin
* Use Azure AD groups
* Restrict pipeline & repo access

---

## 15. Write a simple YAML pipeline to build and deploy a .NET app

**Answer:**

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'

stages:

- stage: Build
  jobs:
  - job: BuildJob
    steps:
    - task: UseDotNet@2
      inputs:
        packageType: 'sdk'
        version: '6.x'

    - script: dotnet restore
    - script: dotnet build --configuration $(buildConfiguration)
    - script: dotnet publish -c $(buildConfiguration) -o $(Build.ArtifactStagingDirectory)

    - task: PublishBuildArtifacts@1
      inputs:
        pathToPublish: $(Build.ArtifactStagingDirectory)
        artifactName: drop

- stage: Deploy
  dependsOn: Build
  jobs:
  - job: DeployJob
    steps:
    - download: current
      artifact: drop

    - script: echo "Deploying application..."
```

---

✅ Ready for interviews
✅ Covers real-world scenarios
✅ Matches 4 years experience expectations

---

**Tip:** Practice explaining each answer with a real project example — that’s what interviewers expect.
