# AWS + Terraform + Docker Scenario-Based Interview Questions

---

## What are SCPs in AWS and how are they used?

* SCP = Service Control Policy
* Used in AWS Organizations
* Controls maximum permissions for AWS accounts/OUs

Important:

* SCP does NOT grant permissions
* It only restricts permissions

Example:

* Deny EC2 termination in production accounts

---

## How would you automate Terraform drift detection for infrastructure state management?

* Run scheduled:

```bash id="tfdrift11"
terraform plan
```

* Compare actual infra vs Terraform state
* Integrate with:

  * Jenkins
  * GitHub Actions
  * Terraform Cloud
* Send alerts if drift detected

Best practice:

* Prevent manual console changes

---

## How do you recover infrastructure if the Terraform state file is lost?

1. Restore from remote backend backup/versioning
2. If unavailable:

   * Use:

```bash id="tfimport11"
terraform import
```

3. Rebuild state manually
4. Run:

```bash id="tfplan12"
terraform plan
```

5. Validate no unwanted changes

Best practice:

* Use S3 versioning + DynamoDB locking

---

## What is mutable vs immutable infrastructure in Terraform?

| Mutable Infrastructure       | Immutable Infrastructure  |
| ---------------------------- | ------------------------- |
| Existing server modified     | Replace server completely |
| In-place updates             | New resource creation     |
| Configuration drift possible | More consistent           |

Example:

* Mutable → update package on same VM
* Immutable → create new AMI and replace VM

---

## Is it possible to host AMD64 and ARM64 Docker images together in the same container registry?

* Yes
* Use multi-architecture images (multi-arch manifests)

Example:

```bash id="dockerarch1"
docker buildx build --platform linux/amd64,linux/arm64
```

* Docker automatically pulls correct image based on architecture

---

## How would you troubleshoot OOM (Out Of Memory) issues in Amazon ECS?

1. Check ECS task logs
2. Verify memory utilization
3. Check CloudWatch metrics
4. Review task definition memory limits
5. Identify memory leaks
6. Increase task memory if needed

Check exit code:

```text id="oom1"
Exit Code 137
```

Usually indicates OOM kill

---

## How do you prevent Terraform taint operations from unintentionally destroying resources?

* Use:

```hcl id="tflifecycle11"
lifecycle {
  prevent_destroy = true
}
```

* Review `terraform plan` carefully
* Use targeted operations cautiously
* Restrict production access

---

## What is the use of Object Lock in Amazon S3?

* Prevents object deletion/modification for retention period
* Used for:

  * Compliance
  * Backup protection
  * Ransomware protection

Modes:

* Governance
* Compliance

---

## How would you implement Cross-Region Replication (CRR) in Amazon S3 without versioning?

* CRR requires versioning
* It is NOT possible without enabling versioning

AWS requirement:

* Versioning must be enabled on source and destination buckets

---

## Difference between Multi-AZ and Multi-Region deployments in Amazon RDS?

| Multi-AZ                | Multi-Region                    |
| ----------------------- | ------------------------------- |
| Same region             | Different regions               |
| High availability       | Disaster recovery/global access |
| Synchronous replication | Usually asynchronous            |
| Faster failover         | Higher latency                  |

---

## Best Interview Tip

For scenario-based questions:

* Explain:

  * Problem
  * Root cause
  * Action taken
  * Preventive measure

This gives strong senior-level answers.

---
