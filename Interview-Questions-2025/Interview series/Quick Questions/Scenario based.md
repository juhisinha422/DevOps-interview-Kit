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


