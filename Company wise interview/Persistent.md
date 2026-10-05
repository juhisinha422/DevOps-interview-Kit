# Kubernetes, Docker, Terraform, and GitHub Actions: Q&A (4+ Years Experience)

## Kubernetes (K8s)

### 1. What is the current version of K8s you are using in your project?
"In our project, we strive to maintain a stable Kubernetes version within 1-2 versions of the latest stable release. Currently, we are using Kubernetes v1.33 in our production environment. We chose this version after evaluating the stability and new features that align with our infrastructure needs.

We follow a structured upgrade cycle, where we monitor the release notes of each version, especially security patches and bug fixes. Our upgrade process typically involves testing new versions in staging environments to ensure compatibility with our workloads before deploying them to production. We also keep an eye on deprecations and ensure that any APIs or features that are being phased out are addressed before upgrading."

### 2. What was the last production issue you faced and how did you resolve it?
A recent issue involved a service being intermittently unavailable due to node resource exhaustion (CPU/Memory). We used `kubectl top nodes` and `kubectl describe pod` to identify which pods were consuming excessive resources. Once pinpointed, we adjusted resource limits in the deployment manifest and used Horizontal Pod Autoscaling (HPA) to balance load. We also updated our Cluster Autoscaler settings to add more nodes dynamically.

### 3. A pod is stuck in CrashLoopBackOff. Logs show failure during initialization — how do you troubleshoot?
- **Check Pod Logs**: `kubectl logs <pod_name> --previous` to get the previous crash logs.
- **Check Pod Events**: `kubectl describe pod <pod_name>` to get detailed events related to the pod’s lifecycle.
- **Check Resource Requests and Limits**: Sometimes pods fail due to insufficient CPU/memory.
- **Examine Configurations**: If the pod has environmental variables or config files, check whether they are properly defined and accessible.
- **Check for Missing Dependencies**: Make sure the pod's dependencies (e.g., database connection) are available.

### 4. How do you enforce tenant isolation in a multi-tenant Kubernetes setup?
- **Namespaces**: Create separate namespaces for each tenant.
- **RBAC**: Use Role-Based Access Control (RBAC) to limit what users and services can access within each namespace.
- **Network Policies**: Ensure tenants are isolated at the network layer by using Kubernetes network policies.
- **Resource Quotas**: Implement resource quotas and limits to prevent tenants from exceeding their allocated resources.
- **Pod Security Policies**: Use PodSecurityPolicy (or alternatives like OPA) to enforce security rules for workloads in each namespace.

### 5. During high traffic, your app shows intermittent 502 errors through Ingress — how do you debug?
- **Check Ingress Controller Logs**: Use `kubectl logs <ingress-controller-pod>` to view any errors or timeouts.
- **Check Backend Pod Logs**: Look at logs of the services behind the ingress (they might be unhealthy or timing out).
- **Check Load Balancer Health**: Ensure that the load balancer (e.g., NGINX, HAProxy) isn’t overwhelmed or misconfigured.
- **Verify Resource Usage**: High traffic could overwhelm app pods—use `kubectl top pods` to monitor resources.
- **Scaling**: Check if Horizontal Pod Autoscaling (HPA) is set up correctly and auto-scaling can handle load.

### 6. How do you prevent bad configs from reaching production in a CI/CD pipeline?
- **GitOps**: Use GitOps with tools like ArgoCD to manage configurations declaratively.
- **Configuration Validation**: Use tools like kube-score, kubeval, and kube-linter to validate Kubernetes manifests.
- **Linting and Tests**: Integrate YAML linting and unit tests for configuration files in CI/CD pipelines to catch errors before deployment.
- **Approval Gates**: Implement approval processes where configurations are reviewed manually before being applied to production.

### 7. How would you ensure zero-downtime deployment during a critical update?
- **Blue-Green or Canary Deployment**: Use blue-green or canary deployments with a controlled rollout strategy. Kubernetes’ native support for rolling updates ensures that only a fraction of pods are updated at a time.
- **Readiness/Liveness Probes**: Ensure readiness and liveness probes are set correctly so the load balancer doesn’t route traffic to unhealthy pods.
- **Pod Disruption Budgets (PDBs)**: Set PDBs to ensure a minimum number of pods are always available during an update.

### 8. Helm deployment fails due to insufficient cluster resources — what’s your approach?
- **Review Resource Requests**: Check the Helm values files for pod resource requests and adjust them.
- **Cluster Autoscaler**: If possible, enable the Cluster Autoscaler to add more nodes dynamically.
- **Prioritize Deployments**: If resources are tight, use PodDisruptionBudgets and adjust priorities for critical workloads.
- **Horizontal Pod Autoscaler (HPA)**: Configure HPA to scale the application in/out based on resource usage.

### 9. How do you share Helm charts internally?
- **Private Helm Repository**: Use a private Helm chart repository (e.g., Harbor, Nexus, or even AWS S3 + Helm).
- **GitHub/GitLab Repos**: You can also store Helm charts in Git repositories, but make sure to tag and version them properly for easy tracking.
- **Helm Chart Registry**: If you use a Helm Chart Registry (e.g., Artifact Hub), you can upload private charts to control access.

### 10. What is Helm chart testing and how is it done?
- **Helm Tests**: Helm has built-in support for test hooks (`helm test`) that allow running tests on deployed resources.
- **Linting**: Helm's `helm lint` command helps identify issues in chart definitions before deployment.
- **Integration Tests**: Using tools like `Kubeval` or `Kube-score` to validate Kubernetes manifests created from Helm templates.
- **CI Integration**: Incorporating Helm chart testing into CI pipelines ensures that charts are functional before deployment.

---

## Docker

### 1. How would you manage Docker workloads across multiple clouds?
- **Container Orchestration (K8s)**: Use Kubernetes to abstract the cloud-specific implementation details and manage workloads seamlessly across cloud providers.
- **Docker Swarm**: For simpler use cases, Docker Swarm can span multiple clouds with minimal configuration.
- **Multi-cloud Registry**: Use a multi-cloud image registry (e.g., Docker Hub, ECR, GCR) to store images, enabling deployment to different clouds.

### 2. How do you handle image cleanup to prevent disk space issues?
- **Scheduled Pruning**: Use `docker image prune` and `docker system prune` regularly to clean up unused images, volumes, and containers.
- **Automated Cleanup**: Set up cron jobs or use tools like `docker-gc` for automated cleanup of old images.
- **Retention Policy**: Implement a retention policy where only the latest X versions of images are kept in the registry.

### 3. How do you manage multi-container dependencies using Docker Compose?
- **Docker Compose**: Use `docker-compose.yml` to define and link multi-container applications. Specify dependencies between containers with `depends_on`.
- **Health Checks**: Ensure that each container has appropriate health checks to prevent dependent containers from starting prematurely.

### 4. How do you monitor container performance in production?
- **Prometheus & Grafana**: Use Prometheus to scrape container metrics and Grafana to visualize them. Kubernetes Metrics Server and Docker Stats API can help monitor performance.
- **cAdvisor**: An open-source tool that provides container-level performance metrics.
- **Log Aggregation**: Use centralized logging solutions like ELK (Elasticsearch, Logstash, Kibana) or Fluentd to collect logs and monitor performance bottlenecks.

### 5. Wrote a multi-stage Dockerfile during screen sharing.
In a multi-stage Dockerfile, we typically use multiple `FROM` statements to create intermediate images that can be discarded, reducing the final image size. For example:
```dockerfile
# Stage 1: Build image
FROM node:14 AS build
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Stage 2: Production image
FROM node:14-slim
WORKDIR /app
COPY --from=build /app/dist /app/dist
COPY --from=build /app/node_modules /app/node_modules
CMD ["node", "dist/server.js"]
```

## Terraform
### 1. How do you manage Terraform provider versioning?

Provider Version Constraints: Specify provider versions in the required_providers block to ensure compatibility and stability across environments.

Terraform Version: Use required_version to enforce a consistent Terraform version across teams.

Terraform Lock Files: Use .terraform.lock.hcl to lock provider versions to avoid unexpected changes when running Terraform.

### 2. How would you provision infra across 10 AWS regions simultaneously?

Multi-Region Provider Setup: Define multiple provider blocks in the Terraform configuration for each AWS region:

```bash
provider "aws" {
  alias  = "us-east-1"
  region = "us-east-1"
}

provider "aws" {
  alias  = "us-west-1"
  region = "us-west-1"
}
```

Module Usage: Create a module for your resources and then invoke that module with different provider configurations for each region.

### 3. What to do when your Terraform state file becomes too large?

State Splitting: Split the Terraform state file by creating different state files for different components (e.g., networking, EC2, etc.).

Backend Configuration: Use a remote backend like S3



### 4. Terraform Plan Shows Destroy + Recreate for a Critical DB — How to Prevent Downtime?

When Terraform plans to destroy and recreate a critical database, it can lead to significant downtime, especially if the database is directly serving production traffic. To prevent such disruptions, there are several strategies you can adopt:

Lifecycle Configuration (prevent_destroy): In Terraform, you can use the lifecycle block with the prevent_destroy argument to prevent accidental destruction of critical resources like databases. This ensures that even if Terraform plans to destroy a resource, it will not be executed unless explicitly overridden. This is useful when you want to avoid Terraform from deleting critical infrastructure like a production database inadvertently.

```bash
resource "aws_db_instance" "example" {
  engine         = "postgres"
  instance_class = "db.t2.micro"
  identifier     = "my-db"

  lifecycle {
    prevent_destroy = true
  }
}
```

create_before_destroy to Ensure Smooth Transition: If the DB resource needs to be replaced (perhaps due to a version upgrade or instance resizing), you can use the create_before_destroy lifecycle rule. This instructs Terraform to create a new resource before destroying the existing one, ensuring there is no downtime between the old and new instances. For critical databases, you might also want to automate DNS switching or update application configurations to point to the new database once it’s ready.

```bash
resource "aws_db_instance" "example" {
  engine         = "postgres"
  instance_class = "db.t2.micro"
  identifier     = "my-db"

  lifecycle {
    create_before_destroy = true
  }
}
```

Multi-AZ or Read Replica Setup: For high-availability setups like AWS RDS, you can leverage Multi-AZ deployments or read replicas to minimize downtime. In such cases, Terraform can manage database failovers or promote a read replica to be the primary DB instance. This allows for a seamless transition during an upgrade or maintenance event, reducing the risk of downtime. If you're dealing with a managed database service, ensure that your DB supports automatic failover or that you implement manual promotion of replicas before making the changes in Terraform.

Rolling Updates and Versioned Migrations: If you're using Terraform to update the schema or replace resources, implement rolling updates with versioned migrations. For example, you can use tools like Flyway or Liquibase for database migrations, which allow for more granular control over schema changes. By managing the DB schema separately, you can ensure that updates are non-disruptive and that the database remains operational during changes.

Manual or Automated Backup and Restore: Before applying changes, always take a snapshot or backup of the database to ensure that you can recover quickly in case anything goes wrong. You can use managed database services that support snapshots or backup policies. After the change, you can restore the database from backup if necessary, which allows for a rollback option in case the plan doesn’t go as expected.

Plan and Validate Changes in Staging: Before running any Terraform plan in production, it’s crucial to validate changes in a staging or pre-production environment that mimics your production setup. This helps catch potential issues early on and prevents any surprises when applying the changes to critical infrastructure. For database-related changes, test for data integrity, application compatibility, and performance in your staging environment.

By combining these approaches, you can mitigate the risk of downtime during Terraform-driven changes to critical infrastructure, such as databases. Terraform's declarative nature allows for automation, but it’s essential to integrate strategies like backups, high availability, and controlled deployments to ensure the safety and availability of your production environment.

This should give you a solid understanding of how to approach this issue based on your 4+ years of experience in managing critical infrastructure with Terraform.


## GitHub Actions

### 1. How do you reuse workflows across repositories?
- **GitHub Actions Reusable Workflows**: You can create reusable workflows by defining them in one repository and referencing them in others using the `workflow_call` event. For example:
  
  # In the calling repo
  ```bash
  jobs:
    call-workflow:
      uses: org/repo/.github/workflows/workflow.yml@main


This makes it easier to manage shared workflows such as deployment or CI processes.

### 2. How to manage large workflow files efficiently?

Modularize Workflows: Break large workflow files into smaller, reusable jobs or steps. GitHub Actions allows workflows to reference other workflows or jobs, making them modular.

Use Action Repositories: Create custom actions and store them in separate repositories, then reference them in your workflow files. This reduces duplication and makes managing changes easier.

Use Workflow Includes: Organize your workflows using includes or external files for common steps, which can be reused across different workflows.

### 3. What’s the difference between public and private workflow repositories?

Public Workflow Repositories: These repositories are accessible to everyone, and workflows in them can be referenced publicly. They are ideal for open-source projects where anyone can contribute.

Private Workflow Repositories: These repositories are restricted to specific users or teams within your organization. They are used to store internal workflows that should not be publicly available or for sensitive workflows like deployments that involve secret management.

### 4. How to implement workflow concurrency?

Concurrency Key: You can limit the number of workflows running at the same time by specifying a concurrency key. This is particularly useful to avoid conflicts or resource contention in workflows. Example:

```bash
concurrency:
  group: production-deployment
  cancel-in-progress: true
```

This ensures that only one deployment to production can occur at a time. If a new deployment is triggered, the old one will be canceled.

### 5. How do you handle failed workflows?

Retries: Use the retry strategy in workflows or jobs. If a job fails, you can automatically retry it for a defined number of times before marking it as failed.

```bash
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Run tests
        run: npm test
        continue-on-error: true  # Allow job to continue even if tests fail
```

**Manual Intervention:** Use workflow_run or dispatch events to trigger workflows that require manual intervention when a failure occurs, like notifying an admin to approve or fix the failure.

**Notifications:** Set up automatic failure notifications (via Slack, email, etc.) to ensure the right team is alerted when a workflow fails.

Failure Handling with if: You can define steps that should only run when a previous step or job fails, like cleanup tasks or sending notifications.

```bash
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Build
        run: npm run build
      - name: Notify on failure
        if: failure()
        run: echo "Build failed!"
```
