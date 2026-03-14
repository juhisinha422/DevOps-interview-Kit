# DevOps Realtime Interview Questions - Answers (4+ Years Experience)

*********Devops realtime interview questions-2**********

1. You’ve got an app running in one AWS account that needs to access an S3 bucket in another account. How would you set that up securely?

Use cross-account IAM roles. Create an IAM role in the S3 bucket account and allow the application account to assume that role. Attach an S3 access policy to the role and update the S3 bucket policy to trust the role. The application assumes the role using AWS STS and receives temporary credentials to access the bucket securely.

--------------------------------------------------

2. Can you write a Dockerfile for a Node.js app using multi-stage builds — just something you’d actually use in a real project?

FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app .
EXPOSE 3000
CMD ["node","server.js"]

Multi-stage builds reduce image size and remove unnecessary build dependencies.

--------------------------------------------------

3. Suppose your Terraform state file gets corrupted or out of sync. What steps would you take to recover from that?

First check if a remote backend like S3 with versioning is enabled. Restore the previous working version of the state file. Run terraform refresh or terraform plan to verify infrastructure state. If resources are missing from state, re-import them using terraform import. Always keep remote state with locking enabled using DynamoDB.

--------------------------------------------------

4. You have an EC2 in a private subnet and can’t use a NAT Gateway. How else can it reach the internet for updates or downloads?

Possible solutions include using a NAT instance, configuring VPC endpoints for AWS services like S3, using a bastion host as a proxy, using AWS Systems Manager Session Manager, or setting up an internal proxy server.

--------------------------------------------------

5. A container has crashed or exited suddenly — how would you go about figuring out what went wrong?

Check container status using docker ps -a. Inspect logs using docker logs container_id. Use docker inspect to check exit codes and configuration. Check if the container was killed due to memory limits or CPU issues. Also verify application logs and resource usage.

--------------------------------------------------

6. There’s an existing AWS VPC created manually, but now you want to manage it through Terraform. How would you import it and make sure nothing breaks?

Write the Terraform configuration for the VPC resource first. Then import the existing VPC using terraform import aws_vpc.main vpc-id. After importing run terraform plan to confirm that Terraform configuration matches the actual infrastructure and does not attempt unwanted changes.

--------------------------------------------------

7. How would you roll out blue-green deployments in a Kubernetes cluster? What would that look like in practice?

Create two deployments: Blue (current version) and Green (new version). Deploy the new version and test it. Once verified, update the Kubernetes service selector to point to the green deployment. This instantly shifts traffic to the new version. If issues occur, switch back to blue.

--------------------------------------------------

8. When using Terraform, how do you manage sensitive values like secrets or API keys without hardcoding them?

Use AWS Secrets Manager, AWS Systems Manager Parameter Store, HashiCorp Vault, environment variables, or Terraform variables marked as sensitive. Secrets should never be committed to version control.

--------------------------------------------------

9. In a Dockerfile, what’s the practical difference between using COPY and ADD? When would you prefer one over the other?

COPY simply copies files from the host to the container. ADD can also extract tar files and download files from URLs. Best practice is to use COPY unless you specifically need ADD features.

--------------------------------------------------

10. If you had to create resources across two different AWS accounts using Terraform, how would you set that up?

Use multiple provider configurations with aliases. Each provider represents a different AWS account. Resources can reference the specific provider alias to create infrastructure in the correct account.

--------------------------------------------------

11. Imagine you have a PHP app in a Docker container that needs MySQL credentials — how would you pass those securely?

Use environment variables, Docker secrets, Kubernetes secrets, or external secret managers such as AWS Secrets Manager. Avoid hardcoding credentials in the Dockerfile or source code.

--------------------------------------------------

12. If someone manually changed an S3 bucket policy that was originally created with Terraform, how would you deal with that kind of drift?

Run terraform plan to detect configuration drift. Terraform will show the differences between the state file and actual infrastructure. Then either reapply the Terraform configuration using terraform apply or update the Terraform code if the manual change was intended.

--------------------------------------------------

13. How do you enforce rules in Kubernetes to control which pods can talk to each other?

Use Kubernetes Network Policies. Network policies define rules that allow or block communication between pods based on labels, namespaces, or IP blocks. This helps enforce micro-segmentation within the cluster.

--------------------------------------------------

14. Could you write a small Python script that backs up all files older than 30 days from a folder?

import os
import shutil
import time

source="/data/files"
backup="/data/backup"
now=time.time()

for file in os.listdir(source):
 path=os.path.join(source,file)

 if os.stat(path).st_mtime < now-30*86400:
  shutil.copy(path,backup)

--------------------------------------------------

15. If your team is seeing a spike in cloud costs, how would you go about figuring out why — and cutting cost without hurting performance?

Use AWS Cost Explorer to identify services causing the increase. Check unused EC2 instances, unattached EBS volumes, idle load balancers, and unused storage. Review autoscaling policies, right-size instances, enable reserved instances or savings plans, and remove unused resources.

--------------------------------------------------

16. Say you want to serve users in different countries using AWS. How would you route traffic based on user location?

Use Amazon Route53 geolocation routing policy or latency-based routing. Route53 directs users to the nearest or region-specific endpoints. CloudFront CDN can also cache content globally for faster delivery.

--------------------------------------------------

17. You’re on-call. A production Kubernetes cluster is a mess — pods aren’t pulling images, some are getting evicted, and users are seeing errors. How do you troubleshoot this, and how would you prevent it next time?

Start by checking cluster health using kubectl get nodes and kubectl get pods -A. Inspect failing pods with kubectl describe pod and kubectl logs. Look for issues like ImagePullBackOff, OOMKilled, node disk pressure, or network failures. Verify registry access, resource limits, and node capacity. For prevention, implement proper resource requests and limits, cluster autoscaling, monitoring with Prometheus and Grafana, and alerting for early detection.
