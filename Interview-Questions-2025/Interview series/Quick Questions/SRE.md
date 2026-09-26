# AWS + Kubernetes + SRE — In-Depth DevOps Interview Preparation

## Introduction

For a 4+ years DevOps/SRE interview, I should not answer only with definitions. I should explain the architecture, internal flow, troubleshooting methodology, production impact, and the reason behind each technology. The interviewer may ask follow-up questions such as **“What happens internally?”, “How is it configured?”, “What happens if this fails?”, “How would you troubleshoot it?”, and “How would you monitor it?”** Therefore, this preparation focuses mainly on AWS, Kubernetes/EKS, SRE, observability, incident management, and production troubleshooting.

---

# 1. AWS ARCHITECTURE

## 1. Explain a typical production AWS architecture.

A typical production architecture can consist of Route 53 for DNS, an Application Load Balancer for HTTP/HTTPS traffic, a VPC containing public and private subnets, Amazon EKS for containerized workloads, and services such as RDS, ElastiCache, S3, ECR, CloudWatch, Secrets Manager, and IAM. Usually, internet-facing components such as an ALB are placed across multiple Availability Zones, while EKS worker nodes and databases are deployed in private subnets. Route 53 resolves the application domain to the ALB. The ALB terminates HTTPS using an ACM certificate and forwards traffic to healthy Kubernetes targets. Kubernetes Services and Ingress resources then route the request to application Pods. The application communicates with databases or other backend services through private networking. Security Groups, IAM, Kubernetes RBAC, Network Policies, encryption, and secrets management provide different layers of security.

```text
                         Internet
                            |
                        Route 53
                            |
                     WAF (optional)
                            |
                    Application ALB
                     HTTPS :443
                            |
                    ACM TLS Certificate
                            |
                      Target Group
                            |
                       EKS Cluster
                            |
                +-----------+-----------+
                |           |           |
              Node        Node        Node
                |           |           |
              Pod         Pod         Pod
                |           |           |
                +-----------+-----------+
                            |
                 Internal Services
                            |
                   +--------+--------+
                   |                 |
                  RDS             ElastiCache
```

---

## 2. Explain the complete request flow from browser to Kubernetes Pod.

When a user enters `https://app.example.com`, the browser first performs DNS resolution. Route 53 resolves the domain to the Application Load Balancer. The browser then establishes a TCP connection to the ALB and performs the TLS handshake on port 443. If the ALB has an HTTPS listener with an ACM certificate attached, the ALB performs TLS termination. After decrypting the request, the listener evaluates its rules and forwards the request to the configured target group. In EKS, the target group can use instance targets or IP targets depending on the AWS Load Balancer Controller configuration. In instance mode, traffic may reach a worker node through a NodePort and then be forwarded through the Kubernetes Service to a Pod. In IP mode, the ALB can target Pod IP addresses directly. The application Pod processes the request and may communicate with RDS, Redis, another microservice, S3, or an external API. The response follows the reverse path back through the load balancer to the client.

The important point in an interview is not simply saying **ALB → Ingress → Pod**. I should clearly identify which component is actually implementing the routing. For example, an AWS Load Balancer Controller-managed ALB can route directly to Pods in IP target mode, while an NGINX Ingress architecture introduces NGINX as another routing layer.

---

# 2. AWS VPC AND NETWORKING

## 3. Explain VPC architecture in depth.

A VPC is an isolated virtual network in AWS where I define IP address ranges, subnets, routing, and network security. I normally divide the VPC into public and private subnets across multiple Availability Zones. Public subnets have a route to an Internet Gateway and are suitable for internet-facing resources such as ALBs. Private application subnets do not have direct inbound internet connectivity and are typically used for EKS worker nodes and application workloads. If private resources need outbound internet access, they can use a NAT Gateway located in a public subnet. Route tables determine where traffic is sent, while Security Groups act as stateful virtual firewalls. NACLs operate at the subnet level and are stateless. In a production architecture, I would normally distribute resources across at least two Availability Zones to improve availability.

---

## 4. What is the difference between Security Group and NACL?

A Security Group is a stateful firewall associated with resources such as EC2 instances or network interfaces. If I allow inbound traffic on a particular port, the corresponding response traffic is automatically allowed because Security Groups are stateful. Security Groups support allow rules and are generally the primary network access-control mechanism for AWS resources.

A Network ACL operates at the subnet level and is stateless. It evaluates both inbound and outbound traffic independently and supports allow and deny rules. Therefore, if I allow an inbound connection, I must also consider the outbound response rule. In troubleshooting, I normally check the Security Group first for resource-level connectivity and then verify NACLs and route tables when the issue involves subnet-level networking.

---

## 5. What happens if a private EKS node needs to access the internet?

A private EKS node generally does not have a direct route to an Internet Gateway. If it needs outbound internet access, the private subnet route table normally sends `0.0.0.0/0` traffic to a NAT Gateway located in a public subnet. The NAT Gateway then communicates with the Internet Gateway and the external service. For EKS, this can be important when nodes need to pull container images or communicate with external endpoints. However, AWS services such as ECR and S3 can often be accessed through VPC endpoints, which can reduce NAT dependency and improve security and cost efficiency.

---

# 3. AWS LOAD BALANCER + TLS

## 6. Explain TLS termination with ALB and ACM.

TLS termination occurs at the ALB when the client connects using HTTPS and the ALB listener has an ACM certificate configured. The browser establishes a TLS session with the ALB, and the ALB uses the certificate to prove its identity. Once the TLS session is established, the ALB decrypts the incoming HTTPS request and forwards it to the backend target according to the listener rules. Depending on the security requirements, traffic from the ALB to the backend can remain HTTP or continue as HTTPS. Security Groups do not terminate TLS; they only control network traffic. ACM is responsible for certificate lifecycle management, while the ALB listener is responsible for using the certificate during TLS termination.

---

## 7. How does ALB know which EKS Pods are healthy?

The exact mechanism depends on the target type and controller configuration. With the AWS Load Balancer Controller, Kubernetes resources such as Ingress and Service are watched by the controller, which configures AWS load-balancing resources accordingly. The controller can register worker nodes or Pod IP addresses into target groups. The ALB continuously performs health checks against the registered targets. If a target fails its health check, the ALB stops sending new requests to that target. This is separate from Kubernetes readiness probes, although in a properly designed system both Kubernetes health and ALB target health should represent application availability appropriately.

---

# 4. EKS ARCHITECTURE

## 8. Explain Amazon EKS architecture.

Amazon EKS provides a managed Kubernetes control plane. The control plane contains components such as the Kubernetes API Server, scheduler, controller managers, and the etcd data store, with AWS managing the control-plane infrastructure. The worker/data plane consists of compute resources such as managed node groups, self-managed EC2 nodes, or other supported compute mechanisms. Each worker node runs components such as kubelet and a container runtime. Kubernetes resources are submitted through the API Server. The scheduler assigns unscheduled Pods to suitable nodes, controllers continuously reconcile desired state with actual state, and kubelet ensures the assigned Pods are running on the node. EKS also integrates with AWS services for IAM, load balancing, networking, storage, and observability.

---

## 9. What happens internally when a Pod is created?

When I create a Deployment, the manifest is sent to the Kubernetes API Server. The API Server authenticates and authorizes the request and persists the desired state. The Deployment controller creates or updates a ReplicaSet, and the ReplicaSet ensures that the required number of Pods exist. Newly created Pods initially have no assigned node. The scheduler watches for unscheduled Pods and evaluates nodes using resource requirements, affinity, taints/tolerations, topology constraints, and other scheduling rules. Once the scheduler selects a node, the kubelet on that node receives the Pod specification. The kubelet asks the container runtime to pull the image if necessary and start the containers. Networking and storage are configured, probes begin executing, and the Pod status is continuously reported back to the API Server.

---

# 5. KUBERNETES SCHEDULING

## 10. How does Kubernetes decide which node should run a Pod?

The Kubernetes scheduler does not simply choose the node with the lowest CPU usage. It first considers whether nodes are feasible for the Pod based on requirements such as CPU and memory requests, node selectors, node affinity, taints and tolerations, topology constraints, volume constraints, and other scheduling conditions. After filtering unsuitable nodes, the scheduler scores the remaining candidates and selects the most appropriate node. For example, if a Pod requests `2 CPU` and `4Gi` memory, a node that does not have sufficient allocatable resources will not be selected. This is why resource requests are extremely important for predictable scheduling.

---

# 6. REQUESTS, LIMITS AND CPU THROTTLING

## 11. What is the difference between CPU request and CPU limit?

A CPU request represents the amount of CPU Kubernetes uses for scheduling decisions and resource accounting. If a Pod requests `500m`, the scheduler considers that requested capacity when deciding where the Pod can run. A CPU limit defines the maximum CPU consumption enforced by the container runtime and Linux cgroups. If a container reaches its CPU limit, it is normally throttled rather than restarted. This is different from memory. If a container exceeds its memory limit and the kernel kills it, Kubernetes may report `OOMKilled`, often associated with exit code 137.

---

## 12. What happens when a container reaches its CPU limit?

When a container reaches its CPU limit, Linux cgroup CPU controls can throttle the process. The container continues running, but it may receive less CPU time than it wants. This can result in increased request latency, slow processing, queue buildup, and potentially cascading failures if the application is sensitive to latency. CPU throttling should therefore be investigated through container CPU metrics, throttling metrics, application latency, and request rate rather than assuming that the container restarted.

---

# 7. KUBERNETES PROBES

## 13. Explain readiness, liveness and startup probes.

A readiness probe determines whether a Pod is ready to receive traffic. If readiness fails, Kubernetes removes the Pod from the Service's ready endpoints, allowing the application to continue running without receiving new traffic. A liveness probe determines whether the application is still functioning; repeated liveness failures can cause kubelet to restart the container. A startup probe is useful for slow-starting applications because it gives the application time to initialize before liveness checking becomes active. In production, readiness should represent whether the application can safely serve requests, while liveness should detect a genuinely unhealthy process rather than temporary dependency failures.

---

# 8. HPA

## 14. How does HPA work internally?

The Horizontal Pod Autoscaler periodically obtains resource or custom metrics and compares the current value with the configured target. For CPU utilization, the utilization percentage is generally calculated relative to the CPU requests of the Pods. For example, if a container requests `500m` CPU and consumes approximately `400m`, its utilization is around 80% of the request. If the HPA target is 70%, the controller calculates that additional replicas may be required. HPA then updates the desired replica count of the target workload, such as a Deployment. The scheduler subsequently places new Pods on suitable nodes. If the cluster does not have enough capacity, node-level autoscaling mechanisms such as Cluster Autoscaler or Karpenter may add capacity.

---

## 15. Does CPU limit determine HPA utilization?

No. HPA CPU utilization is normally based on the Pod/container CPU **requests**, not CPU limits. This is an important interview point. If I configure a CPU request of `500m` and a limit of `1 CPU`, and the container uses `400m`, utilization for HPA purposes is approximately 80% of the request. Therefore, incorrectly configured or missing requests can make resource-based HPA behavior inaccurate or unavailable.

---

# 9. KEDA

## 16. What is KEDA and when would you use it?

KEDA, or Kubernetes Event-driven Autoscaling, is useful when scaling should depend on external or event-based metrics rather than only CPU or memory. Examples include Kafka lag, queue depth, Azure Service Bus messages, AWS SQS queue length, or other supported scalers. A KEDA `ScaledObject` defines the workload and trigger conditions. KEDA evaluates the external metric and integrates with Kubernetes autoscaling mechanisms to adjust replicas. This is particularly useful for event-driven microservices where CPU utilization may remain low even though a large queue is waiting to be processed.

---

## 17. What is the difference between KEDA ScaledObject and ScaledJob?

A `ScaledObject` is normally used when I want to scale a long-running workload such as a Deployment based on an external metric. A `ScaledJob` is designed for workloads where each unit of work can be represented as a Kubernetes Job. For example, if thousands of independent tasks are waiting in a queue, a ScaledJob can create Jobs to process those tasks. The choice depends on whether I need persistent application replicas or individual batch workers.

---

# 10. CLUSTER AUTOSCALER VS KARPENTER

## 18. What is Cluster Autoscaler?

Cluster Autoscaler operates at the node-group level. When Pods cannot be scheduled because the cluster lacks sufficient resources, Cluster Autoscaler can increase the desired size of a suitable node group. It can also remove underutilized nodes when their workloads can be safely rescheduled. Its behavior is closely connected to the configured node groups.

---

## 19. What is Karpenter?

Karpenter is a Kubernetes node provisioning mechanism designed to dynamically provision compute capacity based on pending Pod requirements. Instead of simply increasing a predefined node group, it evaluates pending workloads and provisions suitable EC2 capacity based on requirements such as instance type, architecture, capacity type, zone, and resource requirements. It can therefore provide more flexible node provisioning. In an interview, I would distinguish between understanding the architecture and claiming production hands-on experience. If I have not operated Karpenter in production, I would state that clearly.

---

# 11. STATEFULSET

## 20. Deployment vs StatefulSet?

A Deployment is generally used for stateless workloads where Pods are interchangeable. Pods created by a Deployment normally do not have stable identities. StatefulSet is designed for stateful applications that require stable Pod identity, predictable naming, ordered operations, and persistent storage association. For example, StatefulSet Pods may be named `database-0`, `database-1`, and `database-2`, and each Pod can have its own persistent volume through `volumeClaimTemplates`. StatefulSet itself does not guarantee that data can never be lost; data durability still depends on the underlying storage, application behavior, backup strategy, and failure scenario.

---

# 12. KUBERNETES STORAGE

## 21. Explain PV, PVC and StorageClass.

A PersistentVolume is a Kubernetes representation of persistent storage. A PersistentVolumeClaim is a request for storage made by a workload. A StorageClass defines how storage should be dynamically provisioned. In a dynamic provisioning scenario, the application creates a PVC referencing a StorageClass, and the CSI driver provisions the underlying storage, such as an AWS EBS volume or EFS-backed storage.

```text
Pod
 |
PVC
 |
StorageClass
 |
CSI Driver
 |
AWS EBS / EFS
```

---

## 22. EBS vs EFS?

Amazon EBS provides block storage and is commonly used for workloads requiring block-level persistent storage, such as databases and applications requiring filesystem access from a node. EBS volumes are associated with an Availability Zone, so scheduling and volume attachment constraints must be considered. EFS provides managed NFS-based file storage and supports shared access patterns such as RWX. Therefore, if multiple Pods across Availability Zones need concurrent shared filesystem access, EFS is often more suitable. If I need high-performance block storage for a workload that primarily operates from one node at a time, EBS may be more appropriate.

---

# 13. KUBERNETES TROUBLESHOOTING

## 23. A Pod is in CrashLoopBackOff. How do you troubleshoot it?

CrashLoopBackOff means the container has repeatedly started and terminated, and Kubernetes is applying an increasing restart backoff. I first check `kubectl describe pod` for events and container state. Then I check current and previous logs using `kubectl logs` and `kubectl logs --previous`. I verify the exit code, termination reason, environment variables, ConfigMaps, Secrets, mounted volumes, image version, command/entrypoint, probes, resource limits, and application dependencies. If the container is being OOM-killed, I investigate memory consumption and limits. If the process exits because of an application exception, I investigate the logs and configuration. I then reproduce or validate the root cause and deploy the fix rather than simply restarting the Pod repeatedly.

---

## 24. A Pod is Running but users receive 503. How do you troubleshoot?

A `Running` Pod does not necessarily mean the application is serving traffic. I start from the user-facing symptom and trace the request path through the observability platform. I check the load balancer health, target health, Ingress/controller metrics, Service endpoints, readiness status, application logs, and distributed traces. I verify whether the Service has ready endpoints using Kubernetes commands and whether the application is actually listening on the expected port. I also check Network Policies, Security Groups, DNS, target-group configuration, and backend dependency failures. If centralized observability is available, I use metrics, logs, and traces to narrow the problem instead of manually checking every component blindly.

---

## 25. How do you troubleshoot ImagePullBackOff?

I first inspect the Pod events because Kubernetes usually provides useful information about why the image could not be pulled. I verify the image name and tag, registry availability, node network connectivity, ECR permissions, image-pull secrets where applicable, and whether the image actually exists. For ECR, I verify the node or workload IAM permissions and network access to ECR endpoints. If the image was recently pushed, I also verify that the tag referenced by the Deployment matches the actual image tag. After correcting the issue, I verify that the Pod pulls the image successfully and becomes Ready.

---

# 14. NODE NOTREADY

## 26. A Kubernetes node becomes NotReady. What do you check?

I first inspect the node conditions and events to determine whether the issue is related to kubelet, memory pressure, disk pressure, PID pressure, networking, or the container runtime. I check node CPU and memory, filesystem usage, inode usage, kubelet logs, container runtime status, CNI health, and network connectivity. I also check whether the node has lost communication with the control plane. If the node is unhealthy and workloads can be safely moved, I may cordon and drain it according to the incident procedure, but in production I first consider PodDisruptionBudgets and workload availability. The permanent fix depends on whether the root cause is infrastructure, networking, storage, kubelet, or resource exhaustion.

---

# 15. EKS NETWORKING

## 27. What is AWS VPC CNI in EKS?

AWS VPC CNI allows Kubernetes Pods to receive IP addresses from the VPC networking infrastructure. The CNI plugin manages network interfaces and secondary IP addresses that can be assigned to Pods. This means the number of Pods that can run on a node can be constrained by available ENIs and IP addresses as well as CPU and memory. Therefore, a Pod can remain Pending even when CPU and memory are available if the node cannot allocate another Pod IP.

---

## 28. How would you troubleshoot Pod Pending due to IP exhaustion?

I would first inspect the Pod events and scheduler messages to confirm that the issue is networking/IP capacity rather than CPU or memory. Then I would inspect subnet free IP addresses, ENI/IP allocation on the affected nodes, AWS VPC CNI logs and metrics, and the node's Pod capacity. I would also check whether the subnet has sufficient address space across Availability Zones. The solution could involve adding capacity, using appropriately sized subnets, improving CNI configuration, adding additional subnets, or changing the node architecture. This is why EKS capacity planning must consider not only CPU and memory but also available VPC IP addresses.

---

# 16. AWS S3

## 29. Explain S3 lifecycle management.

S3 Lifecycle policies automatically transition objects between storage classes or expire objects after defined periods. For example, frequently accessed data can remain in S3 Standard initially and older data can transition to less expensive storage classes. Temporary files can be automatically deleted after a defined period. In production, lifecycle rules help reduce storage costs and prevent unlimited accumulation of old logs, backups, artifacts, or application data. Before implementing expiration policies, I verify retention, compliance, recovery, and business requirements.

---

# 17. AWS EBS

## 30. Explain gp3, io1 and io2.

gp3 is a general-purpose SSD volume type where storage size, IOPS, and throughput can be configured independently within supported limits. It is commonly used when I need predictable general-purpose performance without paying for unnecessarily high provisioned performance. io1 and io2 are provisioned-IOPS SSD options intended for workloads requiring higher and more predictable IOPS and durability characteristics. The appropriate choice depends on workload performance requirements, latency, IOPS, throughput, availability, and cost.

---

# 18. AWS COST OPTIMIZATION

## 31. How would you reduce AWS costs in a production environment?

I start with visibility rather than immediately deleting resources. I use AWS cost reports and monitoring to identify major cost drivers. For EC2 and EKS, I look for underutilized nodes, oversized instances, idle resources, and workloads that could use autoscaling or Spot capacity where appropriate. For EBS, I identify unattached volumes and evaluate gp2-to-gp3 migration where appropriate. For S3, I use lifecycle policies and appropriate storage classes. For ECR, I use lifecycle policies to remove obsolete images. For predictable compute usage, I evaluate Savings Plans or Reserved Instances depending on the workload. I also review NAT Gateway, load balancer, data transfer, CloudWatch log retention, and other service-specific costs. Any optimization must be validated against availability and performance requirements.

---

# 19. SRE FUNDAMENTALS

## 32. What is SRE?

Site Reliability Engineering is an engineering approach to operating reliable production systems by applying software engineering principles to infrastructure and operations. Instead of relying primarily on manual operational work, SRE emphasizes automation, monitoring, measurable reliability targets, incident response, capacity planning, scalability, and continuous improvement. The objective is not simply to keep servers running; it is to maintain a measurable level of service reliability while allowing engineering teams to continue delivering changes.

---

## 33. DevOps vs SRE?

DevOps is a broad culture and set of practices focused on collaboration between development and operations, automation, CI/CD, infrastructure as code, and faster and safer software delivery. SRE applies engineering principles specifically to reliability and production operations. SRE introduces concepts such as SLI, SLO, error budgets, toil reduction, incident management, and reliability engineering. In practice, DevOps and SRE overlap heavily, and many organizations use both approaches together.

---

# 20. SLI, SLO AND SLA

## 34. What is an SLI?

An SLI, or Service Level Indicator, is a measurable indicator of service behavior. Examples include availability, latency, error rate, throughput, and successful request percentage. For example, if I measure the percentage of successful API requests over a period, that measurement can be an availability-related SLI.

---

## 35. What is an SLO?

An SLO, or Service Level Objective, defines the reliability target for an SLI. For example, an API might have an availability SLO of 99.9% over a month. The SLO provides an engineering target that can be monitored and used to make operational decisions.

---

## 36. What is an SLA?

An SLA is a formal agreement between a service provider and customer that defines expected service levels and potentially business consequences if those levels are not met. An SLO is primarily an engineering reliability objective, while an SLA is generally a contractual or business commitment.

---

# 21. ERROR BUDGET

## 37. What is an error budget?

An error budget represents the amount of unreliability permitted by an SLO. If the SLO is 99.9% availability, the remaining 0.1% represents the allowed unreliability during the defined measurement period. The error budget provides a practical balance between reliability and delivery velocity. If the service is comfortably within its error budget, the organization can generally take more delivery risk. If the service is consuming its error budget rapidly, the team may prioritize reliability work, incident reduction, testing, and safer deployment practices.

The important point is that an error budget should be tied to a defined SLO and measurement window rather than treated as a generic percentage.

---

# 22. FOUR GOLDEN SIGNALS

## 38. What are the four Golden Signals?

The four Golden Signals are **latency, traffic, errors, and saturation**. Latency measures how long requests take. Traffic measures demand or request volume. Errors measure failed requests or unsuccessful operations. Saturation indicates how close the system is to its capacity limits, such as CPU, memory, disk, database connections, or queue capacity. These signals help SRE teams quickly determine whether a system is healthy and where to investigate when an incident occurs.

---

# 23. OBSERVABILITY

## 39. What is observability?

Observability is the ability to understand the internal state and behavior of a system from the telemetry it produces. The three commonly discussed pillars are metrics, logs, and traces. Metrics provide numerical measurements such as CPU, latency, request rate, and error rate. Logs provide detailed event information. Distributed traces show how an individual request travels across services and where time is spent. Good observability allows engineers to move from symptom to probable root cause quickly instead of manually checking every infrastructure layer.

---

## 40. Why is centralized observability important?

In a distributed microservices environment, a single request may pass through a load balancer, ingress, gateway, multiple microservices, cache, database, and external APIs. Checking every component manually is slow and increases mean time to resolution. Centralized observability correlates metrics, logs, traces, infrastructure information, and application information. For example, if API latency increases, an APM trace can show that most of the request time is being spent in a database call, allowing the engineer to investigate the database rather than randomly checking Kubernetes CPU.

---

# 24. DATADOG

## 41. How would you troubleshoot high API latency using Datadog?

I would first confirm the alert and determine the affected service, endpoints, timeframe, and customer impact. I would check service-level metrics such as request rate, error rate, P50/P95/P99 latency, CPU, memory, network, and container health. Then I would use Datadog APM and distributed traces to identify slow requests and inspect individual spans. If a trace shows that most of the latency is coming from a database query, downstream API, cache, or another microservice, I would move the investigation to that dependency. I would correlate the trace with application logs and infrastructure metrics. The goal is to use centralized telemetry to narrow the root cause rather than manually inspecting ALB, ingress, Pods, and nodes one by one.

---

## 42. What is APM?

Application Performance Monitoring provides visibility into application behavior rather than only infrastructure metrics. APM can provide information about request latency, errors, service dependencies, database calls, external APIs, traces, and transaction performance. Tools such as Datadog and Dynatrace use agents or instrumentation to collect application telemetry. APM is especially useful when infrastructure metrics look normal but users are experiencing high latency or errors.

---

# 25. DYNATRACE

## 43. What is Dynatrace used for?

Dynatrace is an observability and application performance monitoring platform that provides infrastructure monitoring, application monitoring, distributed tracing, service dependency information, logs, metrics, dashboards, and automated problem analysis. Its value in an SRE environment is that it can correlate infrastructure and application telemetry and help identify the service or dependency responsible for a production problem. If my strongest hands-on experience is with Prometheus, Grafana, CloudWatch, and logging platforms, I should explain that honestly while demonstrating that I understand Dynatrace's architecture and troubleshooting model.

---

# 26. PAGERDUTY

## 44. What is PagerDuty?

PagerDuty is an incident management and on-call platform that receives alerts from monitoring systems and routes them to the appropriate responders. A typical flow is:

```text
Application
    |
Monitoring
    |
Alert
    |
PagerDuty
    |
Escalation Policy
    |
On-Call Engineer
    |
Acknowledge
    |
Investigate
    |
Mitigate
    |
Resolve
```

PagerDuty helps ensure that production alerts reach the correct engineer even outside normal working hours.

---

## 45. What happens when a critical production alert is triggered?

A monitoring system such as Datadog detects that a defined threshold or anomaly has occurred. The monitor generates an alert and sends an event to PagerDuty. PagerDuty determines which service and escalation policy are associated with the event and notifies the current on-call engineer. The engineer acknowledges the incident, starts investigation, and communicates impact and status. If the engineer does not acknowledge the incident within the configured time, PagerDuty can escalate it to another responder. After mitigation and recovery, the incident is resolved and the team performs RCA/postmortem activities when required.

---

# 27. SERVICENOW

## 46. What is ServiceNow used for in SRE/IT operations?

ServiceNow is commonly used for IT service management, including incident, problem, change, and request management. During a production incident, an incident record can capture the impact, affected service, priority, assignment group, timeline, investigation, resolution, and communication. A recurring or major incident may result in a Problem record for root-cause investigation. Changes to production systems can be tracked through Change records, including approvals, implementation plans, rollback plans, and validation steps.

---

# 28. INCIDENT MANAGEMENT

## 47. Explain your approach to a production incident.

My first priority during an incident is to understand customer impact and stabilize the system. I first acknowledge the alert and determine the affected service, scope, severity, and start time. I then check the main service-level indicators such as availability, error rate, latency, and traffic. After that I correlate infrastructure metrics, application logs, and distributed traces to identify the failing component or dependency. If there is a safe mitigation such as rollback, scaling, removing an unhealthy instance, disabling a problematic feature, or failing over to a healthy dependency, I prioritize mitigation rather than spending too long searching for the perfect root cause while users remain impacted. Once the service is stable, I continue the RCA, identify the root and contributing causes, document the timeline, and create preventive actions.

---

# 29. PRODUCTION RCA — 503 ERROR

## 48. Users are receiving HTTP 503. How would you troubleshoot?

I first establish whether the 503 is generated by the ALB, ingress controller, application gateway, or application itself. I check the load balancer target health and request metrics, then inspect Kubernetes Service endpoints and Pod readiness. A common cause is that Pods are Running but not Ready, leaving the Service without usable endpoints. I also check Ingress rules, target-group configuration, application listener ports, readiness probes, DNS, Security Groups, Network Policies, and application logs. If APM is available, I trace failed requests to identify whether the application or a downstream dependency is returning the failure. Once the source of the 503 is identified, I mitigate the issue and then investigate why the health condition occurred.

---

# 30. PRODUCTION RCA — 100% DISK

## 49. Disk usage is 100%. What do you do?

I first determine whether the problem is filesystem capacity or inode exhaustion using `df -h` and `df -i`. Then I identify which filesystem and directories are consuming space using `du`. I inspect application logs, container logs, temporary files, core dumps, backups, and old artifacts. I also check for deleted files that are still held open by running processes because `df` can show high usage while `du` does not account for the deleted file. `lsof +L1` can help identify such files. I avoid blindly deleting files from production. After identifying the source, I safely rotate or remove unnecessary data, expand the filesystem if required, and implement log rotation, retention policies, monitoring, and alerting to prevent recurrence.

---

# 31. PRODUCTION RCA — HIGH CPU

## 50. A production application suddenly reaches 100% CPU. How do you troubleshoot?

I first determine whether the CPU increase is at the node, container, or application process level. I correlate CPU with traffic, latency, error rate, deployment activity, and application metrics. In Kubernetes I check Pod CPU usage, CPU requests and limits, throttling, replica count, and HPA behavior. I also investigate whether a recent deployment introduced an inefficient code path, infinite loop, expensive query, traffic spike, or unexpected workload. If the service is overloaded, I can scale replicas as an immediate mitigation while investigating the root cause. I then validate whether the extra replicas are actually reducing latency and whether the cluster has sufficient node capacity.

---

# 32. PRODUCTION RCA — OOMKilled

## 51. Why does a Pod become OOMKilled?

A container can be OOM-killed when it exceeds its configured memory limit and the Linux kernel/cgroup memory enforcement kills the process. I check `kubectl describe pod`, container termination reason, exit code, historical memory metrics, application logs, heap usage if applicable, and memory limits. I then determine whether the issue is a legitimate workload increase, memory leak, inefficient application behavior, incorrect resource limits, or insufficient capacity. Increasing the memory limit may provide temporary relief but should not be treated as the permanent solution if the application has a memory leak.

---

# 33. SRE TOIL

## 52. What is toil?

Toil is repetitive, manual, automatable operational work that does not create lasting value and tends to grow with system scale. Examples include manually restarting failed workloads, manually checking the same dashboards every day, repeatedly cleaning logs, or manually performing predictable deployments. SRE teams try to reduce toil through automation, self-healing systems, better monitoring, runbooks, infrastructure as code, and automated remediation.

---

# 34. MTTR AND MTTD

## 53. What is MTTD and MTTR?

MTTD is Mean Time to Detect, which measures how long it takes to detect a problem after it begins. MTTR is commonly used as Mean Time to Restore or Recover, measuring how long it takes to return the service to normal after detection. Good observability, meaningful alerts, automation, runbooks, and incident processes can reduce both metrics. However, reducing MTTR should not mean immediately applying risky fixes; the objective is fast and safe recovery.

---

# 35. ALERTING

## 54. What makes a good SRE alert?

A good alert should represent a condition that requires human action. It should be actionable, have an appropriate severity, include enough context to begin investigation, and avoid excessive noise. Alerts should preferably be based on service impact or meaningful symptoms rather than every small infrastructure fluctuation. For example, a sustained high error rate or SLO burn may be more useful than simply alerting every time CPU crosses 80% for a few seconds. Alert quality is important because excessive false positives create alert fatigue.

---

# 36. SLO BURN RATE

## 55. What is error-budget burn rate?

Burn rate describes how quickly a service consumes its allowed error budget. If a service is consuming the error budget much faster than expected, it indicates that the SLO may be violated before the measurement period ends. SRE teams can use burn-rate alerts to detect both severe short-term outages and slower reliability degradation. This is generally more meaningful than relying only on static CPU or latency thresholds.

---

# 37. SRE CAPACITY PLANNING

## 56. How would you perform capacity planning for Kubernetes?

I look at historical traffic, CPU and memory utilization, request rates, latency, replica counts, node utilization, autoscaling behavior, Pod density, VPC IP capacity, storage requirements, and growth projections. For EKS, I consider not only EC2 CPU and memory but also subnet IP availability, ENI limits, EBS capacity, load balancer limits, and application dependencies. I use load testing and historical metrics to establish safe scaling thresholds. The goal is to have sufficient headroom for expected traffic spikes while avoiding excessive idle capacity.

---

# 38. ZERO-DOWNTIME DEPLOYMENT

## 57. How do you perform zero-downtime deployments in Kubernetes?

I use multiple replicas, appropriate readiness probes, rolling deployment strategy, controlled surge and unavailable settings, PodDisruptionBudgets where appropriate, and proper application shutdown handling. During a rolling update, Kubernetes creates new Pods while gradually terminating old Pods. Readiness prevents a new Pod from receiving traffic until it is actually ready. Graceful termination allows the application to finish or reject new requests cleanly before shutdown. I also monitor error rate and latency during the rollout and keep rollback available if the new version causes problems.

---

# 39. ROLLBACK

## 58. How do you validate a Kubernetes rollback?

After initiating rollback, I verify that the expected ReplicaSet/image version is running and that the desired number of Pods are Ready. I then check application health, error rate, latency, logs, downstream dependencies, and business-level metrics. I do not consider a rollback successful simply because Pods are Running. The actual validation must confirm that users can successfully perform the affected operations.

---

# 40. CI/CD

## 59. Explain a production CI/CD pipeline.

A typical pipeline starts when code is pushed to Git. A webhook triggers Jenkins or another CI platform. The pipeline checks out the source, performs compilation/build steps, runs unit tests and static analysis, performs security scanning, packages the application, and publishes the artifact to an artifact repository or builds and pushes a container image to a registry such as ECR. The deployment stage then updates the target environment using Kubernetes, a deployment platform, or another release mechanism. Production deployment normally includes approval or change-management controls depending on the organization. After deployment, automated health checks and observability validate the release. If the new version causes errors, the pipeline or operator can perform a controlled rollback.

---

# 41. TERRAFORM

## 60. How does Terraform maintain infrastructure state?

Terraform maintains a state file that maps configuration resources to real infrastructure objects. During `terraform plan`, Terraform compares the desired configuration with the current state and refreshes information from the provider to determine what changes are required. In a team environment, the state should normally be stored remotely with locking and access controls. State locking prevents multiple engineers or pipelines from modifying the same state simultaneously. If state becomes inconsistent or a lock is stuck, I investigate the backend and lock owner rather than manually deleting state data without understanding the impact.

---

# 42. DISASTER RECOVERY

## 61. Explain RPO and RTO.

RPO, or Recovery Point Objective, defines how much data loss measured in time is acceptable. For example, an RPO of 15 minutes means the organization is prepared to potentially lose up to 15 minutes of data. RTO, or Recovery Time Objective, defines how quickly the service should be restored after a failure. For example, an RTO of one hour means the recovery process should restore service within the target timeframe. DR architecture should therefore be designed based on business requirements rather than simply copying production infrastructure.

---

# 43. IMPORTANT SRE SCENARIOS

## 62. API latency increases from 200 ms to 12 seconds, but CPU and memory are normal. What do you do?

I would not assume that the Kubernetes infrastructure is healthy simply because CPU and memory are normal. I would first check the latency distribution, especially P95 and P99, along with request rate and error rate. Then I would use APM distributed tracing to identify where the additional latency is being introduced. The trace may reveal slow database queries, connection-pool exhaustion, downstream API latency, cache misses, network problems, lock contention, or application-level processing. I would correlate the trace with application logs and dependency metrics. If a downstream service is responsible, I would investigate that dependency rather than scaling the application blindly.

---

## 63. Kafka lag suddenly increases from 500 to millions. What do you check?

I would first determine whether the increase is caused by higher producer traffic, reduced consumer throughput, consumer failures, partition imbalance, network problems, broker issues, or application processing latency. I would check consumer group health, consumer count, partition assignment, consumer processing time, rebalance activity, broker health, partition distribution, and producer rate. I would also inspect application logs for errors or downstream dependency latency. If consumers are healthy but traffic has increased significantly, scaling consumers may help, subject to Kafka partition parallelism. If consumers are failing because of a downstream database, simply increasing consumers may make the dependency problem worse.

---

## 64. A deployment completed successfully but application errors increased. What do you do?

I first correlate the error increase with the deployment timestamp. I check the application logs, error rate, latency, traces, health checks, configuration changes, database migrations, dependency compatibility, and resource behavior. If the new version is clearly responsible and customer impact is significant, I prioritize rollback or another safe mitigation. After recovery, I compare the old and new versions to identify the exact failure. Preventive actions may include stronger automated tests, canary deployment, contract testing, better health checks, and deployment verification.

---

# 44. SRE INCIDENT FLOW

## 65. Explain the complete incident lifecycle.

A typical incident lifecycle begins with detection through monitoring or a customer report. The alert is routed to the on-call engineer through the incident-management system. The responder acknowledges the incident, assesses severity and customer impact, and begins investigation. During a major incident, an incident commander may coordinate responders while other engineers investigate specific components. The immediate goal is mitigation and service restoration. Once the service is stable, the team performs root-cause analysis, documents the timeline and contributing factors, and creates corrective and preventive actions. A blameless postmortem focuses on system improvements rather than assigning personal blame.

---

# 45. WHAT AN ARCHITECT MAY ASK

## 66. A Pod is Running. Why can the application still be unavailable?

A Running state only means the container process is running from Kubernetes' perspective. The application can still be unavailable because the process may not be listening on the expected port, the readiness probe may be failing, the Service may have no ready endpoints, the Ingress or ALB may be misconfigured, DNS may be failing, network policies may block traffic, or a downstream dependency may be unavailable. Therefore, I never use Pod Running as proof that the application is healthy. I validate readiness, Service endpoints, network path, application health, and user-facing telemetry.

---

## 67. Why can HPA scale Pods but users still experience high latency?

HPA can increase the number of application Pods, but that does not guarantee that the bottleneck is the application CPU. The bottleneck could be database connections, database CPU, an external API, Kafka, Redis, network bandwidth, a shared lock, or insufficient node capacity. HPA can also create Pods that remain Pending if the cluster has insufficient resources. Therefore, autoscaling must be designed across the complete dependency chain and monitored using service-level indicators.

---

## 68. Why is centralized observability better than manually checking ALB → Ingress → Pod?

Manual investigation can work for small systems but becomes slow and error-prone in a distributed production environment. Centralized observability allows me to correlate the same request across infrastructure, load balancer, Kubernetes, application, database, and downstream services. For example, a distributed trace can show that an API spent 10 seconds waiting for a database call. Without tracing, I might waste time checking CPU, nodes, ingress, and Pods even though the actual bottleneck is the database.

---

# 46. PRODUCTION TROUBLESHOOTING MINDSET

## 69. What is your general troubleshooting methodology?

My troubleshooting methodology is to start from the customer-visible symptom and work inward using evidence. I first establish the impact, affected services, start time, and whether there was a recent change. Then I examine the four golden signals—latency, traffic, errors, and saturation—followed by infrastructure metrics, application logs, and distributed traces. I correlate the timeline with deployments, configuration changes, infrastructure events, and dependency health. I prioritize mitigation if customer impact is ongoing, then continue toward the root cause. Finally, I validate recovery using service-level and business-level metrics and create preventive actions so that the same failure is less likely to recur.

---

# 47. MOST IMPORTANT COMMANDS

## Kubernetes

```bash
kubectl get pods -A
kubectl get nodes
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> --previous
kubectl get events --sort-by=.lastTimestamp
kubectl get svc
kubectl get endpoints
kubectl get endpointslices
kubectl get ingress
kubectl top pods
kubectl top nodes
kubectl describe node <node>
kubectl get pvc
kubectl get pv
kubectl get storageclass
```

## Linux

```bash
top
htop
free -m
df -h
df -i
du -sh *
iostat
vmstat
ps aux
ss -lntp
lsof
lsof +L1
journalctl
systemctl status <service>
```

## AWS CLI

```bash
aws sts get-caller-identity
aws ec2 describe-instances
aws eks describe-cluster
aws eks describe-nodegroup
aws elbv2 describe-load-balancers
aws elbv2 describe-target-health
aws logs describe-log-groups
aws s3 ls
aws ecr describe-repositories
```

---

# 48. FINAL HIGH-PRIORITY PREPARATION

## AWS — Must Prepare In Depth

1. VPC architecture
2. Public/private subnets
3. Route tables
4. Internet Gateway
5. NAT Gateway
6. Security Groups
7. NACL
8. ALB
9. Target Groups
10. ACM
11. TLS termination
12. Route 53
13. EKS
14. EKS networking
15. VPC CNI
16. ENI/IP allocation
17. IAM
18. ECR
19. S3
20. EBS
21. EFS
22. RDS
23. CloudWatch
24. Cost optimization
25. DR / RPO / RTO

---

# 49. KUBERNETES — Must Prepare In Depth

1. Kubernetes architecture
2. API Server
3. etcd
4. Scheduler
5. Controllers
6. kubelet
7. Services
8. Ingress
9. AWS Load Balancer Controller
10. DNS/CoreDNS
11. CNI
12. Deployment
13. ReplicaSet
14. StatefulSet
15. DaemonSet
16. Jobs/CronJobs
17. ConfigMap
18. Secrets
19. RBAC
20. PV/PVC
21. StorageClass
22. EBS CSI
23. EFS CSI
24. Readiness/Liveness/Startup
25. Requests/Limits
26. QoS
27. HPA
28. VPA
29. KEDA
30. Cluster Autoscaler
31. Karpenter
32. Taints/Tolerations
33. Affinity
34. PDB
35. Rolling deployment
36. Rollback
37. CrashLoopBackOff
38. OOMKilled
39. ImagePullBackOff
40. Pending
41. Node NotReady
42. 502/503
43. DNS issues
44. Network issues
45. IP exhaustion

---

# 50. SRE — Must Prepare In Depth

1. What is SRE?
2. DevOps vs SRE
3. SLI
4. SLO
5. SLA
6. Error budget
7. Error-budget burn
8. Four Golden Signals
9. Observability
10. Metrics
11. Logs
12. Distributed tracing
13. APM
14. Datadog
15. Dynatrace
16. PagerDuty
17. ServiceNow
18. Incident management
19. Incident Commander
20. On-call
21. Escalation
22. Severity
23. MTTR
24. MTTD
25. Toil
26. Automation
27. Capacity planning
28. Reliability
29. Availability
30. Scalability
31. Production RCA
32. Postmortem
33. Blameless culture
34. Alert fatigue
35. SLO-based alerting
36. Burn-rate alerting
37. Disaster recovery
38. Business continuity
39. Chaos engineering
40. Continuous improvement

---

# 51. Final Interview Strategy

For the next round, I should answer technical questions using this structure:

```text
1. Definition
      ↓
2. Architecture
      ↓
3. Internal working
      ↓
4. Production example
      ↓
5. Troubleshooting
      ↓
6. Monitoring
      ↓
7. Failure scenario
      ↓
8. Prevention
```

For example, if asked about HPA, I should not stop at **“HPA scales Pods based on CPU.”** A stronger answer is:

> “HPA continuously evaluates the configured metric against the target. For CPU-based scaling, utilization is calculated relative to CPU requests. When utilization remains above the target, the HPA controller calculates a higher desired replica count and updates the workload. The scheduler then places the new Pods on suitable nodes. If the cluster does not have sufficient capacity, Cluster Autoscaler or Karpenter may provision additional nodes. I would then monitor Pod readiness, application latency, error rate, and node capacity to verify that scaling actually resolved the customer impact.”

That is the level of depth I should target for an architect/SRE round.

---

# 52. The 10 Scenarios I Must Be Able to Explain Without Hesitation

### Scenario 1

**Production API returns 503 but all Pods show Running.**

### Scenario 2

**API latency increases from 200 ms to 12 seconds while CPU and memory remain normal.**

### Scenario 3

**Pod goes into CrashLoopBackOff after deployment.**

### Scenario 4

**Pod gets OOMKilled repeatedly.**

### Scenario 5

**Pods are Pending even though nodes have CPU and memory available.**

### Scenario 6

**EKS subnet runs out of IP addresses.**

### Scenario 7

**HPA scales Pods but latency continues increasing.**

### Scenario 8

**Kafka consumer lag increases from hundreds to millions.**

### Scenario 9

**Production server filesystem reaches 100%.**

### Scenario 10

**A deployment completes successfully but production error rate immediately increases.**

For each scenario, I should be able to explain:

```text
Symptom
   ↓
Impact
   ↓
Detection
   ↓
Metrics
   ↓
Logs
   ↓
Traces
   ↓
Investigation
   ↓
Immediate Mitigation
   ↓
Root Cause
   ↓
Permanent Fix
   ↓
Monitoring
   ↓
Prevention
```

---

# Final Reminder

For this interview, I should focus less on memorizing definitions and more on explaining **how components interact in a real production environment**.

The highest-value combination to prepare is:

```text
AWS
  +
EKS / Kubernetes
  +
Observability
  +
APM
  +
SRE
  +
Incident Management
  +
Production Troubleshooting
```

If the interviewer asks **“Why?”**, I should explain the reason.

If they ask **“How?”**, I should explain the implementation.

If they ask **“What happens internally?”**, I should explain the component flow.

If they ask **“How did you troubleshoot it?”**, I should give a production-style RCA.

If they ask **“How do you prevent it?”**, I should explain monitoring, automation, capacity planning, testing, and preventive controls.

That is the depth expected from a 4+ years DevOps/SRE engineer.


# AWS + Kubernetes + SRE — In-Depth DevOps Interview Preparation

## Introduction

For a 4+ years DevOps/SRE interview, I should not answer only with definitions. I should explain the architecture, internal flow, troubleshooting methodology, production impact, and the reason behind each technology. The interviewer may ask follow-up questions such as **“What happens internally?”, “How is it configured?”, “What happens if this fails?”, “How would you troubleshoot it?”, and “How would you monitor it?”** Therefore, this preparation focuses mainly on AWS, Kubernetes/EKS, SRE, observability, incident management, and production troubleshooting.

---

# 1. AWS ARCHITECTURE

## 1. Explain a typical production AWS architecture.

A typical production architecture can consist of Route 53 for DNS, an Application Load Balancer for HTTP/HTTPS traffic, a VPC containing public and private subnets, Amazon EKS for containerized workloads, and services such as RDS, ElastiCache, S3, ECR, CloudWatch, Secrets Manager, and IAM. Usually, internet-facing components such as an ALB are placed across multiple Availability Zones, while EKS worker nodes and databases are deployed in private subnets. Route 53 resolves the application domain to the ALB. The ALB terminates HTTPS using an ACM certificate and forwards traffic to healthy Kubernetes targets. Kubernetes Services and Ingress resources then route the request to application Pods. The application communicates with databases or other backend services through private networking. Security Groups, IAM, Kubernetes RBAC, Network Policies, encryption, and secrets management provide different layers of security.

```text
                         Internet
                            |
                        Route 53
                            |
                     WAF (optional)
                            |
                    Application ALB
                     HTTPS :443
                            |
                    ACM TLS Certificate
                            |
                      Target Group
                            |
                       EKS Cluster
                            |
                +-----------+-----------+
                |           |           |
              Node        Node        Node
                |           |           |
              Pod         Pod         Pod
                |           |           |
                +-----------+-----------+
                            |
                 Internal Services
                            |
                   +--------+--------+
                   |                 |
                  RDS             ElastiCache
```

---

## 2. Explain the complete request flow from browser to Kubernetes Pod.

When a user enters `https://app.example.com`, the browser first performs DNS resolution. Route 53 resolves the domain to the Application Load Balancer. The browser then establishes a TCP connection to the ALB and performs the TLS handshake on port 443. If the ALB has an HTTPS listener with an ACM certificate attached, the ALB performs TLS termination. After decrypting the request, the listener evaluates its rules and forwards the request to the configured target group. In EKS, the target group can use instance targets or IP targets depending on the AWS Load Balancer Controller configuration. In instance mode, traffic may reach a worker node through a NodePort and then be forwarded through the Kubernetes Service to a Pod. In IP mode, the ALB can target Pod IP addresses directly. The application Pod processes the request and may communicate with RDS, Redis, another microservice, S3, or an external API. The response follows the reverse path back through the load balancer to the client.

The important point in an interview is not simply saying **ALB → Ingress → Pod**. I should clearly identify which component is actually implementing the routing. For example, an AWS Load Balancer Controller-managed ALB can route directly to Pods in IP target mode, while an NGINX Ingress architecture introduces NGINX as another routing layer.

---

# 2. AWS VPC AND NETWORKING

## 3. Explain VPC architecture in depth.

A VPC is an isolated virtual network in AWS where I define IP address ranges, subnets, routing, and network security. I normally divide the VPC into public and private subnets across multiple Availability Zones. Public subnets have a route to an Internet Gateway and are suitable for internet-facing resources such as ALBs. Private application subnets do not have direct inbound internet connectivity and are typically used for EKS worker nodes and application workloads. If private resources need outbound internet access, they can use a NAT Gateway located in a public subnet. Route tables determine where traffic is sent, while Security Groups act as stateful virtual firewalls. NACLs operate at the subnet level and are stateless. In a production architecture, I would normally distribute resources across at least two Availability Zones to improve availability.

---

## 4. What is the difference between Security Group and NACL?

A Security Group is a stateful firewall associated with resources such as EC2 instances or network interfaces. If I allow inbound traffic on a particular port, the corresponding response traffic is automatically allowed because Security Groups are stateful. Security Groups support allow rules and are generally the primary network access-control mechanism for AWS resources.

A Network ACL operates at the subnet level and is stateless. It evaluates both inbound and outbound traffic independently and supports allow and deny rules. Therefore, if I allow an inbound connection, I must also consider the outbound response rule. In troubleshooting, I normally check the Security Group first for resource-level connectivity and then verify NACLs and route tables when the issue involves subnet-level networking.

---

## 5. What happens if a private EKS node needs to access the internet?

A private EKS node generally does not have a direct route to an Internet Gateway. If it needs outbound internet access, the private subnet route table normally sends `0.0.0.0/0` traffic to a NAT Gateway located in a public subnet. The NAT Gateway then communicates with the Internet Gateway and the external service. For EKS, this can be important when nodes need to pull container images or communicate with external endpoints. However, AWS services such as ECR and S3 can often be accessed through VPC endpoints, which can reduce NAT dependency and improve security and cost efficiency.

---

# 3. AWS LOAD BALANCER + TLS

## 6. Explain TLS termination with ALB and ACM.

TLS termination occurs at the ALB when the client connects using HTTPS and the ALB listener has an ACM certificate configured. The browser establishes a TLS session with the ALB, and the ALB uses the certificate to prove its identity. Once the TLS session is established, the ALB decrypts the incoming HTTPS request and forwards it to the backend target according to the listener rules. Depending on the security requirements, traffic from the ALB to the backend can remain HTTP or continue as HTTPS. Security Groups do not terminate TLS; they only control network traffic. ACM is responsible for certificate lifecycle management, while the ALB listener is responsible for using the certificate during TLS termination.

---

## 7. How does ALB know which EKS Pods are healthy?

The exact mechanism depends on the target type and controller configuration. With the AWS Load Balancer Controller, Kubernetes resources such as Ingress and Service are watched by the controller, which configures AWS load-balancing resources accordingly. The controller can register worker nodes or Pod IP addresses into target groups. The ALB continuously performs health checks against the registered targets. If a target fails its health check, the ALB stops sending new requests to that target. This is separate from Kubernetes readiness probes, although in a properly designed system both Kubernetes health and ALB target health should represent application availability appropriately.

---

# 4. EKS ARCHITECTURE

## 8. Explain Amazon EKS architecture.

Amazon EKS provides a managed Kubernetes control plane. The control plane contains components such as the Kubernetes API Server, scheduler, controller managers, and the etcd data store, with AWS managing the control-plane infrastructure. The worker/data plane consists of compute resources such as managed node groups, self-managed EC2 nodes, or other supported compute mechanisms. Each worker node runs components such as kubelet and a container runtime. Kubernetes resources are submitted through the API Server. The scheduler assigns unscheduled Pods to suitable nodes, controllers continuously reconcile desired state with actual state, and kubelet ensures the assigned Pods are running on the node. EKS also integrates with AWS services for IAM, load balancing, networking, storage, and observability.

---

## 9. What happens internally when a Pod is created?

When I create a Deployment, the manifest is sent to the Kubernetes API Server. The API Server authenticates and authorizes the request and persists the desired state. The Deployment controller creates or updates a ReplicaSet, and the ReplicaSet ensures that the required number of Pods exist. Newly created Pods initially have no assigned node. The scheduler watches for unscheduled Pods and evaluates nodes using resource requirements, affinity, taints/tolerations, topology constraints, and other scheduling rules. Once the scheduler selects a node, the kubelet on that node receives the Pod specification. The kubelet asks the container runtime to pull the image if necessary and start the containers. Networking and storage are configured, probes begin executing, and the Pod status is continuously reported back to the API Server.

---

# 5. KUBERNETES SCHEDULING

## 10. How does Kubernetes decide which node should run a Pod?

The Kubernetes scheduler does not simply choose the node with the lowest CPU usage. It first considers whether nodes are feasible for the Pod based on requirements such as CPU and memory requests, node selectors, node affinity, taints and tolerations, topology constraints, volume constraints, and other scheduling conditions. After filtering unsuitable nodes, the scheduler scores the remaining candidates and selects the most appropriate node. For example, if a Pod requests `2 CPU` and `4Gi` memory, a node that does not have sufficient allocatable resources will not be selected. This is why resource requests are extremely important for predictable scheduling.

---

# 6. REQUESTS, LIMITS AND CPU THROTTLING

## 11. What is the difference between CPU request and CPU limit?

A CPU request represents the amount of CPU Kubernetes uses for scheduling decisions and resource accounting. If a Pod requests `500m`, the scheduler considers that requested capacity when deciding where the Pod can run. A CPU limit defines the maximum CPU consumption enforced by the container runtime and Linux cgroups. If a container reaches its CPU limit, it is normally throttled rather than restarted. This is different from memory. If a container exceeds its memory limit and the kernel kills it, Kubernetes may report `OOMKilled`, often associated with exit code 137.

---

## 12. What happens when a container reaches its CPU limit?

When a container reaches its CPU limit, Linux cgroup CPU controls can throttle the process. The container continues running, but it may receive less CPU time than it wants. This can result in increased request latency, slow processing, queue buildup, and potentially cascading failures if the application is sensitive to latency. CPU throttling should therefore be investigated through container CPU metrics, throttling metrics, application latency, and request rate rather than assuming that the container restarted.

---

# 7. KUBERNETES PROBES

## 13. Explain readiness, liveness and startup probes.

A readiness probe determines whether a Pod is ready to receive traffic. If readiness fails, Kubernetes removes the Pod from the Service's ready endpoints, allowing the application to continue running without receiving new traffic. A liveness probe determines whether the application is still functioning; repeated liveness failures can cause kubelet to restart the container. A startup probe is useful for slow-starting applications because it gives the application time to initialize before liveness checking becomes active. In production, readiness should represent whether the application can safely serve requests, while liveness should detect a genuinely unhealthy process rather than temporary dependency failures.

---

# 8. HPA

## 14. How does HPA work internally?

The Horizontal Pod Autoscaler periodically obtains resource or custom metrics and compares the current value with the configured target. For CPU utilization, the utilization percentage is generally calculated relative to the CPU requests of the Pods. For example, if a container requests `500m` CPU and consumes approximately `400m`, its utilization is around 80% of the request. If the HPA target is 70%, the controller calculates that additional replicas may be required. HPA then updates the desired replica count of the target workload, such as a Deployment. The scheduler subsequently places new Pods on suitable nodes. If the cluster does not have enough capacity, node-level autoscaling mechanisms such as Cluster Autoscaler or Karpenter may add capacity.

---

## 15. Does CPU limit determine HPA utilization?

No. HPA CPU utilization is normally based on the Pod/container CPU **requests**, not CPU limits. This is an important interview point. If I configure a CPU request of `500m` and a limit of `1 CPU`, and the container uses `400m`, utilization for HPA purposes is approximately 80% of the request. Therefore, incorrectly configured or missing requests can make resource-based HPA behavior inaccurate or unavailable.

---

# 9. KEDA

## 16. What is KEDA and when would you use it?

KEDA, or Kubernetes Event-driven Autoscaling, is useful when scaling should depend on external or event-based metrics rather than only CPU or memory. Examples include Kafka lag, queue depth, Azure Service Bus messages, AWS SQS queue length, or other supported scalers. A KEDA `ScaledObject` defines the workload and trigger conditions. KEDA evaluates the external metric and integrates with Kubernetes autoscaling mechanisms to adjust replicas. This is particularly useful for event-driven microservices where CPU utilization may remain low even though a large queue is waiting to be processed.

---

## 17. What is the difference between KEDA ScaledObject and ScaledJob?

A `ScaledObject` is normally used when I want to scale a long-running workload such as a Deployment based on an external metric. A `ScaledJob` is designed for workloads where each unit of work can be represented as a Kubernetes Job. For example, if thousands of independent tasks are waiting in a queue, a ScaledJob can create Jobs to process those tasks. The choice depends on whether I need persistent application replicas or individual batch workers.

---

# 10. CLUSTER AUTOSCALER VS KARPENTER

## 18. What is Cluster Autoscaler?

Cluster Autoscaler operates at the node-group level. When Pods cannot be scheduled because the cluster lacks sufficient resources, Cluster Autoscaler can increase the desired size of a suitable node group. It can also remove underutilized nodes when their workloads can be safely rescheduled. Its behavior is closely connected to the configured node groups.

---

## 19. What is Karpenter?

Karpenter is a Kubernetes node provisioning mechanism designed to dynamically provision compute capacity based on pending Pod requirements. Instead of simply increasing a predefined node group, it evaluates pending workloads and provisions suitable EC2 capacity based on requirements such as instance type, architecture, capacity type, zone, and resource requirements. It can therefore provide more flexible node provisioning. In an interview, I would distinguish between understanding the architecture and claiming production hands-on experience. If I have not operated Karpenter in production, I would state that clearly.

---

# 11. STATEFULSET

## 20. Deployment vs StatefulSet?

A Deployment is generally used for stateless workloads where Pods are interchangeable. Pods created by a Deployment normally do not have stable identities. StatefulSet is designed for stateful applications that require stable Pod identity, predictable naming, ordered operations, and persistent storage association. For example, StatefulSet Pods may be named `database-0`, `database-1`, and `database-2`, and each Pod can have its own persistent volume through `volumeClaimTemplates`. StatefulSet itself does not guarantee that data can never be lost; data durability still depends on the underlying storage, application behavior, backup strategy, and failure scenario.

---

# 12. KUBERNETES STORAGE

## 21. Explain PV, PVC and StorageClass.

A PersistentVolume is a Kubernetes representation of persistent storage. A PersistentVolumeClaim is a request for storage made by a workload. A StorageClass defines how storage should be dynamically provisioned. In a dynamic provisioning scenario, the application creates a PVC referencing a StorageClass, and the CSI driver provisions the underlying storage, such as an AWS EBS volume or EFS-backed storage.

```text
Pod
 |
PVC
 |
StorageClass
 |
CSI Driver
 |
AWS EBS / EFS
```

---

## 22. EBS vs EFS?

Amazon EBS provides block storage and is commonly used for workloads requiring block-level persistent storage, such as databases and applications requiring filesystem access from a node. EBS volumes are associated with an Availability Zone, so scheduling and volume attachment constraints must be considered. EFS provides managed NFS-based file storage and supports shared access patterns such as RWX. Therefore, if multiple Pods across Availability Zones need concurrent shared filesystem access, EFS is often more suitable. If I need high-performance block storage for a workload that primarily operates from one node at a time, EBS may be more appropriate.

---

# 13. KUBERNETES TROUBLESHOOTING

## 23. A Pod is in CrashLoopBackOff. How do you troubleshoot it?

CrashLoopBackOff means the container has repeatedly started and terminated, and Kubernetes is applying an increasing restart backoff. I first check `kubectl describe pod` for events and container state. Then I check current and previous logs using `kubectl logs` and `kubectl logs --previous`. I verify the exit code, termination reason, environment variables, ConfigMaps, Secrets, mounted volumes, image version, command/entrypoint, probes, resource limits, and application dependencies. If the container is being OOM-killed, I investigate memory consumption and limits. If the process exits because of an application exception, I investigate the logs and configuration. I then reproduce or validate the root cause and deploy the fix rather than simply restarting the Pod repeatedly.

---

## 24. A Pod is Running but users receive 503. How do you troubleshoot?

A `Running` Pod does not necessarily mean the application is serving traffic. I start from the user-facing symptom and trace the request path through the observability platform. I check the load balancer health, target health, Ingress/controller metrics, Service endpoints, readiness status, application logs, and distributed traces. I verify whether the Service has ready endpoints using Kubernetes commands and whether the application is actually listening on the expected port. I also check Network Policies, Security Groups, DNS, target-group configuration, and backend dependency failures. If centralized observability is available, I use metrics, logs, and traces to narrow the problem instead of manually checking every component blindly.

---

## 25. How do you troubleshoot ImagePullBackOff?

I first inspect the Pod events because Kubernetes usually provides useful information about why the image could not be pulled. I verify the image name and tag, registry availability, node network connectivity, ECR permissions, image-pull secrets where applicable, and whether the image actually exists. For ECR, I verify the node or workload IAM permissions and network access to ECR endpoints. If the image was recently pushed, I also verify that the tag referenced by the Deployment matches the actual image tag. After correcting the issue, I verify that the Pod pulls the image successfully and becomes Ready.

---

# 14. NODE NOTREADY

## 26. A Kubernetes node becomes NotReady. What do you check?

I first inspect the node conditions and events to determine whether the issue is related to kubelet, memory pressure, disk pressure, PID pressure, networking, or the container runtime. I check node CPU and memory, filesystem usage, inode usage, kubelet logs, container runtime status, CNI health, and network connectivity. I also check whether the node has lost communication with the control plane. If the node is unhealthy and workloads can be safely moved, I may cordon and drain it according to the incident procedure, but in production I first consider PodDisruptionBudgets and workload availability. The permanent fix depends on whether the root cause is infrastructure, networking, storage, kubelet, or resource exhaustion.

---

# 15. EKS NETWORKING

## 27. What is AWS VPC CNI in EKS?

AWS VPC CNI allows Kubernetes Pods to receive IP addresses from the VPC networking infrastructure. The CNI plugin manages network interfaces and secondary IP addresses that can be assigned to Pods. This means the number of Pods that can run on a node can be constrained by available ENIs and IP addresses as well as CPU and memory. Therefore, a Pod can remain Pending even when CPU and memory are available if the node cannot allocate another Pod IP.

---

## 28. How would you troubleshoot Pod Pending due to IP exhaustion?

I would first inspect the Pod events and scheduler messages to confirm that the issue is networking/IP capacity rather than CPU or memory. Then I would inspect subnet free IP addresses, ENI/IP allocation on the affected nodes, AWS VPC CNI logs and metrics, and the node's Pod capacity. I would also check whether the subnet has sufficient address space across Availability Zones. The solution could involve adding capacity, using appropriately sized subnets, improving CNI configuration, adding additional subnets, or changing the node architecture. This is why EKS capacity planning must consider not only CPU and memory but also available VPC IP addresses.

---

# 16. AWS S3

## 29. Explain S3 lifecycle management.

S3 Lifecycle policies automatically transition objects between storage classes or expire objects after defined periods. For example, frequently accessed data can remain in S3 Standard initially and older data can transition to less expensive storage classes. Temporary files can be automatically deleted after a defined period. In production, lifecycle rules help reduce storage costs and prevent unlimited accumulation of old logs, backups, artifacts, or application data. Before implementing expiration policies, I verify retention, compliance, recovery, and business requirements.

---

# 17. AWS EBS

## 30. Explain gp3, io1 and io2.

gp3 is a general-purpose SSD volume type where storage size, IOPS, and throughput can be configured independently within supported limits. It is commonly used when I need predictable general-purpose performance without paying for unnecessarily high provisioned performance. io1 and io2 are provisioned-IOPS SSD options intended for workloads requiring higher and more predictable IOPS and durability characteristics. The appropriate choice depends on workload performance requirements, latency, IOPS, throughput, availability, and cost.

---

# 18. AWS COST OPTIMIZATION

## 31. How would you reduce AWS costs in a production environment?

I start with visibility rather than immediately deleting resources. I use AWS cost reports and monitoring to identify major cost drivers. For EC2 and EKS, I look for underutilized nodes, oversized instances, idle resources, and workloads that could use autoscaling or Spot capacity where appropriate. For EBS, I identify unattached volumes and evaluate gp2-to-gp3 migration where appropriate. For S3, I use lifecycle policies and appropriate storage classes. For ECR, I use lifecycle policies to remove obsolete images. For predictable compute usage, I evaluate Savings Plans or Reserved Instances depending on the workload. I also review NAT Gateway, load balancer, data transfer, CloudWatch log retention, and other service-specific costs. Any optimization must be validated against availability and performance requirements.

---

# 19. SRE FUNDAMENTALS

## 32. What is SRE?

Site Reliability Engineering is an engineering approach to operating reliable production systems by applying software engineering principles to infrastructure and operations. Instead of relying primarily on manual operational work, SRE emphasizes automation, monitoring, measurable reliability targets, incident response, capacity planning, scalability, and continuous improvement. The objective is not simply to keep servers running; it is to maintain a measurable level of service reliability while allowing engineering teams to continue delivering changes.

---

## 33. DevOps vs SRE?

DevOps is a broad culture and set of practices focused on collaboration between development and operations, automation, CI/CD, infrastructure as code, and faster and safer software delivery. SRE applies engineering principles specifically to reliability and production operations. SRE introduces concepts such as SLI, SLO, error budgets, toil reduction, incident management, and reliability engineering. In practice, DevOps and SRE overlap heavily, and many organizations use both approaches together.

---

# 20. SLI, SLO AND SLA

## 34. What is an SLI?

An SLI, or Service Level Indicator, is a measurable indicator of service behavior. Examples include availability, latency, error rate, throughput, and successful request percentage. For example, if I measure the percentage of successful API requests over a period, that measurement can be an availability-related SLI.

---

## 35. What is an SLO?

An SLO, or Service Level Objective, defines the reliability target for an SLI. For example, an API might have an availability SLO of 99.9% over a month. The SLO provides an engineering target that can be monitored and used to make operational decisions.

---

## 36. What is an SLA?

An SLA is a formal agreement between a service provider and customer that defines expected service levels and potentially business consequences if those levels are not met. An SLO is primarily an engineering reliability objective, while an SLA is generally a contractual or business commitment.

---

# 21. ERROR BUDGET

## 37. What is an error budget?

An error budget represents the amount of unreliability permitted by an SLO. If the SLO is 99.9% availability, the remaining 0.1% represents the allowed unreliability during the defined measurement period. The error budget provides a practical balance between reliability and delivery velocity. If the service is comfortably within its error budget, the organization can generally take more delivery risk. If the service is consuming its error budget rapidly, the team may prioritize reliability work, incident reduction, testing, and safer deployment practices.

The important point is that an error budget should be tied to a defined SLO and measurement window rather than treated as a generic percentage.

---

# 22. FOUR GOLDEN SIGNALS

## 38. What are the four Golden Signals?

The four Golden Signals are **latency, traffic, errors, and saturation**. Latency measures how long requests take. Traffic measures demand or request volume. Errors measure failed requests or unsuccessful operations. Saturation indicates how close the system is to its capacity limits, such as CPU, memory, disk, database connections, or queue capacity. These signals help SRE teams quickly determine whether a system is healthy and where to investigate when an incident occurs.

---

# 23. OBSERVABILITY

## 39. What is observability?

Observability is the ability to understand the internal state and behavior of a system from the telemetry it produces. The three commonly discussed pillars are metrics, logs, and traces. Metrics provide numerical measurements such as CPU, latency, request rate, and error rate. Logs provide detailed event information. Distributed traces show how an individual request travels across services and where time is spent. Good observability allows engineers to move from symptom to probable root cause quickly instead of manually checking every infrastructure layer.

---

## 40. Why is centralized observability important?

In a distributed microservices environment, a single request may pass through a load balancer, ingress, gateway, multiple microservices, cache, database, and external APIs. Checking every component manually is slow and increases mean time to resolution. Centralized observability correlates metrics, logs, traces, infrastructure information, and application information. For example, if API latency increases, an APM trace can show that most of the request time is being spent in a database call, allowing the engineer to investigate the database rather than randomly checking Kubernetes CPU.

---

# 24. DATADOG

## 41. How would you troubleshoot high API latency using Datadog?

I would first confirm the alert and determine the affected service, endpoints, timeframe, and customer impact. I would check service-level metrics such as request rate, error rate, P50/P95/P99 latency, CPU, memory, network, and container health. Then I would use Datadog APM and distributed traces to identify slow requests and inspect individual spans. If a trace shows that most of the latency is coming from a database query, downstream API, cache, or another microservice, I would move the investigation to that dependency. I would correlate the trace with application logs and infrastructure metrics. The goal is to use centralized telemetry to narrow the root cause rather than manually inspecting ALB, ingress, Pods, and nodes one by one.

---

## 42. What is APM?

Application Performance Monitoring provides visibility into application behavior rather than only infrastructure metrics. APM can provide information about request latency, errors, service dependencies, database calls, external APIs, traces, and transaction performance. Tools such as Datadog and Dynatrace use agents or instrumentation to collect application telemetry. APM is especially useful when infrastructure metrics look normal but users are experiencing high latency or errors.

---

# 25. DYNATRACE

## 43. What is Dynatrace used for?

Dynatrace is an observability and application performance monitoring platform that provides infrastructure monitoring, application monitoring, distributed tracing, service dependency information, logs, metrics, dashboards, and automated problem analysis. Its value in an SRE environment is that it can correlate infrastructure and application telemetry and help identify the service or dependency responsible for a production problem. If my strongest hands-on experience is with Prometheus, Grafana, CloudWatch, and logging platforms, I should explain that honestly while demonstrating that I understand Dynatrace's architecture and troubleshooting model.

---

# 26. PAGERDUTY

## 44. What is PagerDuty?

PagerDuty is an incident management and on-call platform that receives alerts from monitoring systems and routes them to the appropriate responders. A typical flow is:

```text
Application
    |
Monitoring
    |
Alert
    |
PagerDuty
    |
Escalation Policy
    |
On-Call Engineer
    |
Acknowledge
    |
Investigate
    |
Mitigate
    |
Resolve
```

PagerDuty helps ensure that production alerts reach the correct engineer even outside normal working hours.

---

## 45. What happens when a critical production alert is triggered?

A monitoring system such as Datadog detects that a defined threshold or anomaly has occurred. The monitor generates an alert and sends an event to PagerDuty. PagerDuty determines which service and escalation policy are associated with the event and notifies the current on-call engineer. The engineer acknowledges the incident, starts investigation, and communicates impact and status. If the engineer does not acknowledge the incident within the configured time, PagerDuty can escalate it to another responder. After mitigation and recovery, the incident is resolved and the team performs RCA/postmortem activities when required.

---

# 27. SERVICENOW

## 46. What is ServiceNow used for in SRE/IT operations?

ServiceNow is commonly used for IT service management, including incident, problem, change, and request management. During a production incident, an incident record can capture the impact, affected service, priority, assignment group, timeline, investigation, resolution, and communication. A recurring or major incident may result in a Problem record for root-cause investigation. Changes to production systems can be tracked through Change records, including approvals, implementation plans, rollback plans, and validation steps.

---

# 28. INCIDENT MANAGEMENT

## 47. Explain your approach to a production incident.

My first priority during an incident is to understand customer impact and stabilize the system. I first acknowledge the alert and determine the affected service, scope, severity, and start time. I then check the main service-level indicators such as availability, error rate, latency, and traffic. After that I correlate infrastructure metrics, application logs, and distributed traces to identify the failing component or dependency. If there is a safe mitigation such as rollback, scaling, removing an unhealthy instance, disabling a problematic feature, or failing over to a healthy dependency, I prioritize mitigation rather than spending too long searching for the perfect root cause while users remain impacted. Once the service is stable, I continue the RCA, identify the root and contributing causes, document the timeline, and create preventive actions.

---

# 29. PRODUCTION RCA — 503 ERROR

## 48. Users are receiving HTTP 503. How would you troubleshoot?

I first establish whether the 503 is generated by the ALB, ingress controller, application gateway, or application itself. I check the load balancer target health and request metrics, then inspect Kubernetes Service endpoints and Pod readiness. A common cause is that Pods are Running but not Ready, leaving the Service without usable endpoints. I also check Ingress rules, target-group configuration, application listener ports, readiness probes, DNS, Security Groups, Network Policies, and application logs. If APM is available, I trace failed requests to identify whether the application or a downstream dependency is returning the failure. Once the source of the 503 is identified, I mitigate the issue and then investigate why the health condition occurred.

---

# 30. PRODUCTION RCA — 100% DISK

## 49. Disk usage is 100%. What do you do?

I first determine whether the problem is filesystem capacity or inode exhaustion using `df -h` and `df -i`. Then I identify which filesystem and directories are consuming space using `du`. I inspect application logs, container logs, temporary files, core dumps, backups, and old artifacts. I also check for deleted files that are still held open by running processes because `df` can show high usage while `du` does not account for the deleted file. `lsof +L1` can help identify such files. I avoid blindly deleting files from production. After identifying the source, I safely rotate or remove unnecessary data, expand the filesystem if required, and implement log rotation, retention policies, monitoring, and alerting to prevent recurrence.

---

# 31. PRODUCTION RCA — HIGH CPU

## 50. A production application suddenly reaches 100% CPU. How do you troubleshoot?

I first determine whether the CPU increase is at the node, container, or application process level. I correlate CPU with traffic, latency, error rate, deployment activity, and application metrics. In Kubernetes I check Pod CPU usage, CPU requests and limits, throttling, replica count, and HPA behavior. I also investigate whether a recent deployment introduced an inefficient code path, infinite loop, expensive query, traffic spike, or unexpected workload. If the service is overloaded, I can scale replicas as an immediate mitigation while investigating the root cause. I then validate whether the extra replicas are actually reducing latency and whether the cluster has sufficient node capacity.

---

# 32. PRODUCTION RCA — OOMKilled

## 51. Why does a Pod become OOMKilled?

A container can be OOM-killed when it exceeds its configured memory limit and the Linux kernel/cgroup memory enforcement kills the process. I check `kubectl describe pod`, container termination reason, exit code, historical memory metrics, application logs, heap usage if applicable, and memory limits. I then determine whether the issue is a legitimate workload increase, memory leak, inefficient application behavior, incorrect resource limits, or insufficient capacity. Increasing the memory limit may provide temporary relief but should not be treated as the permanent solution if the application has a memory leak.

---

# 33. SRE TOIL

## 52. What is toil?

Toil is repetitive, manual, automatable operational work that does not create lasting value and tends to grow with system scale. Examples include manually restarting failed workloads, manually checking the same dashboards every day, repeatedly cleaning logs, or manually performing predictable deployments. SRE teams try to reduce toil through automation, self-healing systems, better monitoring, runbooks, infrastructure as code, and automated remediation.

---

# 34. MTTR AND MTTD

## 53. What is MTTD and MTTR?

MTTD is Mean Time to Detect, which measures how long it takes to detect a problem after it begins. MTTR is commonly used as Mean Time to Restore or Recover, measuring how long it takes to return the service to normal after detection. Good observability, meaningful alerts, automation, runbooks, and incident processes can reduce both metrics. However, reducing MTTR should not mean immediately applying risky fixes; the objective is fast and safe recovery.

---

# 35. ALERTING

## 54. What makes a good SRE alert?

A good alert should represent a condition that requires human action. It should be actionable, have an appropriate severity, include enough context to begin investigation, and avoid excessive noise. Alerts should preferably be based on service impact or meaningful symptoms rather than every small infrastructure fluctuation. For example, a sustained high error rate or SLO burn may be more useful than simply alerting every time CPU crosses 80% for a few seconds. Alert quality is important because excessive false positives create alert fatigue.

---

# 36. SLO BURN RATE

## 55. What is error-budget burn rate?

Burn rate describes how quickly a service consumes its allowed error budget. If a service is consuming the error budget much faster than expected, it indicates that the SLO may be violated before the measurement period ends. SRE teams can use burn-rate alerts to detect both severe short-term outages and slower reliability degradation. This is generally more meaningful than relying only on static CPU or latency thresholds.

---

# 37. SRE CAPACITY PLANNING

## 56. How would you perform capacity planning for Kubernetes?

I look at historical traffic, CPU and memory utilization, request rates, latency, replica counts, node utilization, autoscaling behavior, Pod density, VPC IP capacity, storage requirements, and growth projections. For EKS, I consider not only EC2 CPU and memory but also subnet IP availability, ENI limits, EBS capacity, load balancer limits, and application dependencies. I use load testing and historical metrics to establish safe scaling thresholds. The goal is to have sufficient headroom for expected traffic spikes while avoiding excessive idle capacity.

---

# 38. ZERO-DOWNTIME DEPLOYMENT

## 57. How do you perform zero-downtime deployments in Kubernetes?

I use multiple replicas, appropriate readiness probes, rolling deployment strategy, controlled surge and unavailable settings, PodDisruptionBudgets where appropriate, and proper application shutdown handling. During a rolling update, Kubernetes creates new Pods while gradually terminating old Pods. Readiness prevents a new Pod from receiving traffic until it is actually ready. Graceful termination allows the application to finish or reject new requests cleanly before shutdown. I also monitor error rate and latency during the rollout and keep rollback available if the new version causes problems.

---

# 39. ROLLBACK

## 58. How do you validate a Kubernetes rollback?

After initiating rollback, I verify that the expected ReplicaSet/image version is running and that the desired number of Pods are Ready. I then check application health, error rate, latency, logs, downstream dependencies, and business-level metrics. I do not consider a rollback successful simply because Pods are Running. The actual validation must confirm that users can successfully perform the affected operations.

---

# 40. CI/CD

## 59. Explain a production CI/CD pipeline.

A typical pipeline starts when code is pushed to Git. A webhook triggers Jenkins or another CI platform. The pipeline checks out the source, performs compilation/build steps, runs unit tests and static analysis, performs security scanning, packages the application, and publishes the artifact to an artifact repository or builds and pushes a container image to a registry such as ECR. The deployment stage then updates the target environment using Kubernetes, a deployment platform, or another release mechanism. Production deployment normally includes approval or change-management controls depending on the organization. After deployment, automated health checks and observability validate the release. If the new version causes errors, the pipeline or operator can perform a controlled rollback.

---

# 41. TERRAFORM

## 60. How does Terraform maintain infrastructure state?

Terraform maintains a state file that maps configuration resources to real infrastructure objects. During `terraform plan`, Terraform compares the desired configuration with the current state and refreshes information from the provider to determine what changes are required. In a team environment, the state should normally be stored remotely with locking and access controls. State locking prevents multiple engineers or pipelines from modifying the same state simultaneously. If state becomes inconsistent or a lock is stuck, I investigate the backend and lock owner rather than manually deleting state data without understanding the impact.

---

# 42. DISASTER RECOVERY

## 61. Explain RPO and RTO.

RPO, or Recovery Point Objective, defines how much data loss measured in time is acceptable. For example, an RPO of 15 minutes means the organization is prepared to potentially lose up to 15 minutes of data. RTO, or Recovery Time Objective, defines how quickly the service should be restored after a failure. For example, an RTO of one hour means the recovery process should restore service within the target timeframe. DR architecture should therefore be designed based on business requirements rather than simply copying production infrastructure.

---

# 43. IMPORTANT SRE SCENARIOS

## 62. API latency increases from 200 ms to 12 seconds, but CPU and memory are normal. What do you do?

I would not assume that the Kubernetes infrastructure is healthy simply because CPU and memory are normal. I would first check the latency distribution, especially P95 and P99, along with request rate and error rate. Then I would use APM distributed tracing to identify where the additional latency is being introduced. The trace may reveal slow database queries, connection-pool exhaustion, downstream API latency, cache misses, network problems, lock contention, or application-level processing. I would correlate the trace with application logs and dependency metrics. If a downstream service is responsible, I would investigate that dependency rather than scaling the application blindly.

---

## 63. Kafka lag suddenly increases from 500 to millions. What do you check?

I would first determine whether the increase is caused by higher producer traffic, reduced consumer throughput, consumer failures, partition imbalance, network problems, broker issues, or application processing latency. I would check consumer group health, consumer count, partition assignment, consumer processing time, rebalance activity, broker health, partition distribution, and producer rate. I would also inspect application logs for errors or downstream dependency latency. If consumers are healthy but traffic has increased significantly, scaling consumers may help, subject to Kafka partition parallelism. If consumers are failing because of a downstream database, simply increasing consumers may make the dependency problem worse.

---

## 64. A deployment completed successfully but application errors increased. What do you do?

I first correlate the error increase with the deployment timestamp. I check the application logs, error rate, latency, traces, health checks, configuration changes, database migrations, dependency compatibility, and resource behavior. If the new version is clearly responsible and customer impact is significant, I prioritize rollback or another safe mitigation. After recovery, I compare the old and new versions to identify the exact failure. Preventive actions may include stronger automated tests, canary deployment, contract testing, better health checks, and deployment verification.

---

# 44. SRE INCIDENT FLOW

## 65. Explain the complete incident lifecycle.

A typical incident lifecycle begins with detection through monitoring or a customer report. The alert is routed to the on-call engineer through the incident-management system. The responder acknowledges the incident, assesses severity and customer impact, and begins investigation. During a major incident, an incident commander may coordinate responders while other engineers investigate specific components. The immediate goal is mitigation and service restoration. Once the service is stable, the team performs root-cause analysis, documents the timeline and contributing factors, and creates corrective and preventive actions. A blameless postmortem focuses on system improvements rather than assigning personal blame.

---

# 45. WHAT AN ARCHITECT MAY ASK

## 66. A Pod is Running. Why can the application still be unavailable?

A Running state only means the container process is running from Kubernetes' perspective. The application can still be unavailable because the process may not be listening on the expected port, the readiness probe may be failing, the Service may have no ready endpoints, the Ingress or ALB may be misconfigured, DNS may be failing, network policies may block traffic, or a downstream dependency may be unavailable. Therefore, I never use Pod Running as proof that the application is healthy. I validate readiness, Service endpoints, network path, application health, and user-facing telemetry.

---

## 67. Why can HPA scale Pods but users still experience high latency?

HPA can increase the number of application Pods, but that does not guarantee that the bottleneck is the application CPU. The bottleneck could be database connections, database CPU, an external API, Kafka, Redis, network bandwidth, a shared lock, or insufficient node capacity. HPA can also create Pods that remain Pending if the cluster has insufficient resources. Therefore, autoscaling must be designed across the complete dependency chain and monitored using service-level indicators.

---

## 68. Why is centralized observability better than manually checking ALB → Ingress → Pod?

Manual investigation can work for small systems but becomes slow and error-prone in a distributed production environment. Centralized observability allows me to correlate the same request across infrastructure, load balancer, Kubernetes, application, database, and downstream services. For example, a distributed trace can show that an API spent 10 seconds waiting for a database call. Without tracing, I might waste time checking CPU, nodes, ingress, and Pods even though the actual bottleneck is the database.

---

# 46. PRODUCTION TROUBLESHOOTING MINDSET

## 69. What is your general troubleshooting methodology?

My troubleshooting methodology is to start from the customer-visible symptom and work inward using evidence. I first establish the impact, affected services, start time, and whether there was a recent change. Then I examine the four golden signals—latency, traffic, errors, and saturation—followed by infrastructure metrics, application logs, and distributed traces. I correlate the timeline with deployments, configuration changes, infrastructure events, and dependency health. I prioritize mitigation if customer impact is ongoing, then continue toward the root cause. Finally, I validate recovery using service-level and business-level metrics and create preventive actions so that the same failure is less likely to recur.

---

# 47. MOST IMPORTANT COMMANDS

## Kubernetes

```bash
kubectl get pods -A
kubectl get nodes
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> --previous
kubectl get events --sort-by=.lastTimestamp
kubectl get svc
kubectl get endpoints
kubectl get endpointslices
kubectl get ingress
kubectl top pods
kubectl top nodes
kubectl describe node <node>
kubectl get pvc
kubectl get pv
kubectl get storageclass
```

## Linux

```bash
top
htop
free -m
df -h
df -i
du -sh *
iostat
vmstat
ps aux
ss -lntp
lsof
lsof +L1
journalctl
systemctl status <service>
```

## AWS CLI

```bash
aws sts get-caller-identity
aws ec2 describe-instances
aws eks describe-cluster
aws eks describe-nodegroup
aws elbv2 describe-load-balancers
aws elbv2 describe-target-health
aws logs describe-log-groups
aws s3 ls
aws ecr describe-repositories
```

---

# 48. FINAL HIGH-PRIORITY PREPARATION

## AWS — Must Prepare In Depth

1. VPC architecture
2. Public/private subnets
3. Route tables
4. Internet Gateway
5. NAT Gateway
6. Security Groups
7. NACL
8. ALB
9. Target Groups
10. ACM
11. TLS termination
12. Route 53
13. EKS
14. EKS networking
15. VPC CNI
16. ENI/IP allocation
17. IAM
18. ECR
19. S3
20. EBS
21. EFS
22. RDS
23. CloudWatch
24. Cost optimization
25. DR / RPO / RTO

---

# 49. KUBERNETES — Must Prepare In Depth

1. Kubernetes architecture
2. API Server
3. etcd
4. Scheduler
5. Controllers
6. kubelet
7. Services
8. Ingress
9. AWS Load Balancer Controller
10. DNS/CoreDNS
11. CNI
12. Deployment
13. ReplicaSet
14. StatefulSet
15. DaemonSet
16. Jobs/CronJobs
17. ConfigMap
18. Secrets
19. RBAC
20. PV/PVC
21. StorageClass
22. EBS CSI
23. EFS CSI
24. Readiness/Liveness/Startup
25. Requests/Limits
26. QoS
27. HPA
28. VPA
29. KEDA
30. Cluster Autoscaler
31. Karpenter
32. Taints/Tolerations
33. Affinity
34. PDB
35. Rolling deployment
36. Rollback
37. CrashLoopBackOff
38. OOMKilled
39. ImagePullBackOff
40. Pending
41. Node NotReady
42. 502/503
43. DNS issues
44. Network issues
45. IP exhaustion

---

# 50. SRE — Must Prepare In Depth

1. What is SRE?
2. DevOps vs SRE
3. SLI
4. SLO
5. SLA
6. Error budget
7. Error-budget burn
8. Four Golden Signals
9. Observability
10. Metrics
11. Logs
12. Distributed tracing
13. APM
14. Datadog
15. Dynatrace
16. PagerDuty
17. ServiceNow
18. Incident management
19. Incident Commander
20. On-call
21. Escalation
22. Severity
23. MTTR
24. MTTD
25. Toil
26. Automation
27. Capacity planning
28. Reliability
29. Availability
30. Scalability
31. Production RCA
32. Postmortem
33. Blameless culture
34. Alert fatigue
35. SLO-based alerting
36. Burn-rate alerting
37. Disaster recovery
38. Business continuity
39. Chaos engineering
40. Continuous improvement

---

# 51. Final Interview Strategy

For the next round, I should answer technical questions using this structure:

```text
1. Definition
      ↓
2. Architecture
      ↓
3. Internal working
      ↓
4. Production example
      ↓
5. Troubleshooting
      ↓
6. Monitoring
      ↓
7. Failure scenario
      ↓
8. Prevention
```

For example, if asked about HPA, I should not stop at **“HPA scales Pods based on CPU.”** A stronger answer is:

> “HPA continuously evaluates the configured metric against the target. For CPU-based scaling, utilization is calculated relative to CPU requests. When utilization remains above the target, the HPA controller calculates a higher desired replica count and updates the workload. The scheduler then places the new Pods on suitable nodes. If the cluster does not have sufficient capacity, Cluster Autoscaler or Karpenter may provision additional nodes. I would then monitor Pod readiness, application latency, error rate, and node capacity to verify that scaling actually resolved the customer impact.”

That is the level of depth I should target for an architect/SRE round.

---

# 52. The 10 Scenarios I Must Be Able to Explain Without Hesitation

### Scenario 1

**Production API returns 503 but all Pods show Running.**

### Scenario 2

**API latency increases from 200 ms to 12 seconds while CPU and memory remain normal.**

### Scenario 3

**Pod goes into CrashLoopBackOff after deployment.**

### Scenario 4

**Pod gets OOMKilled repeatedly.**

### Scenario 5

**Pods are Pending even though nodes have CPU and memory available.**

### Scenario 6

**EKS subnet runs out of IP addresses.**

### Scenario 7

**HPA scales Pods but latency continues increasing.**

### Scenario 8

**Kafka consumer lag increases from hundreds to millions.**

### Scenario 9

**Production server filesystem reaches 100%.**

### Scenario 10

**A deployment completes successfully but production error rate immediately increases.**

For each scenario, I should be able to explain:

```text
Symptom
   ↓
Impact
   ↓
Detection
   ↓
Metrics
   ↓
Logs
   ↓
Traces
   ↓
Investigation
   ↓
Immediate Mitigation
   ↓
Root Cause
   ↓
Permanent Fix
   ↓
Monitoring
   ↓
Prevention
```

---

# Final Reminder

For this interview, I should focus less on memorizing definitions and more on explaining **how components interact in a real production environment**.

The highest-value combination to prepare is:

```text
AWS
  +
EKS / Kubernetes
  +
Observability
  +
APM
  +
SRE
  +
Incident Management
  +
Production Troubleshooting
```

If the interviewer asks **“Why?”**, I should explain the reason.

If they ask **“How?”**, I should explain the implementation.

If they ask **“What happens internally?”**, I should explain the component flow.

If they ask **“How did you troubleshoot it?”**, I should give a production-style RCA.

If they ask **“How do you prevent it?”**, I should explain monitoring, automation, capacity planning, testing, and preventive controls.

That is the depth expected from a 4+ years DevOps/SRE engineer.

