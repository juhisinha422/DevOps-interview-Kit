# ********Real time Devops Interview Questions **********

### 1.give me usecase where you use two layer or three layer architecture and what are the aws resources you used.

**Two Tier Architecture**

Use case: Small applications where application and web layer are combined.

Example: Internal company portals, small web apps.

AWS resources used:
- Route53 (DNS)
- Application Load Balancer
- EC2 instances (application + web server)
- RDS (database)
- Security Groups
- VPC
- Auto Scaling Group

Flow:
User → Route53 → ALB → EC2 → RDS


**Three Tier Architecture**

Use case: Production enterprise applications where scalability and security are required.

Example: Ecommerce application.

AWS resources used:

Presentation Layer
- Route53
- CloudFront
- Application Load Balancer

Application Layer
- EC2 / ECS / EKS
- Auto Scaling Group

Database Layer
- RDS / Aurora
- ElastiCache

Flow:
User → Route53 → CloudFront → ALB → App Servers → Database


---

### 2. major drawback of ecs and eks

**ECS Drawbacks**
- Limited flexibility compared to Kubernetes
- AWS vendor lock-in
- Smaller ecosystem compared to Kubernetes
- Less community support

**EKS Drawbacks**
- More complex to manage
- Control plane cost
- Requires Kubernetes expertise
- Cluster upgrades and management complexity


---

### 3. currently you have database instance and it having 100gb of volume cached this volume is under provision user is very less how can you decrease the size and what is the ideal way of doing it.

AWS does not allow direct reduction of EBS volume size.

Ideal way:

1. Take snapshot of the existing volume
2. Create new smaller EBS volume from snapshot
3. Attach the new volume to instance
4. Migrate data if required
5. Detach old volume

For RDS:
- Create snapshot
- Restore with smaller storage if possible
- Migrate database


---

### 4. inside kubernetes what kind of application you are having i am asking domain bsed application you are managing 10 pods others are managing 100pods production base

In my project we deployed **microservices based applications**.

Example domains:
- Ecommerce
- Payment services
- User authentication services
- Order management services

Typical setup:

10 pods
- Non production environments
- Development / testing

100+ pods
- Production workloads
- Multiple microservices
- Autoscaling enabled using HPA

Tools used:
- Kubernetes
- Helm
- ArgoCD (GitOps)
- Prometheus
- Grafana


---

### 5. have you managed end to end application in kubernetes

Yes.

Responsibilities included:

- Containerizing application using Docker
- Creating Kubernetes manifests
- Helm chart development
- Deploying applications in EKS cluster
- Configuring HPA and autoscaling
- Implementing CI/CD pipeline
- Monitoring using Prometheus and Grafana
- Logging using ELK stack
- Managing secrets using Kubernetes secrets


---

### 6. diffrence between init container and statefullset

**Init Container**

- Runs before the main container starts
- Used for initialization tasks
- Runs only once
- Example:
  - DB migration
  - Configuration setup
  - Dependency checks


**StatefulSet**

- Used for stateful applications
- Provides stable network identity
- Provides persistent storage
- Pods are created in order

Example:
- MySQL
- MongoDB
- Kafka
- Redis


---

### 7. shall i attach liveness and readiness probe to init container, if not why its not advisable

No.

Init containers run only once during pod startup.

Liveness and readiness probes are designed for long-running containers.

Because:
- Init container exits after completion
- Probes are meant to check health of running containers


---

### 8. for application you choosen statful set the reason was? we can use simple deployment replicaset, whats the neccessity to use sets over there

StatefulSet is used when application requires:

- Stable pod identity
- Persistent storage
- Ordered deployment
- Stateful workloads

Example:

Database clusters
Kafka
Redis

Deployment is used for **stateless applications** like web apps.


---

### 9. suppose i ask you to deploy new version of image right now, what are the changes expected to be done before the deployment using helm

Steps:

1. Update image tag in `values.yaml`

Example

```
image:
  repository: myapp
  tag: v2
```

2. Validate Helm chart

```
helm lint
```

3. Dry run deployment

```
helm upgrade --install myapp ./chart --dry-run
```

4. Deploy

```
helm upgrade --install myapp ./chart
```


---

### 10. you are using helm iin which stage what code you used for this, at the time of build what its contribution there

Helm is mainly used in **deployment stage of CI/CD pipeline**.

Typical Jenkins pipeline stages:

1. Code checkout
2. Build application
3. Build Docker image
4. Push image to registry
5. Deploy using Helm

Example command:

```
helm upgrade --install app-release ./helm-chart \
--set image.tag=$BUILD_NUMBER
```

Contribution:
Helm helps in **templating Kubernetes manifests** and simplifies deployments.


---

### 11.when you rollback the current version what is happening behind the scencce

When rollback happens:

- Helm fetches previous release configuration
- Kubernetes deployment is updated with previous image version
- Old ReplicaSet becomes active again
- New ReplicaSet scales down


---

### 12. while rollback new replicatset created also will rollback

No.

Rollback does not create a new ReplicaSet.

Instead Kubernetes activates the **previous ReplicaSet** and scales it up while scaling down the current one.


---

### 13.if you have two many lines in docker file how it will effect on my docker image

Too many layers will:

- Increase image size
- Increase build time
- Reduce performance
- Increase attack surface

Best practice:
Combine commands using `&&`.


Example:

```
RUN apt-get update && apt-get install -y nginx
```


---

### 14. what is copy on right layer

Docker uses **copy-on-write layered filesystem**.

Each instruction in Dockerfile creates a layer.

When a container modifies a file:
- It creates a copy in the writable layer
- Base image layers remain unchanged


---

### 15. what kind of docker netework you worked with

Common Docker networks used:

Bridge network
- Default network
- Used for containers on single host

Host network
- Container uses host network directly

Overlay network
- Used in Docker Swarm for multi-host networking

Macvlan
- Container gets its own MAC address


---

### 16. somebody asks you to decrease docker image how can you do it

Ways to reduce Docker image size:

- Use alpine base images
- Use multi stage builds
- Remove unnecessary packages
- Use `.dockerignore`
- Combine RUN commands
- Remove temporary files


Example

```
FROM node:18-alpine
```


---

### 17. what are the benefit to store docker image in ecr, where will you store

Benefits of storing images in **Amazon ECR**

- Fully managed container registry
- Integrated with IAM
- Secure image storage
- High availability
- Easy integration with ECS and EKS
- Image vulnerability scanning


---

### 18. how many stages you have in jenkins

Typical Jenkins CI/CD pipeline stages:

1. Code Checkout
2. Build
3. Unit Testing
4. Code Quality (SonarQube)
5. Build Docker Image
6. Push Image to Registry
7. Deploy to Kubernetes using Helm
8. Post Deployment Validation

Tools used:
- Jenkins
- Docker
- Kubernetes
- Helm
- SonarQube
- AWS ECR
