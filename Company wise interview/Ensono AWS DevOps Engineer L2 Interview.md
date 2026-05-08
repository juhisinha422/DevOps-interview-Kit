# Ensono AWS DevOps Engineer L2 Interview Questions & Answers (4+ Years Experience)

## 1. Self Introduction

Hi, my name is Juhi Sinha, and I have around 4+ years of experience in Cloud and DevOps engineering. I have been primarily working on AWS cloud, Terraform, Docker, Kubernetes, Jenkins, and CI/CD automation. In my current role, I am responsible for infrastructure provisioning, deployment automation, monitoring, troubleshooting production issues, and optimizing cloud resources. I have hands-on experience in managing Linux servers, implementing Infrastructure as Code using Terraform, and working with Kubernetes deployments in production environments. I have also worked on DevSecOps tools like Checkov, Gitleaks, Dependabot, and pre-commit hooks for improving security and code quality. I enjoy solving production issues, automating repetitive tasks, and improving infrastructure reliability and scalability.

---

# 2. Client Raised a P1 Incident for Application Down. What Process Do You Follow?

Whenever I receive a P1 incident, my first priority is to acknowledge the ticket immediately and join the bridge call or incident channel to coordinate with all teams. Since it is a critical production issue, I start by understanding the impact, such as which application is affected, how many users are impacted, and whether the issue is complete downtime or intermittent.

Then I begin initial troubleshooting by checking monitoring tools like CloudWatch, Grafana, Datadog, or application dashboards. I verify the health of infrastructure components such as EC2 instances, Load Balancers, Auto Scaling Groups, Kubernetes pods, databases, and network connectivity.

I also check logs from the application, web server, and system level using tools like CloudWatch Logs, kubectl logs, journalctl, or application monitoring tools. If the issue is intermittent, I correlate logs and metrics with timelines to identify patterns like CPU spikes, memory issues, failed deployments, or network latency.

If a recent deployment happened, I verify whether the issue started after the deployment and perform rollback if necessary. I communicate regular updates to stakeholders and document all findings in the incident ticket.

Once the issue is resolved, I perform root cause analysis, document preventive actions, and suggest permanent fixes like monitoring improvements, auto-scaling tuning, or deployment validation checks to avoid future incidents.

---

# 3. What Things Can You Optimize for AWS Cost?

There are multiple ways to optimize AWS costs.

First, I identify underutilized resources such as idle EC2 instances, unattached EBS volumes, unused Elastic IPs, and old snapshots. I use AWS Cost Explorer, Trusted Advisor, and CloudWatch metrics for analysis.

For EC2 optimization, I resize instances based on utilization and move workloads to Graviton instances wherever possible for better pricing. For predictable workloads, I use Reserved Instances or Savings Plans.

I also implement Auto Scaling so resources scale dynamically based on traffic instead of running at full capacity all the time.

For storage optimization, I move infrequently accessed data to cheaper storage classes like S3 Intelligent-Tiering or Glacier. I also configure lifecycle policies to automatically delete old logs and backups.

In Kubernetes environments, I optimize node sizing, remove unused namespaces and resources, and configure cluster autoscaler.

I also optimize data transfer costs by minimizing cross-region traffic and using CloudFront CDN where applicable.

Additionally, I implement tagging strategies for cost allocation and regularly review AWS billing reports to identify unusual spikes or wastage.

---

# 4. How Do You Create an EC2 Instance via Terraform?

To create an EC2 instance using Terraform, first I define the AWS provider configuration with the required region. Then I create a resource block for the EC2 instance specifying parameters like AMI ID, instance type, key pair, subnet, security group, and tags.

After writing the code, I initialize Terraform using `terraform init`, validate the configuration using `terraform validate`, and check the execution plan using `terraform plan`. Finally, I provision the infrastructure using `terraform apply`.

I usually store Terraform state remotely in an S3 bucket with DynamoDB locking to avoid state conflicts in team environments.

Example flow:

* Write Terraform configuration
* Run `terraform init`
* Run `terraform fmt`
* Run `terraform validate`
* Run `terraform plan`
* Run `terraform apply`

I also follow modular Terraform architecture in production environments for better reusability and maintainability.

---

# 5. How Do You Manage AWS Resources Created Outside Terraform?

If AWS resources are created manually outside Terraform, I first write the Terraform configuration matching the existing infrastructure.

Then I use the `terraform import` command to import the existing resource into Terraform state. This helps Terraform start managing the resource without recreating it.

After importing, I run `terraform plan` to verify whether there are any configuration differences between the actual resource and Terraform code. If there is drift, I update the Terraform code accordingly until the plan shows no changes.

This approach helps bring manually created infrastructure under Infrastructure as Code management.

---

# 6. How Will You Provision Resources Across Multiple AWS Regions Using Terraform?

To provision resources in multiple AWS regions, I use multiple provider blocks with aliases in Terraform.

For example, one provider block can be for `us-east-1` and another for `ap-south-1`. Then while creating resources, I reference the required provider alias inside the resource block.

This allows the same Terraform project to deploy resources across different regions.

In large environments, I usually create reusable modules and pass provider aliases dynamically to keep the code scalable and maintainable.

This approach is useful for disaster recovery setups, multi-region deployments, and high availability architectures.

---

# 7. What Core AWS Services Have You Worked With?

I have worked extensively with AWS services including:

* EC2 for compute workloads
* S3 for object storage
* IAM for access management
* VPC for networking
* Route 53 for DNS management
* Load Balancers like ALB and NLB
* Auto Scaling Groups
* CloudWatch for monitoring and alerts
* RDS for managed databases
* EKS for Kubernetes workloads
* ECS for containerized applications
* Lambda for serverless automation
* CloudFront for CDN
* SNS and SQS for messaging
* Secrets Manager and Parameter Store
* CodePipeline and CodeBuild for CI/CD
* Terraform for Infrastructure as Code

I also have experience with security and compliance services like AWS Config, GuardDuty, and CloudTrail.

---

# 8. Were You Completely Managing Terraform or Also Planning Architecture?

Yes, I was involved not only in writing Terraform code but also in planning infrastructure architecture.

My responsibilities included designing reusable Terraform modules, defining networking architecture, planning VPCs, subnets, security groups, IAM policies, load balancing, auto-scaling, and disaster recovery strategies.

I also participated in infrastructure design discussions with application teams and architects to ensure scalability, security, cost optimization, and high availability.

Additionally, I handled Terraform state management, CI/CD integration for Terraform deployments, and code reviews following best practices.

---

# 9. How Do You Unmanage a Resource from Terraform?

If I no longer want Terraform to manage a resource but do not want to delete the actual resource from AWS, I use the `terraform state rm` command.

This command removes the resource only from the Terraform state file, while the actual infrastructure remains intact in AWS.

Example:

```bash
terraform state rm aws_instance.my_ec2
```

After removing it from the state, Terraform will stop tracking that resource.

However, if the resource block still exists in the Terraform code, Terraform will try to recreate it during the next `terraform plan` or `terraform apply`. So I also remove or comment out the corresponding resource block from the Terraform configuration.

---

# 10. After Removing Resource from State, Terraform Tries to Recreate It. How Do You Fix This Drift?

This happens because the resource definition is still present in the Terraform configuration file but the state no longer tracks it.

Terraform compares the code with the state file and assumes the resource does not exist, so it plans to create it again.

To fix this issue, I do one of the following based on the requirement:

* If I no longer want Terraform to manage the resource, I remove the resource block from the Terraform code as well.
* If I still want Terraform to manage the existing resource, I re-import the resource using `terraform import`.

This ensures Terraform state and actual infrastructure remain aligned and prevents configuration drift.

---

# 11. EC2 Instances in Auto Scaling Group Are Launching and Terminating Repeatedly Due to Health Check Failure. How Do You Investigate and Retain Logs?

First, I would check whether the failure is coming from EC2 health checks or Load Balancer health checks.

I review:

* Auto Scaling Group activity history
* EC2 system status checks
* Target group health status
* CloudWatch metrics
* Application logs
* User data execution logs
* Application startup failures

I connect to the instance if possible and check logs such as:

```bash
/var/log/cloud-init.log
/var/log/messages
/var/log/syslog
application logs
```

If the instance terminates too quickly, I temporarily suspend the Auto Scaling termination process using:

```bash
aws autoscaling suspend-processes
```

or detach the instance from the Auto Scaling Group while keeping it running for investigation.

Another approach is enabling instance scale-in protection so Auto Scaling does not terminate the instance immediately.

I also configure CloudWatch Agent or centralized logging so logs are pushed to CloudWatch before termination. This helps retain logs even if the instance is deleted.

Additionally, I verify:

* Security group rules
* Application port accessibility
* Health check endpoint responses
* Startup script failures
* Dependency failures like database connectivity

After identifying the root cause, I fix the issue and re-enable the Auto Scaling processes.

---

# Additional Tips for Ensono L2 Interview

* Focus on real-time troubleshooting scenarios.
* Explain Terraform state management clearly.
* Mention production support experience confidently.
* Highlight AWS cost optimization and incident handling.
* Show understanding of Auto Scaling, monitoring, and logging.
* Use terms like RCA, drift detection, HA, scaling, monitoring, and automation naturally in answers.
