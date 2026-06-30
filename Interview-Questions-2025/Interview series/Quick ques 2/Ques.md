# DevOps Interview Scenarios (Detailed Answers)

## 1) A deployment succeeded, but traffic is still going to the old version. Explain exactly where you start debugging.

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 2) A Kubernetes application is healthy according to kubectl get pods, but users report 504 errors. Walk me through your troubleshooting flow.

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 3) Your AWS bill spiked 3x overnight. No deployments happened. What's your step-by-step response?

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 4) CI/CD pipeline is taking 40+ minutes. Your CTO wants it under 10 without adding hardware. What will you optimize?

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 5) An SRE says infra is stable, dev team says the system is slow, and monitoring shows everything is GREEN. Who do you believe --- and what do you check first?

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 6) Terraform apply is failing due to drift, but the infra is currently live and critical. How do you fix it without causing downtime?

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 7) Your rollback script fails during a production outage. You have 5 minutes before SLA breach. Walk me through your decision.

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 8) A secret was accidentally committed to GitHub. It has already been cloned. What are your next exact steps?

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconcconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 9) A Kubernetes cluster upgrade works in staging but corrupts CoreDNS in production. How do you approach patching and restoring service?

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.

## 10) Tell me a real scenario where YOU introduced a failure in infrastructure. What happened, what did you learn, and what changed after?

As a DevOps engineer with around four years of experience, I would
approach this systematically by first collecting facts instead of making
assumptions. My first objective is to identify the exact point where the
expected behavior diverges from the actual behavior. I begin by
reviewing recent deployments, infrastructure changes, monitoring
dashboards, application logs, and alerts to establish a timeline. I then
validate each layer involved, including CI/CD, Kubernetes resources,
networking, load balancers, DNS, cloud services, application
configuration, and dependent services. I use commands such as kubectl
get/describe/logs, rollout history, events, CloudWatch metrics and logs,
Terraform plan, Git history, and monitoring dashboards to correlate
evidence. Rather than making risky production changes, I verify each
hypothesis in the safest possible manner, keeping stakeholders informed
throughout the investigation. If customer impact exists, my priority is
restoring service using rollback, traffic shifting, or a known-good
configuration before performing deep root-cause analysis. After
recovery, I document the incident, identify the underlying cause instead
of only treating symptoms, add preventive controls such as monitoring,
alerts, automated validation, peer reviews, runbooks, and pipeline
improvements, and conduct a post-incident review so the same issue is
less likely to occur again. In an interview I would further tailor this
flow to the specific scenario---for example by checking Service
selectors, Ingress rules, ALB target groups, CDN caches and service-mesh
routing for stale traffic; ingress logs, readiness probes, backend
latency and database connectivity for 504 errors; Cost Explorer,
CloudTrail, NAT Gateway, data transfer and orphaned resources for
billing spikes; parallelization, dependency caching, Docker layer
caching and selective testing for pipeline optimization; validating user
experience, tracing and application bottlenecks when dashboards are
green; reconciling Terraform state safely when drift occurs; performing
a controlled manual rollback if automation fails; immediately rotating
exposed credentials and auditing access after a secret leak; restoring
CoreDNS and comparing staging versus production during cluster upgrades;
and honestly explaining a production mistake together with the lessons
learned and engineering improvements introduced afterwards.





# DevOps Interview Questions (4 Years Experience)

## 1. Your pod is running but the application isn't accessible. What do you check first?

When a Pod is in the **Running** state but the application is not accessible, I first understand that the Running status only confirms the container process has started; it does not guarantee that the application is healthy, listening on the required port, or ready to receive traffic. Therefore, I follow a structured troubleshooting approach instead of directly restarting the Pod.

My first step is to verify the Pod status and events using `kubectl describe pod <pod-name>`. This helps me identify issues such as failed readiness probes, failed liveness probes, image pull problems, scheduling events, insufficient resources, or repeated container restarts. If I notice that the restart count is increasing or readiness probes are failing, I know the application is not becoming ready even though the Pod status is Running.

Next, I examine the application logs using `kubectl logs <pod-name>`. In my experience, most production issues are caused by application startup failures, missing environment variables, incorrect ConfigMap or Secret values, database connection failures, dependency timeouts, or invalid application configurations. If the Pod contains multiple containers, such as a sidecar container, I check the logs of each container individually because the issue may not be in the main application container.

After confirming that the application started successfully, I verify whether it is listening on the expected port by accessing the container using `kubectl exec -it <pod-name> -- sh` and running commands like `netstat -tulnp` or `ss -tulnp`. Many times the application is configured to listen on port 8080 while the Kubernetes Service forwards traffic to port 80, resulting in connectivity failures.

I then inspect the Kubernetes Service using `kubectl get svc` and `kubectl describe svc`. I verify that the Service selector exactly matches the labels defined on the Pod. A mismatch in labels prevents Kubernetes from adding the Pod as an endpoint. To confirm this, I execute `kubectl get endpoints <service-name>`. If no endpoints are listed, the Service cannot route traffic because it has not discovered any matching Pods.

If the Service configuration is correct, I continue by checking the Ingress resource and the Ingress Controller logs. I verify the hostname, path rules, TLS configuration, backend Service, and health checks. In AWS EKS, I also verify that the AWS Load Balancer is healthy and forwarding requests correctly.

If NetworkPolicies are implemented, I ensure that traffic is not being blocked between namespaces, Pods, or the Ingress Controller. I also verify DNS resolution and perform connectivity testing using `curl` from another Pod inside the cluster to isolate whether the issue is internal or external.

In one production incident, our Spring Boot application was fully running, but the Service was configured with `targetPort: 80` while the application was listening on port `8080`. Because of this mismatch, the application was unreachable even though the Pod was healthy. Updating the Service configuration immediately restored traffic without requiring a Pod restart.

Overall, my troubleshooting approach is always systematic: **Pod → Logs → Application Port → Service → Endpoints → Ingress → Load Balancer → Network Policies → DNS**, which helps identify the exact failure point quickly while minimizing unnecessary changes in production.

## 2. Explain the difference between a Deployment and a StatefulSet, and when you'd choose one over the other.

Deployment and StatefulSet are both Kubernetes workload controllers, but they are designed for different types of applications. A **Deployment** is primarily used for stateless applications where each Pod is identical and interchangeable. Examples include Java Spring Boot microservices, Node.js applications, Python APIs, frontend applications, and REST services. These applications do not store data inside the Pod, so if a Pod crashes or is deleted, Kubernetes simply creates a new Pod with a different name and IP address. Since the application state is stored externally in services like Amazon RDS, DynamoDB, or Redis, the application continues to function normally. Deployment provides features such as ReplicaSets, rolling updates, rollbacks, self-healing, and horizontal scaling, making it the most commonly used controller in production.

A **StatefulSet**, on the other hand, is specifically designed for stateful applications that require persistent storage, stable network identities, and ordered deployment. Examples include MySQL, PostgreSQL, MongoDB, Cassandra, Kafka, Elasticsearch, ZooKeeper, and RabbitMQ clusters. Unlike Deployments, every Pod in a StatefulSet has a fixed identity such as `mysql-0`, `mysql-1`, or `mysql-2`, and each Pod is permanently associated with its own Persistent Volume. Even if a Pod is deleted or rescheduled to another worker node, Kubernetes reattaches the same storage and preserves the Pod's identity. StatefulSets also create and terminate Pods sequentially, ensuring applications that depend on ordered startup and shutdown continue to function correctly.

Another major difference is storage management. Deployments usually use shared storage or external databases and do not require individual persistent volumes for each Pod. StatefulSets automatically create dedicated Persistent Volume Claims (PVCs) for every Pod using a StorageClass, ensuring that each replica has its own storage. This prevents data corruption and ensures that application data survives Pod restarts or node failures.

Networking also differs between the two. Pods created by a Deployment receive dynamic names and IP addresses whenever they are recreated. StatefulSet Pods always maintain stable hostnames through a Headless Service, allowing clustered applications to reliably communicate with each other. This is particularly important for distributed databases where each node needs a predictable identity.

In my current project, we deploy all Spring Boot microservices using Deployments because they are stateless and store their data in Amazon RDS instead of inside Kubernetes. Deployments allow us to perform rolling updates with zero downtime, automatically replace failed Pods, and scale replicas based on traffic using the Horizontal Pod Autoscaler. If we were deploying a self-managed PostgreSQL database or Kafka cluster inside Kubernetes, I would choose StatefulSet because each instance requires persistent storage, stable networking, and ordered startup to maintain data consistency.

As a DevOps Engineer with four years of experience, my decision is simple: if the application is stateless, scalable, and stores data externally, I use a Deployment. If the application maintains its own data, requires stable identities, and depends on persistent storage, I use a StatefulSet.

---

## 3. Terraform apply failed halfway through. How do you safely recover?

When a `terraform apply` fails halfway through, I understand that Terraform may have already created or modified some infrastructure resources while others remain incomplete. In production, I never rerun `terraform apply` immediately because doing so without understanding the failure can lead to duplicate resources, inconsistent infrastructure, or unnecessary downtime. My first step is to carefully analyze the error message to determine whether the failure occurred due to insufficient IAM permissions, API rate limiting, AWS service quotas, provider issues, dependency failures, network interruptions, invalid resource configuration, or temporary cloud service problems.

After identifying the error, I execute `terraform plan` to compare the Terraform state with the actual infrastructure. This helps me understand which resources were successfully created and which resources are still pending. If the state file correctly reflects the infrastructure, Terraform usually plans to create only the missing resources during the next apply. However, if I discover that some resources were successfully created in AWS but are missing from the Terraform state, I import them into the state using `terraform import` to avoid duplicate resource creation. Similarly, if Terraform believes a resource exists but it was manually deleted, I either recreate the resource or remove the stale entry from the state after validating the impact.

If the project uses a remote backend with Amazon S3 and DynamoDB for state locking, I verify whether the state lock has been released properly. Sometimes a failed execution leaves behind a stale lock. Before removing it, I confirm that no other engineer or CI/CD pipeline is currently running Terraform. If required, I use `terraform force-unlock` carefully because unlocking an active state can corrupt the infrastructure state.

Next, I manually verify the infrastructure using the AWS Management Console or AWS CLI to ensure that the actual cloud resources match the Terraform state. This validation is especially important for networking resources such as VPCs, subnets, route tables, NAT Gateways, Internet Gateways, security groups, and IAM roles because these resources often have dependencies that can affect the remaining deployment.

Before making any manual modifications to the Terraform state, I always create a backup of the current state file. This provides a recovery point if an incorrect state operation causes additional issues. Once I confirm that the state accurately represents the infrastructure, I execute another `terraform plan` to ensure only the intended changes are displayed. Only after reviewing the plan carefully do I proceed with `terraform apply`.

In one of my previous projects, Terraform successfully created the VPC, subnets, and security groups but failed while provisioning NAT Gateways because the AWS account had reached its Elastic IP limit. After requesting a quota increase and releasing unused Elastic IPs, I reran `terraform plan`. Since the state file already contained the networking resources, Terraform planned only the pending NAT Gateway creation. The second apply completed successfully without recreating any existing resources or causing downtime.

To prevent similar incidents in production, I always use a remote backend with S3 and DynamoDB locking, perform infrastructure changes through CI/CD pipelines instead of local machines, review every execution plan before applying, implement IAM least privilege, maintain state backups, use version-controlled modules, and never modify production infrastructure manually outside Terraform. This approach ensures infrastructure remains consistent, recoverable, and auditable across the team.

---

## 4. A container works locally but crashes in production. What's your debugging approach?

When a Docker container works perfectly on my local machine but crashes in the production Kubernetes environment, I avoid assuming that Docker is the problem. Instead, I compare the local and production environments systematically because the issue is usually related to configuration differences, missing dependencies, networking, secrets, resource limits, or Kubernetes configuration rather than the application itself.

My first step is to check the Pod status using `kubectl get pods` and identify whether the Pod is in **CrashLoopBackOff**, **Error**, **OOMKilled**, or another state. I then inspect the Pod events using `kubectl describe pod <pod-name>` to determine whether Kubernetes has reported probe failures, scheduling problems, image pull issues, or resource constraints. If the Pod is restarting continuously, I examine the application logs using `kubectl logs <pod-name>` or `kubectl logs --previous <pod-name>` to capture the logs from the previous failed container. These logs usually indicate startup exceptions, missing configuration files, database connectivity failures, JVM errors, or application crashes.

Next, I compare the runtime environment between my local system and Kubernetes. I verify that all required environment variables are available inside the container by running `kubectl exec -it <pod-name> -- env`. I ensure that ConfigMaps and Secrets are correctly mounted and that sensitive values such as database credentials, API keys, and tokens are available. Many production failures occur because a Secret was not mounted correctly or an environment variable name differs between environments.

I then verify that the application is listening on the expected port by entering the container and running `netstat -tulnp` or `ss -tulnp`. I compare this with the containerPort defined in the Deployment and the targetPort configured in the Kubernetes Service. A mismatch between these values prevents traffic from reaching the application even though the container starts successfully.

Resource limits are another common cause of production-only failures. I inspect the Pod specification to verify CPU and memory requests and limits. If the container is terminated with an **OOMKilled** status, it indicates the application exceeded its memory limit. In that case, I analyze memory usage using Prometheus and Grafana before increasing limits because simply allocating more memory may hide a memory leak rather than solving it.

I also verify external dependencies such as databases, Redis, Kafka, third-party APIs, or message queues. A container running locally may connect to local services, whereas production relies on external endpoints that may be unreachable because of incorrect DNS, NetworkPolicies, Security Groups, or firewall rules.

Image consistency is equally important. I confirm that Kubernetes is running the same image version tested locally by executing `kubectl describe pod` and checking the image tag. I avoid using the `latest` tag because it can lead to different image versions being deployed unintentionally. In our project, every Docker image is tagged using the Jenkins build number or Git commit SHA, ensuring that production always deploys a specific immutable version.

If the application still crashes, I investigate Kubernetes probes. An incorrectly configured liveness or startup probe may repeatedly kill a healthy application before it finishes initialization. I review the probe configuration and adjust parameters such as `initialDelaySeconds`, `timeoutSeconds`, `failureThreshold`, and `periodSeconds` according to the application's startup time.

In one production incident, a Spring Boot application worked perfectly on developers' laptops but repeatedly crashed in Amazon EKS. After investigation, I found that the production Secret containing database credentials had an incorrect password due to a manual update. The application failed during startup while attempting to connect to the database. Updating the Secret and restarting the Deployment immediately resolved the issue. This experience reinforced the importance of validating environment-specific configuration before investigating the application code itself.

My production troubleshooting approach is always systematic: **Pod Status → Events → Logs → Environment Variables → ConfigMaps & Secrets → Ports → Resource Limits → External Dependencies → Image Version → Health Probes → Networking**. This structured process allows me to identify the root cause efficiently while minimizing production impact.

---

## 5. How do you reduce a Docker image size without breaking functionality?

Reducing Docker image size is important because smaller images improve build speed, reduce storage costs, decrease network transfer time, speed up deployments, and reduce the attack surface by removing unnecessary packages. However, optimization should never compromise application functionality. Therefore, I always optimize images carefully while validating the application after every change.

The first optimization I make is selecting an appropriate base image. Instead of using large operating system images such as Ubuntu, I prefer lightweight runtime images like Alpine, Distroless, or Eclipse Temurin JRE, depending on the application requirements. For Java applications, I use JRE images instead of JDK images because production containers only need the Java Runtime Environment.

I also implement multi-stage Docker builds. During the first stage, I use a build image containing Maven or Gradle to compile the application. The second stage copies only the final JAR file into a lightweight runtime image. This removes build tools, caches, source code, and unnecessary dependencies from the final image while significantly reducing its size.

I minimize Docker image layers by combining multiple commands into a single RUN instruction whenever possible. After installing packages, I immediately remove package manager caches and temporary files. I also use a `.dockerignore` file to exclude unnecessary content such as Git repositories, documentation, IDE configuration files, logs, test reports, and local build artifacts from the Docker build context.

Another important practice is copying only the required application files instead of the entire project directory. This reduces image size and improves Docker layer caching because unrelated source code changes no longer invalidate cached layers.

Security scanning also plays an important role. After optimizing the image, I scan it using Trivy to identify vulnerabilities introduced by operating system packages or third-party libraries. If vulnerabilities are detected, I upgrade packages or choose a more secure base image rather than ignoring the findings.

In our CI/CD pipeline, Jenkins automatically builds optimized Docker images, performs Trivy vulnerability scanning, pushes approved images to Amazon ECR, and deploys only images that satisfy security and quality requirements.

In one project, our initial Java image was approximately 1.2 GB because it used a full Ubuntu base image and included Maven inside the runtime container. After implementing a multi-stage build, switching to Eclipse Temurin JRE, removing unnecessary packages, and excluding build artifacts through `.dockerignore`, we reduced the image size to approximately 180 MB. This significantly reduced image pull time during Kubernetes deployments and improved overall deployment speed across all environments.

When optimizing Docker images, my objective is not simply achieving the smallest possible image but creating an image that remains secure, maintainable, reproducible, and production-ready without sacrificing application reliability.


---

## 6. Your CI pipeline passes, but the deployment doesn't reflect the latest code. Why?

When the CI pipeline completes successfully but the deployment still serves the old version of the application, I understand that the problem is usually not in the build stage but somewhere between the image creation and the Kubernetes deployment. Instead of immediately triggering another deployment, I follow a systematic troubleshooting approach to identify exactly where the deployment process failed.

My first step is to verify whether Jenkins actually built the latest source code. I check the Git commit ID in the Jenkins console logs and compare it with the latest commit in the repository. Sometimes the pipeline may have been triggered from the wrong branch or an older commit due to incorrect branch configuration or webhook issues. I also verify that the build artifact, such as the JAR file or frontend bundle, was generated successfully and contains the latest changes.

Next, I verify the Docker image. I inspect the image tag that Jenkins built and pushed to Amazon ECR. In production, I never recommend using the `latest` tag because Kubernetes may continue using a cached image. Instead, I always tag images with the Jenkins build number, Git commit SHA, or application version. I check Jenkins logs to confirm the image was pushed successfully to ECR and verify its existence using the AWS Console or AWS CLI.

After confirming the image exists in the registry, I inspect the Kubernetes Deployment.

```bash
kubectl describe deployment <deployment-name>
```

I verify the image currently running inside Kubernetes.

```bash
kubectl get pods
kubectl describe pod <pod-name>
```

Many deployment issues occur because the Deployment YAML still references the previous image tag. If Jenkins updates only the image in Amazon ECR but never updates the Deployment manifest, Kubernetes continues running the previous version.

I also verify the rollout status.

```bash
kubectl rollout status deployment <deployment-name>
```

If the rollout is stuck, Kubernetes may not have replaced the old Pods due to failing readiness probes, image pull errors, or insufficient cluster resources.

Another important check is the image pull policy.

```yaml
imagePullPolicy: Always
```

If the imagePullPolicy is set to **IfNotPresent** while using the same image tag repeatedly, Kubernetes may continue using the cached image stored on the worker node instead of pulling the updated image from ECR.

I then verify whether ArgoCD, Helm, or GitOps synchronization has completed successfully if those tools are used. Sometimes Jenkins completes successfully, but GitOps synchronization has not yet updated the Kubernetes cluster.

I also compare the ConfigMaps and Secrets because configuration changes may not automatically restart Pods. If only configuration changed while the application image remained the same, Kubernetes may continue running the old configuration until the Pods are restarted or the Deployment is updated.

If the deployment completed successfully but users still see the previous application version, I verify the Load Balancer, Ingress Controller, CDN, and browser caching. In one production incident, CloudFront cached the previous frontend version even though Kubernetes had already deployed the latest application. Invalidating the CloudFront cache immediately resolved the issue.

In another project, Jenkins successfully built and pushed a new Docker image, but the Deployment YAML referenced the old image tag because the image replacement step failed. Kubernetes therefore continued running the previous Pods even though the pipeline showed success. After fixing the image tag replacement stage and triggering a rollout restart, the latest application became available.

To avoid such issues in production, I always use immutable image tags, update Kubernetes manifests automatically through Jenkins or GitOps, validate rollout status, avoid the `latest` tag, enable image digest verification where possible, and include post-deployment validation tests in the pipeline.

---

## 7. Explain how a Kubernetes Service routes traffic to the correct Pods.

A Kubernetes Service provides a stable network endpoint that allows applications to communicate with Pods without depending on their dynamic IP addresses. Since Pods are ephemeral and their IP addresses change whenever they are recreated, directly accessing Pods is unreliable. A Service solves this problem by acting as a permanent abstraction layer between clients and Pods.

Internally, every Service uses **label selectors** to identify which Pods belong to it. For example, if a Service specifies:

```yaml
selector:
  app: payment
```

Kubernetes automatically identifies all Pods that contain the label:

```yaml
labels:
  app: payment
```

Once the matching Pods are identified, Kubernetes creates an **Endpoints** object that stores the IP addresses of all healthy Pods associated with the Service. Whenever a request reaches the Service IP, kube-proxy running on every worker node programs iptables or IPVS rules that distribute traffic to one of the healthy backend Pods.

Traffic routing only occurs to Pods that successfully pass their **Readiness Probe**. If a Pod fails the readiness check, Kubernetes removes that Pod from the Endpoints list, ensuring users never receive traffic from an application that is not ready. This is one of the reasons readiness probes are critical in production.

Depending on the Service type, traffic enters the cluster differently.

A **ClusterIP** Service exposes the application only within the Kubernetes cluster and is typically used for communication between microservices.

A **NodePort** Service exposes the application on a static port on every worker node, allowing external clients to access the application using the node's IP address.

A **LoadBalancer** Service automatically provisions a cloud load balancer, such as an AWS Application Load Balancer or Network Load Balancer, and forwards traffic to the Service, making the application accessible from the internet.

For HTTP and HTTPS applications, an Ingress Controller usually sits in front of multiple Services and performs path-based or host-based routing before forwarding traffic to the appropriate Service.

In my project, internet traffic first reaches an AWS Application Load Balancer, which forwards requests to the AWS Load Balancer Controller running inside Amazon EKS. The Ingress resource routes requests to the correct Kubernetes Service based on the URL path or hostname. The Service then forwards requests to healthy backend Pods through kube-proxy using the Endpoints object. This architecture allows Pods to be created, deleted, or scaled without affecting client connectivity because the Service endpoint remains constant.

In one production incident, a Service was not routing traffic even though all Pods were healthy. Investigation showed that the Service selector was configured as `app=payments`, while the Pods were labeled `app=payment`. Because of this mismatch, Kubernetes created an empty Endpoints object, so the Service had no backend Pods. Correcting the selector immediately restored traffic.

This demonstrates that a Kubernetes Service does not route traffic based on Pod names or IP addresses. Instead, it relies entirely on labels, selectors, Endpoints, kube-proxy, and readiness status to ensure requests are always delivered to healthy application instances.


---

## 8. How would you design zero-downtime deployments for a production application?

In production environments, zero-downtime deployment means deploying a new version of an application without interrupting user traffic or causing service unavailability. Since users may be accessing the application 24/7, it is important that the deployment process does not terminate all running Pods at once. In my current project running on Amazon EKS, we achieve zero-downtime deployments by combining Kubernetes Rolling Updates, Readiness Probes, AWS Application Load Balancer (ALB), Jenkins CI/CD, and automated rollback mechanisms. The objective is to ensure that only healthy Pods receive traffic while the new version is gradually introduced.

The deployment process starts when a developer pushes code to GitHub. Jenkins automatically triggers the CI/CD pipeline, checks out the latest code, builds the application using Maven, executes unit tests, performs SonarQube quality analysis, builds a Docker image, scans it with Trivy, and pushes the image to Amazon ECR with a unique tag such as the Jenkins build number or Git commit SHA. Using immutable image tags is an important best practice because it ensures Kubernetes always deploys the correct version and avoids problems caused by the `latest` tag.

After the image is pushed successfully, Jenkins updates the Kubernetes Deployment manifest or Helm values file with the new image tag and applies the deployment to the EKS cluster. The Deployment is configured with the RollingUpdate strategy.

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

Setting `maxUnavailable` to **0** guarantees that Kubernetes never removes an existing Pod until a replacement Pod is fully operational. The `maxSurge` value allows Kubernetes to create one additional Pod temporarily during the deployment, ensuring application capacity is maintained throughout the update.

Readiness Probes play a critical role in zero-downtime deployments. Kubernetes sends user traffic only to Pods that successfully pass the readiness check. If the application is still initializing, loading configuration, establishing database connections, or warming caches, Kubernetes keeps the Pod out of the Service endpoints until it becomes fully ready. This prevents users from receiving requests from partially initialized applications.

Liveness Probes are configured separately to detect applications that become unhealthy after startup. If a running application hangs or enters a deadlock, Kubernetes automatically restarts the container without requiring manual intervention. For applications with long startup times, Startup Probes are also configured to prevent liveness checks from killing the application before initialization completes.

During deployment, I continuously monitor rollout progress using:

```bash
kubectl rollout status deployment <deployment-name>
```

If any new Pod fails its readiness probe, experiences CrashLoopBackOff, or encounters application errors, Kubernetes automatically pauses the rollout. Existing Pods continue serving production traffic, ensuring users experience no downtime.

Rollback capability is equally important. If monitoring tools such as Prometheus, Grafana, or application health checks detect failures after deployment, I immediately execute:

```bash
kubectl rollout undo deployment <deployment-name>
```

This restores the previous stable ReplicaSet within seconds. Since Kubernetes maintains ReplicaSet history, rollback is fast and reliable.

For critical production releases involving major application changes, I recommend using Blue-Green or Canary deployments instead of simple Rolling Updates. Blue-Green Deployment maintains two complete environments, allowing traffic to switch instantly between them after validation. Canary Deployment gradually shifts a small percentage of production traffic to the new version while monitoring application performance before increasing traffic progressively. These strategies minimize business risk for high-impact releases.

In one production deployment, a newly released microservice contained an incorrect database configuration. The readiness probe failed because the application could not establish a database connection. Kubernetes never added the new Pods to the Service endpoints, while the existing Pods continued serving production traffic. The rollout paused automatically, preventing customer impact. After correcting the configuration, we redeployed the application successfully without any downtime.

To ensure reliable zero-downtime deployments, I always use Rolling Updates with `maxUnavailable: 0`, immutable Docker image tags, properly configured readiness, liveness, and startup probes, automated health verification, rollback capability, multiple replicas distributed across Availability Zones, Pod Disruption Budgets, Horizontal Pod Autoscaler, and monitoring through Prometheus and Grafana. These practices ensure that production deployments remain highly available, resilient, and transparent to end users.

---

## 9. A node suddenly goes NotReady. Walk through your troubleshooting steps.

When a Kubernetes node suddenly enters the **NotReady** state, it indicates that the control plane is no longer receiving healthy status updates from the kubelet running on that worker node. This can happen because of network failures, kubelet crashes, resource exhaustion, disk pressure, memory pressure, container runtime failures, or underlying EC2 instance issues. In a production environment, my priority is to identify the root cause quickly while ensuring that application availability is maintained.

The first step is to confirm the node status.

```bash
kubectl get nodes
```

If the node is marked **NotReady**, I inspect it in detail.

```bash
kubectl describe node <node-name>
```

This command provides valuable information about node conditions such as **MemoryPressure**, **DiskPressure**, **PIDPressure**, **NetworkUnavailable**, or **KubeletNotReady**. It also displays recent events that often indicate the exact reason why the node became unhealthy.

Next, I determine whether workloads are affected.

```bash
kubectl get pods -A -o wide
```

This helps identify which application Pods were running on the affected node. If those Pods belong to a Deployment, Kubernetes usually reschedules them automatically to healthy nodes. However, StatefulSet Pods or applications using local storage may require additional investigation before rescheduling.

Since my project runs on Amazon EKS, I then verify the health of the underlying EC2 instance using the AWS Console or AWS CLI. I check both **System Status Checks** and **Instance Status Checks**. If either check fails, the issue is likely related to the EC2 infrastructure rather than Kubernetes itself.

If the EC2 instance is reachable, I connect to it through SSH or AWS Systems Manager Session Manager and begin examining the operating system. My first check is the kubelet service.

```bash
sudo systemctl status kubelet
```

If kubelet has stopped or crashed, I inspect its logs.

```bash
journalctl -u kubelet -f
```

These logs often reveal certificate issues, API server connectivity failures, authentication problems, or resource exhaustion.

Next, I verify the container runtime.

```bash
sudo systemctl status containerd
```

or

```bash
sudo systemctl status docker
```

If the container runtime is unavailable, kubelet cannot create or manage Pods, causing the node to become NotReady.

Resource utilization is another important area of investigation. I examine CPU, memory, and disk usage.

```bash
top
free -h
df -h
```

If the root filesystem is full or memory is exhausted, kubelet may stop functioning correctly. I also inspect large log files under `/var/log`, remove unnecessary temporary files, and verify whether log rotation is functioning properly.

Network connectivity is equally important. I verify that the worker node can communicate with the Kubernetes API server, DNS, and other required AWS services. I also ensure that Security Groups, Network ACLs, VPC routing, and IAM permissions have not changed unexpectedly.

If the node cannot be recovered quickly, I cordon it to prevent new Pods from being scheduled.

```bash
kubectl cordon <node-name>
```

Then I safely drain the workloads.

```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

This allows Kubernetes to move application Pods to healthy worker nodes while maintenance continues.

After resolving the issue, I restart kubelet if necessary.

```bash
sudo systemctl restart kubelet
```

Once the node reports **Ready**, I re-enable scheduling.

```bash
kubectl uncordon <node-name>
```

In one production incident, an EKS worker node entered the NotReady state because the `/var` partition became completely full due to excessive container logs. Kubelet stopped updating node status, and Pods could no longer be scheduled. We cordoned and drained the node, cleaned the disk by removing old container logs, verified that logrotate was functioning correctly, restarted kubelet, and finally uncordoned the node. Kubernetes automatically resumed scheduling workloads, and there was no application downtime because multiple replicas were already running on other worker nodes.

From my experience, troubleshooting a NotReady node always follows the same logical sequence: **Confirm node status → Inspect node conditions → Identify affected Pods → Verify EC2 health → Check kubelet → Check container runtime → Verify CPU, memory, and disk usage → Validate networking → Cordon and drain if required → Fix the issue → Restart services → Uncordon the node → Monitor application recovery**. This structured approach minimizes downtime and ensures production stability.

1. What exactly did you automate?
Own it. Talk about the full CI/CD lifecycle — builds, tests, Docker images, Terraform provisioning, Kubernetes deployments, and monitoring integration.

2. Explain your CI/CD pipeline.
Walk them through it step by step. Code commit triggers the pipeline. Maven builds. SonarQube scans. Docker image gets pushed. Terraform provisions infra. Kubernetes deploys. Approval gates protect production.

3. Why Maven?
Dependency management, build automation, plugin support, and clean integration with Jenkins and Azure DevOps.

4. What is a YAML pipeline?
Pipeline as code. Versioned. Reusable. Stored right inside the repository.

5. What is Terraform and why use it?
Infrastructure as Code. Consistent cloud provisioning across every environment.

6. What resources did you create with Terraform?
AKS/EKS clusters, VNets, NSGs, Load Balancers, Storage Accounts, Container Registries — the full stack.

7. How did you manage multiple environments?
Terraform variable files, Kubernetes namespaces, and environment-specific YAML variable groups.

8. What are approval gates?
Manual checkpoints before production deployments. Non-negotiable for any serious pipeline.

9. How did you secure pipelines?
Key Vault, RBAC, service connections, branch protection, vulnerability scanning, and approval gates.

10. What issues did you face?
This is where most candidates freeze. Talk about real problems — pod crashes, Terraform state conflicts, Docker version mismatches, disk space failures. Interviewers love honesty here.

11. How do you monitor applications?
Prometheus, Grafana, ELK Stack, Azure Monitor, and Kubernetes health checks.

12. What happens when a pipeline fails?
Deployment stops. Notifications fire. Logs get analyzed. Issue gets fixed. Pipeline gets retriggered. Simple and structured.

13. What are service connections?
Secure authentication links between Azure DevOps and external services like AWS, Docker Hub, and Kubernetes.

14. What is the difference between CI and CD?
CI is about integration and testing. CD is about deployment and release automation. Know this cold.

15. What are Dev, UAT, and Prod environments?
Dev for development testing. UAT for business validation. Prod is live and customer-facing.
