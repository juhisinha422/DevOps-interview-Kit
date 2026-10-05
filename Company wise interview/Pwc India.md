# DevOps Interview Questions & Answers (4+ Years Experience)

## 1. How you can migrate from RHEL 7 to RHEL 9 without downtime.

A direct in-place upgrade from RHEL 7 to RHEL 9 is generally not supported in production environments. The safest approach is a blue-green migration strategy. First, I would provision new RHEL 9 servers alongside the existing RHEL 7 servers. Then I would install all required packages, application dependencies, security patches, monitoring agents, and configurations on the RHEL 9 servers. After validating application functionality in lower environments, I would add the new servers to the load balancer target group while keeping the RHEL 7 servers active. Traffic would gradually be shifted to the RHEL 9 servers using weighted routing or load balancer registration. Once application health, performance, and database connectivity are verified, the old RHEL 7 servers can be drained and decommissioned. This approach provides zero downtime because users continue accessing the application through the load balancer during the migration.

---

## 2. How do you configure and implement an ASG?

An Auto Scaling Group (ASG) automatically adjusts the number of EC2 instances based on workload demand. First, I create a Launch Template containing the AMI, instance type, security groups, IAM role, storage configuration, and user-data script. Then I create an Auto Scaling Group and attach it to one or more Availability Zones. The ASG is integrated with an Application Load Balancer so that traffic is distributed across healthy instances. Scaling policies are configured using CloudWatch metrics such as CPU utilization, memory utilization, request count, or custom application metrics. For example, if CPU utilization exceeds 70% for five consecutive minutes, the ASG launches additional instances. When utilization drops below a threshold, excess instances are terminated. This ensures application availability while optimizing infrastructure costs.

---

## 3. Explain the setup and complete traffic flow of an ALB.

The traffic flow starts when a user accesses a domain name such as [www.company.com](http://www.company.com). Route 53 resolves the domain and returns the DNS name of the Application Load Balancer. The request reaches the ALB listener, typically running on port 80 or 443. If HTTPS is used, SSL termination occurs at the ALB using an ACM certificate. Listener rules inspect the request path, host header, or query parameters and forward traffic to the appropriate target group. The target group contains healthy EC2 instances, ECS tasks, or Kubernetes pods registered through the AWS Load Balancer Controller. Health checks continuously monitor backend targets and only healthy targets receive traffic. The backend application processes the request and sends the response back through the ALB to the client. This architecture provides high availability, SSL offloading, path-based routing, and automatic failover.

---

## 4. If RDS is connected to RHEL 7 then it was working but when we try to migrate to RHEL 9 it stop working. Tell me the reason for it.

There can be multiple reasons. First, I would verify network connectivity between the RHEL 9 server and RDS instance using telnet, nc, or database client tools. Security Group rules may allow the old server IP but not the new one. DNS resolution issues can also occur if the RHEL 9 server is unable to resolve the RDS endpoint correctly. Another common issue is missing database client libraries or JDBC drivers that were available on RHEL 7 but not installed on RHEL 9. SELinux policies may also block outbound database communication. SSL/TLS compatibility issues can occur because newer operating systems enforce stronger encryption protocols that older database drivers may not support. I would investigate connectivity, firewall rules, DNS, database drivers, certificates, and application logs before identifying the exact root cause.

---

## 5. How we can connect ECR to EKS.

Amazon EKS integrates directly with Amazon ECR for container image storage. The worker nodes or pods must have permissions to pull images from ECR. In modern EKS environments, I use IAM Roles for Service Accounts (IRSA) or node IAM roles that contain permissions such as `ecr:GetAuthorizationToken`, `ecr:BatchGetImage`, and `ecr:GetDownloadUrlForLayer`. Developers build Docker images through CI/CD pipelines and push them to ECR repositories. Kubernetes deployment manifests reference the ECR image URL. When a pod starts, Kubernetes instructs the container runtime to pull the image from ECR using the assigned IAM permissions. Once the image is downloaded, the container starts successfully.

---

## 6. Where you are storing docker images in your project.

In my projects, Docker images are stored in Amazon ECR because we primarily use AWS infrastructure. The CI/CD pipeline builds the image, performs vulnerability scanning, tags it with the build version, and pushes it to ECR. Kubernetes deployment manifests then reference the image stored in ECR. ECR provides secure image storage, IAM-based access control, image scanning, lifecycle policies, and seamless integration with EKS. In some projects, organizations may use Docker Hub, JFrog Artifactory, Harbor, or Nexus Registry, but ECR is commonly used in AWS environments.

---

## 7. If night 2 AM IST our disk /var is full then what you will do so that application will not create any impact.

My first priority would be preventing application downtime. I would immediately identify what is consuming space using commands such as `df -h`, `du -sh`, and `find`. Common causes include application logs, temporary files, container logs, audit logs, or failed backup files. If large log files are consuming space, I would rotate or compress them using logrotate without deleting critical data. For containerized workloads, I would inspect Docker or Kubernetes logs under `/var/lib/docker` or `/var/log/containers`. If immediate cleanup is insufficient, I would extend the filesystem using LVM or attach additional storage. After stabilizing the situation, I would investigate why monitoring alerts failed to trigger earlier and implement proactive alerting to avoid future incidents.

---

## 8. In CloudFront service if we updated the logo and it is reflecting in USA and India region but it is not reflecting in Australia region why?

This usually indicates a caching issue. CloudFront uses edge locations across different geographic regions. The updated logo may have been cached in the Australia edge location while other regions have already fetched the latest version from the origin. I would first check cache-control headers and object TTL settings. Then I would verify whether the updated object was uploaded correctly to the origin bucket. If required, I would create a CloudFront invalidation request to clear cached content globally. Another possibility is browser caching at the client side. In production, versioned static assets such as `logo-v2.png` are preferred because they eliminate cache consistency problems.

---

## 9. How you are mapping the domain pwc.com will same if IP address will change also.

This is achieved through DNS abstraction. The domain is mapped to a DNS record in Route 53 rather than directly exposing server IP addresses to users. If the backend infrastructure changes, only the DNS record or load balancer target changes. For example, Route 53 may point to an Application Load Balancer using an Alias Record. The ALB can dynamically add or remove backend instances without affecting the public domain name. Even if server IP addresses change because of scaling, upgrades, or failover events, users continue accessing the application through the same domain name because DNS handles the mapping transparently.

---

## 10. How you are using k8s in your project. In-depth discussion on Kubernetes architecture, deployments, scaling, services, troubleshooting.

In my current project, Kubernetes serves as the primary orchestration platform for microservices running on AWS EKS. Applications are packaged as Docker images and deployed through CI/CD pipelines using Helm charts and ArgoCD. Kubernetes manages container scheduling, scaling, networking, service discovery, rolling updates, and self-healing.

The Kubernetes architecture consists of a control plane and worker nodes. The control plane includes the API Server, which acts as the cluster entry point; etcd, which stores cluster state; the Scheduler, which assigns pods to nodes; and the Controller Manager, which continuously reconciles the desired state. Worker nodes run kubelet, kube-proxy, and container runtimes such as containerd.

For deployments, I typically use Deployment resources with rolling update strategies. Services are exposed using ClusterIP for internal communication and Ingress with an AWS Application Load Balancer for external access. Autoscaling is implemented using HPA based on CPU and memory metrics collected through the Metrics Server.

When troubleshooting issues, I follow a layered approach. For pod failures, I check pod status, events, logs, and resource utilization. For networking issues, I verify Services, Endpoints, Ingress rules, DNS resolution, and Network Policies. For node issues, I inspect node conditions, kubelet logs, and resource pressure indicators. Monitoring is implemented using Prometheus and Grafana, while centralized logging is used for faster root cause analysis. This combination allows us to maintain highly available and scalable production workloads.

