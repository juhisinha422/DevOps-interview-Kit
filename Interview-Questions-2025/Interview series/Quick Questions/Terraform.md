## 𝗤𝘂𝗲 : There is a scenario where, there are many bugs in main.tf file, but i want you to address few of them through terraform, not all, how would you solve this issue ?

```
𝗔𝗻𝘀 :Targeted Bug Fixing Strategies in Terraform
Apply changes to only specific resources using -𝘁𝗮𝗿𝗴𝗲𝘁 flag
The -𝘁𝗮𝗿𝗴𝗲𝘁 flag lets you plan/apply changes to specific resources only:
# 𝗧𝗮𝗿𝗴𝗲𝘁 𝗮 𝘀𝗽𝗲𝗰𝗶𝗳𝗶𝗰 𝗿𝗲𝘀𝗼𝘂𝗿𝗰𝗲
terraform plan -target=aws_instance.web_server
terraform apply -target=aws_instance.web_server

# 𝗧𝗮𝗿𝗴𝗲𝘁 𝗮 𝗺𝗼𝗱𝘂𝗹𝗲
terraform apply -target=module.networking

# 𝗧𝗮𝗿𝗴𝗲𝘁 𝗺𝘂𝗹𝘁𝗶𝗽𝗹𝗲 𝘀𝗽𝗲𝗰𝗶𝗳𝗶𝗰 𝗿𝗲𝘀𝗼𝘂𝗿𝗰𝗲𝘀
terraform apply -target=aws_s3_bucket.logs -target=aws_iam_role.lambda_role

This means Terraform only evaluates and fixes those targeted resources, leaving everything else untouched.

⚠️ 𝗜𝗺𝗽𝗼𝗿𝘁𝗮𝗻𝘁 𝗡𝗼𝘁𝗲 :
Terraform itself warns that -𝘁𝗮𝗿𝗴𝗲𝘁 is meant for 𝗘𝗫𝗖𝗘𝗣𝗧𝗜𝗢𝗡𝗔𝗟 𝗨𝗦𝗘, not routine workflow — because it can cause dependency inconsistencies. Always run a full terraform plan afterward to confirm the overall state is clean.
The cleanest long-term fix is to refactor main.tf into smaller module files so bugs are naturally isolated by scope.
```

# Terraform Interview Questions (3–5 Years DevOps Engineer)

## 1. How do you prevent Terraform state conflicts when multiple engineers work on the same project?

In production environments, I prevent Terraform state conflicts by storing the state file in a remote backend such as AWS S3 and enabling state locking using DynamoDB. Whenever an engineer or CI/CD pipeline runs `terraform apply`, Terraform first acquires a lock in DynamoDB before reading or modifying the state file. If another engineer attempts to run Terraform simultaneously, the lock prevents concurrent modifications and Terraform throws a state lock error. This mechanism prevents race conditions, infrastructure corruption, and inconsistent state updates. Additionally, I enforce deployments through CI/CD pipelines instead of allowing direct manual execution from multiple workstations.

---

## 2. What would you do if the terraform.tfstate file gets corrupted?

The first step is to stop all Terraform operations to avoid further damage. If remote state storage is configured with S3 versioning, I restore the previous healthy version of the state file. If versioning is unavailable, I recover the latest backup from the backup repository. After restoring the state, I run `terraform state list` and `terraform plan` to verify consistency between the state file and actual infrastructure. If certain resources are missing from the state, I use `terraform import` to rebuild the state. Once validation is complete, normal operations can resume.

---

## 3. How do you migrate a local state file to a remote backend?

To migrate a local state file to a remote backend, I first configure the backend block in Terraform with the target backend details such as S3 bucket name, key path, region, and DynamoDB lock table. After updating the configuration, I execute:

```bash
terraform init
```

Terraform detects that the backend configuration has changed and prompts whether the existing local state should be copied to the remote backend. After confirming, Terraform uploads the state file to the remote backend and all future operations use the centralized state location. This improves collaboration, security, and disaster recovery.

---

## 4. How do you recover from accidental state deletion?

If the state file is stored in S3 with versioning enabled, I recover the latest valid version directly from S3 version history. If backups exist, I restore the backup copy. After restoration, I verify the state using `terraform state list` and compare it with actual infrastructure using `terraform plan`. If some resources are missing from state, I import them manually. This is one reason why enabling S3 versioning and regular state backups is considered a best practice in production.

---

## 5. How do you move resources between state files?

Terraform provides the `terraform state mv` command for moving resources between state files without recreating infrastructure. This is commonly required when refactoring configurations, splitting monolithic Terraform projects into modules, or separating environments. The command updates only Terraform state mappings while keeping the actual cloud resources unchanged. This approach ensures infrastructure continuity and avoids unnecessary downtime.

---

## 6. Terraform apply failed after creating some resources. What are your next steps?

When `terraform apply` fails midway, Terraform may have already created some resources successfully. My first step is to carefully review the error message and identify the failing resource. I then verify the current state using `terraform state list` and compare it with cloud resources. After fixing the root cause, such as permission issues, quota limits, or configuration errors, I rerun `terraform apply`. Terraform compares the existing state and infrastructure and creates only the missing resources rather than recreating everything.

---

## 7. A resource was deleted manually from AWS. How do you fix Terraform state?

I first determine whether the resource should continue to exist. If it was deleted accidentally, I run `terraform apply` to recreate it according to the Terraform configuration. If the deletion was intentional and approved, I update the Terraform code accordingly and remove the resource from state if necessary. Running `terraform plan` helps identify the drift and determine the correct remediation strategy. The goal is to ensure Terraform remains the single source of truth.

---

## 8. How do you detect and resolve infrastructure drift?

Infrastructure drift occurs when cloud resources are modified outside Terraform. I detect drift by running:

```bash
terraform plan
```

Terraform compares the current infrastructure state against the state file and configuration files. If differences are found, they appear in the plan output. To resolve drift, I either revert manual changes using Terraform or update Terraform code to reflect approved modifications. Regular drift detection through CI/CD pipelines helps maintain infrastructure consistency.

---

## 9. How do you update a production resource with zero downtime?

For production systems, I avoid in-place destructive changes whenever possible. Instead, I use blue-green deployment patterns, rolling updates, load balancers, Auto Scaling Groups, and Terraform lifecycle configurations. For example, using:

```hcl
lifecycle {
  create_before_destroy = true
}
```

Terraform creates the replacement resource first and destroys the old resource only after successful creation. This strategy minimizes downtime and ensures service availability during updates.

---

## 10. How do you safely destroy only one specific resource?

Terraform allows targeted operations using:

```bash
terraform destroy -target=<resource_name>
```

Before executing, I always review the plan carefully to ensure only the intended resource will be affected. In production environments, targeted destroy operations are typically executed through controlled approval processes because accidental deletions can impact dependent resources.

---

## 11. How would you design the same infrastructure for Dev, QA, and Production?

I would create reusable Terraform modules containing common infrastructure components such as VPCs, EC2 instances, EKS clusters, databases, and load balancers. Each environment would use the same modules but with different variable values. Separate state files, separate backend paths, and environment-specific variable files ensure isolation while maintaining consistent infrastructure architecture across Dev, QA, and Production.

---

## 12. When would you use Workspaces vs separate state files?

Workspaces are suitable for simple environments that share nearly identical infrastructure. They provide logical separation within the same configuration. However, for production environments, I prefer separate state files because they offer stronger isolation, independent access control, separate backends, and lower risk of accidental changes affecting multiple environments. Most enterprise organizations use separate state files for Production.

---

## 13. How do you manage different variable values for different environments?

I use environment-specific variable files such as:

```bash
dev.tfvars
qa.tfvars
prod.tfvars
```

During deployment, the appropriate variable file is selected:

```bash
terraform apply -var-file=prod.tfvars
```

This allows the same Terraform codebase to provision environment-specific resources while maintaining consistency across environments.

---

## 14. How do you keep environments isolated?

Environment isolation is achieved through separate AWS accounts, separate VPCs, separate Terraform state files, separate IAM roles, and separate CI/CD deployment pipelines. Production environments receive stricter security controls and approval mechanisms. This approach prevents accidental changes in one environment from affecting another.

---

## 15. How do you design reusable Terraform modules?

Reusable modules should focus on a single responsibility, such as creating a VPC, EKS cluster, RDS database, or security group. Inputs should be configurable through variables, while outputs should expose useful information for downstream modules. Modules should avoid hardcoded values and follow consistent naming conventions. This promotes standardization and simplifies maintenance across multiple projects.

---

## 16. How do you version modules across multiple teams?

I maintain modules in dedicated Git repositories and use semantic versioning such as v1.0.0, v1.1.0, and v2.0.0. Teams reference specific module versions rather than always consuming the latest code. This ensures stability and prevents unexpected changes from impacting production deployments.

---

## 17. How do you share modules securely?

Modules are typically stored in private Git repositories, private Terraform registries, or internal artifact repositories. Access is controlled through IAM policies, Git permissions, and CI/CD authentication. This ensures only authorized teams can consume or modify shared infrastructure modules.

---

## 18. How do you structure modules for a microservices architecture?

For microservices, I separate infrastructure into modular components such as networking, security, databases, monitoring, Kubernetes clusters, and application-specific services. Each microservice can consume common modules while maintaining its own configuration and deployment lifecycle. This approach improves scalability, maintainability, and team autonomy.

---

## 19. An AWS infrastructure already exists manually. How do you bring it under Terraform?

I first write Terraform configuration that accurately represents the existing infrastructure. Then I use:

```bash
terraform import
```

to associate existing AWS resources with Terraform state. After importing, I run `terraform plan` to identify any configuration mismatches and update the code until Terraform reports no changes. This process allows Terraform to manage existing infrastructure without recreating resources.

---

## 20. What are the limitations of terraform import?

Terraform import only updates the state file. It does not automatically generate Terraform configuration code. Engineers must manually create matching Terraform definitions. Importing complex resources with numerous dependencies can be time-consuming. Additionally, imported resources may require significant code adjustments before Terraform reaches a stable state with no drift.

---

## 21. How do you migrate resources without recreating them?

I use state management commands such as `terraform state mv` and `terraform import` to relocate resources within Terraform configurations while preserving existing infrastructure. For module restructuring, resource renaming, or environment migrations, these commands update Terraform's resource mappings without deleting and recreating live resources. This minimizes downtime and protects production workloads.

## 1. How does an S3 backend with DynamoDB locking work?

In production environments, Terraform state is typically stored in an AWS S3 bucket while state locking is handled through a DynamoDB table. When a user or CI/CD pipeline executes `terraform apply`, Terraform first contacts DynamoDB and creates a unique lock entry. If the lock is acquired successfully, Terraform reads the latest state file from S3, compares the infrastructure with the configuration, and performs the required changes. Once the deployment completes successfully, Terraform updates the state file in S3 and releases the lock by removing the DynamoDB entry. If another engineer attempts to run Terraform while the lock exists, Terraform blocks the operation to prevent concurrent modifications and infrastructure corruption.

---

## 2. Why is state locking important?

State locking prevents multiple users or pipelines from modifying the same infrastructure simultaneously. Without locking, two engineers could run `terraform apply` at the same time, causing race conditions where both deployments attempt to update the same resources. This can lead to inconsistent state files, failed deployments, duplicate resources, or infrastructure corruption. State locking ensures only one deployment can modify the infrastructure at a time, maintaining consistency and reliability.

---

## 3. What happens if the lock isn't released?

If Terraform crashes, the CI/CD pipeline fails unexpectedly, or the network connection is interrupted during deployment, the lock may remain in DynamoDB even though no deployment is running. Future Terraform operations will fail with a state lock error because Terraform believes another process is still modifying the infrastructure. After confirming that no deployment is active, the lock can be safely removed using:

```bash
terraform force-unlock <LOCK_ID>
```

However, force unlocking should be performed carefully because removing an active lock could result in state corruption.

---

## 4. How do you securely manage DB passwords and API keys?

I never store secrets directly in Terraform code or repositories. Instead, I use secret management solutions such as HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, or Google Secret Manager. During deployment, Terraform retrieves secrets dynamically from these systems and injects them into resources as needed. Access is controlled through IAM policies and RBAC, ensuring only authorized users and services can access sensitive credentials.

---

## 5. Why should secrets never be hardcoded in .tf files?

Hardcoding secrets in Terraform files creates significant security risks. Terraform code is usually stored in Git repositories, shared among teams, and reviewed by multiple users. Hardcoded credentials can be exposed through version control history, logs, CI/CD pipelines, and accidental code sharing. Even if secrets are removed later, they remain accessible in Git history. Storing secrets externally ensures better security, easier rotation, and compliance with organizational security standards.

---

## 6. How do you integrate Terraform with Vault or cloud secret managers?

Terraform provides data sources that allow it to retrieve secrets directly from external secret management systems. During deployment, Terraform authenticates with Vault or a cloud secret manager and retrieves the required credentials dynamically. These secrets can then be passed to resources without storing them in Terraform code. This approach centralizes secret management, improves security, and simplifies credential rotation.

---

## 7. Explain create_before_destroy with a production example.

The `create_before_destroy` lifecycle setting instructs Terraform to create a replacement resource before deleting the existing resource. For example, suppose a production Auto Scaling Group or Load Balancer requires a configuration change that forces resource recreation. Without this setting, Terraform would destroy the existing resource first, causing downtime. With `create_before_destroy`, Terraform provisions the new resource, verifies it is healthy, and only then removes the old resource. This approach enables near-zero downtime infrastructure updates.

Example:

```hcl
resource "aws_launch_template" "app" {
  lifecycle {
    create_before_destroy = true
  }
}
```

---

## 8. When would you use prevent_destroy?

The `prevent_destroy` lifecycle rule is used for critical resources that should never be deleted accidentally. Examples include production databases, S3 buckets containing business data, EKS clusters, and critical networking components. If someone attempts to delete a protected resource through Terraform, the deployment fails immediately. This provides an additional safety mechanism against accidental infrastructure destruction.

Example:

```hcl
resource "aws_db_instance" "prod_db" {
  lifecycle {
    prevent_destroy = true
  }
}
```

---

## 9. How does Terraform resolve dependencies internally?

Terraform builds a dependency graph before executing any operations. Dependencies can be explicitly defined using the `depends_on` attribute or automatically inferred when one resource references another resource's attributes. Terraform analyzes this graph and determines the correct creation, modification, and deletion order. Resources without dependencies can be processed in parallel, improving deployment efficiency.

---

## 10. How do you optimize Terraform for very large infrastructures?

For large infrastructures, I split resources into multiple Terraform projects and state files rather than managing everything in a single configuration. I use reusable modules, remote state management, CI/CD automation, and environment isolation. Breaking infrastructure into logical components such as networking, Kubernetes, databases, and applications improves maintainability and reduces deployment complexity. This approach also limits the blast radius of changes.

---

## 11. How do you reduce terraform plan execution time?

I reduce plan execution time by minimizing unnecessary resources within a single state file, using smaller modular deployments, limiting provider API calls, and avoiding overly complex data sources. Running targeted plans for specific components and using remote backends with efficient state management also improves performance. Large monolithic Terraform projects often result in slower planning and should be avoided when possible.

---

## 12. How do you organize code for hundreds of resources?

For large environments, I organize Terraform code using a modular structure. Separate modules are created for networking, security, compute, databases, monitoring, and Kubernetes. Environment-specific configurations are stored separately from reusable modules. A common directory structure might include modules, environments, shared variables, backend configurations, and CI/CD integration files. This organization improves readability, reusability, and team collaboration.

---

## 13. How do you integrate Terraform with Jenkins, GitHub Actions, or Azure DevOps?

Terraform is typically integrated into CI/CD pipelines through stages such as validation, planning, approval, and deployment. The pipeline checks out code, executes `terraform fmt`, `terraform validate`, and `terraform plan`, then publishes the plan for review. After approval, the pipeline executes `terraform apply`. Remote backends, state locking, secret management, and role-based access control are used to ensure secure and reliable deployments.

---

## 14. How do you implement approval before terraform apply?

In production environments, I separate the planning and deployment stages. The pipeline first generates a Terraform plan and presents it for review. Manual approval is required before the apply stage can proceed. Tools such as Jenkins input steps, GitHub environment approvals, GitLab manual jobs, or Azure DevOps approval gates are commonly used. This ensures infrastructure changes are reviewed before execution.

---

## 15. How do you handle rollbacks in Terraform pipelines?

Terraform does not provide a direct rollback mechanism like application deployments. Instead, rollback is achieved by reverting the Terraform code to the previous known-good version and reapplying the configuration. Because infrastructure state is tracked declaratively, Terraform reconciles the environment back to the desired state. Maintaining version-controlled infrastructure code is critical for safe rollback operations.

---

## 16. count vs for_each—when would you use each?

The `count` meta-argument is best suited when creating multiple identical resources based on a numeric value. For example, creating three identical EC2 instances. The `for_each` meta-argument is preferred when resources are based on unique keys or names, such as creating different security groups or IAM users. `for_each` provides more stable resource tracking because resources are associated with unique identifiers rather than numeric indexes.

---

## 17. What is the difference between a resource and a data source?

A resource creates, modifies, or manages infrastructure objects. Examples include EC2 instances, VPCs, databases, and security groups. A data source does not create infrastructure. Instead, it retrieves information about existing resources for use within Terraform configurations. For example, fetching an existing VPC ID or retrieving the latest AMI ID. Resources manage infrastructure, while data sources consume infrastructure information.

---

## 18. How does Terraform ensure idempotency?

Terraform ensures idempotency by continuously comparing the desired state defined in code with the current state of the infrastructure. If the infrastructure already matches the desired configuration, Terraform performs no changes regardless of how many times the deployment is executed. This guarantees predictable and repeatable deployments while preventing duplicate resource creation.

---

## 19. Why should provisioners be avoided in production?

Provisioners such as `local-exec` and `remote-exec` introduce procedural behavior into Terraform, which is designed to be declarative. Provisioners can fail unpredictably, are difficult to track in state, increase deployment complexity, and often create dependencies outside Terraform's control. Production environments typically use configuration management tools such as Ansible, cloud-init, user data scripts, or CI/CD automation instead of provisioners. Provisioners should only be used when no alternative solution exists.

