# How do you replace the Docker image and version in a deployment file? Write a script for this.

In my project, Docker image versions are updated automatically as part of the CI/CD pipeline instead of manually editing the Kubernetes deployment YAML. During every successful build, Jenkins generates a new Docker image with a unique tag such as the Jenkins build number or Git commit ID and pushes it to Amazon Elastic Container Registry (ECR). The deployment manifest or Helm values file is then updated with the latest image tag before deployment.

One approach is to use the `sed` command to replace the image tag inside the deployment YAML. This script updates the deployment file with the new image version before applying it to the Kubernetes cluster.

```bash
#!/bin/bash

DEPLOYMENT_FILE="deployment.yaml"
IMAGE_NAME="123456789012.dkr.ecr.ap-south-1.amazonaws.com/my-app"
NEW_TAG="v2.5.0"

sed -i "s|image: .*|image: ${IMAGE_NAME}:${NEW_TAG}|g" $DEPLOYMENT_FILE

echo "Deployment file updated successfully."

kubectl apply -f $DEPLOYMENT_FILE
```

Another and more commonly used approach is to update the running deployment directly without modifying the YAML file by using the Kubernetes command below:

```bash
kubectl set image deployment/my-app \
my-container=123456789012.dkr.ecr.ap-south-1.amazonaws.com/my-app:v2.5.0 \
-n abc
```

This command performs a rolling update where Kubernetes gradually replaces the old pods with new ones while maintaining application availability. During the rollout, I monitor the deployment using `kubectl rollout status deployment/my-app` and verify the new image using `kubectl describe deployment`.

---

# There are 100 pods in namespace `abc`, and 30 pod names contain `def`. How do you restart only these 30 pods?

In Kubernetes, Pods are managed by controllers such as Deployments, ReplicaSets, StatefulSets, or DaemonSets. Therefore, instead of manually restarting containers, we delete only the required pods, and Kubernetes automatically recreates them. If there are 100 pods in namespace `abc` and only 30 pod names contain `def`, I first list those pods using `kubectl get pods` combined with `grep` to filter the required pod names.

The following script deletes only the matching pods:

```bash
#!/bin/bash

NAMESPACE="abc"

kubectl get pods -n $NAMESPACE --no-headers | \
grep "def" | \
awk '{print $1}' | \
while read POD
do
    echo "Restarting $POD"
    kubectl delete pod $POD -n $NAMESPACE
done

echo "Selected pods restarted successfully."
```

If all the `def` pods belong to the same Deployment, a better production approach is to perform a rolling restart instead of deleting individual pods:

```bash
kubectl rollout restart deployment <deployment-name> -n abc
```

This is the preferred method because Kubernetes replaces the pods one by one while ensuring zero or minimal downtime. In production environments, I always verify the rollout using:

```bash
kubectl rollout status deployment <deployment-name> -n abc
```

and confirm that the newly created pods are healthy using:

```bash
kubectl get pods -n abc
```

This approach ensures controlled pod replacement, maintains application availability, and follows Kubernetes best practices.

# DevOps Production Scenario Interview Questions & Detailed Answers (4 Years Experience)

# 1) A deployment succeeded, but traffic is still going to the old version. Explain exactly where you start debugging."

The first thing I do is avoid assuming that the deployment was actually successful just because the CI/CD pipeline shows a green status. A successful pipeline only confirms that the deployment job completed without errors; it does not guarantee that production traffic is reaching the newly deployed application. Therefore, I begin my investigation by validating the deployment inside the Kubernetes cluster itself. I check the deployment status using `kubectl rollout status deployment <deployment-name>` to ensure Kubernetes completed the rollout successfully. Then I inspect the deployment details using `kubectl describe deployment` and verify that the latest image version or image digest matches the version that was built by the pipeline. If my organization follows immutable image tagging, I verify that the new tag is present. If the application uses the `latest` tag, I confirm that Kubernetes has actually pulled the latest image because image caching can sometimes cause old images to continue running.

After confirming that the deployment completed successfully, I verify whether Kubernetes actually created new pods. I execute `kubectl get pods -o wide` and compare the pod creation timestamps with the deployment time. If the pods are old, it indicates that Kubernetes never replaced them or that the rollout is stuck. I also check the ReplicaSets using `kubectl get rs` because Kubernetes keeps multiple ReplicaSets during rolling updates. Sometimes the old ReplicaSet may still be serving traffic while the new ReplicaSet has failed readiness checks. If multiple ReplicaSets exist, I inspect the desired, current, and ready replica counts to determine whether Kubernetes has shifted traffic to the new version.

Once I verify that the correct pods are running, I investigate the Kubernetes Service because the Service determines which pods receive production traffic. I compare the labels on the pods with the Service selectors by running `kubectl describe service <service-name>` and `kubectl get pods --show-labels`. A simple label mismatch can prevent the Service from routing traffic to the new pods, causing production traffic to continue flowing to the previous version. I also check the Service endpoints using `kubectl get endpoints` to confirm that the IP addresses listed belong to the newly created pods. If the endpoints still reference old pod IPs, I know the issue is related to Service discovery rather than the deployment itself.

The next layer I verify is the Ingress Controller or Load Balancer. If the application is exposed through an AWS Application Load Balancer (ALB), AWS Network Load Balancer (NLB), NGINX Ingress Controller, or Traefik, I inspect the routing configuration carefully. I review the Ingress resource using `kubectl describe ingress` and ensure that it points to the correct backend Service. If I am using AWS ALB Ingress Controller, I also inspect the Target Groups in the AWS console to verify that the newly created pods have successfully registered as healthy targets. Sometimes new pods remain in an unhealthy state because of failing readiness probes, causing the Load Balancer to continue forwarding traffic to the previous healthy targets.

I then investigate Kubernetes readiness and liveness probes because they directly affect traffic routing. Kubernetes only sends traffic to pods that successfully pass readiness checks. Even if the deployment completed successfully, a failing readiness probe prevents new pods from receiving production traffic. I examine the pod description using `kubectl describe pod` and review application logs with `kubectl logs`. If I find readiness failures, I determine whether the application startup time, database connectivity, external API dependencies, or configuration issues are causing the probe failures.

If Kubernetes resources appear healthy, I move outside the cluster and verify DNS resolution. I use commands such as `nslookup`, `dig`, or `host` to ensure the domain resolves to the expected Load Balancer. DNS propagation delays can occasionally cause different clients to reach different application versions. If a Content Delivery Network such as CloudFront or Cloudflare is used, I check whether cached responses are still being served. I invalidate the cache if necessary after confirming that the origin server is serving the correct version.

I also verify whether sticky sessions are enabled on the Load Balancer. Sticky sessions can cause existing users to continue interacting with older application instances until their session expires. Although this behavior may be expected, it often creates the impression that the deployment failed. Similarly, if a service mesh such as Istio or Linkerd is deployed, I inspect VirtualService, DestinationRule, and traffic routing policies because incorrect traffic weights may intentionally or unintentionally continue directing requests to the older application version.

Application-level caching is another common cause. I verify Redis, Memcached, API Gateway caches, browser caching, and application caches to ensure users are not receiving stale responses. Sometimes only static assets such as JavaScript bundles remain cached while the backend has already been updated, resulting in version mismatches.

Throughout the investigation, I continuously monitor metrics using Prometheus, Grafana, CloudWatch, or Datadog. I compare request rates, response times, error rates, healthy target counts, and deployment timestamps to correlate exactly where traffic stops reaching the new version. Once I identify the root cause, I implement the fix in a controlled manner, validate the deployment using both automated health checks and manual testing, and continue monitoring production until stability is confirmed.

Finally, I perform a post-incident review. If the issue resulted from incorrect labels, missing readiness probes, image tagging problems, CDN caching, or load balancer configuration, I implement preventive controls such as immutable image tags, automated deployment verification, smoke tests, GitOps validation, deployment checklists, and monitoring alerts. My objective is not only to restore production traffic quickly but also to ensure the same issue cannot easily occur during future deployments.

---

# 2) "A Kubernetes application is healthy according to kubectl get pods, but users report 504 errors. Walk me through your troubleshooting flow."

A `kubectl get pods` command showing all pods in the **Running** state does not necessarily indicate that the application is healthy. The `Running` status simply means that the containers are executing successfully; it does not guarantee that requests are successfully reaching the application or that the application is responding correctly. Therefore, I always begin my troubleshooting from the user's perspective instead of assuming that Kubernetes is the source of the problem. My first objective is to reproduce the issue by accessing the application through the same endpoint that users are using. I note whether the 504 Gateway Timeout occurs consistently, only for specific APIs, or only during peak traffic. Understanding the scope of the issue helps narrow down the affected component.

The next step is identifying exactly where the 504 error is being generated. A 504 response typically originates from an API Gateway, Load Balancer, Reverse Proxy, or Ingress Controller because these components return a timeout when the backend application fails to respond within the configured timeout period. Therefore, I first determine whether the timeout is coming from AWS Application Load Balancer (ALB), NGINX Ingress Controller, AWS API Gateway, HAProxy, or another reverse proxy. Once I know which component is generating the timeout, I can focus my investigation on the communication between that component and the backend application.

I then verify the Kubernetes Ingress configuration using `kubectl describe ingress` to ensure that traffic is correctly routed to the appropriate Kubernetes Service. I confirm that the backend Service name, service port, path rules, and annotations are configured correctly. Misconfigured annotations, incorrect backend ports, or missing TLS settings can all contribute to connectivity problems. If the application uses AWS ALB Ingress Controller, I also inspect the Target Groups in AWS to ensure that the pods are registered as healthy targets. If the Load Balancer considers the pods unhealthy, requests will eventually timeout and return a 504 error.

After confirming the Ingress configuration, I investigate the Kubernetes Service. Using `kubectl describe service` and `kubectl get endpoints`, I verify that the Service selectors correctly match the labels assigned to the application pods. I confirm that the endpoints listed belong to the currently running pods and not terminated or outdated pods. A label mismatch or missing endpoints means that the Service has no backend to forward traffic to, even though the pods themselves are running successfully.

The next step is examining the application pods in greater detail. Although the pods are running, they may not actually be ready to serve traffic. I execute `kubectl describe pod` to review readiness probe events, liveness probe status, restart counts, and resource limits. If readiness probes are failing intermittently, Kubernetes temporarily removes those pods from the Service endpoints, which can reduce available backend capacity and lead to request timeouts. I also inspect pod logs using `kubectl logs` to identify application exceptions, database connection failures, thread pool exhaustion, memory issues, or slow API calls that may not be visible from Kubernetes alone.

Once Kubernetes resources appear healthy, I validate application connectivity from inside the cluster. I launch a temporary debug pod using a lightweight image such as BusyBox or Alpine and perform requests directly to the Service using `curl` or `wget`. If the application responds successfully inside the cluster but users continue receiving 504 errors externally, I know the problem likely exists between the Load Balancer and Kubernetes rather than within the application itself. If the request also fails inside the cluster, my investigation shifts toward the application or its dependencies.

Database performance is another area I investigate carefully because slow database queries frequently cause backend services to exceed Load Balancer timeout limits. I review database connection pools, active sessions, slow query logs, CPU utilization, disk latency, and query execution plans. Even if Kubernetes is functioning perfectly, an overloaded database can prevent the application from responding before the gateway timeout expires. Similarly, I verify whether the application depends on external APIs, authentication providers, payment gateways, or messaging systems that may themselves be responding slowly.

Resource utilization is another important consideration. Using `kubectl top pods` and monitoring dashboards such as Prometheus and Grafana, I examine CPU usage, memory consumption, request latency, garbage collection activity, thread utilization, and container restarts. High CPU utilization, memory pressure, or OutOfMemoryKilled events can significantly delay application responses without necessarily causing pods to crash. I also inspect Horizontal Pod Autoscaler (HPA) metrics to determine whether the application is receiving more traffic than the available replicas can handle.

Networking issues are another possible cause of 504 errors. I verify Kubernetes Network Policies to ensure they are not blocking communication between application components. I also confirm that CoreDNS is resolving internal service names correctly by performing DNS lookups from within a pod. If service discovery is failing, backend services may spend excessive time waiting for DNS resolution, eventually leading to gateway timeouts. Additionally, I inspect node health, kube-proxy, Container Network Interface (CNI) plugins such as Calico or Cilium, and security groups if running on AWS EKS to identify networking bottlenecks.

Timeout configuration itself should also be reviewed. Sometimes the backend application legitimately requires more processing time than the configured gateway timeout allows. I compare timeout values configured in NGINX Ingress, AWS ALB, API Gateway, reverse proxies, and the application itself. If backend processing consistently requires more time because of business logic, increasing timeout values may be appropriate after confirming that the application performance is acceptable. However, I treat timeout increases as a temporary solution until the underlying performance issue is resolved.

Throughout the investigation, I continuously correlate logs, metrics, and distributed traces. If tools such as Jaeger, Zipkin, AWS X-Ray, or OpenTelemetry are available, I trace requests across multiple microservices to identify exactly where latency increases. This helps distinguish whether the bottleneck exists in the frontend, API layer, authentication service, database, or downstream services.

Once the root cause is identified, I implement the least risky corrective action. This may involve fixing Service selectors, updating Ingress configuration, scaling application replicas, optimizing database queries, restarting unhealthy pods, adjusting resource limits, resolving networking issues, or correcting timeout settings. After implementing the fix, I validate the application by performing functional testing, monitoring production traffic, checking error rates, and ensuring response times return to normal.

Finally, I conduct a post-incident review to identify why the issue was not detected earlier. If monitoring showed everything as healthy despite users experiencing failures, I improve observability by adding synthetic monitoring, endpoint health checks, distributed tracing, application performance monitoring, and alerting based on user experience rather than infrastructure metrics alone. My objective is not just to resolve the current outage but to strengthen the monitoring strategy so similar issues are detected proactively before users report them.

---

# 3) "Your AWS bill spiked 3x overnight. No deployments happened. What's your step-by-step response?"

Whenever I see an unexpected increase in the AWS bill, especially when no deployments or planned infrastructure changes have taken place, I treat it as both an operational and a potential security incident. My objective is not only to identify which AWS service caused the increase but also to determine whether the spike was caused by legitimate traffic, an infrastructure issue, a configuration mistake, or unauthorized activity. I avoid making assumptions and instead follow a structured investigation process that minimizes production risk while quickly identifying the root cause.

The first step is to verify the billing information using AWS Cost Explorer and the Billing Dashboard. I compare today's costs with previous days and filter the data by AWS service, linked account, region, and usage type. This immediately tells me whether the additional cost is coming from EC2, EKS, ECS, Lambda, S3, CloudWatch, NAT Gateway, RDS, Elastic Load Balancer, Data Transfer, or another AWS service. Instead of looking only at the total amount, I identify exactly which service contributed to the increase because each service requires a different troubleshooting approach.

After identifying the affected service, I narrow down the timeline. I determine the exact hour when the cost began increasing because this helps correlate the billing spike with operational events. Even if no deployments occurred, there may have been scheduled jobs, auto-scaling events, backups, log retention changes, traffic spikes, or manual infrastructure modifications. By comparing timestamps from Cost Explorer with CloudWatch metrics, I can often identify what changed during that period.

The next step is reviewing CloudWatch metrics for the affected resources. If EC2 costs increased, I examine CPU utilization, network traffic, disk I/O, and instance count. If Lambda costs increased, I review invocation count, execution duration, concurrency, and error rates. If EKS or ECS costs increased, I inspect pod scaling events, cluster autoscaler activity, node group changes, and container resource utilization. If S3 charges increased, I review storage growth, PUT and GET requests, lifecycle policy execution, and cross-region replication. This allows me to determine whether the increase resulted from actual application demand or from infrastructure behaving unexpectedly.

One of the most common causes of sudden AWS billing increases is Auto Scaling. Therefore, I verify whether Auto Scaling Groups launched additional EC2 instances overnight. I review scaling policies, CloudWatch alarms, and scaling history to determine whether legitimate traffic triggered scaling or whether incorrect thresholds caused unnecessary instance creation. If the application runs on Amazon EKS, I inspect the Cluster Autoscaler logs and node groups to determine whether additional worker nodes were provisioned. Sometimes a single application with incorrect resource requests can cause Kubernetes to schedule new nodes unnecessarily, significantly increasing infrastructure costs.

If compute resources appear normal, I investigate networking charges because Data Transfer and NAT Gateway costs frequently surprise organizations. I review VPC Flow Logs, NAT Gateway metrics, Internet Gateway traffic, and AWS Cost Explorer usage categories. High outbound traffic could indicate increased customer demand, application bugs causing retry storms, large file transfers, or even malicious activity. Since NAT Gateway charges are based on processed data, a misconfigured application repeatedly communicating with external services can generate substantial costs in a short period.

CloudWatch Logs are another area I inspect carefully. Excessive logging caused by an application entering a failure loop can rapidly increase CloudWatch charges. I verify log ingestion volume, retention policies, and the number of generated log events. If a recent application issue caused millions of repetitive log entries, I work with the development team to reduce unnecessary logging while ensuring critical operational information is still retained.

I also investigate storage-related services. For Amazon S3, I review bucket size, lifecycle policies, multipart uploads, replication configuration, and object creation patterns. Unfinished multipart uploads, disabled lifecycle rules, or accidental backup duplication can significantly increase storage costs. Similarly, for Amazon EBS, I verify whether snapshots have accumulated due to failed cleanup automation or whether unattached volumes remain allocated after instance termination.

Another critical step is reviewing AWS CloudTrail. Since no deployments occurred, I need to verify whether someone manually provisioned resources. CloudTrail provides a complete audit trail of API activity, allowing me to identify who created, modified, or deleted AWS resources. I check for recently launched EC2 instances, new RDS databases, EBS volumes, Elastic IPs, Load Balancers, Lambda functions, or IAM changes. If unexpected API calls appear, I immediately determine whether they were performed by an authorized user, an automation pipeline, or a compromised credential.

Security is an important consideration during cost investigations because a compromised AWS account can generate significant charges very quickly. I review AWS GuardDuty findings, IAM Access Analyzer, CloudTrail authentication events, unusual login locations, and recently created IAM users or access keys. I also verify whether cryptomining workloads, unauthorized EC2 instances, or suspicious Lambda executions have been launched. If I suspect credential compromise, I immediately revoke affected credentials, rotate IAM keys, notify the security team, and follow the organization's incident response process.

Once I identify the root cause, I implement corrective actions carefully to avoid impacting production workloads. If unnecessary EC2 instances were launched, I first confirm that they are not serving production traffic before terminating them. If Kubernetes has over-provisioned nodes, I investigate why the scheduler requested additional capacity instead of simply deleting nodes. If excessive logging caused the increase, I adjust application log levels and configure appropriate CloudWatch retention policies. If storage growth is responsible, I enable lifecycle policies, remove obsolete snapshots, clean up unattached volumes, and delete incomplete multipart uploads after confirming they are no longer required.

After resolving the immediate issue, I focus on preventing similar incidents. I ensure AWS Budgets are configured with email and SNS notifications so cost anomalies are detected immediately rather than during monthly billing reviews. I enable AWS Cost Anomaly Detection to automatically identify unusual spending patterns. I also implement mandatory resource tagging for all infrastructure, allowing costs to be traced back to applications, environments, and teams. Regular cost optimization reviews, Trusted Advisor recommendations, Compute Optimizer reports, and automated cleanup scripts become part of the operational process to continuously reduce unnecessary spending.

Finally, I document the incident thoroughly. My documentation includes the timeline of the investigation, the affected AWS services, the root cause, the financial impact, the corrective actions taken, and the preventive measures implemented. During the post-incident review, I share the findings with development, operations, security, and management teams so everyone understands how the cost increase occurred and how similar incidents can be avoided in the future. As a DevOps engineer, I believe cost optimization is an ongoing operational responsibility, and every unexpected billing event is an opportunity to improve both infrastructure efficiency and governance.

---

# 4) "CI/CD pipeline is taking 40+ minutes. Your CTO wants it under 10 without adding hardware. What will you optimize?"

When a CI/CD pipeline takes more than 40 minutes, my first approach is not to optimize randomly but to identify exactly which stages consume the most time. I begin by analyzing the pipeline execution history in Jenkins, GitHub Actions, GitLab CI, or Azure DevOps to determine the duration of each stage such as source code checkout, dependency installation, unit testing, static code analysis, security scanning, Docker image building, artifact upload, infrastructure deployment, integration testing, and deployment verification. This helps me identify the bottleneck rather than making assumptions.

Usually, dependency installation is one of the biggest contributors to slow pipelines. If tools like Maven, Gradle, npm, pip, or Composer download dependencies every time, I enable dependency caching so only new packages are downloaded. Similarly, Docker builds are optimized using multi-stage builds and Docker layer caching. Frequently changing instructions are moved towards the bottom of the Dockerfile while stable layers such as dependency installation are placed earlier so Docker can reuse cached layers instead of rebuilding everything from scratch.

The next optimization is parallel execution. Many organizations execute unit tests, code quality analysis, security scans, and linting sequentially, even though these jobs are independent. I configure these stages to run in parallel, significantly reducing overall pipeline duration. Integration tests and deployment stages continue sequentially only where dependencies exist. If the repository is a monorepo, I implement path-based builds so that only the modified services are rebuilt and redeployed instead of rebuilding every microservice after every commit.

Testing strategy is another major optimization area. Unit tests should complete within minutes, while integration, regression, and performance tests generally consume much longer. Instead of executing every test during every commit, I categorize tests into fast and slow suites. Fast tests run during pull requests, while complete regression and performance tests execute after deployment to staging or during scheduled nightly builds. This maintains software quality while significantly reducing developer feedback time.

Infrastructure deployment is also analyzed. If Terraform spends several minutes refreshing the complete infrastructure state unnecessarily, I optimize the configuration by reducing unnecessary resources, improving module structure, and ensuring only modified resources are evaluated whenever possible. Similarly, Kubernetes deployments are optimized by using rolling updates efficiently rather than waiting for excessive health-check intervals. Artifact upload and download are improved by compressing artifacts and storing them in optimized repositories such as Nexus, Artifactory, or Amazon S3.

Security should never be sacrificed for speed, but security scans can be optimized. Instead of repeatedly scanning identical dependencies, I cache vulnerability databases and execute incremental scans where supported. Static code analysis tools are configured to analyze only changed files whenever possible, reducing scan duration without compromising security.

Finally, after implementing each optimization, I compare pipeline execution metrics before and after the changes. The objective is not simply reducing build time but maintaining reliability, security, and deployment quality. By combining dependency caching, Docker optimization, pipeline parallelization, selective testing, incremental scanning, and deployment optimization, I have seen pipelines reduce from more than 40 minutes to under 10 minutes without adding additional build agents or hardware.

---

# 5) "An SRE says infra is stable, dev team says the system is slow, and monitoring shows everything is GREEN. Who do you believe — and what do you check first?"

In this situation, I do not immediately believe either team because every team observes the system from a different perspective. The SRE team generally focuses on infrastructure metrics such as CPU, memory, disk usage, node health, and network availability. Developers usually observe application behavior, response times, exceptions, and business logic. Monitoring dashboards often display only the metrics that have been configured, meaning they can appear green while users still experience poor performance. Therefore, my priority is to collect evidence rather than relying on opinions.

The first thing I do is validate the problem from the user's perspective. I attempt to reproduce the issue by accessing the application through the same URL, API, or user workflow. I measure actual response times and determine whether the slowness affects all users or only specific APIs, geographic regions, or workloads. If users are genuinely experiencing latency while dashboards remain green, it immediately indicates that the monitoring strategy is incomplete.

Next, I review Application Performance Monitoring (APM) tools such as Datadog, New Relic, Dynatrace, AWS X-Ray, or OpenTelemetry. These tools provide distributed tracing and help identify where requests spend the most time. Even if Kubernetes nodes have low CPU utilization, a single database query, authentication service, cache miss, or external API call can introduce several seconds of latency. Distributed tracing allows me to pinpoint the exact component responsible for the delay.

I then examine application logs and error rates. Slow response times are often caused by repeated retries, database lock contention, thread pool exhaustion, connection pool saturation, or long-running background jobs. I review pod logs using `kubectl logs`, application metrics, JVM garbage collection logs (if applicable), and server logs to identify hidden bottlenecks that infrastructure monitoring may not capture.

Database performance is one of the most common causes of slow applications. I check slow query logs, active database connections, CPU usage, storage latency, locking issues, and query execution plans. Even when infrastructure is healthy, inefficient SQL queries or exhausted database connection pools can significantly degrade application performance.

Caching layers are also verified. Redis, Memcached, CloudFront, or application-level caches may have experienced cache eviction or reduced hit ratios, causing every request to reach the backend database. Similarly, I inspect message queues such as Kafka, RabbitMQ, or Amazon SQS to determine whether requests are accumulating because downstream consumers cannot process them quickly enough.

Another important step is validating monitoring itself. "Everything is GREEN" often means that alert thresholds are poorly configured or that important metrics are not being collected. I verify whether dashboards include response time, request latency, error percentages, database performance, queue depth, dependency latency, and user experience metrics instead of only infrastructure health. Infrastructure may appear healthy while application performance continues to deteriorate.

After identifying the root cause, I communicate my findings with both teams using evidence rather than assumptions. My goal is not to prove one team right or wrong but to restore service quickly and improve monitoring so future incidents are detected automatically before users report them. I also recommend adding synthetic monitoring, end-to-end health checks, and business-level metrics so dashboards accurately reflect the customer experience instead of only infrastructure status.


---

# 6) "Terraform apply is failing due to drift, but the infra is currently live and critical. How do you fix it without causing downtime?"

When Terraform detects drift on a critical production environment, my first priority is to **avoid making any changes that could impact running services**. I never use `terraform apply` blindly because it may recreate or destroy production resources. Instead, I begin by running `terraform plan` to identify exactly which resources have drifted. I compare the Terraform state file with the actual infrastructure and determine whether the drift was caused by a manual change, another automation tool, or an AWS-managed update. I review CloudTrail logs and Git history to identify who made the changes and when they occurred. If the manual change was intentional and production is stable, I update the Terraform code to reflect the current infrastructure or import unmanaged resources using `terraform import`. If the drift is accidental, I evaluate whether correcting it requires resource replacement. For resources such as EC2 instances, RDS databases, Load Balancers, or EKS clusters, I avoid destructive operations and instead plan a maintenance window if necessary. Before applying any changes, I back up the Terraform state, enable state locking, verify dependencies, and test the changes in a staging environment. During the implementation, I continuously monitor CloudWatch, application health, and business metrics to ensure no customer impact occurs. After the issue is resolved, I identify why the drift occurred and implement preventive measures such as restricting manual production changes, enforcing Infrastructure as Code policies, enabling drift detection, requiring peer reviews, and using GitOps so that Terraform remains the single source of truth.

---

# 7) "Your rollback script fails during a production outage. You have 5 minutes before SLA breach.

Walk me through your decision."

During a production outage, my priority is **restoring customer service**, not fixing the rollback script. I immediately assess whether the failed deployment is the root cause by checking deployment history, monitoring dashboards, logs, and recent infrastructure changes. If the rollback automation fails, I do not spend valuable minutes debugging the script. Instead, I execute a controlled manual rollback using the last known stable version. In Kubernetes, this may involve using `kubectl rollout undo`, manually updating the Deployment image, or redirecting traffic back to the previous ReplicaSet. If a Blue-Green or Canary deployment strategy is being used, I switch production traffic back to the healthy environment using the Load Balancer or service mesh. Throughout the incident, I keep stakeholders informed about the recovery progress and avoid making unnecessary configuration changes that could introduce additional failures. Once production traffic is successfully restored, I verify application functionality through health checks, API testing, monitoring dashboards, and business metrics before declaring the incident resolved. Only after service stability is confirmed do I investigate why the rollback automation failed. I perform a root cause analysis, repair the rollback script, add automated rollback testing to the CI/CD pipeline, improve runbooks, and conduct a post-incident review so future production incidents can be resolved more quickly and reliably.

---

# 8) "A secret was accidentally committed to GitHub. It has already been cloned. What are your next exact steps?"

Once I discover that a secret has been committed to GitHub, I immediately assume that it has been compromised because anyone who cloned the repository may have access to it. My first action is **not** deleting the commit but rotating the exposed credential. If the secret is an AWS Access Key, I immediately deactivate and replace it. If it is a database password, API token, SSH key, or Kubernetes Secret, I generate new credentials and update all applications using them. After ensuring production continues to function with the new credentials, I review CloudTrail, GitHub audit logs, and application logs to identify whether the exposed secret has been used maliciously. I then remove the secret from the repository history using tools such as `git filter-repo` or BFG Repo-Cleaner and force-push the cleaned repository after coordinating with the team. Since other developers may already have cloned the repository, I communicate the incident and ask them to re-clone or clean their local repositories. I also notify the security team and document the incident according to organizational procedures. Finally, I implement preventive controls by enabling GitHub Secret Scanning, GitHub Push Protection, pre-commit hooks, automated secret detection tools such as Gitleaks or TruffleHog, secure secret management through AWS Secrets Manager or HashiCorp Vault, and developer awareness training to prevent similar incidents in the future.

---

# 9) "A Kubernetes cluster upgrade works in staging but corrupts CoreDNS in production. How do you approach patching and restoring service?"

When a Kubernetes cluster upgrade succeeds in staging but causes CoreDNS failures in production, I treat it as a **high-priority production incident** because DNS is a critical component of the Kubernetes control plane. Without CoreDNS, applications cannot resolve service names, resulting in widespread communication failures between microservices. My first priority is restoring production service rather than continuing the upgrade. I immediately check the CoreDNS pod status using `kubectl get pods -n kube-system` and inspect logs with `kubectl logs` to determine whether the pods are crashing, failing readiness checks, or experiencing configuration errors. I also verify whether the CoreDNS Deployment, ConfigMap, and Service were modified during the upgrade. If the issue is directly related to the upgrade, I restore the previous working CoreDNS version or roll back the cluster upgrade if the rollback process is supported and safe. While restoring the service, I continuously monitor application health, DNS resolution, and customer-facing APIs to confirm that communication between services has resumed.

Once production is stabilized, I investigate why the issue occurred despite staging succeeding. I compare Kubernetes versions, CoreDNS versions, node operating systems, CNI plugins, kube-proxy configuration, VPC networking, and cluster add-ons between staging and production. Production environments often contain larger workloads, different network policies, additional security configurations, or custom DNS settings that may not exist in staging. I validate DNS resolution by launching temporary pods and performing `nslookup` and `dig` commands against internal Kubernetes services. I also review node health, kubelet logs, and network plugin logs to determine whether the issue originated from networking instead of CoreDNS itself. After identifying the root cause, I reproduce the problem in a non-production environment and verify the fix before scheduling another production upgrade. Finally, I improve the upgrade process by implementing canary upgrades, automated DNS health checks, rollback validation, compatibility testing, infrastructure backups, and documented runbooks so future Kubernetes upgrades can be performed with significantly lower operational risk.

---

# 10) "Tell me a real scenario where YOU introduced a failure in infrastructure. What happened, what did you learn, and what changed after?"

One production incident that taught me a valuable lesson occurred during a Terraform deployment involving AWS Security Groups. While implementing a change request, I modified an existing Security Group to improve security by restricting inbound traffic. The Terraform plan appeared correct, and the deployment completed successfully without any errors. However, within a few minutes, application health alerts started appearing in CloudWatch, and users began reporting failures while accessing the application. Investigation showed that I had unintentionally removed a rule that allowed communication between the application servers and the backend database. Since the application could no longer establish database connections, API requests started failing even though the EC2 instances, Kubernetes pods, and Load Balancer remained healthy.

As soon as the issue was identified, I informed the incident manager and the development team while simultaneously reviewing the Terraform changes. Instead of making additional configuration changes, I restored the previous Security Group rule through Terraform and verified that database connectivity was re-established. I monitored application logs, CloudWatch metrics, Kubernetes health checks, and customer transactions to ensure that the service had fully recovered before closing the incident. Throughout the incident, I kept stakeholders updated with clear communication regarding the root cause, estimated recovery time, and validation status. The outage lasted only a short period because the issue was identified quickly, but it highlighted the importance of validating infrastructure changes beyond simply checking whether Terraform completed successfully.

This incident significantly changed the way I approach infrastructure deployments. We introduced mandatory peer reviews for all Terraform pull requests, especially those involving networking, IAM policies, and Security Groups. We also added automated connectivity tests in the CI/CD pipeline to verify communication between application servers, databases, and dependent services before production deployments were approved. Infrastructure changes were first validated in staging using production-like network configurations instead of simplified environments. We implemented detailed change documentation, rollback procedures, and post-deployment smoke tests to detect issues immediately after deployment. Personally, the incident reinforced the importance of never assuming that a successful Terraform execution guarantees a healthy application. Since then, I have always validated infrastructure changes from both the infrastructure perspective and the application perspective before considering any production deployment successful. This experience strengthened my troubleshooting skills, improved my understanding of production risk management, and helped our team build a much more reliable deployment process.

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
