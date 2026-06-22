# AWS & Kubernetes/EKS Interview Questions and Answers (4+ Years DevOps Engineer)

## 1. Difference between IAM Role and IAM Policy?

An IAM Policy is a JSON document that defines permissions, specifying what actions are allowed or denied on AWS resources. An IAM Role, on the other hand, is an AWS identity that can be assumed by users, applications, AWS services, or external accounts. Policies define permissions, while roles provide temporary credentials that use those permissions. In production environments, roles are preferred over access keys because they eliminate the need to manage long-term credentials and improve security. For example, an EC2 instance can assume an IAM Role to access S3 instead of storing AWS access keys on the server.

---

## 2. What are the different types of IAM Policies?

AWS supports several types of IAM policies. Identity-Based Policies are attached directly to users, groups, or roles. Resource-Based Policies are attached directly to AWS resources such as S3 buckets, KMS keys, or SNS topics. Permissions Boundaries define the maximum permissions a user or role can have. Service Control Policies (SCPs) are used within AWS Organizations to control permissions across accounts. Session Policies provide temporary restrictions during role assumption. Each policy type serves a different purpose and is often combined to implement secure access controls.

---

## 3. What is Service Control Policy (SCP)?

A Service Control Policy is an AWS Organizations feature used to define permission guardrails across AWS accounts. SCPs do not grant permissions directly; instead, they specify the maximum permissions available to accounts within an Organization. Even if a user has AdministratorAccess, actions blocked by an SCP cannot be performed. For example, an organization may create an SCP that denies the deletion of CloudTrail logs or prevents launching resources outside approved regions. SCPs are commonly used to enforce security and compliance requirements across multiple AWS accounts.

---

## 4. What is Permissions Boundary?

A Permissions Boundary is an advanced IAM feature that defines the maximum permissions a user or role can obtain, regardless of the policies attached to it. It acts as a permission ceiling. Even if a user attaches an Administrator policy, actions outside the boundary remain blocked. Permissions Boundaries are commonly used in large organizations where developers are allowed to create roles but should not gain unrestricted access.

---

## 5. What are the use cases of Permissions Boundary?

Permissions Boundaries are useful when delegating IAM administration to teams without granting excessive privileges. For example, developers may be allowed to create IAM roles for applications, but a boundary ensures they cannot grant access to critical services such as IAM, Organizations, or billing resources. Boundaries are also used in self-service environments where teams provision infrastructure but must remain within predefined security constraints.

---

## 6. Have you worked in a Multi-Account AWS environment?

Yes. In enterprise environments, workloads are typically distributed across multiple AWS accounts to improve security, governance, and cost management. Common account structures include Development, QA, UAT, Production, Shared Services, Security, and Logging accounts. AWS Organizations is used to centrally manage these accounts. Cross-account IAM roles enable secure access between accounts. This approach limits blast radius, improves compliance, and simplifies resource isolation.

---

## 7. What is AWS Organizations?

AWS Organizations is a service that allows centralized management of multiple AWS accounts. It enables account grouping using Organizational Units (OUs), centralized billing, Service Control Policies, and governance controls. Organizations help enterprises manage permissions, security policies, and compliance requirements consistently across all AWS accounts. It is commonly used in multi-account architectures.

---

## 8. What is AWS Control Tower?

AWS Control Tower is a service that automates the setup and governance of a secure multi-account AWS environment. It uses AWS Organizations, SCPs, CloudTrail, Config, and predefined guardrails to establish best practices. Control Tower simplifies account provisioning, compliance enforcement, and centralized governance. Organizations use it to accelerate landing zone creation while maintaining security standards.

---

## 9. How do you create Private DNS in AWS?

Private DNS is typically implemented using Route53 Private Hosted Zones. A Private Hosted Zone is associated with one or more VPCs and resolves DNS queries only within those VPCs. Internal services such as databases, internal APIs, and private load balancers can be accessed using friendly domain names without exposing them to the public internet. This improves security and simplifies service discovery.

---

## 10. What is Route53 Private Hosted Zone?

A Route53 Private Hosted Zone is a DNS zone accessible only within associated VPCs. Unlike public hosted zones, records are not resolvable from the internet. Private Hosted Zones are commonly used for internal applications, databases, microservices, and service discovery. For example, an internal application can access a database using db.internal.company.local instead of a private IP address.

---

## 11. Steps to create a fresh EKS cluster?

To create an EKS cluster, I first create a VPC with public and private subnets across multiple Availability Zones. Then I configure IAM roles for the EKS control plane and worker nodes. Next, I create the EKS cluster using Terraform, eksctl, or AWS Console. Managed Node Groups or Karpenter are configured to provide worker nodes. Networking components such as VPC CNI, CoreDNS, and kube-proxy are deployed automatically. After cluster creation, I configure kubectl access, install Ingress Controllers, Metrics Server, Cluster Autoscaler or Karpenter, monitoring tools such as Prometheus and Grafana, logging solutions such as Fluent Bit, and GitOps tools such as ArgoCD.

---

## 12. Have you created EKS using Terraform?

Yes. In my projects, EKS clusters are fully provisioned using Terraform modules. Terraform manages VPCs, subnets, IAM roles, security groups, EKS clusters, node groups, load balancers, and supporting infrastructure. Using Terraform provides consistency, version control, automation, and repeatability. It also allows environments such as Dev, QA, and Production to be deployed using the same codebase with environment-specific variables.

---

## 13. What issues have you faced while creating an EKS cluster?

Common issues include IAM permission errors, subnet tagging problems, insufficient IP addresses, worker nodes failing to join the cluster, VPC CNI misconfigurations, ECR access failures, security group restrictions, and DNS resolution issues. One common production issue occurs when private subnets lack NAT Gateway access, preventing nodes from pulling container images. Another issue is missing IAM permissions required by worker nodes. Troubleshooting typically involves checking CloudFormation events, node logs, IAM policies, subnet configurations, and cluster events.

---

## 14. What EKS version are you using?

The exact version depends on the organization's upgrade cycle. A good interview response is: "We generally stay within one or two supported versions of the latest EKS release. In my recent project, we were running EKS version 1.30 and regularly performed upgrades following AWS recommendations to maintain security and support."

---

## 15. Difference between Public and Private EKS Cluster?

In a Public EKS Cluster, the Kubernetes API Server endpoint is accessible through the internet, though access can be restricted using CIDR ranges and IAM authentication. In a Private EKS Cluster, the API endpoint is accessible only from within the VPC, making it more secure. Production environments often use private clusters to reduce exposure to the public internet. Administrators connect through VPNs, bastion hosts, or AWS Systems Manager Session Manager.

---

## 16. Difference between Gateway Endpoint and Interface Endpoint?

Gateway Endpoints are available only for S3 and DynamoDB and are configured through route tables. They provide direct access without using NAT Gateways or internet access. Interface Endpoints use AWS PrivateLink and create Elastic Network Interfaces (ENIs) within subnets. Interface Endpoints support many AWS services such as Secrets Manager, CloudWatch, and ECR. Gateway Endpoints are generally free, while Interface Endpoints incur hourly and data processing charges.

---

## 17. Types of VPC Endpoints?

AWS provides three types of VPC Endpoints:

1. Gateway Endpoint (S3 and DynamoDB)
2. Interface Endpoint (AWS PrivateLink)
3. Gateway Load Balancer Endpoint

Each type enables private communication between VPC resources and AWS services without traversing the public internet.

---

## 18. Which subnet is required for VPC Endpoint?

For Interface Endpoints, private subnets are typically used because the goal is to allow private access to AWS services. The endpoint creates ENIs within selected subnets. Gateway Endpoints do not require subnet selection because they operate through route table associations.

---

## 19. How can a private EC2 access S3 without internet?

A private EC2 instance can access S3 using a Gateway VPC Endpoint. The endpoint updates route tables so traffic to S3 remains within the AWS network without traversing the internet. This improves security, reduces NAT Gateway costs, and eliminates dependency on internet connectivity.


