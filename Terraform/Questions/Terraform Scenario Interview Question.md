# 🚀 Terraform Scenario Interview Question

---

## ❓ Question

If you provision 100 servers and someone deletes 50 VMs manually, what happens if you apply the terraform apply command?

---

## ✅ Answer

In Terraform, infrastructure is managed using a desired state defined in configuration files and tracked in the state file.

When 100 servers are provisioned, Terraform records this in its state file. If someone manually deletes 50 VMs outside of Terraform, the actual infrastructure will no longer match the desired state.

When you run:

terraform apply

Terraform compares the state file with the real infrastructure and detects this difference (known as drift).

As a result, Terraform will attempt to recreate the missing 50 VMs to match the desired state defined in the configuration.

---

## 🎯 One-Line Answer (Interview Ready)

Terraform detects infrastructure drift and recreates the deleted resources to match the desired state.
OR
Terraform will detect drift and recreate the 50 deleted VMs to match the desired state defined in the configuration

---

## ⚠️ Key Points to Mention

* Terraform always tries to match real infrastructure with the desired state
* Manual changes cause drift
* Running terraform plan will show resources to be recreated
* Best practice is to avoid manual changes outside Terraform

---
