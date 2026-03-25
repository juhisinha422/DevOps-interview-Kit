# 📘 Advanced Terraform Interview Questions & Answers (4+ Years Experience)

---

### 1. What happens if two engineers run terraform apply at the same time on the same remote backend?

If state locking is enabled, one execution will acquire the lock and the other will fail with a lock error.
If locking is not enabled, it can lead to **state corruption and inconsistent infrastructure**.

---

### 2. How does Terraform state locking work with S3 and DynamoDB?

* S3 stores the state file
* DynamoDB is used for **state locking**
* When `terraform apply` runs → a lock entry is created in DynamoDB
* Once complete → lock is released

---

### 3. What are the risks of manually editing the .tfstate file, and how would you recover from corruption?

**Risks:**

* Resource mismatch
* Drift
* Broken dependencies

**Recovery:**

* Restore from backup
* Use `terraform refresh`
* Re-import resources

---

### 4. How do you design Terraform architecture for multiple environments (Dev / QA / Prod) in a scalable way?

* Use reusable modules
* Separate environments using workspaces or folders
* Remote backend per environment
* CI/CD pipeline per environment

---

### 5. What is Terraform drift? How do you detect and prevent it in production?

Drift occurs when infrastructure changes outside Terraform.

**Detection:**

* `terraform plan`
* Drift detection tools

**Prevention:**

* Restrict manual changes
* Use IAM policies
* Regular plan checks

---

### 6. Explain the difference between count and for_each. Why can count be risky in production?

* **count** → index-based
* **for_each** → key-based

**Risk:**
If order changes in count → resources get recreated unintentionally.

---

### 7. How would you migrate Terraform state from local to remote backend without downtime?

* Configure backend in code
* Run `terraform init -migrate-state`
* Validate with plan before apply

---

### 8. How do you handle Terraform version upgrades safely in production?

* Test in lower environments
* Use version constraints
* Backup state file
* Gradual rollout

---

### 9. How do you securely manage Terraform state (encryption, access control, backups)?

* Enable S3 encryption (SSE)
* IAM role-based access
* Versioning enabled
* Regular backups

---

### 10. What is the difference between taint, import, and state rm? When would you use each?

* **taint** → recreate resource
* **import** → bring existing resource into state
* **state rm** → remove resource from state without deleting infra

---

### 11. How do you design Terraform workflows for multi-account AWS environments?

* Use separate AWS accounts
* Cross-account IAM roles
* Separate state files
* CI/CD pipelines per account

---

### 12. How do you validate Terraform code in CI/CD before deployment?

* `terraform fmt`
* `terraform validate`
* `terraform plan`
* Static code analysis (tflint, checkov)

---

### 13. How do you design reusable, version-controlled Terraform modules for enterprise use?

* Create modular structure
* Use versioning (Git tags)
* Maintain inputs/outputs clearly
* Publish modules internally

---

### 14. How would you implement disaster recovery for Terraform state?

* Enable S3 versioning
* Cross-region replication
* Backup state files
* Store in secure location

---

### 15. How would you handle a situation where Terraform partially created resources and failed midway?

* Check `terraform state`
* Run `terraform plan` to identify drift
* Fix issues and re-run apply
* Import missing resources if needed

---

✅ Ready to copy as **README.md**
Strong answers tailored for **real-time 4+ years DevOps interviews 🚀**
