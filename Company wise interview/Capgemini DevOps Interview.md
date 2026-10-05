# 🚀 Capgemini DevOps Interview – Scenario Questions (4 Years Experience)

---

## 1. Jenkins Pipeline for Multiple Environments (Single Script)

**Question:**
They asked how you write Jenkins pipeline for multiple environments in single script.

**Answer:**

To handle multiple environments (Dev, QA, Prod) in a single Jenkins pipeline, I use a **parameterized pipeline**.

**Approach:**

* Use input parameter to select environment
* Use conditional logic to deploy based on selected environment
* Maintain single Jenkinsfile for all environments

---

### ✅ Example Pipeline:

```groovy id="jenv1"
pipeline {
    agent any

    parameters {
        choice(name: 'ENV', choices: ['dev', 'qa', 'prod'], description: 'Select Environment')
    }

    environment {
        APP_NAME = "my-app"
    }

    stages {

        stage('Build') {
            steps {
                echo "Building application..."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (params.ENV == 'dev') {
                        sh 'echo Deploying to DEV environment'
                    } else if (params.ENV == 'qa') {
                        sh 'echo Deploying to QA environment'
                    } else if (params.ENV == 'prod') {
                        sh 'echo Deploying to PROD environment'
                    }
                }
            }
        }
    }
}
```

---

### 🎯 Key Points to Say in Interview:

* Single Jenkinsfile for all environments
* Parameterized builds
* Conditional deployment logic
* Can also use:

  * Separate config files (values-dev.yaml, etc.)
  * Credentials per environment

---

## 2. Terraform – Run Latest Version Without Changing Provider Version

**Question:**
Without changing provider version, how to run Terraform script using latest version?

---

### ✅ Understanding:

Terraform has:

* Terraform CLI version
* Provider version (AWS, Azure, etc.)

We should not change provider version but want latest Terraform CLI.

---

### ✅ Solution 1: Use Latest Terraform CLI

Use **tfenv** to manage versions:

```bash id="tfv1"
tfenv install latest
tfenv use latest
```

---

### ✅ Solution 2: Keep Provider Version Same

Terraform provider version is controlled by:

* `required_providers` block
* `.terraform.lock.hcl`

To **keep provider same**, run:

```bash id="tfv2"
terraform init
```

✔ Uses locked provider version
✔ No change in provider

---

### ✅ Optional: Upgrade Within Constraints

```bash id="tfv3"
terraform init -upgrade
```

✔ Upgrades provider only within allowed version range
✔ Does NOT break constraints

---

### 🎯 Best Interview Answer:

* Use **tfenv** for Terraform CLI version control
* Use `.terraform.lock.hcl` to lock provider versions
* Run `terraform init` to keep provider unchanged
* Run `terraform init -upgrade` only if upgrade is required

---

## 🔥 Final Tips

* Always explain **approach + example**
* Show **real-time usage**
* Keep answers simple and structured

---

✅ These are real scenario-based answers expected in Capgemini interviews
