# Bajaj Finserv Interview Questions & Answers (4+ Years Experience)

## 1. Difference between ECS and EKS

— practical experience with ECS?

Amazon ECS (Elastic Container Service) is AWS's native container orchestration service, while Amazon EKS (Elastic Kubernetes Service) is a managed Kubernetes service. ECS is simpler to manage because AWS handles most orchestration components, and it integrates tightly with AWS services such as IAM, ALB, CloudWatch, and Auto Scaling. EKS provides Kubernetes compatibility, making it suitable for organizations that want cloud portability and Kubernetes-native features. In my experience, I have worked more extensively with EKS for microservices deployments. However, I have also used ECS Fargate for lightweight containerized applications where operational overhead needed to be minimized. ECS was easier to set up and maintain, while EKS provided greater flexibility and ecosystem support.

---

## 2. What is Azure Tenant? Explain

Azure hierarchy (Tenant →
Subscription → Resource Group
→ Resource)

An Azure Tenant represents an organization's dedicated instance of Microsoft Entra ID (formerly Azure Active Directory). It acts as the identity and access management boundary. The Azure hierarchy starts with the Tenant at the top level. Within a Tenant, there can be multiple Subscriptions used for billing and resource isolation. Each Subscription contains Resource Groups, which logically organize resources based on application, environment, or project. Inside Resource Groups, actual Azure Resources such as Virtual Machines, Storage Accounts, Virtual Networks, AKS Clusters, and Databases are created. This hierarchy helps manage governance, security, permissions, and cost allocation effectively.

---

## 3. RBAC in AWS vs Azure — differences

and how you configure it?

RBAC (Role-Based Access Control) in both AWS and Azure is used to grant permissions based on roles rather than individual permissions. In AWS, RBAC is implemented primarily through IAM Users, Groups, Roles, and Policies. Permissions are attached via JSON-based IAM policies. In Azure, RBAC is integrated with Azure AD and uses built-in or custom roles assigned at different scopes such as Management Group, Subscription, Resource Group, or Resource level. In AWS, I usually create IAM Roles and attach least-privilege policies. In Azure, I assign built-in roles like Contributor, Reader, or Owner through Azure Portal, CLI, or Terraform. Azure RBAC provides more granular scope-based assignments, while AWS focuses on policy-based permission management.

---

## 4. HTTP error codes — specifically

408 (timeout) and 429 (rate limit)

HTTP 408 Request Timeout indicates that the client did not send a complete request within the time the server was willing to wait. It commonly occurs due to slow client connections, network issues, or misconfigured timeouts. HTTP 429 Too Many Requests occurs when a client exceeds the rate limit configured on the server or API Gateway. It is typically used to prevent abuse and ensure service availability. Troubleshooting 408 involves checking network latency, load balancer timeout settings, and backend response times. For 429 errors, I verify API rate limits, throttling policies, client request patterns, and implement retries with exponential backoff where appropriate.

---

## 5. Cross-cloud DNS resolution between

AWS and Azure private domains —
how to configure?

Cross-cloud private DNS resolution requires network connectivity between AWS and Azure, usually through VPN or ExpressRoute-to-Direct Connect connectivity. On AWS, private domains are managed using Route 53 Private Hosted Zones, while Azure uses Azure Private DNS Zones. DNS forwarding is configured on both sides using Route 53 Resolver inbound and outbound endpoints in AWS and Azure DNS Private Resolver in Azure. Conditional forwarding rules are created so AWS can resolve Azure private domains and Azure can resolve AWS private domains. This setup enables seamless name resolution across both cloud environments without exposing records publicly.

---

## 6. Troubleshoot inconsistent nslookup

results for private domains in Azure

When nslookup results are inconsistent for Azure private domains, I first verify whether the client is using the correct DNS server. Then I check Azure Private DNS Zone records and ensure virtual network links are configured properly. I validate DNS forwarding rules if custom DNS servers are involved. I also inspect DNS cache on the client and DNS servers because stale records often cause inconsistent responses. Network connectivity, conditional forwarding configuration, and TTL values are reviewed. Tools like nslookup, dig, Azure Network Watcher, and packet captures help identify the root cause.

---

## 7. Node Exporter in Prometheus —

what is it, which port, how Grafana
uses it?

Node Exporter is a Prometheus exporter used to collect operating system and hardware-level metrics from Linux servers. It provides metrics such as CPU utilization, memory usage, disk I/O, filesystem statistics, and network activity. By default, Node Exporter listens on port 9100. Prometheus scrapes metrics from Node Exporter endpoints at regular intervals and stores them in its time-series database. Grafana connects to Prometheus as a data source and uses PromQL queries to visualize these metrics through dashboards. This setup provides comprehensive infrastructure monitoring and alerting capabilities.

---

## 8. Designing a scalable API service

on AWS EKS — steps from scratch?

To design a scalable API service on AWS EKS, I would first create a VPC with public and private subnets across multiple Availability Zones. Then I would provision an EKS cluster with managed node groups. Application containers would be packaged using Docker and stored in ECR. Kubernetes Deployments and Services would be created for application deployment. An AWS Load Balancer Controller would expose services through an Application Load Balancer. Horizontal Pod Autoscaler would automatically scale pods based on CPU or custom metrics. Cluster Autoscaler or Karpenter would dynamically scale worker nodes. Monitoring would be implemented using Prometheus, Grafana, and CloudWatch, while security would be enforced through IAM Roles for Service Accounts, Network Policies, and Secrets Management.

---

## 9. Kubernetes — what happens internally

when you run kubectl apply?
(control plane flow)

When kubectl apply is executed, the manifest file is sent to the Kubernetes API Server. The API Server validates the request through authentication, authorization, and admission controllers. If valid, the desired state is stored in etcd. Controllers continuously compare the desired state stored in etcd with the actual cluster state. If new pods are required, the Scheduler selects suitable worker nodes based on available resources and constraints. The Kubelet on the selected node receives instructions from the API Server and interacts with the container runtime to create containers. The pod status is updated back to the API Server, making the new state visible to users.

---

## 10. Pod scheduling on specific node —

```
nodeSelector vs nodeAffinity vs
taints and tolerations difference?
```

nodeSelector is the simplest scheduling mechanism and schedules pods based on exact label matching. nodeAffinity provides more advanced scheduling rules, supporting both mandatory and preferred node selection criteria. Taints and Tolerations work differently; taints are applied to nodes to repel pods, while tolerations allow specific pods to be scheduled onto tainted nodes. nodeSelector and nodeAffinity attract pods to nodes, whereas taints and tolerations prevent unwanted workloads from running on certain nodes. In production, nodeAffinity is generally preferred because it offers greater flexibility and control.

---

## 11. Caching in AWS — ElastiCache,

```
Redis vs Memcached — when to use
which and how cache invalidation
works?
```

AWS ElastiCache supports both Redis and Memcached. Redis is preferred when persistence, replication, clustering, pub/sub messaging, or advanced data structures are required. Memcached is suitable for simple key-value caching with high throughput and minimal complexity. Cache invalidation can be performed using Time-To-Live (TTL), explicit deletion when data changes, write-through caching, or cache-aside patterns. In most production environments, Redis is commonly used because of its durability, replication support, and richer feature set.

---

## 12. Cross-account AWS access — how to

```
give Lambda in Account A access
to S3 in Account B?
(S3 bucket policy + IAM role +
STS AssumeRole)
```

To provide Lambda access across AWS accounts, I create an IAM Role in Account B with permissions to access the target S3 bucket. The role's trust policy allows Account A's Lambda execution role to assume it. The S3 bucket policy grants access to the IAM role in Account B. From Lambda in Account A, STS AssumeRole is used to obtain temporary credentials for the role in Account B. Using these temporary credentials, Lambda can securely access the S3 bucket. This approach follows AWS security best practices by avoiding long-term credentials.

---

## 13. Terraform state file — what is it,

```
where is it stored, what if it
gets corrupted?
```

The Terraform state file stores the mapping between infrastructure resources and Terraform configuration. It helps Terraform determine what resources already exist and what changes need to be applied. By default, the state file is stored locally as terraform.tfstate, but in production environments it is typically stored remotely in an S3 bucket with DynamoDB state locking. If the state file becomes corrupted, I first restore it from backups or versioning enabled on the backend storage. Terraform state commands such as state rm, state mv, and import can be used to repair inconsistencies. Proper backup and versioning of state files are critical to prevent infrastructure management issues.

---

## 14. Static pods in Kubernetes — what

```
are they, where are they defined,
who manages them without scheduler?
```

Static Pods are pods managed directly by the Kubelet rather than through the Kubernetes API Server. They are defined as YAML manifests on the node filesystem, typically under /etc/kubernetes/manifests. The Kubelet continuously monitors this directory and automatically creates or restarts the pods if needed. Static Pods are commonly used for critical control plane components such as kube-apiserver, kube-controller-manager, and kube-scheduler in self-managed Kubernetes clusters. Since they are directly managed by Kubelet, they do not require scheduling by the Kubernetes Scheduler.

---

## 15. Monitoring — difference between

```
application monitoring and
infrastructure monitoring?
Which tools for each?
```

Infrastructure monitoring focuses on the health and performance of servers, containers, networks, storage, and operating systems. Metrics include CPU utilization, memory consumption, disk usage, and network traffic. Tools commonly used are Prometheus, Node Exporter, Grafana, CloudWatch, and Datadog. Application monitoring focuses on application behavior, response times, request rates, error rates, transaction tracing, and user experience. Tools such as Prometheus application exporters, Grafana, New Relic, Dynatrace, AppDynamics, Elastic APM, and OpenTelemetry are commonly used. Together, both monitoring types provide complete observability and help quickly identify whether an issue originates from infrastructure or the application layer.

---
