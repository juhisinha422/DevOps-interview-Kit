# 📘 Deloitte DevOps Interview (4 Years Experience) – Kubernetes | Terraform | AWS | CI/CD

---

## 1. Write a Terraform resource block for S3 bucket

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-devops-bucket-123"

  tags = {
    Name        = "MyBucket"
    Environment = "Dev"
  }
}

resource "aws_s3_bucket_versioning" "versioning" {
  bucket = aws_s3_bucket.my_bucket.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

👉 Best Practice (4 yrs level):

* Enable versioning
* Add encryption
* Use lifecycle policies

---

## 2. CI/CD pipeline using GitHub Actions

```yaml
name: CI-CD Pipeline

on:
  push:
    branches: [ "main" ]

jobs:
  build-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Build Application
      run: mvn clean package

    - name: Build Docker Image
      run: docker build -t myapp:${{ github.sha }} .

    - name: Push to DockerHub
      run: |
        docker login -u ${{ secrets.USER }} -p ${{ secrets.PASS }}
        docker push myapp:${{ github.sha }}

    - name: Deploy to Kubernetes
      run: kubectl apply -f k8s/
```

---

## 3. End-to-End CI/CD Pipeline with Kubernetes Integration

**Flow:**

1. Developer pushes code → GitHub
2. GitHub Actions triggered
3. Build using Maven
4. Docker image build & push
5. Helm chart updated with new image tag
6. Deploy to Kubernetes cluster
7. Monitor using CloudWatch

---

## 4. Usage of Helm Chart

* Package manager for Kubernetes
* Helps in templating YAML
* Supports versioning & rollback

**Example:**

```bash
helm install myapp ./chart
helm upgrade myapp ./chart
```

---

## 5. CloudWatch Usage

* Logs monitoring
* Metrics (CPU, memory)
* Alerts (CloudWatch Alarms)
* Container insights (EKS monitoring)

---

## 6. Difference between IAM policy and Inline policy

| Feature     | IAM Policy           | Inline Policy             |
| ----------- | -------------------- | ------------------------- |
| Type        | Managed              | Embedded                  |
| Reusability | Reusable             | Not reusable              |
| Attachment  | Multiple entities    | Single user/role          |
| Use Case    | Standard permissions | Strict one-to-one control |

---

## 7. Git Merging Strategy

**Common Strategies:**

* **Merge Commit** → preserves history
* **Rebase** → clean linear history
* **Squash Merge** → combines commits

👉 Best Practice (Enterprise):

* Feature branch → PR → Squash merge
* Use protected branches

---

## 8. Kubernetes Deployment Strategy (Real-Time)

* Rolling Update (default)
* Blue-Green
* Canary

👉 Use case:

* Canary → gradual release
* Blue-Green → zero downtime

---

## 9. How Terraform + Kubernetes + AWS Work Together

* Terraform → provision infra (EKS, VPC, S3)
* Kubernetes → deploy workloads
* CI/CD → automate deployment
* CloudWatch → monitoring

---

## 10. Real Interview Add-ons (Important 🔥)

### 🔹 How do you secure credentials in CI/CD?

* GitHub Secrets
* IAM roles
* Kubernetes secrets

---

### 🔹 How do you handle rollback in Kubernetes?

```bash
kubectl rollout undo deployment app
```

---

### 🔹 How do you manage environments (Dev/Prod)?

* Separate namespaces
* Separate Helm values files
* Different AWS accounts (best practice)

---

### 🔹 How do you debug failed deployment?

```bash
kubectl describe pod
kubectl logs pod-name
```

---

## 🎯 Final Interview Tip (Deloitte Level)

For 4 yrs experience, always explain like this:

👉 “I worked on end-to-end pipeline where GitHub triggers CI, builds Docker image, pushes to registry, and deploys via Helm to EKS. Monitoring is handled via CloudWatch and logs are centralized.”

---

✅ Focused on **Kubernetes + Terraform + AWS + CI/CD**
💼 Perfect for **Deloitte DevOps (4 yrs)**
🚀 Ready to copy as **README.md**
