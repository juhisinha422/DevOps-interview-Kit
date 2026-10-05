# AWS DevOps Interview Questions (4 Years Experience)

---

## 1. Can you explain the CI/CD pipeline process from code commit to production deployment?

### Answer

In my project, developers push code to GitHub/GitLab, which triggers a Jenkins pipeline through a webhook. Jenkins checks out the code, builds the application using Maven, runs unit tests, performs SonarQube code quality analysis, builds a Docker image, scans it for vulnerabilities, pushes it to Amazon ECR, and deploys it to Amazon EKS using Kubernetes manifests or Helm. After deployment, health checks are performed, and if everything is successful, the deployment is marked complete. If any stage fails, the pipeline stops immediately and notifies the team.

---

## 2. What are the common failure points in a CI/CD pipeline, and how do you address them?

### Answer

Common failures include Git checkout issues, build failures, failed unit tests, SonarQube quality gate failures, Docker build errors, image push failures, Kubernetes deployment failures, and application health check failures. I troubleshoot each stage by reviewing Jenkins logs, application logs, Kubernetes events, and deployment status. Once the root cause is identified, I fix the issue, rerun the pipeline, and validate the deployment.

---

## 3. How do you manage multi-cloud infrastructure using Terraform?

### Answer

Terraform supports multiple cloud providers through provider plugins. For multi-cloud environments, I configure separate providers for AWS, Azure, or GCP and organize the infrastructure using reusable modules. Variables and separate backend configurations are maintained for each environment, allowing the same code structure to provision resources across different cloud platforms.

---

## 4. How do you handle Terraform state management in multi-cloud projects?

### Answer

I store Terraform state remotely using cloud storage such as AWS S3 with DynamoDB for state locking. Separate backend configurations and workspaces are maintained for different cloud providers and environments. This prevents conflicts and allows teams to manage infrastructure independently while maintaining a single source of truth.

---

## 5. What does "large-scale Kubernetes" mean in your experience, and how do you manage it?

### Answer

In my experience, large-scale Kubernetes means managing multiple microservices running across several namespaces and worker nodes with high availability and auto scaling. We use Deployments, HPA, Cluster Autoscaler, Ingress Controllers, ConfigMaps, Secrets, monitoring through Prometheus and Grafana, centralized logging, and Infrastructure as Code to efficiently manage the cluster.

---

## 6. How do you troubleshoot a failing Pod in Kubernetes?

### Answer

I first check the Pod status using `kubectl get pods`, then inspect the Pod using `kubectl describe pod` to view Events. Next, I review application logs, verify ConfigMaps, Secrets, resource limits, readiness and liveness probes, image accessibility, and node health. Based on the findings, I resolve the root cause and redeploy the application if necessary.

---

## 7. Can you explain Blue-Green deployment in Kubernetes?

### Answer

In Blue-Green deployment, two identical environments exist: Blue (current production) and Green (new version). The new application is deployed to the Green environment and validated through testing. Once verified, the Kubernetes Service or Ingress is updated to route traffic from Blue to Green. If any issues occur, traffic is immediately switched back to Blue, enabling a fast rollback with minimal downtime.

---

## 8. How do you ensure Docker image consistency across environments?

### Answer

I use the same Docker image across Development, UAT, and Production. Environment-specific configuration is provided using ConfigMaps, Secrets, or environment variables instead of modifying the image. Images are versioned with tags and stored in Amazon ECR, ensuring that every environment runs the exact same application artifact.

---

## 9. Why did you integrate SonarQube into your CI/CD pipeline, and at what stage?

### Answer

We integrated SonarQube to improve code quality and detect bugs, vulnerabilities, and code smells before deployment. It runs immediately after the build and unit testing stages. If the Quality Gate fails, the pipeline stops automatically, preventing poor-quality code from reaching higher environments.

---

## 10. How do you handle Terraform state drift caused by manual changes?

### Answer

Terraform detects drift during `terraform plan` by comparing the state file with the actual infrastructure. If the manual change is unauthorized, I run `terraform apply` to restore the infrastructure. If the change is valid, I update the Terraform code first and then apply it so the infrastructure and code remain synchronized.

---

## 11. How do you make Terraform code reusable?

### Answer

I create reusable Terraform modules for common resources such as VPCs, EC2 instances, security groups, and IAM roles. Variables are used to customize each deployment, while outputs expose resource information to other modules. This approach reduces duplication and improves maintainability.

---

## 12. What monitoring and observability tools have you worked with?

### Answer

I have worked with Prometheus, Grafana, Amazon CloudWatch, and the ELK Stack. Prometheus collects Kubernetes metrics, Grafana visualizes dashboards, CloudWatch monitors AWS resources, and ELK centralizes application logs for troubleshooting. Together, these tools provide complete visibility into infrastructure and application health.

---

## 13. How does Kubernetes handle traffic spikes?

### Answer

Kubernetes handles traffic spikes using the Horizontal Pod Autoscaler (HPA), which automatically increases the number of Pods based on CPU or memory utilization. If additional worker nodes are required, the Cluster Autoscaler provisions new nodes. Services automatically distribute traffic across healthy Pods, ensuring high availability during peak loads.

---

## 14. How do you perform rollbacks in Kubernetes deployments?

### Answer

If a deployment causes issues, I first verify the problem using logs and health checks. Then I perform a rollback using Kubernetes deployment history, which restores the previous stable ReplicaSet. After the rollback, I monitor the application to ensure it is functioning correctly before investigating the failed release.

---

## 15. How does AWS handle traffic spikes?

### Answer

AWS handles traffic spikes using Auto Scaling Groups and Elastic Load Balancers. The Load Balancer distributes incoming requests across healthy EC2 instances, while Auto Scaling automatically launches additional instances based on CloudWatch metrics such as CPU utilization or request count. This ensures application availability during high traffic.

---

## 16. What could cause errors when EC2 instances are running?

### Answer

Even if an EC2 instance is running, applications may fail due to insufficient CPU or memory, full disk space, application crashes, incorrect security group rules, failed services, network issues, database connectivity problems, expired SSL certificates, or misconfigured load balancers. I investigate using CloudWatch metrics, system logs, application logs, and health checks to identify the root cause.

---

## 17. What is a real-time production issue you identified and how you troubleshooted it?

### Answer

In one production deployment, users started receiving **503 Service Unavailable** errors immediately after a new release. I first checked the Kubernetes Pods and found several were failing their readiness probes. Using `kubectl describe pod` and application logs, I discovered a misconfigured environment variable in the ConfigMap that prevented the application from connecting to the database. I corrected the configuration, redeployed the application, and verified that all Pods became Ready. Traffic was restored within a few minutes, and we later added automated configuration validation in the CI/CD pipeline to prevent similar issues in future deployments.

---
