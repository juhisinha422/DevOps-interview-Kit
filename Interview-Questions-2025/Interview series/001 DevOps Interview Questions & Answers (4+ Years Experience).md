# 📘 DevOps Interview Questions & Answers (4+ Years Experience)

---

# 1️⃣ AWS & Cloud

### 1. Consider a VPC with only one subnet. There are no route tables, no security groups, no configurations — it’s completely empty. Do you think it is a private subnet or a public subnet?

By default, it is a **private subnet** because there is no route to an Internet Gateway.

---

### 2. What is EBS and EFS? Explain clearly.

* **EBS (Elastic Block Store):** Block storage attached to EC2, used like a disk.
* **EFS (Elastic File System):** Shared file storage accessible by multiple EC2 instances.

---

### 3. How do you set up a highly available application?

* Use Multi-AZ deployment
* Load Balancer (ALB)
* Auto Scaling Group
* RDS Multi-AZ
* Health checks

---

### 4. How many accounts have you managed in your project, and how do you configure them with Jenkins?

Managed multiple AWS accounts using IAM roles and integrated Jenkins using cross-account role assumption.

---

### 5. If you are not able to access the EC2 instance, what might be the issues?

* Security group blocking port 22
* NACL rules
* Wrong key pair
* Instance stopped
* SSH service not running

---

### 6. What is your project architecture?

ALB → EC2/EKS → RDS, with S3 for storage and CloudWatch for monitoring.

---

### 7. How many AWS accounts have you managed in your project?

Typically managed 2–5 accounts (Dev, QA, Stage, Prod).

---

### 8. How many types of S3 buckets are there?

* Standard
* Versioning enabled
* Lifecycle configured
* Static hosting

---

### 9. What are Security Groups and NACLs, and what are the differences between them?

* SG: Instance-level, stateful
* NACL: Subnet-level, stateless

---

### 10. How do you reduce AWS costs in your project?

* Reserved/Spot instances
* Auto scaling
* Right sizing
* S3 lifecycle

---

### 11. What is VPC peering and Transit Gateway, and how do you set them up?

* Peering: direct VPC connection
* Transit Gateway: hub-and-spoke architecture

---

### 12. Have you used load balancers? What kinds of load balancers have you used, and why are they important?

Used ALB and NLB for traffic distribution, high availability, and fault tolerance.

---

### 13. How do you set up a highly available application?

Multi-AZ, Load Balancer, Auto Scaling, health checks.

---

### 14. What is a NAT Gateway, and how does it work?

Allows private subnet instances to access internet without exposing them.

---

### 15. If an EC2 instance has 50GB storage, how do you attach extra storage to an existing instance?

Create and attach EBS volume, then mount it.

---

### 16. What S3 buckets have you used?

Used for logs, backups, static websites, and artifact storage.

---

### 17. Have you worked with AWS Lambda functions? How did you use them in your projects?

Used for serverless automation, event-driven processing, and CI/CD tasks.

---

### 18. How many organizations are using AWS Lambda instead of EC2, and in which scenarios would you prefer Lambda over EC2?

Used when event-driven, short-lived, serverless workloads are required.

---

### 19. How do you design AWS platforms for production workloads?

High availability, scalability, security, monitoring, and cost optimization.

---

### 20. How do you handle AWS account and environment separation?

Separate accounts for Dev, QA, Prod using IAM roles.

---

### 21. How do you design a multi-tier network where the database has zero internet access?

DB in private subnet, no IGW, accessed via app layer.

---

### 22. ALB vs NLB — when do you use each?

* ALB → HTTP/HTTPS
* NLB → TCP/low latency

---

# 2️⃣ Jenkins & CI/CD

### 1. What are parameters in Jenkins?

Used to pass dynamic values at runtime.

---

### 2. What are shared libraries and why is it used?

Reusable pipeline code across projects.

---

### 3. Jenkins architecture

Master-agent architecture.

---

### 4. How to configure cluster in Jenkins

Using multiple agents connected to master.

---

### 5. How do secure jenkins pipeline while running

Use credentials, RBAC, secrets masking.

---

### 6. If you got issue when deploy stage what steps do you follow

Check logs, validate configs, rollback.

---

### 7. What branching strategies are you using?

GitFlow (dev → qa → prod).

---

### 8. How to reusable the jenkins pipeline

Using shared libraries.

---

### 9. How do you trigger a pipeline, how many types we have.

Manual, webhook, Poll SCM.

---

### 10. How many pipelines we have in Jenkins.

Freestyle and Pipeline (Declarative/Scripted).

---

### 11. How many CICD stages will you follow in your current project?

Build → Test → Scan → Deploy.

---

### 12. Where do you store documentation from your current project?

Confluence, GitHub.

---

### 13. How do you share jenkins file with your colleagues

Git repository.

---

### 14. How do you improve the jenkins cicd process

Parallel builds, caching, automation.

---

### 15. If nodes are not active or not working how do fix it, for example master node and slave node.

Check connectivity, restart agent, verify configs.

---

### 16. Write a parallel Jenkins pipeline (Jenkinsfile)

```groovy
stage('Parallel') {
  parallel {
    stage('A'){ steps { echo 'A' } }
    stage('B'){ steps { echo 'B' } }
  }
}
```

---

### 17. How to deploy a .NET application using a CI/CD pipeline

Build using MSBuild → package → deploy to IIS/Azure.

---

### 18. Common issues faced during CI/CD pipelines and how to resolve them

Dependency, credential, network issues.

---

### 19. Debugging steps when an application is not accessible from the web

Check DNS, LB, firewall, logs.

---

### 20. What are the different ways to run and build pipelines? Explain Poll SCM and Webhooks.

* Poll SCM → periodic check
* Webhook → event-based trigger

---

### 21. How do you run multiple jobs in Jenkins?

Parallel execution or pipelines.

---

### 22. If your Jenkins pipeline failed because the artifact is not accessible due to credential issues. What could be the possible reasons?

Wrong credentials, expired token, permission issue.

---

### 23. If you launched the service and installed Jenkins, but Jenkins is not accessible, what might be the reason?

Port blocked, service down, firewall.

---

### 24. How do you install plugins, and what are the steps to install plugins?

Manage Jenkins → Plugins → Install → Restart.

---

### 25. What are pipeline stages, and can you write them?

Stages define steps like build/test/deploy.

---

### 26. How do you migrate Jenkins from one server to another?

Backup JENKINS_HOME → restore.

---

### 27. How do you store credentials in Jenkins?

Credentials Manager.

---

### 28. For example, Docker credentials.

Store in Jenkins credentials and use in pipeline.

---

### 29. How do you store username and password securely?

Use encrypted credentials store.

---

# 3️⃣ Docker

### 1. How do you reduce Docker images?

* Use lightweight base images (Alpine)
* Multi-stage builds
* Remove unnecessary packages
* Clean cache

---

### 2. What is docker volumes

Docker volumes are used for **persistent data storage** outside the container lifecycle.

---

### 3. Explain how to write a docker image for nodejs

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["node","app.js"]
```

---

### 4. How do you go into the container, what is the command to use?

```bash
docker exec -it <container_id> /bin/bash
```

---

### 5. What is docker architecture

* Docker Client
* Docker Daemon
* Docker Registry

---

### 6. What is cmd and entrypoint

* CMD → default command
* ENTRYPOINT → fixed execution

---

### 7. How do you pass the port in Docker when writing a Dockerfile?

Using EXPOSE instruction

```dockerfile
EXPOSE 3000
```

---

### 8. What is CMD and ENTRYPOINT in Docker?

CMD provides default command, ENTRYPOINT makes container behave like executable.

---

### 9. Docker image optimization & reduse strategies

* Minimize layers
* Use .dockerignore
* Multi-stage builds

---

### 10. How to reduce Docker image size

Use Alpine, remove cache, combine RUN commands.

---

### 11. Docker volumes and their use cases

Used for database storage, logs, shared data.

---

### 12. Difference between COPY and ADD instructions

* COPY → simple file copy
* ADD → supports URL and auto extraction

---

### 13. How to share Dockerfiles with team members

Using Git repositories.

---

### 14. How to check running vs stopped containers

```bash
docker ps
docker ps -a
```

---

### 15. What Docker steps do you follow to create an image?

Write Dockerfile → build image → run container.

---

### 16. Just explain Dockerfile creation.

Defines base image, dependencies, commands, ports, and entry point.

---

### 17. What is Docker Compose?

Tool to manage multi-container applications using YAML.

---

### 18. How do you reduce or reuse Docker image size?

Use base images, caching layers, multi-stage builds.

---

### 19. Write a Docker Compose file.

```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

---

# 4️⃣ Kubernetes

### 1. I have 5 pods — 3 are running and 2 are not running. What might be the issue?

* Image pull error
* Resource limits exceeded
* Node issue
* CrashLoopBackOff

---

### 2. Pod vs ReplicaSet vs Deployment — which one should you choose for application deployment?

Deployment is preferred as it manages ReplicaSets and supports rolling updates.

---

### 3. Cluster security.

* RBAC
* Network policies
* Secrets management
* IAM roles

---

### 4. If one service is consuming too much CPU and memory, how do you check which component is causing high usage?

Use `kubectl top pod/node` and monitoring tools.

---

### 5. What is configmap and secrets

ConfigMap → non-sensitive config
Secrets → sensitive data

---

### 6. What is statefulset and daemonset

StatefulSet → stateful apps
DaemonSet → runs on all nodes

---

### 7. How do you scale up min and max replicas

Using HPA or manual scaling.

---

### 8. High availability for eks cluster

Multi-AZ nodes, load balancer, autoscaling.

---

### 9. How to configure monitoring to eks cluster.

Prometheus + Grafana + CloudWatch.

---

### 10. Difference between Kubernetes and Docker Swarm

Kubernetes is more advanced and scalable.

---

### 11. What is ReplicaSet

Ensures desired number of pods are running.

---

### 12. Writing a basic Pod manifest file

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
    - name: app
      image: nginx
```

---

### 13. Static Pods explanation

Managed directly by kubelet, not API server.

---

### 14. HPA vs VPA (Horizontal vs Vertical Pod Autoscaling)

HPA scales pods, VPA scales resources.

---

### 15. Node troubleshooting (checking active/inactive nodes)

```bash
kubectl get nodes
kubectl describe node
```

---

### 16. High availability architecture for applications

Multiple replicas + load balancing.

---

### 17. What happens after running Node?

Node joins cluster and runs pods.

---

### 18. Write a Deployment manifest file?

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app
```

---

### 19. What happens internally when you apply a Kubernetes manifest file?

API server validates → etcd stores → scheduler assigns → kubelet runs pods.

---

### 20. What is taint and toleration in Kubernetes?

Controls which pods can run on nodes.

---

### 21. What load balancers are you using in Kubernetes, and why are you using them in your project?

ALB/NLB for external traffic routing.

---

### 22. How many Kubernetes service types are there, and how do you use them?

ClusterIP, NodePort, LoadBalancer.

---

### 23. Why do we use an Ingress service in Kubernetes?

For HTTP routing and domain-based access.

---

### 24. What is the default namespace in Kubernetes?

default

---

### 25. How do two pods or two namespaces communicate?

Using services, DNS.

---

### 26. How do you troubleshoot and fix a CrashLoopBackOff error?

Check logs, fix configs, restart pod.

---

### 27. How do you design a highly available production application using Kubernetes?

Multi-replica, autoscaling, load balancing.

---

### 28. Can you write a Kubernetes manifest file for a Horizontal Pod Autoscaler (HPA)?

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  minReplicas: 2
  maxReplicas: 10
```

---

### 29. How do you design a secure Amazon EKS cluster architecture?

IAM roles, private nodes, RBAC, network policies.

---

### 30. What is the node scale-up and scale-down time? How do you define minimum and maximum nodes?

Defined in Auto Scaling Group settings.

---

### 31. How do you secure workloads in EKS?

IAM roles, secrets, security groups.

---

### 32. Explain IRSA and why it’s important.

IAM Roles for Service Accounts allows pods to access AWS securely.

---

### 33. How do you expose applications in EKS?

Using services, ingress, load balancers.

---

### 34. Cluster Autoscaler vs Karpenter?

Autoscaler adjusts nodes, Karpenter dynamically provisions.

---

### 35. Can you write a script to detect NotReady nodes in an EKS cluster with proper error handling?

```bash
#!/bin/bash
nodes=$(kubectl get nodes --no-headers | grep NotReady)
if [ -z "$nodes" ]; then
  echo "All nodes are healthy"
else
  echo "NotReady nodes found:"
  echo "$nodes"
fi
```

---

# 5️⃣ Terraform

### 1. When creating EC2, S3, Lambda — how are resources created? Parallel or sequential? How does Terraform create resources?

Parallel execution unless dependencies defined.

---

### 2. How do you create 10 resources one by one?

Use count or for_each.

---

### 3. How do you secure the Terraform state file?

Use S3 backend + encryption + DynamoDB locking.

---

### 4. Terraform lifecycle, terraform refresh, terraform import

Lifecycle defines behavior, refresh syncs state, import brings existing resource.

---

### 5. Writing a VPC creation Terraform script

Basic script with CIDR, subnets, IGW.

---

### 6. Recommended Terraform project folder structure

modules/, env/, main.tf, variables.tf.

---

### 7. terraform destroy vs terraform taint

Destroy deletes all, taint recreates resource.

---

### 8. Securing Terraform state files

Encryption + access control.

---

### 9. What is Terraform drift?

Difference between actual infra and state file.

---

### 10. If two people are working on the same Terraform module, how does it work?

Use remote backend and locking.

---

### 11. How do you remove a Terraform lock file so the next person can apply?

Delete lock from DynamoDB or use force-unlock.

---

### 12. If someone created a resource from the AWS console, how do you sync it with the current Terraform state?

Use terraform import.

---

### 13. When you change a Terraform module (e.g., S3), what happens during the terraform plan?

Shows changes before apply.

---

### 14. How do you create 50 instances using Terraform?

Use count = 50.

---

### 15. What is “dynamic” in Terraform and where is it used in real projects?

Used to generate repeated blocks dynamically.

---

### 16. If resources are created via AWS Console/CLI, how do you bring them under Terraform without recreating them?

terraform import.

---

### 17. How do you securely store DB usernames and passwords using Terraform?

Use Secrets Manager or Vault.

---

### 18. How do you create and consume AWS Secrets Manager in CI/CD pipelines?

Store secrets → fetch during pipeline execution.

---

### 19. In production, when EBS storage is full, how do you extend storage without downtime?

Increase volume size → resize filesystem.

---

### 20. How do you manage Terraform state in production?

Remote backend + locking.

---

### 21. How do you structure Terraform modules?

Reusable modules with inputs/outputs.

---

### 22. Have you used Terragrunt or Terraform testing?

Yes, used Terragrunt for managing environments.

---

### 23. How do you manage Terraform state locking and safely run Terraform in CI/CD pipelines?

DynamoDB locking and pipeline controls.

---

### 24. How do you design reusable Terraform modules while supporting different environments?

Use variables and workspaces.

---

### 25. How did you use Terraform workspaces in your project? Can you explain the folder structure?

Separate environments using workspaces.

---

### 26. Can you write a Terraform module for creating a VPC?

Includes VPC, subnets, IGW, route tables.

---

# 6️⃣ Git

### 1. git merge

Combines branches.

---

### 2. git pull

Fetch + merge.

---

### 3. git fetch

Downloads changes.

---

### 4. git conflict

Occurs when merging changes.

---

### 5. What are the commands called git cherry-pick, git config, and git rebase?

* cherry-pick → apply commit
* config → set config
* rebase → rewrite history

---

### 6. PR and MR requests — how do they work?

Code review process before merging.

---

# 7️⃣ Monitoring & Observability

### 1. What are the activities from monitoring

Metrics, logs, alerts.

---

### 2. Do you know how to configure monitoring to eks cluster.

Prometheus + Grafana setup.

---

### 3. How monitoring tools collect metrics

Agents scrape/export metrics.

---

### 4. Real-world monitoring strategies

Alerting, dashboards, auto-healing.

---

### 5. What observability stack have you used?

Prometheus, Grafana, ELK.

---

# 8️⃣ Linux / Scripting / Automation

### 1. Are you able to write shell scripts, if I give a scenario.

Yes, used for automation tasks.

---

### 2. Do you have exp in python?

Yes, for automation and scripting.

---

### 3. Crontab usage with scheduling scenarios

Used for scheduling jobs.

---

### 4. Write a Bash script to create and write log files in the current directory.

```bash
#!/bin/bash
echo "Log entry $(date)" >> app.log
```

---

# 9️⃣ Security & DevSecOps

### 1. How do you secure the Terraform state file?

S3 encryption + IAM + locking.

---

### 2. Cluster security.

RBAC, network policies.

---

### 3. How do you manage secrets?

Secrets Manager, Vault.

---

### 4. What security checks do you add in pipelines?

SAST, DAST, dependency scan.

---

### 5. How do you design IAM policies?

Least privilege principle.

---

### 6. AWS IAM policies & writing a sample IAM JSON policy

Define actions, resources, effect.

---

### 7. How do you store passwords in terraform, what is the best practice you followed secure the passwords.

Use Secrets Manager.

---

### 8. If your colleague tries to upload your project’s important files to their personal account, how would you secure against this type of threat?

DLP, access control, monitoring.

---

### 9. What is Policy as Code?

Managing policies using code (OPA, Sentinel).

---

# 🔟 Project / Process / Agile

### 1. What is agile methodology, and what about sprints release.

Iterative development with sprint cycles.

---

### 2. How jira was used, and explain to me.

Used for tracking tasks, bugs, sprints.

---

### 3. Explain your project architecture

Microservices with CI/CD pipeline and cloud infra.

---

### 4. CI/CD flow followed in your project

Code → Build → Test → Deploy.

---

### 5. Branching strategy (Dev, QA, Staging, Prod)

Environment-based branching.

---

### 6. How do you manage promotions across environments?

Automated pipelines with approvals.

---

### 7. GitOps with Argo CD — how does it work?

Uses Git as source of truth.

---

### 8. What advanced Argo CD patterns have you used?

App of apps, sync waves.

---

### 9. Discuss how AI agents are important in modern technology.

Used for automation, monitoring, predictive analysis.

---

# 1️⃣1️⃣ Ansible

### 1. What is Ansible and how does it work?

Configuration management tool using agentless architecture.

---

### 2. What is idempotency?

Same result even if run multiple times.

---

### 3. Difference between playbook and ad-hoc commands.

Playbook → reusable YAML
Ad-hoc → one-time command

---

### 4. Difference between command and shell module.

command → no shell
shell → supports shell features

---

### 5. What are roles in Ansible?

Reusable components for structuring playbooks.

---

### 6. What is an Ansible Vault?

Used to encrypt sensitive data.

---

### 7. What is inventory?

List of managed hosts.

---




