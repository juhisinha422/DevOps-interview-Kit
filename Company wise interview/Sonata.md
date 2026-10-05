# DevOps Support Engineer Interview Questions (4–6 YOE) – Sonata Software

## 1. How would you design a highly available production-grade microservices architecture across multiple environments?

I would design the architecture using separate environments such as Dev, QA/Staging, and Production to ensure proper isolation and controlled deployments. In AWS, I would use a Multi-AZ architecture with public and private subnets, Application Load Balancers, Auto Scaling Groups, and Kubernetes (EKS) for orchestration. For high availability, I would configure multiple replicas of microservices along with Horizontal Pod Autoscaler (HPA) and Multi-AZ RDS databases. CI/CD pipelines would be implemented using Jenkins or GitHub Actions with separate namespaces and configurations for each environment. For monitoring and security, I would use Prometheus, Grafana, centralized logging using ELK, IAM least privilege access, and secrets management tools such as AWS Secrets Manager or Vault.

## 2. A critical incident occurred in the production environment. How would you handle and troubleshoot it?

During a production incident, my first priority would be to identify the business impact and affected services. I would immediately check alerts, monitoring dashboards, logs, and infrastructure health to understand the issue. Then I would verify recent deployments or configuration changes and inspect application logs using commands like kubectl logs, kubectl describe pod, top, and journalctl. If the issue is deployment-related, I would perform a rollback to restore service quickly. After stabilization, I would conduct RCA analysis, document the findings, and implement preventive measures to avoid similar incidents in the future.

## 3. Can you explain the concept of immutable infrastructure and its advantages?

Immutable infrastructure means infrastructure components are never modified after deployment. Instead of patching or changing an existing server, a completely new server or container image is created and replaced. This approach reduces configuration drift, improves consistency across environments, simplifies rollback, and increases overall reliability. For example, instead of updating packages manually on a running VM, I would build a new AMI or Docker image and redeploy the application.

## 4. Can you explain different deployment strategies such as Blue-Green, Rolling, and Canary deployments?

Blue-Green deployment uses two identical environments where one serves production traffic while the other hosts the new version. Once validation is complete, traffic is switched to the new environment, which enables quick rollback. Rolling deployment gradually replaces old pods with new pods to minimize downtime during deployment. Canary deployment releases the new version to a small percentage of users initially, allowing monitoring and validation before full rollout.

## 5. What would you do if a Terraform deployment failed due to a state file lock?

If Terraform deployment fails because of a state file lock, I would first verify whether another Terraform process is currently running. Since remote state locking is commonly managed through DynamoDB, I would check the lock entry carefully. If the lock is stale and no process is using it, I would release it using terraform force-unlock after proper verification. I would avoid force unlocking without validation because it can cause state corruption.

## 6. What happens if a Terraform-managed resource is manually deleted outside Terraform but still exists in the code? How would you handle it?

If a Terraform-managed resource is manually deleted outside Terraform, Terraform detects this drift when terraform plan is executed. Since the resource still exists in the code but not in the actual infrastructure, Terraform will attempt to recreate it during terraform apply. Before applying changes, I would validate dependencies and ensure recreation does not impact production systems.

## 7. During terraform apply, if Terraform plans to delete critical resources unexpectedly, how would you handle that situation?

If Terraform unexpectedly plans to delete critical resources, I would immediately stop the execution and carefully review the terraform plan output. I would verify the Terraform state file, inspect recent code changes, and check for infrastructure drift. To protect important resources, I would use lifecycle rules such as prevent_destroy = true. I would also validate all changes in lower environments before deploying to production.

## 8. Can you write a shell script to monitor application health status and automatically restart the application if it is down?

I would create a shell script that continuously checks the application health endpoint using curl. If the application returns a non-200 status code, the script would restart the service automatically using systemctl restart. This helps reduce downtime and provides basic self-healing capabilities for applications.

## 9. If your CI/CD pipeline execution time increases significantly (for example, 1–1.5 hours), how would you optimize it?

To optimize a slow CI/CD pipeline, I would first identify bottleneck stages from pipeline logs. Then I would implement parallel execution of independent stages, enable Docker layer caching, use incremental builds, and optimize Docker image size. I would also reduce unnecessary test execution, use reusable Jenkins shared libraries, and configure distributed runners or agents to improve execution speed.

## 10. A Kubernetes pod is stuck in CrashLoopBackOff state. How would you troubleshoot and resolve the issue?

If a pod is in CrashLoopBackOff state, I would first check the pod logs and describe output to identify the root cause. Common reasons include incorrect environment variables, missing dependencies, application startup failures, port conflicts, or insufficient memory. I would verify resource limits, secrets, ConfigMaps, and health probes. After identifying the issue, I would apply the fix and redeploy the application.

## 11. How does proactive monitoring and alerting help reduce downtime in production environments?

Proactive monitoring and alerting help identify issues before they impact end users. Tools like Prometheus, Grafana, CloudWatch, and ELK continuously monitor infrastructure and application metrics such as CPU, memory, latency, error rates, and disk usage. Alerts integrated with Slack, email, or PagerDuty notify teams immediately when thresholds are breached. This allows faster troubleshooting, reduced MTTR (Mean Time to Resolution), and improved system reliability.
