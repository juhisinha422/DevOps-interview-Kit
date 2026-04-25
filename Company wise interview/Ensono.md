# 🚀 AWS DevOps Interview Guide – Ensono (4 Years Experience)

---

## 1. Self intro

I am a Cloud DevOps Engineer with around 4 years of experience working on AWS and modern DevOps tools. I have hands-on experience in building and managing CI/CD pipelines using Jenkins, containerization using Docker, and orchestration with Kubernetes. I have also worked extensively with Terraform for infrastructure as code, where I manage multiple environments and handle state using remote backends like S3 with DynamoDB for locking. My daily responsibilities include automating deployments, monitoring systems, troubleshooting production issues, and ensuring high availability and scalability of applications. I also focus on implementing best practices around security, cost optimization, and reliability.

---

## 2. How you handle the state management in your environment?

In my environment, I manage Terraform state using a remote backend, typically AWS S3, with DynamoDB enabled for state locking. This ensures that the state file is centralized, secure, and prevents concurrent modifications by multiple users. I enable versioning on the S3 bucket to maintain history and allow recovery in case of accidental changes or corruption. Access to the state file is controlled using IAM policies. This setup ensures consistency, collaboration, and safety in managing infrastructure.

---

## 3. where do we use the Terraform workspaces? What is the exact use of it?

Terraform workspaces are used to manage multiple environments using the same Terraform configuration. Each workspace maintains its own separate state file, allowing us to deploy identical infrastructure for environments like dev, QA, and prod without duplicating code. This helps in maintaining consistency while isolating resources. However, for complex environments, I usually combine workspaces with separate backend configurations or directory structures for better control.

---

## 4. you have a workspace for dev QA product. so unfortunately, you've deleted the prod workspace, so what will happen now?

If the production workspace is deleted, Terraform will lose reference to the associated state in that workspace, meaning it no longer tracks the infrastructure created for production. However, the actual infrastructure resources in AWS will still exist because deleting a workspace does not delete resources. The main issue is that Terraform no longer knows about them, which can lead to inconsistencies or accidental recreation if not handled properly.

---

## 5. you have stored everything in S3 back-end, So only workspace is deleted. So can we bring that state once again in a new workspace? Will it work?

Yes, since the state file is stored in S3, it is still available. We can recreate the workspace and point it to the correct state file by configuring the backend properly. If needed, we can manually pull the state using `terraform state pull` and push it back using `terraform state push`. Once the workspace is reconnected to the correct state, Terraform will resume managing the existing infrastructure without recreating resources.

---

## 6. what happens if a provider is getting upgraded? for example, you are in an AWS 7.5 provider, and that needs to be updated to 7.6. It's a major version. So how do you plan for this?

When upgrading a provider, I first review the release notes to understand breaking changes and deprecated features. Then I update the provider version in the Terraform configuration and run `terraform init -upgrade` to fetch the new version. I always test changes in a lower environment like dev or QA before applying to production. Running `terraform plan` helps identify any changes or issues caused by the upgrade. If everything looks stable, I proceed with production deployment. Backup of state and rollback plans are also kept ready in case of unexpected issues.

---

## 7. you have a S3 bucket, so that needs to be provided access. So you can do it in two ways. One is from IAM perspective, you can provide a IAM policy.

Access to an S3 bucket can be managed in two main ways: using IAM policies or bucket policies. IAM policies are attached to users, groups, or roles and define what actions they can perform on the bucket. Bucket policies are attached directly to the S3 bucket and define who can access it and under what conditions. In practice, IAM policies are used for internal access control, while bucket policies are useful for cross-account access or public access configurations. A combination of both provides flexible and secure access management.

---

## 8. you have a Linux server, this has been deployed via Terraform like a month ago with the data volume through Terraform itself. So currently it is sizing with 500 GB. the application team has started using it and they wanted to extend the block system to 750 GB. so now you are going into the your own workspace in Terraform and reviewing the code and you are changing the additional disk volume size GB from 500 to 750 GB. Okay, if you run a Terraform plan and apply, what will happen here?

When the disk size is increased from 500 GB to 750 GB, Terraform will detect this change and attempt to modify the existing volume. In AWS, increasing the size of an EBS volume is supported without replacing the resource, so Terraform will perform an in-place update. After the volume is expanded, additional steps may be required at the OS level to extend the file system so the application can use the extra space. There will be no data loss, but proper validation is required to ensure the application continues to function correctly.

---

## 9. Diffrence between Private VPC and Public VPC?

A public VPC (or more accurately, a public subnet within a VPC) contains resources that have direct access to the internet via an Internet Gateway. These resources can have public IP addresses and are accessible externally. A private VPC (private subnet) contains resources that do not have direct internet access and are typically used for backend systems like databases. These resources access the internet through a NAT Gateway if needed. The separation improves security by isolating sensitive components.

---

## 10. you have a RDS database. So there is a concept called Multi-AZ. and there is a concept called Read Replicas. So what is the difference between these two?

Multi-AZ is used for high availability and disaster recovery. It creates a standby instance in another availability zone and automatically fails over in case of failure, but it does not improve read performance. Read Replicas, on the other hand, are used for scaling read traffic. They create copies of the primary database that can serve read requests, improving performance. However, failover to a read replica is not automatic and requires manual intervention.

---

## 11. for example, there is an EC2 maintenance that's been called out for after two weeks, so there is a notification has been published on the AWS dashboard for a particular EC2 instance. So now that fault has a critical change window, so you don't want that set last deadline as per the notification, but you don't want to do that. So is there a way to postpone this?

AWS scheduled maintenance events usually have a deadline, and they cannot be postponed indefinitely beyond that deadline. However, we can choose a convenient time within the given window to perform the maintenance manually, such as stopping and starting the instance or rebooting it to apply updates earlier. This allows us to control the timing and avoid unexpected downtime.

---

## 12. what are the cost optimization initiatives you have taken in your current environment?

Cost optimization initiatives include rightsizing EC2 instances based on usage metrics, using reserved or savings plans for predictable workloads, and shutting down unused resources like idle instances or unattached volumes. I also implement auto-scaling to match demand and use spot instances where appropriate. Storage optimization includes lifecycle policies for S3 and deleting unused snapshots. Monitoring tools like AWS Cost Explorer help track and optimize spending continuously.

---

## 13. Explain where the Jenkins have been used in your environment?

In my environment, Jenkins is used as the central CI/CD tool to automate build, test, and deployment processes. It integrates with Git for code changes, triggers pipelines using webhooks, builds Docker images, performs code quality checks using SonarQube, and pushes artifacts to repositories. Jenkins then deploys applications to Kubernetes or cloud environments. It also handles rollback, approvals, and environment-specific deployments, ensuring consistent and automated delivery pipelines.

---

## 🚀 Final Tip

For this level of interview:

* Always explain with **real scenarios + AWS services**
* Show **Terraform + AWS + CI/CD integration**
* Focus on **production thinking (HA, cost, security)**

---
.
