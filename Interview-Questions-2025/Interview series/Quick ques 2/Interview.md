# AWS DevOps Engineer Interview Questions and Answers (4+ Years Experience)

## 1. What is Jenkins Shared Libraries?

Jenkins Shared Libraries are reusable Groovy scripts that allow organizations to standardize and centralize common CI/CD pipeline logic. In large enterprises, multiple projects often require similar pipeline stages such as code checkout, build, testing, security scanning, Docker image creation, artifact publishing, and deployment. Instead of writing the same code repeatedly in every Jenkinsfile, these common functions are stored in a separate Git repository called a Shared Library and are referenced by Jenkins pipelines when needed.

In my current project, we use Jenkins Shared Libraries extensively to enforce CI/CD standards across teams. For example, we have reusable functions for SonarQube scans, Docker image builds, Helm deployments, and Slack notifications. This approach improves maintainability because any pipeline enhancement can be implemented in one place and automatically becomes available to all projects. It also reduces code duplication, ensures consistency, and accelerates pipeline development.

---

## 2. What Branching Strategy Do You Follow?

In our project, we primarily follow a Git Flow-based branching strategy with slight modifications depending on release requirements. The main branch represents production-ready code, while the develop branch serves as the integration branch where all completed features are merged. Developers create feature branches from the develop branch and work independently on assigned tasks.

Once development is completed, a Pull Request is raised and reviewed by peers before merging into the develop branch. For major releases, a release branch is created to perform integration testing, UAT validation, and bug fixes. After successful validation, the release branch is merged into the main branch and tagged with a release version. For urgent production issues, hotfix branches are created directly from the main branch, tested, and merged back into both main and develop branches.

This strategy provides better control over releases, improves collaboration among development teams, and minimizes the risk of unstable code reaching production.

---

## 3. How Do You Resolve Git Merge Conflicts?

Git merge conflicts occur when two developers modify the same portion of a file and Git cannot automatically determine which change should be retained. Whenever a conflict occurs, I first pull the latest changes from the target branch and attempt the merge locally. Git identifies the conflicting files and marks the conflict sections using special conflict indicators.

I carefully analyze both versions of the code to understand the purpose of each change. If necessary, I coordinate with the respective developers to understand the business logic behind their modifications. After deciding the correct implementation, I manually edit the file, remove conflict markers, and retain the appropriate code. Once the conflict is resolved, I perform a local build and execute tests to ensure functionality is not impacted. Finally, I commit the resolved changes and push them to the remote repository.

In production projects, communication is critical during conflict resolution because incorrect merges can introduce defects or break application functionality.

---

## 4. Explain Jenkins Pipeline Script Flow

A Jenkins Pipeline is an automated workflow that defines the complete CI/CD process from source code commit to deployment. In our organization, we use Declarative Pipelines because they provide better readability and maintainability.

The pipeline starts with the checkout stage where Jenkins retrieves the latest source code from Git repositories such as GitHub, GitLab, or Bitbucket. Once the code is available, the build stage compiles the application using tools like Maven, Gradle, or npm depending on the technology stack. After successful compilation, automated unit tests are executed to validate application functionality and ensure code quality.

The next stage performs static code analysis using SonarQube, which identifies code smells, bugs, vulnerabilities, and maintainability issues. Security scanning tools such as Trivy, Snyk, or OWASP Dependency Check are then executed to identify known vulnerabilities in dependencies and container images. If all quality gates pass, the pipeline packages the application and publishes artifacts to repositories such as JFrog Artifactory or Nexus.

For containerized applications, Jenkins builds Docker images, tags them with version numbers, and pushes them to a container registry such as Amazon ECR. The deployment stage then uses Kubernetes manifests, Helm charts, or Terraform configurations to deploy the application into development, testing, staging, or production environments. Finally, notifications are sent through email, Slack, or Microsoft Teams to inform stakeholders about the deployment status.

This end-to-end automation reduces manual effort, increases deployment frequency, and improves software reliability.

---

## 5. What Security Tools Do You Use?

Security is integrated into our DevOps process through a DevSecOps approach. We use SonarQube for Static Application Security Testing (SAST), which helps identify coding vulnerabilities and quality issues early in the development cycle. For dependency vulnerability scanning, we use tools such as Snyk and OWASP Dependency Check to identify insecure libraries used within the application.

For container security, Trivy is integrated into Jenkins pipelines to scan Docker images for vulnerabilities before deployment. Within AWS, we use services such as IAM for access control, CloudTrail for auditing API activities, AWS Config for compliance monitoring, GuardDuty for threat detection, Security Hub for centralized security visibility, and Inspector for vulnerability assessments.

By integrating security checks into every stage of the CI/CD pipeline, vulnerabilities are identified and remediated before reaching production environments.

---

## 6. Where Do You Store Artifacts and How Do You Maintain Versions?

In our environment, build artifacts are stored in centralized artifact repositories such as JFrog Artifactory and Nexus Repository Manager. These repositories act as secure storage locations for application binaries, Docker images, Helm charts, and other deployment packages.

Version management follows Semantic Versioning principles using a Major.Minor.Patch format. For example, version 1.0.0 represents the initial release, version 1.1.0 introduces new features, and version 1.1.1 includes bug fixes. Jenkins automatically generates build numbers and tags artifacts accordingly. Docker images are similarly versioned using application versions, build IDs, or Git commit hashes. This versioning strategy enables traceability, rollback capabilities, and effective release management.

---

## 7. Do You Have Any Idea About JFrog?

JFrog Artifactory is a universal artifact repository manager used to store, manage, secure, and distribute software artifacts throughout the development lifecycle. It supports multiple package formats including Maven, Gradle, npm, Docker, Helm, PyPI, and generic binaries.

In our CI/CD process, after a successful build, Jenkins uploads generated artifacts to JFrog Artifactory. During deployments, deployment tools retrieve the required artifact versions directly from Artifactory. This ensures consistency between environments because the exact same artifact tested in lower environments is deployed to production.

JFrog also provides access control, repository replication, build promotion, metadata tracking, and integration with security scanning tools, making it a critical component of enterprise DevOps ecosystems.

---

## 8. What Do You Deploy In Your Project?

In our current project, we primarily deploy microservices-based applications running on Kubernetes clusters hosted in AWS EKS. The deployment package includes Docker container images, Kubernetes deployment manifests, service definitions, ingress configurations, ConfigMaps, Secrets, and Helm charts.

Apart from application deployments, we also deploy infrastructure resources using Terraform. These resources include VPCs, EC2 instances, EKS clusters, RDS databases, load balancers, IAM roles, security groups, and monitoring components. Both application and infrastructure deployments are automated through Jenkins pipelines, ensuring consistency and repeatability across environments.

---

## 9. Difference Between RUN and CMD

RUN and CMD are Dockerfile instructions, but they serve different purposes. RUN executes commands during the Docker image build process and creates new image layers. It is commonly used for installing packages, updating operating system dependencies, or configuring software inside the image.

CMD, on the other hand, specifies the default command that runs when a container starts. Unlike RUN, CMD is executed at container runtime rather than during image creation. An image can contain multiple RUN instructions but only one effective CMD instruction. If a command is provided during container startup, it overrides the default CMD value.

Therefore, RUN is used to build and configure the image, whereas CMD is used to define the container's startup behavior.

---

## 10. Is CI Used or CD Used in Production Deployment and What Is Used in Testing Environment?

In our organization, Continuous Integration is implemented across all environments. Every code commit triggers automated builds, unit testing, code quality analysis, and security scanning. This ensures rapid feedback and early defect detection.

For testing environments such as Development, QA, and UAT, Continuous Deployment is commonly used because deployments can occur automatically after successful pipeline execution. However, for production environments, we generally implement Continuous Delivery rather than fully automated Continuous Deployment. After all automated validations pass, a manual approval step is required before deployment to production. This approach balances automation with governance and risk management while ensuring compliance with organizational policies.

---

# Terraform and AWS Scenario-Based Questions

## 11. How Do You Transfer Payloads Between Lambda Functions in Two Different AWS Accounts?

Cross-account communication between Lambda functions can be achieved using several AWS services. The most direct approach involves configuring cross-account IAM permissions and allowing one Lambda function to invoke another using resource-based policies. This method is suitable for synchronous communication where an immediate response is required.

For loosely coupled architectures, Amazon SQS is commonly used. The Lambda function in Account A sends messages to an SQS queue, and the Lambda function in Account B consumes those messages. EventBridge can also be used for event-driven architectures where events need to be shared securely across AWS accounts. SNS can be used when multiple subscribers must receive the same payload.

In production systems, EventBridge and SQS are generally preferred because they improve reliability, scalability, and fault tolerance.

---

## 12. What Are Terraform Lifecycle Policies?

Terraform lifecycle policies are configuration settings that control how resources are created, updated, and destroyed. These policies help prevent downtime and accidental infrastructure changes.

The create_before_destroy option ensures a replacement resource is created before the existing resource is removed, thereby reducing service interruptions. The prevent_destroy option protects critical resources such as databases from accidental deletion. The ignore_changes option allows Terraform to ignore changes to specific resource attributes that may be modified externally. The replace_triggered_by option forces resource replacement when dependent resources change.

Lifecycle policies are particularly useful when managing production infrastructure because they provide greater control over resource behavior during Terraform operations.

---

## 13. How Do You Ensure Least Privilege Access To IAM Users?

The principle of least privilege is implemented by granting users only the permissions necessary to perform their job functions. Instead of assigning broad administrative permissions, I create fine-grained IAM policies that restrict access to specific resources and actions.

IAM roles are preferred over long-term access keys because they provide temporary credentials and improve security. Multi-Factor Authentication is enforced for privileged accounts. IAM groups are used to simplify permission management, while AWS CloudTrail and IAM Access Analyzer help monitor and review access patterns. Regular permission audits are conducted to identify and remove excessive privileges.

This approach minimizes security risks and helps organizations meet compliance requirements.

---

## 14. What Is Terraform External Command and When Should It Be Used?

Terraform External Data Source allows Terraform to execute external programs or scripts and consume their output. This functionality is useful when required information cannot be obtained through native Terraform providers.

For example, an external Python script may retrieve data from a custom API, internal CMDB, or external inventory system. Terraform executes the script and uses the returned values during resource provisioning.

Although powerful, external data sources should be used only when necessary because they introduce dependencies outside Terraform's normal resource management model.

---

## 15. How Do You Ensure a Particular AMI Image Is Present in AWS Account Using Terraform?

To ensure that a specific AMI is used during provisioning, Terraform data sources are commonly used to search for approved AMIs based on filters such as image name, owner account, tags, or creation date. The configuration retrieves the latest approved AMI that matches predefined criteria.

In enterprise environments, golden AMIs are typically created using image pipelines and shared across AWS accounts. Terraform validates the existence of these approved AMIs before provisioning EC2 instances. This ensures infrastructure consistency, compliance, and security standards are maintained across environments.

---

## 16. What Are Terraform Provisioners?

Terraform provisioners are mechanisms used to execute scripts or commands after resource creation. The local-exec provisioner runs commands on the machine executing Terraform, while the remote-exec provisioner runs commands directly on the target resource.

Provisioners can be used for tasks such as installing software, updating configuration files, or performing initialization activities. However, HashiCorp recommends minimizing provisioner usage because they are not fully idempotent and can complicate infrastructure management. Configuration management tools such as Ansible, Chef, or Puppet are generally preferred for post-provisioning activities.

---

## 17. What Are S3 Bucket Lifecycle Policies?

Amazon S3 lifecycle policies automate object management throughout their lifecycle. Organizations often store large volumes of logs, backups, and archived data, making lifecycle policies essential for cost optimization.

Lifecycle rules can automatically transition objects from Standard storage to lower-cost storage classes such as Standard-IA, Glacier Instant Retrieval, Glacier Flexible Retrieval, or Glacier Deep Archive. Policies can also automatically delete objects after a defined retention period. For version-enabled buckets, lifecycle policies can remove old object versions and expired delete markers.

These automated actions reduce storage costs and support compliance requirements without requiring manual intervention.

---

## 18. What Are Meta-Arguments in Terraform?

Meta-arguments are special Terraform constructs that modify resource behavior rather than configuring the resource itself. They provide flexibility and help manage complex infrastructure deployments.

Common meta-arguments include count, which creates multiple instances of a resource; for_each, which creates resources dynamically from maps or sets; depends_on, which defines explicit dependencies between resources; provider, which selects a specific provider configuration; and lifecycle, which controls resource lifecycle behavior.

Meta-arguments are heavily used in enterprise Terraform implementations because they improve scalability, reduce code duplication, and provide better control over resource provisioning.
