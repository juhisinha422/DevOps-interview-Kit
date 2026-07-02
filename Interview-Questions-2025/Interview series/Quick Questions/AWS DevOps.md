## If I assign a /24 CIDR to a VPC, how many usable IPs are there?

A /24 provides 256 IPs – right.

But AWS reserves 5 IPs per subnet.

These include: network address, broadcast, router, DNS, and one more for future use.

So, /24 gives you only 251 usable IPs, not 256.

<img width="800" height="576" alt="Image" src="https://github.com/user-attachments/assets/b13e1da0-9b96-46ec-bee6-70fec4d12aea" />

# What is the next step after deployment?

What did you deploy?
Was it infrastructure changes, application changes, or database changes? Based on what was deployed, you should verify that the expected changes have been applied successfully and validate that everything is working as intended.

# DevOps Engineer Interview Questions & Answers (Capgemini)


# 1. What is the CI/CD process in your project?

In my current project, we follow a fully automated CI/CD pipeline that enables faster, reliable, and consistent software delivery. The process starts when a developer creates a feature branch from the main branch and begins working on a new feature or bug fix. Once development is completed, the developer raises a Pull Request, which undergoes peer review to ensure coding standards, security practices, and business requirements are met. After approval, the code is merged into the main branch. This merge automatically triggers our Jenkins pipeline through Git webhooks.

The Continuous Integration phase begins with Jenkins checking out the latest source code from GitHub. It installs project dependencies, compiles the application if necessary, and executes unit tests to validate the code. We also integrate SonarQube into the pipeline to perform static code analysis, identify code smells, bugs, duplicated code, and security vulnerabilities. If any quality gate fails, Jenkins immediately stops the pipeline and sends notifications to the development team through Microsoft Teams and email. This ensures that only high-quality code progresses further in the deployment process.

Once the code passes all validation stages, Jenkins builds a Docker image using the project's Dockerfile. The Docker image contains the application, runtime environment, libraries, and dependencies, ensuring that the application behaves consistently across all environments. The image is tagged using the Jenkins build number or Git commit hash to maintain version traceability. After the image is built successfully, Jenkins pushes it to Amazon Elastic Container Registry (ECR), which serves as our centralized container image repository.

The infrastructure required for deployment is managed entirely through Terraform. Instead of manually provisioning AWS resources, Terraform creates and updates VPCs, IAM Roles, Security Groups, EC2 instances, Load Balancers, EKS clusters, Route53 records, and other cloud resources. Since Terraform follows Infrastructure as Code, every infrastructure change is version-controlled, peer-reviewed, and reproducible. Before applying any infrastructure changes, we execute `terraform plan` to review the proposed modifications and ensure no unintended resources will be affected.

During the Continuous Deployment stage, Jenkins deploys the newly built Docker image to Amazon EKS using Helm charts. Kubernetes performs rolling updates, gradually replacing old application pods with new ones while maintaining application availability. Readiness and liveness probes ensure that traffic is routed only to healthy pods. After deployment, automated smoke tests validate that critical application functionality is working correctly. Monitoring tools such as Prometheus, Grafana, and AWS CloudWatch continuously monitor pod health, application latency, CPU utilization, memory consumption, and error rates. If any critical issue is detected after deployment, we perform an automated or manual rollback to the previous stable version. This entire CI/CD process enables multiple deployments every week while minimizing downtime, reducing manual effort, improving deployment consistency, and accelerating software delivery.

---

# 2. What is UAT?

UAT stands for User Acceptance Testing, which is the final testing phase before an application is released to production. Unlike unit testing, integration testing, or system testing, which are performed by developers and QA engineers, UAT is conducted by business users or clients to verify that the application satisfies business requirements and behaves as expected in real-world scenarios. The objective of UAT is not to identify coding defects but to confirm that the delivered solution meets customer expectations and supports business processes correctly.

In our project, once all automated testing phases have been completed successfully, Jenkins deploys the application to the UAT environment. This environment closely resembles production in terms of infrastructure, application configuration, database schema, networking, and security policies. Business users then execute predefined test cases that simulate actual business workflows. They validate reports, dashboards, user interfaces, API responses, integrations with third-party systems, authentication flows, and business rules. If any issues are discovered during UAT, they are documented, prioritized, and assigned back to the development team for resolution before the application can be approved for production deployment.

As a DevOps engineer, my responsibilities during UAT include provisioning the required cloud infrastructure using Terraform, deploying the application through Jenkins pipelines, configuring Kubernetes resources, managing environment-specific configurations using ConfigMaps and Secrets, monitoring application health, and resolving any deployment or infrastructure issues encountered by testers. Once all UAT test cases pass and business stakeholders provide formal approval, the same CI/CD pipeline promotes the application to the production environment following the organization's release approval process.

---

# 3. Explain Docker.

Docker is an open-source containerization platform that allows applications and all their dependencies to be packaged into lightweight, portable containers. Unlike traditional virtual machines, Docker containers share the host operating system kernel, making them significantly faster to start and much more resource efficient. Containers ensure that applications behave consistently across development, testing, UAT, and production environments by eliminating environment-specific differences.

In my current project, developers create Dockerfiles that define the application environment, including the base image, required packages, environment variables, startup commands, and configuration files. During the Jenkins pipeline, Docker builds an image using this Dockerfile. Each image is tagged with the build number or Git commit ID and pushed to Amazon Elastic Container Registry (ECR). Kubernetes running on Amazon EKS then pulls the required image version and deploys it as application pods.

To improve efficiency, we use multi-stage Docker builds, which separate the build environment from the runtime environment, significantly reducing image size. We also use lightweight base images such as Alpine Linux whenever possible to reduce vulnerabilities and improve deployment speed. Docker layer caching is implemented in the CI pipeline to avoid rebuilding unchanged layers, reducing build times considerably. Before deployment, container images are scanned using vulnerability scanning tools to identify outdated packages and security risks. Docker has greatly simplified application deployment by providing environment consistency, faster deployments, simplified dependency management, efficient resource utilization, and improved scalability.

---

# 4. Explain Jenkins.

Jenkins is an open-source automation server used to implement Continuous Integration and Continuous Deployment. It automates repetitive software delivery tasks, enabling faster, more reliable, and consistent application deployments. In our organization, Jenkins serves as the central orchestration tool responsible for executing the entire CI/CD pipeline.

Whenever developers push code to GitHub, a webhook automatically triggers the Jenkins pipeline. Jenkins checks out the latest source code, installs project dependencies, compiles the application, executes unit tests, performs SonarQube code quality analysis, runs security scans, builds Docker images, pushes those images to Amazon ECR, provisions or updates infrastructure using Terraform when necessary, and deploys the application to Kubernetes using Helm charts. All pipeline stages are defined in a Jenkinsfile, allowing pipeline configurations to be version controlled alongside application code.

To reduce build time, we use multiple Jenkins agents that execute independent stages in parallel. Sensitive credentials such as AWS access keys, Kubernetes kubeconfig files, SSH keys, and API tokens are securely stored using Jenkins Credentials Manager instead of being hardcoded into pipeline scripts. Build notifications, deployment status, and pipeline failures are automatically communicated to developers through Microsoft Teams and email. Jenkins has significantly improved deployment speed, reduced manual effort, ensured repeatable deployments, and enabled our teams to release software multiple times each week with minimal operational risk.

---

# 5. How would you implement DevOps practices in a project that currently has manual deployments?

If I join a project that relies entirely on manual deployments, my first objective would be understanding the existing release process before introducing automation. I would document every deployment step, identify repetitive manual tasks, understand approval workflows, analyze deployment failures, and determine the biggest operational pain points. Rather than replacing the entire process immediately, I would introduce DevOps practices gradually to minimize risk and encourage team adoption.

The first improvement would be implementing Git as the central version control system if it is not already being used. Once source code management is standardized, I would build a Continuous Integration pipeline using Jenkins that automatically checks out code, installs dependencies, executes unit tests, performs static code analysis, executes security scans, and generates deployable artifacts. This ensures every code change is validated automatically before deployment.

Next, I would containerize the application using Docker so that the same application image runs consistently across development, testing, UAT, and production environments. Infrastructure provisioning would then be automated using Terraform, eliminating manual cloud resource creation and ensuring infrastructure remains version controlled. If the application architecture supports containers, I would introduce Kubernetes for orchestration to improve scalability, self-healing, high availability, and rolling deployments.

After the application and infrastructure become fully automated, I would implement Continuous Deployment pipelines that automatically deploy applications to development and QA environments while maintaining approval gates for UAT and production. Monitoring would be established using Prometheus, Grafana, and AWS CloudWatch, while centralized logging would be implemented using the ELK Stack. Secrets would be managed securely through AWS Secrets Manager or Kubernetes Secrets instead of configuration files. Finally, I would conduct training sessions for developers, testers, and operations teams to ensure everyone understands DevOps practices and automation workflows. This gradual transformation reduces deployment failures, improves collaboration, shortens release cycles, enhances infrastructure reliability, and enables organizations to deliver software more efficiently.


---

# 6. What is Terraform drift?

Terraform drift occurs when the actual infrastructure deployed in the cloud no longer matches the infrastructure defined in the Terraform code or the Terraform state file. This usually happens when someone manually modifies resources through the AWS Management Console, AWS CLI, or another automation tool instead of making changes through Terraform. For example, if an engineer manually changes the EC2 instance type, adds a new security group rule, resizes an EBS volume, or modifies an Auto Scaling Group directly from the AWS console, Terraform will not be aware of those changes until a `terraform plan` is executed. During the next Terraform execution, Terraform detects that the live infrastructure differs from the desired state stored in the Terraform configuration and reports the differences as drift.

In my project, Infrastructure as Code is considered the single source of truth, so Terraform drift is taken very seriously. Manual modifications can cause deployment failures, unexpected infrastructure changes, security risks, and inconsistencies between environments. Therefore, before every infrastructure deployment, we execute `terraform plan` to compare the Terraform state with the actual AWS resources. This helps us identify unauthorized or accidental changes before they impact production. Detecting drift early ensures that our infrastructure remains predictable, reproducible, and fully managed through code rather than manual intervention.

---

# 7. How do you overcome Terraform drift?

When Terraform drift is detected, I first identify the exact resources that have changed by executing `terraform plan`. Instead of immediately applying changes, I carefully review the output to understand whether the drift was caused intentionally or accidentally. I also review AWS CloudTrail logs, Git history, and change requests to determine who modified the infrastructure and why. This helps prevent accidental overwriting of legitimate production changes.

If the manual modification is approved and should remain in production, I update the Terraform configuration so that it accurately represents the current infrastructure. If the resource was created outside Terraform, I import it into the Terraform state using `terraform import`. On the other hand, if the change was accidental or unauthorized, I use Terraform to reconcile the infrastructure back to the desired state after verifying that the changes will not cause downtime. For production environments, I always review the execution plan with my team before applying any modifications.

To prevent future drift, we follow strict Infrastructure as Code practices. Direct production access is restricted through IAM policies, all infrastructure changes are performed through pull requests, peer reviews are mandatory, CloudTrail continuously audits AWS API activity, and regular drift detection is included in our CI/CD pipeline. This approach ensures Terraform remains the single source of truth for managing cloud infrastructure.

---

# 8. What is a data source in Terraform?

A data source in Terraform allows us to retrieve information about existing infrastructure without creating or modifying those resources. Instead of hardcoding values such as VPC IDs, subnet IDs, AMI IDs, Route53 hosted zones, or security group IDs, Terraform dynamically fetches them during execution. This makes the infrastructure code reusable, portable, and easier to maintain across multiple environments.

In my current project, we frequently use data sources while provisioning AWS resources. For example, when launching EC2 instances, we use the `aws_ami` data source to retrieve the latest approved Amazon Linux AMI instead of specifying an image ID manually. Similarly, we use data sources to retrieve existing VPCs, private subnets, IAM roles, ACM certificates, and Route53 hosted zones created by other Terraform modules or teams. This eliminates duplication and ensures that infrastructure always references the correct existing resources.

Using data sources also improves maintainability because infrastructure automatically adapts when underlying resource IDs change. Instead of updating multiple configuration files manually, Terraform retrieves the latest resource information dynamically during execution, reducing configuration errors and improving deployment consistency.

---

# 9. What is a Terraform workspace?

A Terraform workspace is a feature that allows multiple environments to use the same Terraform configuration while maintaining separate state files. Instead of duplicating Terraform code for development, QA, UAT, and production environments, workspaces enable us to reuse the same infrastructure code while isolating each environment's resources.

In my project, we use separate workspaces for Development, QA, UAT, and Production. Each workspace maintains its own Terraform state file, ensuring that infrastructure changes made in one environment do not affect another. For example, the development workspace provisions smaller EC2 instances and fewer Kubernetes worker nodes, while the production workspace creates larger instances with higher availability and additional monitoring resources. Environment-specific values such as instance sizes, subnet IDs, and scaling limits are controlled through variables while the Terraform code remains the same.

Using workspaces significantly reduces code duplication, simplifies infrastructure maintenance, and ensures consistency across multiple environments. Combined with remote state storage in Amazon S3 and state locking using DynamoDB, Terraform workspaces provide a secure and efficient way to manage multi-environment infrastructure.

---

# 10. What are dependencies in Terraform?

Dependencies in Terraform define the order in which resources are created, updated, or destroyed. Terraform automatically builds a dependency graph by analyzing references between resources. For example, if an EC2 instance references a subnet, security group, IAM role, and key pair, Terraform automatically creates those resources first before provisioning the EC2 instance. Similarly, when destroying infrastructure, Terraform deletes dependent resources in the correct sequence to avoid failures.

Although Terraform usually detects dependencies automatically, there are situations where explicit dependencies are required. In such cases, we use the `depends_on` argument to instruct Terraform to wait until another resource has been created successfully before proceeding. In my project, I have used explicit dependencies while provisioning IAM policies, Kubernetes resources, EKS node groups, Route53 records, and Load Balancer components where creation order is critical.

Proper dependency management is extremely important because it prevents race conditions during infrastructure provisioning. Without correct dependencies, Terraform may attempt to create resources before their prerequisites are available, resulting in deployment failures. By defining dependencies correctly, infrastructure provisioning becomes predictable, reliable, and easier to troubleshoot.


---

# 11. A Linux server with a 500 GB data volume was provisioned using Terraform. The application team wants to increase it to 750 GB. How would you perform this change?

Whenever I receive a request to increase the size of a production EBS volume, my first priority is ensuring that the change is performed safely without impacting application availability. Since the server was provisioned using Terraform, I never modify the EBS volume directly from the AWS Console because doing so would introduce Terraform drift. Instead, I first review the existing Terraform configuration to identify where the EBS volume size is defined. I update the `volume_size` parameter from **500 GB to 750 GB** in the Terraform code and execute `terraform plan` to verify that Terraform intends only to modify the existing volume instead of recreating it. AWS supports online expansion of EBS volumes, so in most cases this operation does not require stopping the EC2 instance.

After confirming the execution plan with my team, I apply the changes using `terraform apply`. Once AWS completes the volume expansion, I log in to the Linux server and verify that the operating system still detects the previous partition size using commands such as `lsblk`, `df -h`, and `sudo fdisk -l`. Since increasing the EBS volume only expands the block device, I then extend the partition if required using `growpart`. After the partition is resized, I expand the file system. If the server uses an XFS file system, I execute `xfs_growfs`; if it uses an EXT4 file system, I use `resize2fs`. Finally, I verify the updated capacity using `df -h` to ensure that the operating system recognizes the new 750 GB volume.

Before considering the task complete, I perform application validation by checking application logs, monitoring dashboards, disk utilization, and file accessibility to ensure no production impact has occurred. I also update the infrastructure documentation and Git repository with the approved Terraform changes so that the Infrastructure as Code remains the single source of truth. This approach avoids configuration drift, minimizes downtime, and ensures that future infrastructure deployments remain consistent.

---

# 12. You have three nodes, and one node is not receiving traffic. How would you identify the problematic node, troubleshoot it, and fix the issue?

If one node in a three-node Kubernetes cluster is not receiving traffic, I begin by confirming whether the issue is related to Kubernetes scheduling, networking, the application itself, or the underlying infrastructure. My first step is checking the health of all nodes using `kubectl get nodes`. If one node shows a **NotReady** status, I immediately inspect it using `kubectl describe node` to identify conditions such as Memory Pressure, Disk Pressure, PID Pressure, or Kubelet failures. If the node is healthy and shows a **Ready** status, I continue investigating Kubernetes scheduling.

Next, I verify whether application pods are actually running on the affected node by executing `kubectl get pods -o wide`. If no pods are scheduled on that node, I check for taints, node selectors, affinity rules, or cordon status that may be preventing workloads from being scheduled there. If pods are present, I verify that they are passing readiness probes because Kubernetes Services send traffic only to Ready pods. Using `kubectl describe pod` and `kubectl logs`, I investigate application errors, readiness failures, or repeated container restarts.

The next layer I examine is the Kubernetes Service and Endpoints. I verify that the Service selectors correctly match the pod labels and ensure that the endpoints include pod IP addresses running on the affected node. If the endpoints are missing, traffic will never reach those pods regardless of node health. I also inspect the Ingress Controller or AWS Application Load Balancer Target Groups to confirm that targets associated with the affected node remain healthy. If using Amazon EKS, I verify Security Groups, Network ACLs, Route Tables, and AWS Load Balancer Controller configurations to ensure network traffic is not blocked.

If networking appears normal, I investigate the node itself. I review Kubelet logs, kube-proxy status, CNI plugin logs, CPU utilization, memory usage, disk utilization, and network interfaces. Sometimes kube-proxy failures, CNI plugin issues, or firewall rules prevent traffic from reaching pods even though Kubernetes reports the node as healthy. I also verify that the node can communicate with the Kubernetes API Server and other worker nodes.

After identifying the root cause, I implement the appropriate fix. If the Kubelet is unhealthy, I restart the service. If networking is the issue, I restore the CNI configuration or kube-proxy. If pods are failing readiness probes, I resolve the application issue and redeploy. Once fixed, I monitor traffic distribution, pod health, response times, and application metrics using Prometheus, Grafana, and CloudWatch to confirm that traffic is evenly distributed across all three nodes. Finally, I document the incident, perform a root cause analysis, and implement monitoring alerts so similar node issues are detected before they affect users.

---

# 13. What is a CRD (Custom Resource Definition) in Kubernetes?

A Custom Resource Definition (CRD) is a Kubernetes feature that allows users to extend the Kubernetes API by creating their own custom resource types. By default, Kubernetes provides built-in resources such as Pods, Deployments, Services, StatefulSets, ConfigMaps, and Secrets. However, modern cloud-native applications often require additional resource types that Kubernetes does not provide natively. CRDs enable developers and platform engineers to create these custom resources while allowing Kubernetes to manage them just like built-in objects.

In my experience, CRDs are commonly used by Kubernetes Operators. For example, when installing Prometheus Operator, Cert Manager, ArgoCD, or External Secrets Operator, several CRDs are automatically created. These CRDs introduce new Kubernetes objects such as `ServiceMonitor`, `Certificate`, `Application`, or `ExternalSecret`. Once the CRDs are installed, Kubernetes understands these new resource types and Operators continuously monitor them to perform automated actions.

The biggest advantage of CRDs is automation. Instead of manually performing repetitive administrative tasks, we simply define the desired state using a custom resource, and the corresponding Operator automatically reconciles the cluster to achieve that state. This follows Kubernetes' declarative model and greatly simplifies application lifecycle management, certificate management, database provisioning, monitoring configuration, and many other operational tasks.

---

# 14. What is CNI (Container Network Interface)?

Container Network Interface (CNI) is a networking standard used by Kubernetes to configure networking for containers and pods. Whenever Kubernetes creates a new pod, it delegates networking responsibilities to the installed CNI plugin. The CNI plugin assigns IP addresses, configures routing, establishes pod-to-pod communication, and ensures connectivity between pods, nodes, and external services.

Without a CNI plugin, Kubernetes pods would not be able to communicate with each other. Every Kubernetes cluster must therefore have a CNI implementation installed before workloads can function correctly. Popular CNI plugins include Calico, Cilium, Flannel, Weave Net, and Amazon VPC CNI. Each plugin offers different networking capabilities, security features, and performance characteristics depending on organizational requirements.

In Amazon EKS, the Amazon VPC CNI plugin is commonly used because it assigns VPC IP addresses directly to pods, enabling native AWS networking. In other Kubernetes environments, plugins such as Calico are frequently selected because they provide advanced network policies and enhanced security capabilities. Understanding CNI is essential because many Kubernetes networking issues, including pod communication failures, DNS issues, and network policy enforcement, are directly related to the CNI implementation.

---

# 15. Which CNI plugin have you used, and how did you implement it?

In my current project, we primarily use the **Amazon VPC CNI plugin** because our Kubernetes clusters are hosted on Amazon EKS. The Amazon VPC CNI allows each Kubernetes pod to receive an IP address directly from the AWS VPC subnet. This enables pods to communicate with other AWS resources without requiring additional overlay networks, resulting in better performance and simplified network management.

The implementation begins during Amazon EKS cluster creation. AWS automatically deploys the VPC CNI as a DaemonSet running on every worker node. I verify the installation using `kubectl get daemonset -n kube-system` and ensure that all `aws-node` pods are healthy. I also configure appropriate IAM Roles for Service Accounts (IRSA), subnet allocation, and Security Groups to ensure that pods receive IP addresses correctly. During production operations, I monitor available IP addresses, subnet utilization, pod networking, and VPC CNI logs because subnet IP exhaustion is a common issue in large EKS clusters.

In another project, I also worked with **Calico** as the CNI plugin for self-managed Kubernetes clusters. Calico was selected because it provides advanced Kubernetes Network Policies that allow us to control communication between namespaces and applications. We installed Calico using its official manifests, verified node readiness, tested pod-to-pod connectivity, and created Network Policies that restricted traffic based on security requirements. This improved application security by ensuring that only authorized services could communicate with each other while blocking unnecessary east-west traffic inside the cluster. My experience with both Amazon VPC CNI and Calico has given me a strong understanding of Kubernetes networking, network troubleshooting, IP management, and production-grade cluster security.

# Advanced AWS Interview Questions & Answers (4+ Years DevOps Engineer)

## 1. An EC2 instance passes status checks but the app on port 80 doesn't respond. Walk through every layer you check.

When an EC2 instance passes both system and instance status checks but the application is not responding on port 80, I troubleshoot layer by layer. First, I verify whether the application process is actually running using commands such as `ps -ef`, `systemctl status`, or `docker ps` if containerized. Next, I check whether the application is listening on port 80 using `netstat -tulnp` or `ss -tulnp`. If the process is not listening, the issue is application-related. If it is listening, I verify Security Group rules to ensure inbound HTTP traffic is allowed. Then I check NACL rules, route tables, and whether the instance has a public IP or is behind a Load Balancer. If an ALB is used, I validate target group health checks and target registration. I also review web server logs such as Nginx or Apache logs, application logs, and CloudWatch metrics. Finally, I test connectivity locally using curl and externally from another system. This structured approach helps isolate whether the issue is application, OS, networking, or AWS infrastructure related.

---

## 2. Explain a real scenario where adding more EC2 capacity made latency worse, not better.

One common production scenario occurs when applications depend heavily on a backend database. During a traffic spike, engineers often increase EC2 instances assuming it will improve performance. However, adding more application servers can increase the number of concurrent database connections, overwhelming the database. Instead of reducing latency, database contention, locking, and connection pool exhaustion cause response times to increase significantly. I experienced a situation where scaling application servers doubled the number of database connections, causing RDS CPU utilization to reach 95% and query latency to increase dramatically. The solution was database optimization, query tuning, connection pooling, and read replicas rather than simply adding more compute capacity.

---

## 3. How do you safely rotate IAM credentials in production without breaking active sessions?

The safest approach is to follow a phased rotation strategy. First, create new credentials while keeping existing credentials active. Update applications, CI/CD pipelines, Lambda functions, and automation scripts to use the new credentials. Validate that all services can authenticate successfully using the new credentials. Monitor logs for authentication failures and verify production traffic remains healthy. After confirming successful usage of the new credentials, disable the old credentials temporarily while continuing to monitor. Finally, permanently delete the old credentials. Wherever possible, I prefer IAM Roles over Access Keys because roles eliminate manual credential rotation and significantly improve security.

---

## 4. What problems arise when multiple services share the same Security Group, and how do you design around it?

Sharing a single Security Group across multiple services often leads to excessive permissions and reduced security. Over time, teams keep adding rules to support new applications, resulting in a broad attack surface. Troubleshooting also becomes difficult because changes intended for one service can unintentionally affect others. In production, I prefer assigning dedicated Security Groups per application or service tier. For example, web servers, application servers, and databases each receive their own Security Group. Communication is controlled through Security Group references rather than wide-open CIDR ranges. This follows least-privilege principles and improves maintainability.

---

## 5. Difference between Security Group and NACL - and why misunderstanding this opens production to attack.

Security Groups operate at the instance level and are stateful, meaning return traffic is automatically allowed. NACLs operate at the subnet level and are stateless, requiring both inbound and outbound rules to be explicitly defined. Many engineers mistakenly assume NACLs provide the same protection as Security Groups. Misconfigured NACLs can unintentionally allow or block traffic across entire subnets. In production environments, Security Groups are typically used as the primary firewall mechanism, while NACLs provide an additional subnet-level security layer. Understanding the distinction is critical because overly permissive NACLs or Security Groups can expose applications to unauthorized access.

---

## 6. How do you handle secrets in AWS without exposing them in environment variables or Lambda config?

I use AWS Secrets Manager or AWS Systems Manager Parameter Store to securely store sensitive information such as database passwords, API keys, and tokens. Applications retrieve secrets dynamically at runtime through IAM-authenticated API calls. This ensures secrets are encrypted at rest using KMS and are never hardcoded in source code, Terraform files, Docker images, or Lambda configurations. Secret rotation can also be automated through Secrets Manager. This approach significantly improves security and compliance while reducing operational risk.

---

## 7. Explain cost drift. How do you detect a silent billing spike before it shows up on the invoice?

Cost drift occurs when cloud spending gradually increases without intentional infrastructure changes. Common causes include idle resources, oversized instances, unattached EBS volumes, data transfer growth, snapshot accumulation, or inefficient architecture changes. To detect cost drift early, I configure AWS Budgets, Cost Explorer alerts, Cost Anomaly Detection, and CloudWatch billing alarms. Daily cost monitoring dashboards allow teams to identify unusual spending patterns before monthly invoices arrive. Regular FinOps reviews and tagging strategies also help pinpoint cost ownership and optimization opportunities.

---

## 8. What happens internally when an S3 bucket policy and IAM policy conflict on the same action?

AWS follows an explicit deny model. First, AWS evaluates all applicable policies, including IAM policies, bucket policies, SCPs, and permission boundaries. If any policy explicitly denies access, the request is denied regardless of any allow statements. If there are no explicit denies and at least one allow exists, access is granted. For example, even if an IAM policy allows access to an S3 bucket, a bucket policy containing an explicit deny will override the allow and block access. Understanding policy evaluation order is essential for troubleshooting authorization issues.

---

## 9. How do you design a multi-account AWS setup without permissions becoming tightly coupled?

I follow a multi-account strategy using AWS Organizations. Separate accounts are maintained for Production, Non-Production, Security, Shared Services, and Logging. Access between accounts is controlled using IAM Roles and STS AssumeRole instead of long-term credentials. Shared services such as CI/CD, monitoring, and logging operate from centralized accounts. Service Control Policies enforce governance at the organizational level. This architecture improves security, isolation, compliance, and operational scalability while preventing tightly coupled permission structures.

---

## 10. Explain NAT Gateway vs NAT Instance vs VPC Endpoint - when does choosing wrong waste money?

A NAT Gateway is a managed AWS service that allows private subnet resources to access the internet. It offers high availability but incurs hourly and data processing charges. A NAT Instance is a self-managed EC2 instance providing similar functionality but requires maintenance and scaling. A VPC Endpoint allows private communication with AWS services without internet access or NAT usage. Choosing a NAT Gateway for workloads that only access S3 or DynamoDB can waste significant money because a VPC Endpoint would eliminate NAT data transfer costs. Selecting the right option depends on traffic patterns, availability requirements, and cost considerations.

---

## 11. How does cross-account role assumption actually work, and why is it dangerous if misconfigured?

Cross-account role assumption uses AWS Security Token Service (STS). An IAM user or role in one account assumes a role in another account based on trust policies. Temporary credentials are generated and used for authorized actions. If trust relationships are overly permissive, unauthorized accounts may gain access to critical resources. Misconfigured AssumeRole permissions can create privilege escalation paths. Therefore, trust policies should be tightly controlled, least privilege should be enforced, and CloudTrail monitoring should be enabled for auditing.

---

## 12. How do you refactor a flat VPC into private/public subnets without taking production down?

The migration must be gradual. First, create public and private subnets alongside the existing infrastructure. Deploy NAT Gateways, route tables, and Security Groups. Then move stateless workloads incrementally into the new subnet architecture while validating connectivity. Load Balancers are used to direct traffic between old and new environments during migration. Database and stateful components are migrated carefully with replication and failover planning. Infrastructure as Code and extensive testing are critical. The goal is to perform migration in phases rather than attempting a risky big-bang cutover.

---

## 13. What are partial failovers, and how do you recover safely during a Multi-AZ RDS failure?

A partial failover occurs when some components recover while others remain unavailable or degraded. In a Multi-AZ RDS setup, AWS automatically promotes the standby instance during failures. However, applications may still experience connection issues, DNS propagation delays, stale connection pools, or transaction retries. During recovery, I verify RDS failover completion, application connectivity, DNS resolution, database replication status, and application health. Monitoring dashboards help confirm full service restoration before closing the incident.

---

## 14. What is the real difference between an Elastic IP and a Public IP, and why does that matter at scale?

A Public IP is automatically assigned by AWS and may change when an instance stops and starts. An Elastic IP is a static public IPv4 address allocated to an AWS account and retained until released. At scale, Elastic IPs are important for systems requiring fixed IP allowlists, partner integrations, VPN connections, or external firewall configurations. However, unused Elastic IPs incur charges, so proper lifecycle management is important for cost optimization.

---

## 15. Describe a real incident caused by an overly permissive IAM policy. How did you fix it?

A common production incident involved an IAM policy containing `s3:*` permissions on all buckets using the `*` wildcard. A developer accidentally deleted objects from a production bucket while testing automation scripts. Although versioning allowed recovery, the incident highlighted excessive permissions. The fix involved implementing least-privilege IAM policies, restricting actions to specific buckets, enabling MFA for sensitive operations, introducing approval workflows for production changes, and conducting periodic IAM access reviews. We also implemented IAM Access Analyzer and automated compliance checks to prevent similar issues in the future.



# AWS DevOps Engineer (4 Years Experience) - Interview Questions & Answers

## 1. Can you describe the different types of Load Balancers and provide examples?

AWS provides three main types of load balancers. The Application Load Balancer (ALB) operates at Layer 7 and is used for HTTP and HTTPS traffic. It supports advanced routing features such as path-based and host-based routing, making it ideal for microservices and web applications. The Network Load Balancer (NLB) operates at Layer 4 and is designed for handling TCP and UDP traffic with very low latency and high performance. It is commonly used for applications requiring millions of requests per second. The Classic Load Balancer (CLB) is the older generation load balancer that supports both Layer 4 and Layer 7 features but is generally used only for legacy applications. In my projects, I have primarily used ALB for web applications hosted on EC2 and EKS.

## 2. What is the maximum runtime for a Lambda function?

AWS Lambda supports a maximum execution timeout of 15 minutes, which is equivalent to 900 seconds. If a function exceeds this duration, AWS automatically terminates the execution. For long-running workflows, I typically use AWS Step Functions to orchestrate multiple Lambda functions instead of increasing the execution duration.

## 3. What is the maximum memory size for a Lambda function?

AWS Lambda allows memory allocation from 128 MB up to 10,240 MB (10 GB). Increasing the memory allocation also increases the CPU and network throughput available to the function. During performance optimization, I monitor CloudWatch metrics and adjust memory settings based on execution time and resource utilization.

## 4. How can you increase the runtime for a Lambda function?

The maximum timeout of a Lambda function can be configured up to 15 minutes in the function settings. If the workload requires more processing time than the Lambda limit allows, I typically optimize the code, split the process into multiple Lambda functions, or use AWS Step Functions to manage longer workflows. In some cases, containerized workloads on ECS or EKS may be a better solution for long-running processes.

## 5. What automations have you performed using Lambda in your project?

In my projects, I have used Lambda for several automation tasks, including automatic EC2 start and stop operations based on schedules, monitoring S3 bucket events, sending SNS notifications for infrastructure alerts, automating backup processes, rotating IAM access keys, cleaning up unused snapshots, and processing CloudWatch log events. These automations helped reduce manual intervention and improve operational efficiency.

## 6. Why did you choose Terraform over CloudFormation for infrastructure provisioning?

I prefer Terraform because it is cloud-agnostic and supports multiple cloud providers such as AWS, Azure, and GCP. Terraform uses HashiCorp Configuration Language (HCL), which is simple and easy to maintain. It provides modularity, reusable code, state management, and a large community ecosystem. Compared to CloudFormation, Terraform offers better flexibility and easier integration with CI/CD pipelines. In my projects, Terraform has been used extensively to provision VPCs, EC2 instances, IAM roles, Lambda functions, and networking resources.

## 7. What modules have you used in your Lambda function?

In Python-based Lambda functions, I have commonly used modules such as boto3 for AWS service interactions, json for handling data, logging for monitoring and debugging, datetime for scheduling operations, os for environment variables, and requests for calling external APIs. These modules help build scalable and maintainable serverless applications.

## 8. Have you created an SNS topic for your project?

Yes, I have created and configured SNS topics for infrastructure monitoring and alerting. SNS was integrated with CloudWatch alarms to send email notifications whenever CPU utilization, memory consumption, or application errors crossed predefined thresholds. I have also used SNS to trigger Lambda functions and fan-out notifications to multiple subscribers.

## 9. If you've exhausted IP addresses in your VPC, how would you provision new resources?

If the IP address range in a VPC is exhausted, I would first evaluate the existing subnet utilization and remove unused resources if possible. If additional IP addresses are required, I would associate a secondary CIDR block with the VPC and create new subnets from that range. Another option is to redesign the network architecture with larger CIDR ranges or migrate workloads to a new VPC with sufficient address space.

## 10. What is Groovy, and how is it used in Jenkins?

Groovy is a scripting language that runs on the Java Virtual Machine (JVM). In Jenkins, Groovy is used to create Pipeline as Code through Jenkinsfiles. It allows developers and DevOps engineers to define build, test, and deployment stages programmatically. Groovy provides flexibility for creating complex CI/CD workflows and automation logic.

## 11. Why do you use Groovy in Jenkins, and where do you save Jenkins files?

Groovy is used in Jenkins because it enables the implementation of CI/CD pipelines as code, making them version-controlled and reusable. Jenkinsfiles are typically stored in the application's source code repository, such as GitHub, GitLab, or Bitbucket. This approach ensures that pipeline definitions are maintained alongside application code and can be tracked through version control.

## 12. What is Ansible, and what is its purpose?

Ansible is an open-source automation and configuration management tool used for provisioning, configuration management, application deployment, and orchestration. It simplifies infrastructure management by automating repetitive tasks across multiple servers. In my projects, I have used Ansible to install software packages, configure web servers, deploy applications, and manage operating system configurations.

## 13. What language do you use in Ansible?

Ansible playbooks are written in YAML (Yet Another Markup Language). YAML provides a simple and human-readable format for defining automation tasks. Ansible also uses Jinja2 templates for dynamic configurations and Python modules for backend execution.

## 14. Where do you run Terraform code, remotely or locally?

I have experience running Terraform both locally and through CI/CD pipelines. In enterprise environments, Terraform is typically executed through Jenkins pipelines, while the state file is stored remotely in an S3 bucket with DynamoDB used for state locking. This setup ensures collaboration, security, and consistency across team members.

## 15. What is the purpose of access keys and secret keys in AWS?

AWS Access Keys and Secret Keys are used for programmatic authentication to AWS services. They enable applications, scripts, Terraform, and AWS CLI commands to securely interact with AWS resources. As a best practice, I prefer IAM roles whenever possible to avoid hardcoding credentials and improve security.

## 16. What are Terraform modules, and have you used any in your project?

Terraform modules are reusable collections of Terraform resources that simplify infrastructure deployment and maintenance. They help standardize infrastructure across environments and reduce code duplication. In my projects, I have created and used modules for VPCs, EC2 instances, security groups, IAM roles, Lambda functions, and S3 buckets.

## 17. What environments have you set up for your project?

I have worked with multiple environments, including Development (Dev), Quality Assurance (QA), User Acceptance Testing (UAT), Staging, and Production. Each environment has separate infrastructure and configurations to ensure proper testing before changes are deployed to production.

## 18. Do you use the same AWS account for all environments?

No, following AWS best practices, we use separate AWS accounts for Development, Testing, and Production environments. This approach improves security, governance, cost management, and resource isolation. AWS Organizations is typically used to manage multiple accounts centrally.

## 19. Do you have separate Jenkins servers for each environment?

In most projects, a single Jenkins server is used to manage deployments across multiple environments. Different pipelines, credentials, and deployment stages are configured for Dev, QA, UAT, and Production. However, for highly secure environments, separate Jenkins instances may be maintained for production workloads.

## 20. Where do you write and save your Lambda function code?

I usually develop Lambda functions locally using Visual Studio Code and maintain the source code in Git repositories such as GitHub or GitLab. The deployment process is automated through Jenkins pipelines and Terraform. The deployment packages are stored in S3 and then deployed to AWS Lambda.

## 21. How do you manage cost optimization in cloud environment services?

I follow several cloud cost optimization strategies, including right-sizing EC2 instances, using Auto Scaling groups, purchasing Reserved Instances or Savings Plans for predictable workloads, utilizing Spot Instances for non-critical workloads, implementing S3 lifecycle policies, removing unused resources, monitoring costs through AWS Cost Explorer and AWS Budgets, and optimizing Lambda memory allocations. Regular audits help identify and eliminate unnecessary expenses.

## 22. Do you have any questions for me?

Yes. I would like to understand the team's current CI/CD process and deployment strategy. I am also interested in learning about the cloud architecture used in the organization, the tools used for monitoring and automation, and the opportunities available for working on modern technologies such as Kubernetes, serverless computing, and Infrastructure as Code. Additionally, I would like to know how success is measured for this role and what growth opportunities are available within the team.



----
# Common DevOps Networking & Port Interview Questions

## Which port is used by SSH?

SSH (Secure Shell) uses **TCP port 22** by default. It is used for secure remote login, command execution, file transfers (SCP/SFTP), and server administration. In production environments, some organizations change the default port for additional security, but 22 remains the standard SSH port.

---

## Which port is used by HTTP and HTTPS?

HTTP uses **TCP port 80** and HTTPS uses **TCP port 443**.

* **HTTP (Port 80):** Transfers data in plain text and is generally used for redirects to HTTPS.
* **HTTPS (Port 443):** Encrypts communication using SSL/TLS and is the standard protocol for secure web traffic.

Most modern applications use HTTPS to ensure secure communication between users and servers.

---

## Which port is required for Amazon EFS?

Amazon Elastic File System (EFS) uses **TCP port 2049**.

EFS is based on the Network File System (NFS) protocol. When EC2 instances, EKS worker nodes, or containers need to mount an EFS filesystem, Security Groups and NACLs must allow TCP traffic on port 2049 between clients and EFS mount targets.

---

## Which port does MySQL use?

MySQL uses **TCP port 3306** by default.

Applications connect to MySQL databases through this port for executing queries, reading data, and performing transactions. When troubleshooting database connectivity issues, verifying Security Groups, NACLs, and firewall access to port 3306 is a common step.

---

## Difference between TCP and UDP?

TCP (Transmission Control Protocol) is a connection-oriented protocol that guarantees reliable data delivery. UDP (User Datagram Protocol) is a connectionless protocol that prioritizes speed over reliability.

### TCP Characteristics:

* Connection-oriented
* Reliable communication
* Error checking and retransmission
* Ordered packet delivery
* Slower than UDP

### UDP Characteristics:

* Connectionless
* Faster communication
* No delivery guarantee
* No retransmission
* Lower overhead

### Examples:

* TCP: HTTP, HTTPS, SSH, FTP, MySQL
* UDP: DNS, VoIP, Video Streaming, Online Gaming

In production systems, TCP is used when reliability is critical, while UDP is preferred for latency-sensitive workloads.

---

## Which port is used by Jenkins?

Jenkins uses **TCP port 8080** by default.

This port provides access to the Jenkins web interface where users can create pipelines, monitor builds, manage plugins, and configure jobs. Organizations often place Jenkins behind a reverse proxy such as NGINX or an Application Load Balancer and expose it through HTTPS on port 443.

---

## Which port is used by Kubernetes API Server?

The Kubernetes API Server uses **TCP port 6443**.

All Kubernetes components communicate with the API Server through this port, including:

* kubectl
* kubelets
* controllers
* schedulers
* external automation tools

When kubectl commands fail, one of the first troubleshooting steps is verifying connectivity to port 6443.

---

## Which port is used by Docker daemon?

The Docker daemon typically uses:

* **Unix Socket:** `/var/run/docker.sock` (most common)
* **TCP Port 2375:** Unsecured Docker API
* **TCP Port 2376:** Secure Docker API with TLS

In production environments, Docker communication generally occurs through the Unix socket or TLS-secured port 2376 rather than the unsecured 2375 port.

---

## Which port is used by Grafana?

Grafana uses **TCP port 3000** by default.

Users access dashboards, metrics visualizations, alerts, and monitoring data through this port. In production environments, Grafana is often placed behind NGINX, Ingress Controllers, or Load Balancers and exposed through HTTPS.

---

## Which port is used by Prometheus?

Prometheus uses **TCP port 9090** by default.

This port provides:

* Prometheus UI
* Query interface (PromQL)
* Metrics exploration
* Alert configuration

Grafana commonly connects to Prometheus on port 9090 to retrieve monitoring metrics for dashboard visualization.

---

# Quick Revision Table

| Service                | Protocol | Default Port |
| ---------------------- | -------- | ------------ |
| SSH                    | TCP      | 22           |
| HTTP                   | TCP      | 80           |
| HTTPS                  | TCP      | 443          |
| Amazon EFS (NFS)       | TCP      | 2049         |
| MySQL                  | TCP      | 3306         |
| Jenkins                | TCP      | 8080         |
| Kubernetes API Server  | TCP      | 6443         |
| Docker Daemon (TLS)    | TCP      | 2376         |
| Docker Daemon (No TLS) | TCP      | 2375         |
| Grafana                | TCP      | 3000         |
| Prometheus             | TCP      | 9090         |

# Most Asked DevOps Interview Ports

| Component             | Port        |
| --------------------- | ----------- |
| SSH                   | 22          |
| HTTP                  | 80          |
| HTTPS                 | 443         |
| DNS                   | 53          |
| MySQL                 | 3306        |
| PostgreSQL            | 5432        |
| Jenkins               | 8080        |
| Grafana               | 3000        |
| Prometheus            | 9090        |
| Kubernetes API Server | 6443        |
| Docker Daemon         | 2375 / 2376 |
| EFS                   | 2049        |
| Redis                 | 6379        |
| MongoDB               | 27017       |
| Elasticsearch         | 9200        |
| Kibana                | 5601        |



