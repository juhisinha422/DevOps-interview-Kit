# What is HELM? 
Helm is the package manager for Kubernetes. It helps you deploy and manage applications in EKS using a single command instead of multiple YAML files.

✅ Faster deployments

✅ Easy upgrades & rollbacks

✅ Reusable Helm Charts

✅ Simplified Kubernetes management

Think of Helm as “apt/yum” for Kubernetes.

## Helm Chart: -
A Helm Chart is a collection of YAML templates and configuration files that define a Kubernetes application. It includes metadata (Chart.yaml), default values (values.yaml), templates, and dependencies.

## Structure of a Helm Chart

A Helm Chart typically contains:

1. Chart.yaml – metadata about the chart.

2. values.yaml – default configuration values.

3. templates/ – Kubernetes manifest templates.

4. charts/ – dependent charts.

5. NOTES.txt – post-installation instructions.

# 🚀 DevOps Scenario-Based Interview Questions (3+ Years Experience)

---

# 1️⃣ Your Docker container is running but the app is not accessible. How do you debug it step by step?

When a Docker container is running but the application is inaccessible, I troubleshoot layer by layer.

First, I verify whether the container is actually running:

```bash
docker ps
```

Then I check container logs:

```bash
docker logs <container-id>
```

This helps identify application startup failures, port binding issues, dependency errors, or crashes.

Next, I verify port mapping:

```bash
docker port <container-id>
```

I ensure the container port is correctly mapped to the host port.

Then I enter the container:

```bash
docker exec -it <container-id> /bin/bash
```

Inside the container, I check:
- Whether the application process is running
- Whether the app is listening on the correct port
- Internal connectivity using curl or netstat

Example:

```bash
netstat -tulnp
curl localhost:8080
```

I also verify:
- Firewall rules
- Security groups
- Reverse proxy configuration
- Kubernetes service/ingress configuration

In production, the most common causes are:
- Incorrect port mapping
- Application binding only to localhost
- Failed application startup
- Missing environment variables
- Network policy restrictions

---

# 2️⃣ You have 10 microservices. Each needs a different base image. How do you manage Dockerfiles efficiently?

When managing multiple microservices, maintaining separate repetitive Dockerfiles becomes difficult.

I usually standardize Dockerfile structures using:
- Multi-stage builds
- Shared base images
- Template-based Dockerfiles
- CI/CD automation

For example, backend Java services may use a common internal base image:

```dockerfile
FROM company-base-java:17
```

Node.js services may use another reusable base image.

This provides:
- Consistent security patches
- Smaller maintenance effort
- Standardized runtime configuration
- Faster builds

I also maintain:
- Common linting rules
- Centralized image versioning
- Automated vulnerability scanning

For enterprise environments, platform teams often maintain approved golden base images for all services.

---

# 3️⃣ Your CI/CD pipeline is passing but deployment is failing in prod. What do you check first?

If the pipeline succeeds but deployment fails in production, my first focus is deployment validation and runtime behavior.

I first check:
- Kubernetes events
- Pod status
- Deployment rollout status
- Application logs

Commands:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

Common causes include:
- Missing secrets
- Wrong environment variables
- Database connectivity issues
- Image pull failures
- Readiness probe failures
- Resource limits

I also verify:
- Whether the correct image tag was deployed
- Helm values/configuration
- Service discovery
- External API access

In production systems, monitoring dashboards and alerts help quickly identify deployment failures.

---

# 4️⃣ A developer pushed bad code and it went live. How do you rollback using your pipeline?

Rollback should be automated and fast in production environments.

If using Kubernetes with Helm:

```bash
helm rollback <release-name>
```

If using native Kubernetes deployments:

```bash
kubectl rollout undo deployment/<deployment-name>
```

In GitOps environments using ArgoCD, rollback can be done by reverting Git commits or syncing previous application versions.

A good production pipeline always supports:
- Versioned artifacts
- Immutable Docker images
- Automated rollback
- Deployment history tracking

Rollback triggers usually include:
- Increased error rate
- Failed health checks
- Pod crashes
- High latency
- Monitoring alerts

Fast rollback capability is extremely important for reducing production downtime.

---

# 5️⃣ Your Jenkins build is taking 45 minutes. How do you reduce it to under 10 minutes?

To optimize Jenkins build performance, I first identify the major bottlenecks.

Common optimization techniques include:
- Parallel pipeline stages
- Dependency caching
- Incremental builds
- Distributed Jenkins agents
- Docker layer caching
- Reducing unnecessary test execution

For example:
- Maven dependencies can be cached
- Docker builds can use layer caching
- Unit and integration tests can run in parallel

Jenkins declarative pipeline example:

```groovy
parallel {
  stage('Unit Tests') {
    steps {
      sh 'mvn test'
    }
  }
  stage('Lint') {
    steps {
      sh 'npm run lint'
    }
  }
}
```

I also optimize:
- SCM checkout time
- Artifact storage
- Jenkins executor allocation
- Build agent sizing

In enterprise CI/CD systems, proper caching and parallel execution can drastically reduce build times.

---

# 6️⃣ Two services are working fine individually but fail when integrated. How do you catch this in your pipeline before it hits prod?

This is a common microservices problem where unit tests pass but integration fails.

To catch this early, I implement:
- Integration testing
- Contract testing
- End-to-end testing
- API compatibility checks

Common tools include:
- Postman/Newman
- Pact
- Selenium
- Cypress
- Karate

In CI/CD pipelines, I deploy dependent services into temporary test environments and run integration test suites automatically.

Example validations include:
- API schema compatibility
- Authentication flow
- Database interaction
- Message queue communication
- Timeout handling

I also use:
- Mock services
- Service virtualization
- Smoke testing after deployment

In production-grade DevOps pipelines, integration testing is critical because many failures occur only when services communicate with each other.


----------
# "How do Docker containers talk to each other?"

Docker creates virtual networks.

If two containers are on the same network, they can communicate using container names.

Example 👇

𝗖𝗿𝗲𝗮𝘁𝗲 𝗻𝗲𝘁𝘄𝗼𝗿𝗸

docker network create app-network

𝗥𝘂𝗻 𝗰𝗼𝗻𝘁𝗮𝗶𝗻𝗲𝗿𝘀
```
docker run -d --name backend --network app-network backend-image
docker run -d --name frontend --network app-network frontend-image

𝗡𝗼𝘄 𝗳𝗿𝗼𝗻𝘁𝗲𝗻𝗱 𝗰𝗮𝗻 𝗰𝗮𝗹𝗹:
http://backend:5000

𝗗𝗼𝗰𝗸𝗲𝗿 𝗮𝘂𝘁𝗼𝗺𝗮𝘁𝗶𝗰𝗮𝗹𝗹𝘆 𝗿𝗲𝘀𝗼𝗹𝘃𝗲𝘀:
backend → container IP

And if you want your laptop/browser to access a container:

docker run -p 8080:80 nginx

𝗡𝗼𝘄:
localhost:8080 → container port 80

```

# DevOps Scenario-Based Interview Questions & Answers (3+ Years Experience)

## 1️⃣ Your microservice is returning 500 errors only for specific users.

### How do you isolate and debug it?

When a microservice returns 500 errors only for specific users, my first step is identifying what makes those users different from others. I start by collecting failed request details such as user IDs, request payloads, API endpoints, timestamps, and correlation IDs. Next, I check application logs, distributed tracing tools, and monitoring dashboards to identify exceptions occurring during those requests. I verify whether the issue is related to user-specific data, authorization permissions, feature flags, database records, caching inconsistencies, or third-party integrations. I also compare successful and failed requests to identify differences. If needed, I reproduce the issue in a lower environment using the affected user's data. Once the root cause is identified, I implement the fix, validate the affected workflow, monitor error rates, and document the incident to prevent recurrence.

---

## 2️⃣ Jenkins pipeline is running for 2 hours. Normal time is 15 minutes.

### What do you check first?

If a Jenkins pipeline suddenly increases from 15 minutes to 2 hours, I first identify which stage is consuming the additional time. I review Jenkins build logs and stage duration metrics to locate the bottleneck. Common causes include infrastructure resource constraints, agent issues, network latency, dependency download delays, external service failures, artifact repository slowness, test execution problems, or stuck deployment stages. I verify CPU, memory, disk utilization, and network performance on Jenkins agents. I also check whether recent code changes introduced long-running tests or inefficient build steps. If the issue is related to infrastructure, I scale build agents or increase resources. Once the root cause is found, I optimize the pipeline and establish monitoring to detect future performance degradation early.

---

## 3️⃣ Kubernetes HPA is configured but not scaling during peak traffic.

### What could be wrong?

If Horizontal Pod Autoscaler is not scaling during high traffic, I begin by verifying that the Metrics Server is running and collecting resource metrics successfully. I inspect the HPA status using kubectl commands and check whether CPU, memory, or custom metrics are reaching the configured threshold. Common issues include missing resource requests, incorrect HPA thresholds, unavailable metrics, custom metric adapter failures, or workload bottlenecks unrelated to the configured scaling metric. I also verify that the Deployment, ReplicaSet, and Cluster Autoscaler are functioning correctly. Sometimes traffic increases but CPU remains low because the application is blocked on database queries or external APIs. In such cases, CPU-based scaling may not trigger. After identifying the issue, I adjust scaling metrics, thresholds, or infrastructure capacity accordingly.

---

## 4️⃣ Your team pushed a change and latency jumped from 200ms to 3 seconds.

### How do you find the exact cause?

When latency increases immediately after a deployment, I first correlate the timing of the change with monitoring data to confirm the deployment is responsible. I compare application metrics before and after deployment, focusing on response time, error rates, database queries, CPU utilization, memory usage, and network latency. I analyze application logs and distributed tracing data to identify slow transactions and determine where delays are occurring. I also review the deployment changes, configuration updates, feature flags, dependency versions, and infrastructure modifications introduced in the release. If necessary, I perform a rollback to restore service while continuing the investigation. Once the root cause is identified, I validate the fix in lower environments before redeploying it to production.

---

## 5️⃣ New developer committed AWS secret keys to GitHub by mistake.

### What do you do in the next 5 minutes?

This is a critical security incident requiring immediate action. My first step is revoking or disabling the exposed AWS access keys from the IAM console to eliminate the security risk. Next, I verify whether the repository is public or private and assess the exposure scope. I then remove the secrets from Git history using tools such as BFG Repo-Cleaner or git filter-repo because simply deleting the file is not sufficient. After key rotation, I review AWS CloudTrail logs to determine whether the compromised credentials were used by unauthorized parties. I notify security teams and stakeholders, document the incident, and implement preventive controls such as secret scanning, GitHub secret detection, pre-commit hooks, and secure secret management solutions like AWS Secrets Manager or HashiCorp Vault.

---

## 6️⃣ You need to deploy to 3 environments with one click and zero downtime.

### How do you design the pipeline?

For this requirement, I design a multi-stage CI/CD pipeline that follows a build-once-deploy-many approach. The application is built, tested, scanned, and packaged only once, producing a versioned artifact or container image. The same artifact is promoted sequentially through Development, UAT, and Production environments to ensure consistency. Each stage includes automated validation checks and approval gates where required. For zero-downtime deployments, I use Kubernetes Rolling Updates, Blue-Green Deployments, or Canary Deployments depending on application requirements. Health checks, readiness probes, and automated rollback mechanisms are integrated into the pipeline to ensure failed releases do not impact users. Monitoring, alerting, and deployment verification steps are executed after each deployment stage to confirm application health before promoting the release further. This design provides reliability, traceability, consistency, and minimal business disruption.

----

𝗜𝗻𝘁𝗲𝗿𝘃𝗶𝗲𝘄𝗲𝗿: 𝗔 𝗱𝗲𝘃𝗲𝗹𝗼𝗽𝗲𝗿 𝗷𝘂𝘀𝘁 𝗽𝘂𝘀𝗵𝗲𝗱 𝘁𝗼 𝗺𝗮𝗶𝗻 𝗯𝗿𝗮𝗻𝗰𝗵. 𝗖𝗜 𝗽𝗮𝘀𝘀𝗲𝗱. 𝗖𝗗 𝗱𝗲𝗽𝗹𝗼𝘆𝗲𝗱 𝘁𝗼 𝗽𝗿𝗼𝗱𝘂𝗰𝘁𝗶𝗼𝗻 𝗮𝘂𝘁𝗼𝗺𝗮𝘁𝗶𝗰𝗮𝗹𝗹𝘆. 𝗦𝗼𝗺𝗲𝘁𝗵𝗶𝗻𝗴 𝗯𝗿𝗼𝗸𝗲. 𝗪𝗵𝗮𝘁 𝘄𝗲𝗻𝘁 𝘄𝗿𝗼𝗻𝗴 𝗶𝗻 𝘆𝗼𝘂𝗿 𝗗𝗲𝘃𝗢𝗽𝘀 𝘀𝗲𝘁𝘂𝗽?

My answer: Challenge accepted. Multiple things could have gone wrong. Here is how I find it.

► No manual approval gate — production should never deploy automatically without approval.

  Add manual approval step before production deployment in pipeline.

► No staging environment test — code went from dev straight to prod.
 
  Every change must pass staging first. Staging must mirror production.

► No rollback strategy — when something breaks, team is scrambling.
 
  Every deployment must have one-click rollback ready before going live.

► No smoke test after deployment — pipeline marked success but app is broken.
 
  Add automated smoke test step that hits critical endpoints after deploy.

► Branch protection missing — direct push to main allowed without PR review.
 
  Protect main branch. Require at least one reviewer approval.

► No feature flags — half finished feature went live.
 
  Use feature flags to deploy code without activating the feature.

𝗛𝗼𝘄 𝗜 𝗱𝗲𝘀𝗶𝗴𝗻 𝗮 𝘀𝗮𝗳𝗲 𝗽𝗶𝗽𝗲𝗹𝗶𝗻𝗲:

• Dev → Staging (auto) → Production (manual approval only)

• Smoke test after every deployment

• Rollback ready before every release

• Branch protection on main always


-----------------
# CrashLoopBackOff Error

After successfully deploying an application, a pod continuously restarting and showing:

❌ CrashLoopBackOff

This is one of the most frequently encountered issues in Kubernetes environments.

Recently, I faced a situation where an application pod was repeatedly crashing immediately after startup. Kubernetes kept trying to restart the container, resulting in a CrashLoopBackOff state.

🔍 My troubleshooting approach:

✅ Checked pod logs using:
kubectl logs <pod-name>

✅ Reviewed previous container logs:
kubectl logs <pod-name> --previous

✅ Inspected pod events:
kubectl describe pod <pod-name>

✅ Verified environment variables and ConfigMaps

✅ Checked Secrets and application configuration

✅ Reviewed resource limits and requests

✅ Confirmed database and external service connectivity

🎯 Root Cause:

The application was expecting a mandatory environment variable that was missing from the deployment configuration. As a result, the application exited immediately after startup.

Once the configuration was corrected and the deployment was restarted, the pod became healthy and stable.

💡 Key Takeaway:

When troubleshooting CrashLoopBackOff, focus on understanding why the application is exiting rather than simply restarting the pod.

Always verify:

• Application logs

• Configuration settings

• Environment variables

• Secrets and ConfigMaps

• Resource limits

• External dependencies

-----

# 𝗬𝗼𝘂𝗿 𝗔𝗪𝗦 𝗯𝗶𝗹𝗹 𝗱𝗼𝘂𝗯𝗹𝗲𝗱 𝘁𝗵𝗶𝘀 𝗺𝗼𝗻𝘁𝗵. 𝗡𝗼 𝗻𝗲𝘄 𝗿𝗲𝘀𝗼𝘂𝗿𝗰𝗲𝘀 𝗮𝗱𝗱𝗲𝗱. 𝗙𝗶𝗻𝗱 𝘁𝗵𝗲 𝗰𝗮𝘂𝘀𝗲. 𝗬𝗼𝘂 𝗵𝗮𝘃𝗲 𝟮 𝗺𝗶𝗻𝘂𝘁𝗲𝘀.

Here is exactly how I find it.

► AWS Cost Explorer — open immediately. Filter by service. Find which service spiked.

► Check EC2 — any instances running 24x7 that should be stopped at night?
  Large instance types left on by mistake = massive bill.

► Check NAT Gateway — most hidden cost. High data processing charges.
  Fix: replace with VPC Endpoints for S3 and DynamoDB. Free.

► Check Data Transfer — cross region traffic is expensive.
  Moving data between regions without realizing = surprise bill.

► Check RDS — Multi-AZ or read replicas running in wrong environment?

► Check Elastic IPs — charged when not attached to running instance.
  Release unused EIPs immediately.

► Check S3 requests — millions of API calls add up silently.
  Add CloudFront in front of S3 to reduce direct calls.

𝗛𝗼𝘄 𝗜 𝗽𝗿𝗲𝘃𝗲𝗻𝘁 𝘀𝘂𝗿𝗽𝗿𝗶𝘀𝗲 𝗯𝗶𝗹𝗹𝘀:

• AWS Budgets alert when cost crosses threshold

• Cost Anomaly Detection for automatic alerts

• Tag every resource — know which team spends what

• Schedule non-prod EC2 to stop at night

--------------------

# DevOps Engineer Interview Answers (4 Years Experience)

## 1. Tell me about yourself / your responsibilities in previous organization.

I have around 4 years of experience as a DevOps Engineer working on AWS, Kubernetes, Jenkins, Docker, Terraform, GitLab CI/CD, Helm, Prometheus, Grafana, and Linux administration. My responsibilities included managing CI/CD pipelines, containerizing applications, deploying workloads on Kubernetes/EKS clusters, automating infrastructure using Terraform, monitoring applications and infrastructure, troubleshooting production issues, implementing security best practices, and collaborating with development teams to ensure reliable and scalable deployments. I was also responsible for cluster maintenance, application releases, vulnerability remediation, and cloud resource optimization.

---

## 2. Do you have experience in OpenShift or only Kubernetes?

My primary experience is with Kubernetes, particularly AWS EKS. I understand OpenShift architecture, deployment models, Routes, Security Context Constraints (SCC), integrated registry, and Operator-based management. Since OpenShift is built on Kubernetes, most concepts such as Pods, Deployments, Services, ConfigMaps, Secrets, and Ingress remain similar.

---

## 3. What are the differences between Kubernetes and OpenShift?

Kubernetes is an open-source container orchestration platform used to manage containerized workloads. OpenShift is a Kubernetes distribution provided by Red Hat with additional enterprise features. OpenShift includes integrated CI/CD tools, image registry, monitoring, enhanced security through SCC, built-in Routes, and Operator management. Kubernetes provides flexibility while OpenShift focuses on enterprise-grade security and ease of operations.

---

## 4. Do you have experience in Jenkins pipelines?

Yes, I have extensive experience creating and maintaining Declarative Jenkins Pipelines. Pipelines were used for building applications, running unit tests, performing code quality checks with SonarQube, scanning vulnerabilities using Trivy, building Docker images, pushing images to registries, and deploying applications to Kubernetes environments. I have integrated Jenkins with GitHub, GitLab, Docker, Kubernetes, SonarQube, and Slack notifications.

---

## 5. Your Jenkins pipeline is taking too much time. How will you troubleshoot it?

I first identify which stage consumes the most time by reviewing Jenkins stage logs. Then I check build duration history, agent resource utilization, network latency, Docker image build times, dependency downloads, test execution duration, and deployment steps. I optimize by enabling parallel execution, caching dependencies, using lightweight Docker images, reducing unnecessary stages, scaling Jenkins agents, and cleaning workspace artifacts. If infrastructure is overloaded, I verify CPU, memory, and disk utilization on Jenkins nodes.

---

## 6. Have you worked on vulnerabilities / DevSecOps?

Yes. I have worked on vulnerability management using Trivy, SonarQube, and container image scanning. Vulnerabilities were identified during CI/CD execution before deployment. Critical and high severity findings were remediated by upgrading libraries, patching OS packages, using secure base images, removing unused packages, implementing least privilege access, and enforcing security policies before production releases.

---

## 7. If a production cyber attack happens due to API endpoint exposure, how will you handle it?

First, I would contain the attack by blocking malicious traffic using WAF, security groups, network policies, or API gateway rules. Then I would analyze logs to identify the attack source and affected systems. Compromised credentials would be rotated immediately. I would verify application integrity, perform root cause analysis, patch vulnerabilities, restore services if required, and monitor the environment for further suspicious activities. Finally, I would document the incident and implement preventive controls to avoid recurrence.

---

## 8. How do applications communicate with each other in Kubernetes?

Applications communicate through Kubernetes Services. Each microservice is exposed internally using a ClusterIP Service. Kubernetes DNS resolves service names to cluster IPs, allowing applications to communicate using service names instead of pod IP addresses.

Example:

http://user-service.default.svc.cluster.local

---

## 9. How does traffic flow between two applications running on different nodes?

When Application A sends a request to Application B using a Service name, Kubernetes DNS resolves the Service IP. Kube-proxy programs iptables/IPVS rules that forward traffic to one of the backend pods. If the target pod is on another node, the CNI plugin handles inter-node networking and routes traffic to the destination node and pod.

---

## 10. Can we apply rules to control pod-to-pod communication?

Yes. Kubernetes Network Policies are used to control communication between pods. We can define ingress and egress rules based on namespaces, pod labels, and IP ranges. This helps enforce zero-trust networking and restrict unauthorized traffic between applications.

---

## 11. Do you have experience provisioning Kubernetes clusters?

Yes. I have provisioned AWS EKS clusters using Terraform and eksctl. The process involved creating VPCs, subnets, IAM roles, worker node groups, security groups, storage classes, ingress controllers, monitoring tools, and application namespaces.

---

## 12. What is bootstrap in cluster provisioning?

Bootstrap is the initial process where worker nodes join the Kubernetes cluster. During bootstrap, nodes download required configurations, register with the control plane, install kubelet and networking components, and become available to schedule workloads.

---

## 13. How do you deploy applications without downtime?

I use Rolling Update deployment strategy. Kubernetes gradually replaces old pods with new pods while maintaining application availability. Readiness probes ensure traffic is sent only to healthy pods. In critical applications, blue-green or canary deployments can also be used for zero-downtime releases.

---

## 14. If pods go into CrashLoopBackOff state, how will you troubleshoot?

I start by checking pod logs using:

kubectl logs <pod-name>

Then I describe the pod:

kubectl describe pod <pod-name>

I verify application errors, environment variables, secrets, configuration issues, resource limits, image problems, database connectivity, and health probe failures. Based on findings, I fix the root cause and redeploy the application.

---

## 15. How will you increase CPU and memory limits for a deployment?

I modify the deployment manifest and update the resources section under the container specification. After applying the changes, Kubernetes performs a rolling update and recreates pods with the new resource limits.

---

## 16. Which command is used to increase CPU and memory limits?

Using kubectl edit:

kubectl edit deployment <deployment-name>

Or update YAML and apply:

kubectl apply -f deployment.yaml

Example:

resources:
requests:
cpu: "500m"
memory: "512Mi"
limits:
cpu: "1"
memory: "1Gi"

---

## 17. Do you have experience with Route 53?

Yes. I have used Route 53 for DNS management, domain routing, load balancer integration, health checks, and traffic routing. It was commonly used to map application domains to AWS Application Load Balancers and Kubernetes ingress endpoints.

---

## 18. What DNS records have you used in Route 53?

I have worked with A Records, CNAME Records, Alias Records, MX Records, TXT Records, and NS Records for application routing, email verification, SSL validation, and domain management.

---

## 19. Difference between A Record, CNAME Record, and Alias Record.

A Record maps a domain name directly to an IP address.

CNAME Record maps one domain name to another domain name.

Alias Record is an AWS-specific feature that maps domains directly to AWS resources such as Load Balancers, CloudFront distributions, and S3 websites without requiring an IP address.

---

## 20. Which monitoring tools have you used?

I have worked with Prometheus, Grafana, CloudWatch, AlertManager, and Container Insights. These tools were used for infrastructure monitoring, application monitoring, log analysis, alerting, and capacity planning.

---

## 21. How did you utilize monitoring tools in your project?

Prometheus collected metrics from Kubernetes clusters and applications. Grafana dashboards visualized CPU, memory, disk, pod health, API response times, and application metrics. AlertManager generated alerts for threshold breaches. CloudWatch was used for AWS resource monitoring and centralized logging.

---

## 22. How do you verify and scan metrics in Prometheus/Grafana?

In Prometheus, I use PromQL queries to validate metrics and verify targets through the Targets page. In Grafana, I validate dashboard queries, compare historical trends, create alerts, and correlate infrastructure metrics with application performance issues.

---

## 23. Can you write a Kubernetes deployment YAML file?

Yes. A Deployment YAML includes API version, kind, metadata, replica count, selectors, pod template, container image, ports, environment variables, resource limits, and health probes.

---

## 24. Deployment YAML with 2 replicas, port 8080, secret mounted as environment variable

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
      - name: sample-app
        image: nginx:latest
        ports:
        - containerPort: 8080
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: password
```

---

## 25. Was your application monolithic or microservices-based?

The applications I worked on were primarily microservices-based. Different services handled separate business functions and were independently developed, deployed, scaled, and monitored.

---

## 26. How were microservices deployed and connected?

Microservices were containerized using Docker and deployed to Kubernetes using Helm charts. Each service had its own Deployment and Service resources. Communication occurred through internal Kubernetes Services, API Gateway, and DNS-based service discovery.

---

## 27. Do you have experience with Helm charts?

Yes. I have created and maintained Helm charts for deploying applications, databases, ingress resources, ConfigMaps, Secrets, and monitoring components. Helm helped standardize deployments across development, staging, and production environments.

---

## 28. How do you pass values in Helm using values.yaml?

Configuration values are defined in values.yaml and referenced in templates using Helm syntax.

Example values.yaml:

```yaml
replicaCount: 2

image:
  repository: nginx
  tag: latest
```

Deployment template:

```yaml
replicas: {{ .Values.replicaCount }}

image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

Deploy command:

```bash
helm install my-app ./chart -f values.yaml
```

For environment-specific overrides:

```bash
helm upgrade my-app ./chart -f prod-values.yaml
```
--------
# DevOps Scenario-Based Interview Questions & Answers (4+ Years Experience)

## 1️⃣ Pod is running. App returns 503. What is your first command?

My first command would be:

```bash
kubectl describe svc <service-name>
```

A 503 error typically indicates that the service has no healthy backend endpoints available. Even though the pod is running, it may not be passing readiness probes, may not be selected by the service labels, or may not be registered as an endpoint. After checking the service, I would run `kubectl get endpoints <service-name>` to verify whether endpoints exist. Then I would inspect the pod status, readiness probes, labels, logs, and ingress configuration. In production, many 503 issues are caused by readiness probe failures rather than actual application crashes.

---

## 2️⃣ terraform plan shows destroy and recreate. Production is live. What do you do?

I would never immediately run `terraform apply`. My first step is understanding why Terraform wants to recreate the resource. I would carefully review the plan output to identify which attribute change is triggering replacement. Sometimes resource names, immutable properties, provider version changes, or manual infrastructure modifications cause recreation. I would compare the current state file with actual infrastructure and verify whether drift exists. If the replacement could impact production availability, I would investigate alternatives such as importing resources, modifying lifecycle rules, or performing changes during a maintenance window. In production, blindly applying a destroy-and-recreate plan can cause outages, so validation and impact assessment come before execution.

---

## 3️⃣ Pipeline passed. Prod has old code. Name 3 possible reasons.

The first possibility is that the deployment stage never actually deployed the new artifact despite the build succeeding. The second possibility is image caching where Kubernetes or the deployment platform continues using an old container image tag such as `latest`. The third possibility is that the deployment completed successfully but traffic is still being routed to older pods through a load balancer, ingress, CDN, or cache layer. I would verify artifact versions, deployment history, running pod image versions, ingress routing, and cache invalidation. In production, successful pipelines do not always guarantee successful deployments.

---

## 4️⃣ EC2 unreachable. You cannot SSH. Walk me through every step.

I start by verifying whether the EC2 instance is running and passing both AWS status checks. Next, I check whether the correct Security Group allows inbound TCP port 22 from my source IP. Then I verify subnet route tables, Internet Gateway configuration, Elastic IP assignment, and Network ACL rules. If networking appears healthy, I inspect the instance console output and system logs through the AWS console. For private instances, I verify VPN, bastion host, or Session Manager access. If necessary, I detach the root EBS volume, attach it to another instance, and inspect SSH configuration files, disk usage, and system logs. My troubleshooting always follows a layered approach: infrastructure, network, operating system, and application.

---

## 5️⃣ Docker image is 2GB. You have 10 minutes to reduce it. Go.

My first action would be checking the Dockerfile for inefficient layers and unnecessary dependencies. I would immediately switch to a smaller base image such as Alpine if compatible. Next, I would implement multi-stage builds so that build tools remain only in the build stage while the final runtime image contains only the application artifacts. I would remove package manager caches, temporary files, logs, and unused binaries. I would review `.dockerignore` to exclude source control files, test data, documentation, and unnecessary assets. In many cases, multi-stage builds alone reduce image sizes by more than 70%, making them one of the quickest optimization techniques.

---

## 6️⃣ AWS bill doubled. No new resources. Find the cause in under 5 minutes.

My first stop would be AWS Cost Explorer to identify which service category increased spending. I would compare current and previous billing periods and filter by service, region, and usage type. Common causes include data transfer charges, NAT Gateway traffic, increased EBS snapshot storage, S3 requests, CloudWatch logs growth, load balancer traffic, or autoscaling events. I would also review Trusted Advisor and Cost Anomaly Detection alerts. Even if no new resources were created, increased utilization of existing services can significantly increase costs. The objective is identifying the service responsible before performing deeper analysis.

---

## 7️⃣ Node is NotReady. 3 pods stuck in Pending. What do you check first?

My first check is:

```bash
kubectl describe node <node-name>
```

I want to determine why the node entered the NotReady state. Common causes include kubelet failure, network connectivity issues, resource exhaustion, disk pressure, memory pressure, or container runtime failures. I would review node conditions, events, kubelet logs, and container runtime status. For the Pending pods, I would inspect scheduling events using `kubectl describe pod`. Frequently, pending pods occur because Kubernetes cannot find a healthy node with sufficient CPU, memory, storage, or matching taints and tolerations.

---

## 8️⃣ Developer committed secret keys to GitHub. What do you do right now?

The immediate action is revoking or disabling the exposed credentials. Security comes before cleanup. After revocation, I rotate all affected secrets and verify whether the repository is public or private. Next, I remove the secrets from Git history using tools such as BFG Repo-Cleaner or git filter-repo because deleting the file alone is insufficient. I review CloudTrail logs to determine whether the credentials were abused. Finally, I implement preventive controls including GitHub secret scanning, pre-commit hooks, branch protection policies, and secret management systems such as AWS Secrets Manager or HashiCorp Vault.

---

## 9️⃣ HPA is set. Traffic spiked. Pods not scaling. Why?

The most common reason is that Metrics Server is unavailable or not reporting metrics. I would verify HPA status and metrics collection first. Other possibilities include missing resource requests, incorrect scaling thresholds, misconfigured custom metrics, API server issues, or traffic patterns that do not impact the configured metric. For example, traffic may increase while CPU remains low due to external bottlenecks such as database latency. I would inspect HPA events, metrics availability, Deployment configuration, and Cluster Autoscaler status. Understanding which metric drives scaling is critical for troubleshooting.

---

## 🔟 State file locked. Team cannot deploy. How do you fix it safely?

First, I confirm whether another engineer or pipeline is currently running Terraform. I never force unlock without verification because it can corrupt infrastructure state. If the deployment process crashed and left a stale lock behind, I identify the lock ID from the error message and verify no active Terraform operations exist. After confirmation, I execute:

```bash
terraform force-unlock <LOCK_ID>
```

Then I run `terraform plan` to ensure state consistency before allowing further deployments. In production environments using S3 and DynamoDB backends, stale locks commonly occur after interrupted CI/CD executions. Safe validation before unlocking is essential to prevent concurrent modifications and state corruption.

---


# Capgemini DevOps Interview Questions & Answers (4+ Years Experience)

## 1. Can you give your introduction.

I am a DevOps Engineer with around 4 years of experience working on cloud infrastructure, CI/CD automation, containerization, Kubernetes orchestration, and Infrastructure as Code. My primary expertise is in AWS, Kubernetes, Docker, Jenkins, GitLab CI/CD, Terraform, Helm, ArgoCD, and monitoring tools such as Prometheus and Grafana. In my current role, I am responsible for designing and maintaining CI/CD pipelines, managing Kubernetes and OpenShift clusters, automating infrastructure provisioning, implementing GitOps practices, and ensuring application reliability in production environments. I have worked closely with development, QA, and operations teams to improve deployment efficiency, reduce downtime, and strengthen security and compliance controls.

---

## 2. How the typical CI/CD architecture looks like. Entire Process.

A typical CI/CD architecture starts when a developer commits code to a Git repository such as GitHub, GitLab, or Bitbucket. This commit triggers a Jenkins or GitLab CI pipeline. During the CI phase, the application code is compiled, unit tests are executed, code quality analysis is performed using SonarQube, and security scans are conducted using tools such as Nexus IQ, Trivy, or Aqua Security. Once validation succeeds, a Docker image is built and pushed to a container registry such as Nexus, Harbor, Docker Hub, or Amazon ECR. During the CD phase, deployment manifests or Helm charts are updated and synchronized to Kubernetes using ArgoCD. Kubernetes performs rolling updates while monitoring readiness and health checks. Finally, Prometheus and Grafana monitor the deployed application, and alerts are generated through Alertmanager if issues occur.

---

## 3. How the Helm chart structure looks like.

A Helm chart consists of a predefined directory structure used to package Kubernetes applications. The chart contains a Chart.yaml file that stores chart metadata such as name and version. The values.yaml file contains configurable parameters that can be customized per environment. The templates directory contains Kubernetes resource templates such as Deployment, Service, Ingress, ConfigMap, Secret, and HPA definitions. The charts directory can contain dependent charts. During deployment, Helm combines templates with values from values.yaml and generates Kubernetes manifests dynamically. This allows the same chart to be reused across development, staging, and production environments with different configurations.

---

## 4. What is the difference between ReplicaSet and Replication Controller?

Replication Controller is the older Kubernetes resource responsible for maintaining a specified number of pod replicas. ReplicaSet is the newer generation resource that extends Replication Controller functionality by supporting set-based label selectors in addition to equality-based selectors. In modern Kubernetes environments, ReplicaSets are rarely created directly because Deployments manage them automatically. Deployments use ReplicaSets internally to support rolling updates, rollbacks, and version management. Therefore, ReplicaSet is more flexible and commonly used in production through Deployments.

---

## 5. What all stages have you written in Jenkins pipeline?

In my projects, Jenkins pipelines typically contain stages such as Checkout, Build, Unit Testing, Static Code Analysis, Dependency Scanning, Security Scanning, Docker Image Build, Docker Image Scan, Artifact Upload, Container Registry Push, Helm Chart Validation, Deployment to Development, Integration Testing, UAT Deployment, Production Approval, Production Deployment, Smoke Testing, and Notifications. Depending on project requirements, rollback and post-deployment validation stages are also included. The objective is to ensure code quality, security, and deployment reliability before production release.

---

## 6. Did you use GitOps automation. How do you manage this approach in production?

Yes, I have used GitOps extensively with ArgoCD. In GitOps, Git serves as the single source of truth for infrastructure and application deployment configurations. Developers commit deployment changes to a Git repository, and ArgoCD continuously monitors the repository. Whenever changes are detected, ArgoCD automatically synchronizes the Kubernetes cluster with the desired state defined in Git. In production, we maintain separate repositories or branches for different environments and enforce pull request approvals before changes are merged. This approach provides version control, auditability, rollback capability, and deployment consistency across environments.

---

## 7. What happens if CMD or ENTRYPOINT is not provided in Dockerfile? Will the container run?

A container requires a running process to stay alive. If neither CMD nor ENTRYPOINT is specified in the Dockerfile and the base image also does not define one, the container will start and immediately exit because no process is available to execute. However, if the base image already defines an ENTRYPOINT or CMD, the container may still run successfully using the inherited configuration. Therefore, whether the container runs depends on the base image configuration. In production, it is recommended to explicitly define CMD or ENTRYPOINT to avoid ambiguity and ensure predictable behavior.

---

## 8. What is RBAC? Have you provided any RBAC roles for users or service accounts?

RBAC stands for Role-Based Access Control and is used to control permissions within Kubernetes. RBAC determines what actions users, groups, or service accounts can perform on cluster resources. I have implemented Roles and RoleBindings for namespace-level permissions and ClusterRoles and ClusterRoleBindings for cluster-wide access. For example, developers may receive read-only access to pods, deployments, and logs, while DevOps engineers receive deployment and administration permissions. Service accounts are assigned specific permissions following the principle of least privilege to reduce security risks.

---

## 9. How did you package Maven builds?

Maven builds are packaged using the Maven lifecycle. After code checkout, I execute commands such as `mvn clean package` or `mvn clean install`. The build process compiles source code, executes unit tests, resolves dependencies, and generates artifacts such as JAR or WAR files. These artifacts are then uploaded to repositories such as Nexus or Artifactory. If containerization is required, the generated artifact is copied into a Docker image and pushed to a container registry for deployment through Kubernetes.

---

## 10. What is Nexus IQ and Aqua Security scan used for?

Nexus IQ is a Software Composition Analysis (SCA) tool used to identify vulnerabilities, license violations, and risks in open-source dependencies. It helps ensure that applications do not use insecure or non-compliant third-party libraries. Aqua Security is a container security platform used to scan Docker images, Kubernetes workloads, and cloud-native applications for vulnerabilities, malware, secrets, and configuration risks. In production pipelines, Nexus IQ is typically used for dependency security while Aqua Security is used for container and runtime security validation.

---

## 11. Is HashiCorp Vault and CyberArk both used for secret management?

Yes, both HashiCorp Vault and CyberArk are secret management solutions, but they are commonly used for different purposes. HashiCorp Vault is widely used in cloud-native and DevOps environments to manage dynamic secrets, API keys, certificates, and application credentials. CyberArk is traditionally focused on privileged access management and securing high-privilege credentials such as administrator accounts. Many enterprises use both tools together, with CyberArk managing privileged accounts and Vault managing application and infrastructure secrets.

---

## 12. What is the OpenShift version you are using?

The exact version depends on the organization. A strong interview answer is: "In my recent project, we were using OpenShift 4.x, which is based on Kubernetes and provides additional enterprise features such as integrated authentication, enhanced security policies, operator-based management, built-in image registry, and developer tooling. The specific version may vary depending on the project's upgrade cycle."

---

## 13. How did you configure OpenShift workloads?

OpenShift workloads are configured similarly to Kubernetes workloads using DeploymentConfigs, Deployments, Services, Routes, ConfigMaps, Secrets, and Horizontal Pod Autoscalers. We define resource requests and limits, configure health probes, assign service accounts, and manage environment-specific settings using ConfigMaps and Secrets. Routes are used instead of traditional Kubernetes Ingress in many OpenShift environments. Security Context Constraints (SCCs) are also configured to comply with OpenShift security requirements.

---

## 14. How did you make sure vulnerability thresholds are defined for SonarQube?

In SonarQube, vulnerability thresholds are enforced through Quality Gates. We configure Quality Gates to define acceptable limits for vulnerabilities, code smells, bugs, duplicated code, and code coverage. During pipeline execution, SonarQube analyzes the code and compares results against the configured thresholds. If the Quality Gate fails, the Jenkins or GitLab pipeline automatically fails and prevents deployment progression. This ensures that only code meeting predefined quality and security standards can move toward production environments.

# DevOps Production Scenarios – Interview Answers (4+ Years Experience)

## 1. Pod is Running but Returning 503. Is it the Pod or Service?

When a pod is running but users receive a 503 error, my first step is to identify whether the issue is at the application layer, service layer, or ingress layer. I start by checking the pod status using `kubectl get pods` and reviewing container logs with `kubectl logs`. Next, I verify whether the service has healthy endpoints using `kubectl get endpoints <service-name>`. If the endpoints list is empty, the service selector is not matching the pod labels. If endpoints exist, I test connectivity directly to the pod IP and service ClusterIP using curl. I also inspect ingress or load balancer configurations if traffic is routed through them. Within a couple of minutes, I can determine whether the issue is due to an unhealthy application, incorrect service selectors, readiness probe failures, or ingress routing problems.

---

## 2. Terraform Apply Failed Halfway and State File is Out of Sync

When Terraform apply fails midway, I first avoid making any manual changes. My priority is to understand the current state by running `terraform state list` and comparing it with the actual infrastructure in AWS. I then execute `terraform plan` to identify resource drift. If resources were created successfully but are missing from the state file, I import them using `terraform import`. If the state file itself is corrupted, I restore it from the remote backend version history, such as an S3 bucket with versioning enabled. Once the state is synchronized with the actual infrastructure, I run another plan to ensure no unexpected changes are pending before executing a controlled apply. This approach prevents accidental resource recreation or deletion.

---

## 3. Pipeline Passed but Production Still Has Old Code

A successful pipeline does not always mean the deployment succeeded. I begin by validating whether a new Docker image was actually built and pushed by checking image tags and registry timestamps. Next, I verify whether the deployment was triggered and completed successfully in Kubernetes using rollout history and deployment status. I check if the new image tag was updated in the deployment manifest and confirm the running pods are using the latest image. Other possibilities include browser caching, CDN caching, ingress routing issues, failed rolling updates, image pull policy misconfiguration, or deployments targeting the wrong environment. I systematically verify each layer—from CI pipeline, container registry, deployment configuration, Kubernetes rollout, and application version endpoint—to identify where the deployment process broke.

---

## 4. EC2 Instance is Unreachable and SSH Access is Not Available

When an EC2 instance becomes unreachable, I follow a structured troubleshooting approach. First, I verify the instance state in AWS and ensure it is running. I then review system status checks and instance status checks from the EC2 console. If checks fail, I inspect the system logs and console output for kernel panics, filesystem issues, or boot failures. I validate security group rules, network ACLs, route tables, internet gateway configuration, and public IP assignment. If the instance appears healthy but SSH remains inaccessible, I use AWS Systems Manager Session Manager if configured. Otherwise, I detach the root EBS volume, attach it to a rescue instance, inspect logs, correct configuration issues such as SSH daemon failures or disk space exhaustion, and then reattach the volume. This method allows recovery without rebuilding the server.

---

## 5. Docker Image is 2.1 GB and Deployment is Slow

To reduce a large Docker image, I first identify which layers consume the most space using image history analysis. I replace full operating system images with lightweight base images such as Alpine or Distroless whenever compatible. I implement multi-stage builds to ensure only runtime artifacts are included in the final image while build dependencies remain in intermediate stages. I remove unnecessary packages, caches, logs, documentation files, and development tools. Language-specific optimizations such as pruning Node.js dependencies, excluding Maven repositories, or removing Python build tools are also applied. By combining these techniques, I have reduced multi-gigabyte images to a few hundred megabytes while maintaining application functionality and security.

---

## 6. AWS Bill Doubled Without New Resources

When AWS costs suddenly increase, I immediately open Cost Explorer and compare the current billing period with the previous month grouped by service. This quickly identifies the service contributing to the cost spike. I then drill down by usage type, linked account, region, and resource tags. Common causes include increased data transfer, NAT Gateway usage, CloudWatch log ingestion, EBS snapshots, load balancer traffic, or autoscaling events. I also review AWS Budgets, Cost Anomaly Detection alerts, and CloudTrail logs to identify unusual activities. Within a few minutes, I can usually isolate the exact service and resource responsible for the increase and recommend corrective actions.

---

## 7. HPA Configured but Pods Are Not Scaling During Traffic Spike

If Horizontal Pod Autoscaler is not scaling, I investigate four primary areas. First, I verify that the Metrics Server is healthy and metrics are available. Second, I confirm resource requests are defined because HPA calculates utilization based on requests. Third, I inspect HPA events and current metrics using `kubectl describe hpa` to determine whether scaling thresholds are being met. Fourth, I check whether cluster capacity exists to schedule additional pods. Even if HPA requests more replicas, scaling will fail if nodes lack CPU or memory resources. These checks usually reveal whether the issue is metrics collection, configuration, threshold tuning, or infrastructure capacity.

---

## 8. Secret Keys Accidentally Committed to GitHub

If credentials are exposed in GitHub, my first action is to treat them as compromised. I immediately revoke or rotate the affected keys, tokens, passwords, or certificates. Next, I assess the exposure scope and determine whether the repository is public or private. I remove the secret from Git history using repository cleaning tools and force-push the sanitized history if appropriate. I review audit logs, monitor for suspicious activity, and notify relevant stakeholders. Finally, I implement preventive controls such as secret scanning, pre-commit hooks, CI/CD security checks, and centralized secret management systems like Vault or AWS Secrets Manager.

---

## 9. Grafana Shows Green but Users Are Reporting Issues

When dashboards indicate healthy systems while users experience problems, it usually means observability is focused only on infrastructure metrics. CPU, memory, and disk usage can appear normal while business transactions fail. To solve this, I ensure observability includes the three pillars: metrics, logs, and distributed tracing. In addition, I monitor application-level KPIs such as response time, error rate, request success percentage, transaction completion rate, and user journey metrics. Synthetic monitoring and real-user monitoring help detect issues that infrastructure dashboards miss. Effective observability should reflect actual user experience, not just server health.

---

## 10. Designing a Zero-Downtime Blue-Green Deployment Strategy

For zero-downtime deployments, I create two identical production environments: Blue and Green. Blue serves live traffic while Green receives the new application version. After deploying to Green, I execute automated smoke tests, health checks, and validation tests. Once verification succeeds, I gradually switch traffic using a load balancer, ingress controller, or weighted routing mechanism. The previous Blue environment remains intact, enabling immediate rollback if issues are detected. Database migrations are designed to be backward compatible to support both versions during transition. This strategy minimizes risk, provides instant rollback capability, and ensures continuous service availability during production releases.




