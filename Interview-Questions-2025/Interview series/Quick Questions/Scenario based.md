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

