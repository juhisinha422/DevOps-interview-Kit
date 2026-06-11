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


