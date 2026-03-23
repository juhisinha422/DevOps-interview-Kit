# 🚀 Terraform Interview Question (Simplified Explanation – 4 Years Experience)

---

## ❓ Question

Without changing provider version, I want to run Terraform script using latest version. How can we achieve this?

---

## ✅ Step 1: Understand the Core Concept

Terraform has **2 types of versions**:

### 1. Terraform CLI Version

* This is the Terraform tool installed on your system
* Example: 1.3, 1.5, 1.7

---

### 2. Provider Version

* This is the plugin (AWS, Azure, etc.)
* Defined inside code:

```hcl id="tfex1"
required_providers {
  aws = {
    source  = "hashicorp/aws"
    version = "4.0"
  }
}
```

---

## 🧠 What Interviewer Is Asking

* Use **latest Terraform CLI**
* BUT **do NOT change provider version**

---

## ✅ Step 2: Use Latest Terraform CLI

We can use **tfenv** (Terraform version manager):

```bash id="tfex2"
tfenv install latest
tfenv use latest
```

✔ This updates Terraform CLI to latest version

---

## ✅ Step 3: Keep Provider Version Same

Terraform automatically stores provider version in:

```id="tfex3"
.terraform.lock.hcl
```

Now run:

```bash id="tfex4"
terraform init
```

✔ It will use the SAME provider version
✔ No changes to provider

---

## ❌ What NOT to do

```bash id="tfex5"
terraform init -upgrade
```

🚫 This upgrades provider version → NOT required in this question

---

## 🎯 Real-Time Example

| Component     | Before | After Update  |
| ------------- | ------ | ------------- |
| Terraform CLI | 1.3    | 1.7 (updated) |
| AWS Provider  | 4.0    | 4.0 (same) ✅  |

---

## 🎯 Best Interview Answer

> We upgrade Terraform CLI using tools like tfenv, and keep the provider version unchanged using `.terraform.lock.hcl`. Running `terraform init` ensures the same provider version is used without upgrading it.

---

## 💡 Easy Trick to Remember

* CLI → managed by **tfenv**
* Provider → controlled by **lock file**

---

✅ This is the exact explanation expected in real DevOps interviews
