# 🚀 Terraform Interview Guide (4 Years Experience – Detailed)

---

## 1) What is the difference between terraform import and terraform taint?

The `terraform import` command is used to bring existing infrastructure that was created outside Terraform under Terraform management by adding it to the state file without modifying the actual resource. This is useful when you want to manage already existing resources using Terraform going forward. On the other hand, `terraform taint` marks a resource in the state file as “tainted,” which means Terraform will destroy and recreate that resource during the next apply. It is typically used when a resource is in a bad or inconsistent state and needs to be rebuilt without affecting the rest of the infrastructure.

---

## 2) How do you manage secrets in Terraform without hardcoding them?

Secrets should never be hardcoded in Terraform configuration files. Instead, they can be managed using environment variables, Terraform variable files with sensitive flags, or external secret management systems like AWS Secrets Manager, HashiCorp Vault, or Azure Key Vault. Terraform can fetch secrets dynamically using data sources, ensuring they are not exposed in code. Additionally, remote backends with encryption (like S3 with KMS) help secure the state file, which may contain sensitive values. Access control using IAM policies further ensures secrets are protected.

---

## 3) What’s the difference between count and for_each? Give a real-world use case.

The `count` argument is used to create multiple instances of a resource based on a numeric value, while `for_each` is used to iterate over a map or set of strings, allowing more flexibility with unique identifiers. The key difference is that `for_each` maintains stable resource identities, whereas `count` can cause issues if the order changes. In real-world scenarios, `count` is useful for creating identical resources like multiple EC2 instances, while `for_each` is better when resources have unique attributes, such as creating multiple S3 buckets with different names or configurations.

---

## 4) How do you handle drift detection in Terraform?

Drift occurs when the actual infrastructure differs from the Terraform state. To detect drift, I run `terraform plan`, which compares the desired state with the actual state and highlights differences. Additionally, `terraform refresh` (or automatic refresh during plan/apply) updates the state file with real infrastructure data. In production, I ensure that all changes are made through Terraform to avoid drift. Monitoring and periodic audits also help identify unauthorized changes, and drift can be corrected by applying the Terraform configuration again.

---

## 5) What is a Terraform remote backend, and why is it important?

A remote backend is used to store the Terraform state file in a centralized location like AWS S3, along with state locking using DynamoDB. It is important because it enables team collaboration, prevents state file conflicts, and ensures security with encryption and access control. Remote backends also support state locking, which avoids concurrent modifications that could corrupt infrastructure. This is essential in production environments where multiple engineers work on the same infrastructure.

---

## 6) How do you manage multiple environments (dev, staging, prod) in Terraform?

Multiple environments can be managed using separate workspaces, variable files, or directory structures. A common approach is to use different `.tfvars` files for each environment and pass them during execution. Another approach is to maintain separate backend configurations for each environment. Modules are reused across environments to ensure consistency. This setup ensures that infrastructure for dev, staging, and production remains isolated while sharing the same codebase.

---

## 7) Difference between local-exec and remote-exec provisioners.

The `local-exec` provisioner runs commands on the machine where Terraform is executed, such as triggering scripts or API calls. In contrast, `remote-exec` runs commands on the provisioned resource itself, such as executing setup scripts on a newly created EC2 instance via SSH. In real-world usage, `remote-exec` is used for initial configuration, while `local-exec` is used for integration tasks. However, provisioners are generally avoided in favor of configuration management tools for better reliability.

---

## 8) How do you safely roll back infrastructure changes after a failed deployment?

Terraform does not have a direct rollback command, so rollback is handled by reverting to a previous stable configuration and reapplying it. Version control plays a key role here, as we can revert changes in Git and run `terraform apply` to restore the previous state. Additionally, using state backups and versioned storage (like S3 versioning) helps recover previous states if needed. In critical systems, blue-green or canary deployment strategies are used to minimize impact and enable quick rollback.

---

## 9) Explain terraform refresh vs terraform plan.

`terraform refresh` updates the Terraform state file to match the real-world infrastructure without making any changes to resources. It ensures the state reflects the current infrastructure. `terraform plan`, on the other hand, shows the difference between the desired configuration and the current state, providing a preview of changes Terraform will make. In modern Terraform versions, refresh is automatically included in the plan and apply process, making standalone refresh less commonly used.

---

## 10) How do you write reusable Terraform modules?

Reusable Terraform modules are created by grouping related resources into a separate directory with input variables and outputs. These modules can then be called from different environments or projects. Best practices include keeping modules small and focused, using variables for flexibility, and documenting inputs and outputs clearly. Versioning modules and storing them in a central repository ensures consistency and reuse across teams. This approach improves maintainability and reduces duplication in infrastructure code.

---

## 🚀 Final Tip

At 4 years experience:

* Focus on **real-world usage + best practices**
* Always mention **security, scalability, and team collaboration**
* Show **how you actually use Terraform in projects**

---
