# 🚀 Infosys AWS Engineer Interview — Questions & Answers

## AWS, Kubernetes & Terraform Interview Preparation

This README contains **20 AWS Engineer interview questions with practical answers**, tailored for a **4 years experienced AWS/DevOps Engineer**.

The answers are designed around real-world experience with:

* ☁️ AWS
* ☸️ Kubernetes / Amazon EKS
* 🏗️ Terraform
* 🐳 Docker
* 🔄 CI/CD
* 📊 Monitoring & Observability
* 🔐 Cloud Security

---

# 1. Introduce Yourself

### Answer

I have around **4 years of experience in Cloud and DevOps**, primarily working with AWS, Kubernetes, Terraform, Docker, and CI/CD tools.

In my current role, I work on automating infrastructure provisioning, application deployments, and cloud operations. I have hands-on experience with AWS services such as **EC2, VPC, IAM, S3, ALB, Auto Scaling, CloudWatch, and EKS**.

On the DevOps side, I have worked with **Docker, Kubernetes, Terraform, Jenkins, Git, and GitHub**. I have also worked on monitoring and observability using tools such as **Prometheus and Grafana**.

My responsibilities include infrastructure automation using Terraform, managing Kubernetes workloads, troubleshooting production issues, implementing CI/CD pipelines, and improving the reliability and scalability of applications.

---

# 2. What Projects Have You Worked On?

### Answer

I have worked on cloud and DevOps projects involving **AWS infrastructure, Kubernetes, CI/CD automation, and Infrastructure as Code**.

One of my major responsibilities was managing application infrastructure on AWS and deploying containerized applications on Kubernetes.

The project involved:

* AWS infrastructure provisioning using Terraform
* EC2 and EKS management
* VPC, subnets, route tables and security groups
* Docker image creation
* Kubernetes deployments and services
* CI/CD pipelines using Jenkins
* Git-based source code management
* Monitoring using Prometheus and Grafana
* Application and infrastructure troubleshooting

The main objective was to make deployments more **automated, scalable, reliable, and repeatable**.

---

# 3. What AWS Services Have You Worked With?

### Answer

I have worked with several AWS services, mainly:

### Compute

* EC2
* Auto Scaling Groups
* EKS
* Lambda — depending on application requirements

### Networking

* VPC
* Public and Private Subnets
* Internet Gateway
* NAT Gateway
* Route Tables
* Security Groups
* Application Load Balancer

### Storage

* S3
* EBS

### Security

* IAM
* IAM Roles
* Policies
* KMS
* Secrets management

### Monitoring

* CloudWatch
* CloudWatch Logs
* CloudWatch Alarms

I have primarily used these services for deploying, securing, monitoring, and scaling production workloads.

---

# 4. Describe the Recent Project/Work You Were Involved In

### Answer

In my recent project, I was involved in building and maintaining a **containerized application platform on AWS**.

The infrastructure was provisioned using **Terraform**, and applications were containerized using Docker and deployed on **Amazon EKS**.

The high-level flow was:

```text
Developer
    ↓
Git Repository
    ↓
Jenkins CI/CD Pipeline
    ↓
Build & Test
    ↓
Docker Image
    ↓
Container Registry
    ↓
Amazon EKS
    ↓
Application
    ↓
Load Balancer
```

My responsibilities included:

* Creating AWS infrastructure using Terraform
* Managing VPC and networking components
* Managing EKS workloads
* Creating Kubernetes Deployments and Services
* Managing application configuration and secrets
* Troubleshooting failed deployments
* Implementing CI/CD pipelines
* Monitoring applications and infrastructure
* Handling production incidents

---

# 5. What Challenges Did You Encounter in Your Recent Project, and How Did You Resolve Them?

### Answer

One common challenge was **application deployment failures in Kubernetes**.

For example, sometimes a Pod would enter states such as:

```text
ImagePullBackOff
CrashLoopBackOff
Pending
```

I followed a systematic troubleshooting approach.

First, I checked the Pod:

```bash
kubectl get pods -n <namespace>
```

Then:

```bash
kubectl describe pod <pod-name> -n <namespace>
```

I checked the events and container logs:

```bash
kubectl logs <pod-name> -n <namespace>
```

If it was an image issue, I verified:

* Image name
* Image tag
* Registry availability
* Registry credentials
* `imagePullSecrets`

For resource-related issues, I checked:

* CPU/memory requests
* CPU/memory limits
* Node capacity
* Resource quotas

The important part is that I don't immediately restart the Pod. I first identify the **root cause**, fix it, and then validate the deployment.

---

# 6. What Challenges Do You Typically Encounter During a Kubernetes Upgrade?

### Answer

During a Kubernetes upgrade, I normally consider both the **control plane and worker nodes**, along with application compatibility.

Some common challenges are:

### 1. API Deprecation

Older Kubernetes APIs may no longer be supported.

For example:

```text
Old API → New API
```

So I check whether our manifests use deprecated APIs.

### 2. Application Compatibility

I verify whether workloads, Helm charts, controllers, and operators support the target Kubernetes version.

### 3. Node Upgrade Issues

Worker nodes need to be upgraded carefully to avoid application downtime.

### 4. CNI and CSI Compatibility

I verify compatibility of:

* CNI plugin
* CSI drivers
* Ingress controllers
* Load balancer controllers

### 5. Pod Disruption

During node upgrades, Pods may be evicted or rescheduled.

I verify:

* PodDisruptionBudgets
* Replica counts
* Readiness probes
* Pod scheduling

### 6. Helm/Controller Compatibility

I also verify whether installed Helm charts and Kubernetes operators support the target version.

My approach is:

```text
Backup / Review
      ↓
Check Compatibility
      ↓
Upgrade Control Plane
      ↓
Upgrade Add-ons
      ↓
Upgrade Worker Nodes
      ↓
Validate Workloads
      ↓
Monitor
```

---

# 7. What Checks Do You Perform After a Kubernetes Upgrade?

### Answer

After the upgrade, I perform checks at multiple levels.

### Cluster Version

```bash
kubectl version
kubectl get nodes
```

I verify that nodes are in:

```text
Ready
```

### System Pods

```bash
kubectl get pods -A
```

I check whether system components are healthy.

### Nodes

```bash
kubectl get nodes -o wide
```

I verify:

* Node status
* Kubernetes version
* Roles
* Conditions

### Workloads

```bash
kubectl get deployments -A
kubectl get pods -A
kubectl get daemonsets -A
kubectl get statefulsets -A
```

### Services

```bash
kubectl get svc -A
```

I verify that LoadBalancers, ClusterIP and NodePort services are working correctly.

### Application Validation

Finally, I test:

* Application connectivity
* Ingress
* DNS
* Load Balancer
* API endpoints
* Database connectivity

I also monitor the cluster for some time after the upgrade to identify unexpected issues.

---

# 8. What Was the Kubernetes Version Before the Upgrade, and Which Version Did You Upgrade To?

### Answer

A good way to answer this question is to provide a **real version from your project**.

For example:

> In one of our EKS environments, we upgraded Kubernetes from **1.28 to 1.29**. Before the upgrade, we checked API deprecations, application compatibility, Helm charts, CNI/CSI components, and workload disruption requirements. After upgrading, we validated nodes, system Pods, applications, services, ingress, and monitoring.

### Interview Tip

Don't simply say:

> "We upgraded Kubernetes."

Mention:

```text
Old Version
     ↓
Compatibility Check
     ↓
Upgrade
     ↓
Validation
     ↓
Monitoring
```

Only mention a version you can confidently explain if the interviewer asks follow-up questions.

---

# 9. What Checks Do You Perform After the Upgrade to Ensure Everything Is Working Correctly?

### Answer

I divide the validation into several layers.

### Cluster

```bash
kubectl get nodes
kubectl cluster-info
```

### System Components

```bash
kubectl get pods -n kube-system
```

### Workloads

```bash
kubectl get pods -A
kubectl get deployments -A
kubectl get daemonsets -A
```

### Events

```bash
kubectl get events -A --sort-by=.lastTimestamp
```

### Networking

I verify:

* Services
* Ingress
* Load Balancers
* DNS
* Pod-to-Pod connectivity

### Application

I perform smoke testing on critical applications.

### Monitoring

I check:

* CPU
* Memory
* Pod restarts
* Error rates
* Application logs
* Prometheus metrics
* Grafana dashboards

The goal is not only to confirm that the cluster is `Ready`, but also to verify that **applications are actually functioning correctly**.

---

# 10. What Is the Difference Between Managed and Self-Managed Nodes?

### Answer

In Amazon EKS, worker nodes can be managed in different ways.

## Managed Nodes

AWS manages much of the node lifecycle through **EKS Managed Node Groups**.

AWS helps with:

* AMI updates
* Node provisioning
* Node lifecycle
* Rolling updates

However, we are still responsible for:

* Instance configuration
* Kubernetes workloads
* Scaling configuration
* Security configuration

## Self-Managed Nodes

With self-managed nodes, we have more control over the EC2 instances and are responsible for:

* Provisioning
* AMI updates
* Kubernetes configuration
* Node upgrades
* Scaling
* Patching
* Lifecycle management

### Simple Comparison

| Feature        | Managed Nodes | Self-Managed Nodes |
| -------------- | ------------- | ------------------ |
| Node lifecycle | AWS-assisted  | Customer managed   |
| Updates        | Easier        | Manual/custom      |
| Control        | Moderate      | High               |
| Maintenance    | Lower         | Higher             |
| Customization  | Moderate      | High               |

For most standard workloads, I prefer **managed node groups** because they reduce operational overhead.

---

# 11. What Is the CIDR Range of Your Subnet?

### Answer

CIDR defines the IP address range available in a network.

For example:

```text
VPC:
10.0.0.0/16

Private Subnet:
10.0.1.0/24
```

A `/24` subnet provides **256 total IPv4 addresses**, although AWS reserves 5 addresses in each subnet.

To check subnet information, I can use:

```bash
aws ec2 describe-subnets
```

Or check it directly from the AWS VPC console.

In an EKS environment, I generally use multiple subnets across different Availability Zones for high availability.

---

# 12. How Many Route Tables Can a Subnet Be Associated With?

### Answer

A subnet can be associated with **only one route table at a time**.

However, a single route table can be associated with **multiple subnets**.

For example:

```text
Route Table
     |
     ├── Subnet A
     ├── Subnet B
     └── Subnet C
```

Each subnet has one effective route table association.

If no explicit association exists, the subnet uses the VPC's **main route table**.

---

# 13. If You Need to Make Changes to a Route Table, Would You Modify the Existing One or Create a New One? Why?

### Answer

It depends on the requirement.

If the route change is required by all subnets associated with that route table, I can modify the existing route table.

But if only one group of subnets needs a different routing behavior, I prefer creating a **new route table** and associating it with those specific subnets.

For example:

```text
Existing Route Table
        |
   Multiple Subnets
```

If one subnet requires different routing:

```text
New Route Table
        |
   Specific Subnet
```

This provides better isolation and reduces the risk of unintentionally affecting other workloads.

Before making the change, I also check:

* Current routes
* Dependencies
* NAT Gateway
* Internet Gateway
* VPN/Transit Gateway
* Application connectivity

---

# 14. What Would You Do If the Existing Route Table Does Not Allow the Required Modification?

### Answer

First, I would identify **why the required modification cannot be made**.

I would check:

* Existing routes
* Route conflicts
* Subnet associations
* Gateway dependencies
* Network architecture
* Whether the change could affect other subnets

If the required routing behavior is different from the existing design, I would create a **new route table**, add the required routes, and associate it with the relevant subnet.

For example:

```text
Existing Route Table
       ↓
Used by Multiple Subnets
       ↓
Cannot safely change
       ↓
Create New Route Table
       ↓
Add Required Routes
       ↓
Associate Specific Subnet
       ↓
Test Connectivity
```

For production changes, I would follow the organization's change-management process and test connectivity before considering the change complete.

---

# 15. Have You Ever Created a Kubernetes Cluster? What Steps Would You Consider?

### Answer

Yes. I have worked with Kubernetes clusters, including Amazon EKS.

For an EKS cluster, I would consider the following:

### 1. Networking

First, I design:

* VPC
* CIDR
* Public subnets
* Private subnets
* Availability Zones
* Route tables
* NAT Gateway

### 2. IAM

I configure appropriate IAM roles and policies for:

* EKS
* Nodes
* Controllers
* Applications

### 3. Create EKS Cluster

I create the EKS control plane and configure cluster access.

### 4. Worker Nodes

I configure:

* Managed node groups or self-managed nodes
* Instance types
* Scaling
* Labels
* Taints

### 5. Add-ons

I configure required components such as:

* VPC CNI
* CoreDNS
* kube-proxy
* EBS CSI driver

### 6. Security

I configure:

* Security groups
* IAM
* Network policies where required
* Secrets management

### 7. Monitoring

I configure monitoring and logging using tools such as:

* CloudWatch
* Prometheus
* Grafana

### 8. Validation

Finally:

```bash
kubectl get nodes
kubectl get pods -A
kubectl get svc -A
```

Then I deploy a test application and verify networking and connectivity.

---

# 16. How Would You Configure Autoscaling Policies for a Kubernetes Cluster?

### Answer

Kubernetes autoscaling can happen at different levels.

## Horizontal Pod Autoscaler — HPA

HPA increases or decreases the number of Pod replicas based on metrics such as CPU or memory.

Example:

```text
CPU increases
     ↓
HPA detects threshold
     ↓
More Pod replicas
     ↓
Load distributed
```

Example:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

## Cluster Autoscaler

Cluster Autoscaler adjusts the number of worker nodes based on pending Pods and available capacity.

## Vertical Pod Autoscaler — VPA

VPA can adjust CPU and memory requests based on workload behavior.

In an EKS environment, I would choose the autoscaling approach based on workload requirements and also configure proper **resource requests and limits**.

---

# 17. What Are the Different Ways You Can Create a Kubernetes Cluster?

### Answer

There are several approaches.

### 1. Managed Kubernetes

For AWS:

```text
Amazon EKS
```

This is generally preferred for production AWS environments because AWS manages the Kubernetes control plane.

### 2. kubeadm

We can create a Kubernetes cluster manually using:

```bash
kubeadm init
```

and configure worker nodes using:

```bash
kubeadm join
```

### 3. Local Development Clusters

For development and testing:

* Minikube
* Kind
* Docker Desktop Kubernetes

### 4. Infrastructure Automation

We can also automate cluster creation using:

* Terraform
* eksctl
* CloudFormation

For production AWS environments, I would typically consider **EKS + Terraform** because it provides managed control-plane operations and Infrastructure as Code.

---

# 18. How Many Kubernetes Clusters Are You Currently Managing?

### Answer

A good practical answer is:

> I have worked with multiple Kubernetes environments, typically separated into **development, staging, and production**. Depending on the project, these may be separate EKS clusters. My responsibilities include workload deployments, node management, upgrades, troubleshooting, monitoring, and access management.

If the interviewer asks for an exact number, give the **actual number from your experience** rather than exaggerating.

For example:

> Currently, I manage three EKS clusters — development, staging, and production.

---

# 19. How Do You Handle Conflicts When Multiple People Are Trying to Modify Infrastructure Using Terraform?

### Answer

Terraform uses a **state file** to track infrastructure.

If multiple engineers run Terraform against the same infrastructure without state locking, they can overwrite each other's changes.

To prevent this, I use a **remote backend with state locking**.

For AWS, a common architecture is:

```text
Developer A ──┐
              │
Developer B ──┼──> Terraform
              │
Developer C ──┘
                  ↓
            Remote State
                  ↓
              State Lock
```

Historically, Terraform on AWS commonly used **S3 for remote state and DynamoDB for locking**. With newer Terraform versions, S3 backends also support native locking, so the exact configuration depends on the Terraform version and project setup.

I also follow a Git-based workflow:

```text
Developer
    ↓
Git Branch
    ↓
Pull Request
    ↓
Terraform Plan
    ↓
Code Review
    ↓
Approval
    ↓
Terraform Apply
```

This prevents multiple people from making uncontrolled changes directly against production infrastructure.

---

# 20. How Do You Secure a Terraform State File?

### Answer

Terraform state can contain sensitive information, so I never store it in a public Git repository.

For AWS, I prefer a secure remote backend, such as an **S3 bucket**, with appropriate access controls.

### Security Measures

#### 1. Encryption

Enable encryption for the state stored in S3.

```text
Terraform
    ↓
Encrypted S3 State
```

#### 2. IAM

Only authorized users and CI/CD roles should have access to the state bucket.

Use least-privilege IAM policies.

#### 3. State Locking

Use state locking to prevent concurrent modifications.

#### 4. Versioning

Enable S3 versioning so previous versions of the state can be recovered if required.

#### 5. Block Public Access

The S3 bucket should have public access blocked.

#### 6. Secrets

I avoid putting sensitive credentials directly into Terraform code or variables committed to Git.

Instead, I use services such as:

* AWS Secrets Manager
* AWS Systems Manager Parameter Store
* CI/CD secret stores

### Example Architecture

```text
Terraform
    |
    ↓
AWS S3
    |
    ├── Encryption
    ├── Versioning
    ├── IAM Access Control
    └── Public Access Block
```

The overall goal is to protect **confidentiality, integrity, and availability** of the Terraform state.

---

# 🎯 Quick Revision — 20 Questions

| #  | Topic                    | Key Concept                           |
| -- | ------------------------ | ------------------------------------- |
| 1  | Introduction             | 4 years AWS/DevOps experience         |
| 2  | Projects                 | AWS + Kubernetes + Terraform          |
| 3  | AWS Services             | EC2, VPC, IAM, EKS, S3, ALB           |
| 4  | Recent Project           | Containerized application on EKS      |
| 5  | Challenges               | Systematic troubleshooting            |
| 6  | Kubernetes Upgrade       | Compatibility + workloads + add-ons   |
| 7  | Post Upgrade             | Nodes, Pods, services, applications   |
| 8  | Kubernetes Version       | Explain actual upgrade experience     |
| 9  | Validation               | Cluster + application health          |
| 10 | Managed Nodes            | AWS-assisted lifecycle                |
| 11 | CIDR                     | IP address range                      |
| 12 | Route Tables             | One effective route table per subnet  |
|



# CI/CD, Jenkins, Docker & HR Interview Preparation

### Interview Answers for a DevOps Engineer with 4 Years of Experience

---

## 1. CI/CD & Jenkins

### Q1. Explain your current organisation’s Jenkins setup.

**Answer:**

In my current organisation, Jenkins is primarily used as the CI/CD automation platform. We use it to automate activities such as source-code checkout, compilation, unit testing, static-code analysis, Docker image creation, security scanning, and deployment.

Our typical flow looks like this:

```text
Developer
   |
   v
Git Repository
   |
   v
Jenkins Webhook
   |
   v
Jenkins Pipeline
   |
   +----> Checkout Code
   |
   +----> Build
   |
   +----> Unit Tests
   |
   +----> SonarQube / Code Quality
   |
   +----> Docker Build
   |
   +----> Security Scan
   |
   +----> Push Image to Registry
   |
   +----> Deploy
   |
   v
Dev / QA / Production
```

We generally use **Jenkins Pipeline as Code**, where the pipeline is maintained in a `Jenkinsfile` inside the source-code repository.

For example:

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        REGISTRY = "registry.example.com"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh './build.sh'
            }
        }

        stage('Test') {
            steps {
                sh './run-tests.sh'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-registry',
                        usernameVariable: 'USERNAME',
                        passwordVariable: 'PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$PASSWORD" | docker login \
                        -u "$USERNAME" \
                        --password-stdin $REGISTRY

                        docker push $REGISTRY/$IMAGE_NAME:$BUILD_NUMBER
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }
    }
}
```

### Jenkins architecture

A typical enterprise Jenkins setup can contain:

* Jenkins Controller
* Jenkins Agents
* Git/GitHub/GitLab/Bitbucket
* Docker
* Docker Registry
* SonarQube
* Kubernetes
* Cloud infrastructure
* Monitoring and notification systems

I prefer not to run heavy builds directly on the Jenkins controller. Instead, builds are executed on **dedicated agents**.

For example:

```text
                 Jenkins Controller
                        |
          +-------------+-------------+
          |             |             |
          v             v             v
      Linux Agent   Docker Agent   Kubernetes Agent
          |             |             |
       Build/Test    Docker Build   Deployment
```

### Jenkins job strategy

For multiple applications, I prefer using:

* Multibranch Pipelines
* Pipeline as Code
* Shared Libraries
* Separate credentials by environment
* Role-based access control
* Dedicated build agents
* Webhooks instead of continuous polling
* Manual approval before production deployment

This makes Jenkins easier to maintain and reduces duplication.

---

# Q2. How do you actually secure a Jenkins pipeline?

**Answer:**

I consider Jenkins security at multiple levels: authentication, authorization, secrets management, agent security, pipeline security, network security, and auditability.

### 1. Authentication

Jenkins should integrate with an enterprise identity provider such as:

* Active Directory
* LDAP
* SSO
* OAuth/OIDC

We avoid sharing Jenkins accounts.

Every user should have an individual identity.

---

### 2. Authorization / RBAC

I follow the principle of **least privilege**.

For example:

```text
Jenkins Admin
    |
    +-- Full administration

DevOps
    |
    +-- Create/modify pipelines
    +-- Manage builds

Developer
    |
    +-- Trigger builds
    +-- View logs

Read Only
    |
    +-- View jobs/builds only
```

Production deployment permissions should be restricted.

---

### 3. Secrets management

I never hardcode passwords, API keys, tokens, or cloud credentials in a `Jenkinsfile`.

Bad:

```groovy
environment {
    PASSWORD = "MyPassword123"
}
```

Instead, I store credentials in Jenkins Credentials Store or integrate Jenkins with an external secrets manager.

Example:

```groovy
withCredentials([
    usernamePassword(
        credentialsId: 'docker-creds',
        usernameVariable: 'USER',
        passwordVariable: 'PASS'
    )
]) {
    sh '''
        echo "$PASS" | docker login \
        -u "$USER" \
        --password-stdin
    '''
}
```

For sensitive environments, I prefer solutions such as:

* HashiCorp Vault
* AWS Secrets Manager
* Azure Key Vault
* Cloud-native workload identities

---

### 4. Secure the Jenkins agent

I avoid giving every build unrestricted access to the Jenkins controller.

Agents should be isolated where possible.

For example:

```text
Jenkins Controller
        |
        +------ Build Agent
                  |
                  +-- Docker
                  +-- Build Tools
                  +-- Limited Permissions
```

Ephemeral agents are even better because the environment can be destroyed after the build.

---

### 5. Plugin security

I install only required plugins and keep them updated.

I also regularly review:

* Plugin versions
* Security advisories
* Unused plugins
* Plugin dependencies

Unused plugins should be removed because every plugin increases the attack surface.

---

### 6. Pipeline security

I use:

* Protected branches
* Pull-request reviews
* Jenkinsfile stored in Git
* Approval gates
* Restricted production deployment
* Input validation
* Secret masking
* Dependency/image scanning

For production:

```text
Build
  |
  v
Test
  |
  v
Security Scan
  |
  v
Approval
  |
  v
Production Deployment
```

---

### 7. Network security

Jenkins should not be directly exposed to the public internet.

Typically:

```text
Internet
   |
Load Balancer / Reverse Proxy
   |
Firewall / Security Group
   |
Jenkins
```

I also use HTTPS/TLS and restrict network access to trusted users and systems.

---

# Q3. How do you manage credentials, plugins and RBAC?

## Credentials Management

I use Jenkins Credentials Store for credentials that Jenkins needs.

Credentials can include:

* Username/password
* SSH keys
* API tokens
* Secret text
* Certificates
* Cloud credentials

Each credential gets a meaningful ID.

Example:

```groovy
withCredentials([
    string(
        credentialsId: 'github-token',
        variable: 'GITHUB_TOKEN'
    )
]) {
    sh 'git clone https://$GITHUB_TOKEN@github.com/company/repo.git'
}
```

I make sure credentials are:

* Never committed to Git
* Never printed in logs
* Restricted to required jobs
* Rotated periodically
* Scoped appropriately

For larger environments, I prefer integrating Jenkins with an external secrets-management solution.

---

## Plugin Management

I follow a controlled plugin-management process.

### My approach:

1. Install only required plugins.
2. Check plugin compatibility.
3. Review Jenkins security advisories.
4. Test plugin upgrades.
5. Upgrade plugins during maintenance windows.
6. Remove unused plugins.
7. Keep Jenkins core and plugins reasonably current.

Examples of commonly required plugin categories include:

* Git integration
* Pipeline
* Credentials
* Docker
* Kubernetes
* Configuration as Code
* Authentication/RBAC

I avoid installing plugins just because they provide a convenient feature. Every plugin should have a business or technical reason.

---

## RBAC

I use role-based access control to give users only the permissions they require.

Example:

| Role          | Typical Permissions         |
| ------------- | --------------------------- |
| Jenkins Admin | Full administration         |
| DevOps        | Manage pipelines and agents |
| Developer     | Build and view jobs         |
| QA            | Trigger/view test jobs      |
| Read Only     | View jobs and logs          |

For production jobs, I would restrict who can:

* Modify the pipeline
* Deploy
* Approve deployment
* Manage credentials
* Manage Jenkins configuration

The key principle is **least privilege**.

---

# Q4. Scripted vs Declarative Pipeline — when and why would you choose each?

Both are Jenkins Pipeline approaches, but they differ mainly in structure and flexibility.

## Declarative Pipeline

Declarative Pipeline has a predefined structure.

Example:

```groovy
pipeline {

    agent any

    stages {

        stage('Build') {
            steps {
                sh './build.sh'
            }
        }

        stage('Test') {
            steps {
                sh './test.sh'
            }
        }

        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }
    }
}
```

### Advantages

* Easy to understand
* Standard structure
* Easier for teams to maintain
* Built-in validation
* Easier error handling
* Supports `post`, `when`, `environment`, `parameters`, etc.

For most application CI/CD pipelines, **I prefer Declarative Pipeline**.

---

## Scripted Pipeline

Scripted Pipeline is Groovy-based and provides more programming flexibility.

Example:

```groovy
node {

    def environments = ['dev', 'qa', 'prod']

    for (environment in environments) {

        stage("Deploy ${environment}") {

            if (environment == 'prod') {
                input "Deploy to production?"
            }

            sh "./deploy.sh ${environment}"
        }
    }
}
```

### Advantages

* More programming flexibility
* Complex loops and conditions
* Dynamic pipeline generation
* Useful for complicated workflows

### Disadvantages

* More difficult to maintain
* More Groovy knowledge required
* Easier to create complicated pipelines
* Less standardized

### My choice

I use:

```text
Simple/standard CI/CD
        |
        v
Declarative Pipeline
```

For highly dynamic or complex logic:

```text
Complex dynamic workflow
        |
        v
Scripted Pipeline
```

I generally prefer **Declarative Pipeline + Shared Libraries** rather than putting large amounts of Groovy logic directly into the Jenkinsfile.

---

# 2. Docker & Containerisation

# Q5. How do you create and structure a custom Dockerfile?

**Answer:**

A Dockerfile contains the instructions required to create a Docker image.

A basic structure is:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --only=production

COPY . .

EXPOSE 3000

USER node

CMD ["npm", "start"]
```

### Explanation

#### FROM

Defines the base image.

```dockerfile
FROM node:20-alpine
```

I prefer minimal trusted base images where practical.

---

#### WORKDIR

Sets the working directory.

```dockerfile
WORKDIR /app
```

---

#### COPY

Copies files into the image.

```dockerfile
COPY package*.json ./
COPY . .
```

---

#### RUN

Executes commands while building the image.

```dockerfile
RUN npm ci --only=production
```

The result becomes part of the image layer.

---

#### EXPOSE

Documents the port used by the application.

```dockerfile
EXPOSE 3000
```

It does not itself publish the port to the host.

---

#### USER

I prefer running applications as a non-root user.

```dockerfile
USER node
```

This reduces the impact of a container compromise.

---

#### CMD

Defines the default command.

```dockerfile
CMD ["npm", "start"]
```

---

## Multi-stage Dockerfile

For compiled applications, I prefer multi-stage builds.

Example:

```dockerfile
FROM golang:1.23-alpine AS builder

WORKDIR /src

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN go build -o app .

FROM alpine:3.20

WORKDIR /app

COPY --from=builder /src/app .

RUN adduser -D appuser
USER appuser

EXPOSE 8080

ENTRYPOINT ["./app"]
```

### Why multi-stage builds?

The build environment contains compilers and development dependencies.

The final image doesn't need them.

Therefore:

```text
Builder Image
  |
  | compile
  v
Application Binary
  |
  v
Small Runtime Image
```

Benefits:

* Smaller image
* Faster deployment
* Reduced attack surface
* Fewer unnecessary packages

---

## Dockerfile best practices

I follow these practices:

* Use small trusted base images.
* Pin important image versions.
* Use multi-stage builds.
* Run as non-root.
* Avoid storing secrets in images.
* Add `.dockerignore`.
* Minimize layers where appropriate.
* Scan images for vulnerabilities.
* Don't install unnecessary packages.
* Keep application and runtime dependencies separate.

Example `.dockerignore`:

```text
.git
.gitignore
node_modules
.env
Dockerfile
README.md
*.log
```

---

# Q6. What is the exact difference between CMD and ENTRYPOINT?

This is a common Docker interview question.

## CMD

`CMD` defines the **default command or default arguments** for a container.

Example:

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

If I run:

```bash
docker run myimage
```

Docker uses the CMD.

But if I run:

```bash
docker run myimage bash
```

the `bash` command replaces the CMD.

---

## ENTRYPOINT

`ENTRYPOINT` defines the main executable of the container.

Example:

```dockerfile
ENTRYPOINT ["python"]
```

If I run:

```bash
docker run myimage app.py
```

Docker effectively runs:

```bash
python app.py
```

The argument is appended to the entrypoint.

---

## CMD + ENTRYPOINT together

This is often the most useful pattern.

```dockerfile
ENTRYPOINT ["python"]

CMD ["app.py"]
```

Running:

```bash
docker run myimage
```

results in:

```bash
python app.py
```

Running:

```bash
docker run myimage test.py
```

results in:

```bash
python test.py
```

So:

```text
ENTRYPOINT = main executable
CMD        = default arguments
```

### Interview-friendly answer

> I use ENTRYPOINT when I want the container to behave like a specific executable, and CMD when I want to provide defaults that can easily be overridden. When used together, ENTRYPOINT provides the executable and CMD provides its default arguments.

---

# Q7. A Docker container is completely stuck. Walk me through your debugging process step by step.

**Answer:**

I would troubleshoot systematically rather than immediately restarting or deleting the container.

---

## Step 1 — Check container status

```bash
docker ps
```

If I don't see it:

```bash
docker ps -a
```

I check:

* Status
* Exit code
* Container name
* Image
* Port mappings

Example:

```text
CONTAINER ID   IMAGE       STATUS
123abc         myapp       Up 10 minutes
```

---

## Step 2 — Check logs

My first step for an application issue is usually:

```bash
docker logs <container>
```

For recent logs:

```bash
docker logs --tail 100 <container>
```

Follow logs:

```bash
docker logs -f <container>
```

I look for:

* Application exceptions
* Connection failures
* Authentication failures
* Configuration errors
* Out-of-memory errors
* Dependency failures

---

## Step 3 — Inspect the container

```bash
docker inspect <container>
```

I check:

* Environment variables
* Mounts
* Network configuration
* Entrypoint
* CMD
* Restart policy
* Health status
* Resource configuration

For example:

```bash
docker inspect <container> | grep -i health
```

---

## Step 4 — Check whether the container is actually running

```bash
docker exec -it <container> sh
```

If Bash exists:

```bash
docker exec -it <container> bash
```

Then I inspect:

```bash
ps
```

or:

```bash
ps aux
```

I check whether the expected application process exists.

---

## Step 5 — Check CPU and memory

```bash
docker stats
```

I look for:

```text
CPU %
MEM USAGE
MEM %
NET I/O
BLOCK I/O
```

If CPU is extremely high, the application may be stuck in a loop.

If memory is exhausted, it could be an OOM issue.

---

## Step 6 — Check container health

If the image has a health check:

```bash
docker inspect --format='{{json .State.Health}}' <container>
```

I check whether the container is:

```text
healthy
unhealthy
starting
```

An unhealthy container does not necessarily mean the main process has crashed. It can mean the health-check command is failing.

---

## Step 7 — Check networking

I inspect:

```bash
docker network ls
```

Then:

```bash
docker network inspect <network>
```

From inside the container, I test connectivity:

```bash
ping <host>
```

If available:

```bash
curl http://service:8080
```

I also check DNS:

```bash
nslookup service
```

or:

```bash
getent hosts service
```

Typical issues include:

* Wrong network
* Incorrect service name
* DNS failure
* Firewall
* Incorrect port
* Application listening only on localhost

---

## Step 8 — Check port mappings

```bash
docker port <container>
```

For example:

```text
8080/tcp -> 0.0.0.0:8080
```

I verify that the application is actually listening:

```bash
ss -lntp
```

Inside the container, I check whether the application is listening on the expected port.

---

## Step 9 — Check environment/configuration

I inspect:

```bash
docker inspect <container>
```

and compare configuration with the expected values.

Common problems:

```text
Wrong environment variable
Wrong database URL
Wrong API endpoint
Missing secret
Wrong configuration file
```

I never expose sensitive credentials while debugging or paste them into logs.

---

## Step 10 — Check filesystem and mounts

```bash
docker inspect <container>
```

I check mounted volumes.

Then:

```bash
df -h
```

and:

```bash
df -i
```

A full filesystem or inode exhaustion can cause unexpected application behavior.

---

## Step 11 — Check Docker daemon/system logs

On Linux:

```bash
journalctl -u docker
```

I check for:

* Docker daemon errors
* Storage problems
* Network problems
* Container runtime issues

---

## Step 12 — Check whether the process is unresponsive

If the application is completely hung, I determine whether it is:

* CPU-bound
* Memory-bound
* Waiting on I/O
* Waiting on a network connection
* Deadlocked
* Blocked by an external dependency

I don't immediately restart it because restarting can remove useful evidence.

---

## Step 13 — Check image/configuration changes

I compare the currently running image with the previous working version.

For example:

```bash
docker image ls
```

I check:

* Image tag
* Image digest
* Dockerfile changes
* Application version
* Configuration changes

If the issue started immediately after a deployment, I compare the current release with the last known-good release.

---

## Step 14 — Restart only after collecting evidence

If I have enough information:

```bash
docker restart <container>
```

If necessary:

```bash
docker stop <container>
docker start <container>
```

For production systems, I follow the established incident/change-management process rather than manually restarting containers without understanding the impact.

---

## My overall Docker debugging flow

```text
Container Issue
      |
      v
docker ps -a
      |
      v
docker logs
      |
      v
docker inspect
      |
      +------> Health Check
      |
      +------> CPU / Memory
      |
      +------> Network
      |
      +------> Ports
      |
      +------> Environment
      |
      +------> Volumes / Filesystem
      |
      +------> Docker Daemon
      |
      v
Identify Root Cause
      |
      v
Fix
      |
      v
Validate
      |
      v
Monitor
```

### Strong interview closing statement

> My approach is to first collect evidence using `docker ps`, `docker logs`, and `docker inspect`. Then I check resources, health checks, networking, ports, environment variables, volumes, and Docker daemon logs. Once I identify the root cause, I fix and validate it rather than simply restarting the container.

---

# 3. HR & Cultural Fit

# Q8. Why are you looking for a job change?

### Recommended answer

> I have gained good experience in my current organisation, especially in DevOps, CI/CD automation, Jenkins, Docker, cloud infrastructure and deployment processes. After around four years of experience, I am looking for an opportunity where I can take on more challenging responsibilities, work on larger-scale infrastructure and automation, and continue improving my technical skills.
>
> I am not looking to move because of any negative reason. My main motivation is career growth, learning, and getting exposure to more complex DevOps and cloud environments.
>
> I believe this opportunity aligns well with the direction I want to take in my career.

### Short version

> I am looking for a change primarily for career growth and better technical exposure. I have built a strong foundation in DevOps and CI/CD over the last few years, and now I want to work on larger-scale systems, automation, cloud technologies and more challenging projects.

### Avoid saying

Don't say:

* "My manager is bad."
* "I hate my company."
* "I want more money" as the only reason.
* "There is too much workload."
* "I don't like my team."

Even if compensation is part of your motivation, position the change primarily around **growth, responsibility, learning and technical exposure**.

---

# Q9. Are you comfortable with the location and working from office (WFO)?

### If you are comfortable

> Yes, I am comfortable with the location and working from office. I understand that collaboration is important, especially for DevOps and infrastructure teams, where we may need to coordinate closely with developers, QA, security and operations teams. I am comfortable following the organisation's working model and office requirements.

### If you need some flexibility

> Yes, I am comfortable with the location and I am open to working from office. I understand the importance of collaboration and team interaction. If there is flexibility around hybrid working, I would appreciate it, but I am comfortable with the organisation's WFO requirements.

### If relocation is required

> Yes, I am open to relocation and comfortable with working from office. I would just like to understand the expected joining timeline and office location so that I can plan the relocation accordingly.

---

# 4. Quick Interview Revision

## Jenkins

### What is Jenkins?

> Jenkins is an automation server primarily used to implement CI/CD pipelines. It can automate building, testing, security scanning, packaging and deployment.

### Why Jenkinsfile?

> Jenkinsfile provides Pipeline as Code. It keeps the CI/CD definition in source control, allowing versioning, code review and reproducibility.

### Why use agents?

> Agents execute builds and keep heavy workloads away from the Jenkins controller.

### How do you secure Jenkins?

> I use SSO/LDAP, RBAC, least privilege, credentials management, HTTPS, restricted agents, plugin security, protected repositories, secret masking and controlled production access.

---

# Docker

### What is an image?

> A Docker image is an immutable package containing the application, runtime, libraries and required filesystem content.

### What is a container?

> A container is a running instance of an image with isolated processes, networking and filesystem layers.

### CMD vs ENTRYPOINT

```text
ENTRYPOINT → Main executable
CMD        → Default command/arguments
```

### Why multi-stage builds?

> To separate the build environment from the runtime environment and produce smaller, cleaner and more secure production images.

### Why non-root containers?

> Running as a non-root user reduces the potential impact if the application or container is compromised.

---

# 5. Sample End-to-End CI/CD Answer

If the interviewer asks:

### "Explain your complete CI/CD process."

A strong 4-year-experience answer would be:

> In my current setup, developers push code to Git, and a webhook triggers Jenkins. Jenkins checks out the code and starts the pipeline defined in the Jenkinsfile.
>
> First, we compile or build the application and execute unit tests. Then we perform code-quality checks and security scanning. Once the application passes the required checks, we build a Docker image and tag it using a version or build number.
>
> The image is then scanned for vulnerabilities and pushed to our container registry. After that, Jenkins deploys the image to the appropriate environment, such as development or QA.
>
> For production, we generally have additional controls such as approval gates and restricted deployment permissions.
>
> Jenkins credentials are stored securely rather than hardcoded in the pipeline. Access is controlled through RBAC, and production credentials are restricted to the jobs and users that require them.
>
> After deployment, we validate the application using health checks, logs and monitoring. If there is a deployment issue, we investigate the logs, container status, resource usage, networking and application configuration, and if required we roll back to the last known-good version.

---

# 6. Senior-Level Points to Add During the Interview

For a **4-year DevOps profile**, don't stop at definitions. Try to explain **why** you use a particular approach.

Good phrases to use naturally:

* "I follow the principle of least privilege."
* "We maintain pipelines as code."
* "I prefer immutable artifacts."
* "I avoid hardcoding secrets."
* "I use multi-stage Docker builds."
* "I prefer running containers as non-root."
* "Production deployments have additional approval controls."
* "I investigate the root cause before restarting a production workload."
* "We try to keep the Jenkins controller dedicated to orchestration rather than heavy build workloads."
* "I prefer reusable Jenkins Shared Libraries for common pipeline logic."
* "I use vulnerability scanning as part of the CI/CD process."
* "I prefer ephemeral build agents where practical."
* "I separate build, test, security validation and deployment stages."

---

# 7. One-Minute Introduction Connecting All These Topics

> I have around four years of experience in DevOps and CI/CD, with hands-on experience in Jenkins, Docker, Git-based workflows and deployment automation.
>
> My experience includes creating and maintaining Jenkins pipelines, implementing Pipeline as Code using Jenkinsfiles, managing credentials and access controls, and automating application build and deployment processes.
>
> On the containerisation side, I have worked with Dockerfiles, Docker image creation, multi-stage builds, container troubleshooting, networking, logs and resource analysis.
>
> From a security perspective, I follow practices such as least-privilege access, secure credential management, avoiding secrets in source code, controlled production deployments and vulnerability scanning.
>
> At this stage of my career, I am looking for an opportunity where I can work on more challenging DevOps and cloud environments, improve my automation skills and contribute to building reliable and secure CI/CD platforms.


# Infosys DevOps Interview Questions & Answers – 4 Years Experience

## 🐳 Docker

### 1. A Docker container keeps restarting. How would you troubleshoot the issue?

If a Docker container keeps restarting, I would first check the container status and restart count using `docker ps -a`. Then I would check the container logs using `docker logs <container-id>` to identify application errors, configuration issues, missing environment variables, or dependency failures. I would also inspect the container using `docker inspect <container-id>` to verify the entrypoint, command, environment variables, mounts, networking, and restart policy. If the application exits immediately, I would check whether the CMD or ENTRYPOINT is correct and whether the application process is running in the foreground. I would also check resource-related issues such as memory limits using `docker stats`. If the container is part of Docker Compose, I would check the dependent services and configuration in `docker-compose.yml`. Based on the root cause, I would fix the configuration or application issue, rebuild the image, and redeploy it.

### 2. What is the difference between `CMD` and `ENTRYPOINT`? When would you use each?

Both `CMD` and `ENTRYPOINT` define what runs when a Docker container starts, but they behave differently. `CMD` provides the default command or default arguments and can easily be overridden when running the container. `ENTRYPOINT` defines the main executable of the container and is generally used when the container should always execute a specific application. For example, for a Python application I can use `ENTRYPOINT ["python"]` and `CMD ["app.py"]`, where the ENTRYPOINT remains fixed and CMD provides the default argument. In real projects, I prefer ENTRYPOINT when the container represents a specific executable and CMD when I want to provide configurable default arguments.

### 3. Your Docker image is 2GB and takes a long time to build and deploy. How would you reduce its size?

I would first inspect the image layers using `docker history` and identify which layers are consuming most of the space. Then I would use a smaller base image such as Alpine or Slim where it is compatible with the application. I would remove unnecessary packages, cache files, temporary files, and development dependencies from the final image. I would also use a multi-stage Docker build where compilation or dependency installation happens in a builder image and only the required application artifacts are copied into the final lightweight image. I would optimize the Dockerfile ordering so that frequently changing application code is copied later, which improves Docker layer caching. I would also create an appropriate `.dockerignore` file to prevent files such as `.git`, logs, node_modules, test files, and local configuration from being copied into the build context. This reduces image size, build time, deployment time, and storage consumption.

---

# ☸️ Kubernetes

### 4. A pod is stuck in `CrashLoopBackOff`. What steps would you take to identify the root cause?

When a pod is in `CrashLoopBackOff`, Kubernetes is repeatedly starting the container and the container is exiting or failing. I would first run `kubectl get pods -n <namespace>` and then `kubectl describe pod <pod-name> -n <namespace>` to check events, restart counts, probes, scheduling, and container state. Next, I would check the application logs using `kubectl logs <pod-name> -n <namespace>` and, if the container has restarted, I would use `kubectl logs <pod-name> -n <namespace> --previous` to see logs from the previous failed instance. I would verify environment variables, ConfigMaps, Secrets, mounted volumes, image versions, and application configuration. I would also check whether liveness or readiness probes are incorrectly configured. If the container is being killed because of memory exhaustion, I would check resource limits and Kubernetes events for OOMKilled. After identifying the root cause, I would fix the application or Kubernetes configuration, deploy the corrected version, and monitor the pod until it becomes stable.

### 5. What happens when a Kubernetes pod is OOMKilled?

`OOMKilled` means the container exceeded the memory limit assigned to it and the Linux kernel or Kubernetes runtime terminated the container because of memory exhaustion. I would confirm this using `kubectl describe pod <pod-name>` and check the container's last state and reason. I would then compare the application's actual memory consumption with the configured requests and limits. If the application genuinely requires more memory, I would increase the memory limit and request appropriately. However, I would not simply keep increasing memory without investigation. I would check for memory leaks, inefficient application behavior, excessive concurrency, or large workloads. I would also review historical memory metrics in Prometheus, Grafana, or CloudWatch to understand whether the issue is a sudden spike or a continuous increase. After making the required change, I would monitor the pod and ensure it remains stable.

### 6. How would you troubleshoot a service that is reachable inside the cluster but not from outside?

I would troubleshoot this layer by layer. First, I would verify that the pods are healthy using `kubectl get pods` and confirm that the Kubernetes Service has the correct selector and endpoints using `kubectl get svc` and `kubectl get endpoints`. Then I would test connectivity from another pod inside the cluster using `curl` or `wget` to confirm that the application is actually responding. If internal connectivity works, I would check the Service type such as `ClusterIP`, `NodePort`, or `LoadBalancer`. For external access through an Ingress, I would check the Ingress resource, Ingress Controller, listener configuration, hostname, TLS configuration, and routing rules. On AWS EKS, I would also check the AWS Load Balancer, target health, security groups, subnet configuration, and network ACLs. I would review DNS resolution and verify that the application port exposed by the Service matches the target container port. This helps identify whether the problem is with Kubernetes routing, the Ingress/load balancer, DNS, or AWS networking.

### 7. What is the difference between Deployment, StatefulSet, and DaemonSet? Give a real-world use case for each.

A Deployment is mainly used for stateless applications where pods are interchangeable. It provides replica management, rolling updates, and rollback capabilities, so I would use it for applications such as REST APIs, frontend applications, or microservices. A StatefulSet is used when applications require stable identities, persistent storage, and ordered deployment or termination. A common example is databases such as PostgreSQL, MySQL, or distributed systems such as Kafka, depending on the architecture. A DaemonSet ensures that a pod runs on every eligible node or on a selected set of nodes. I would use a DaemonSet for node-level agents such as Fluent Bit for log collection, Prometheus Node Exporter for monitoring, or security agents. In short, Deployment is generally for stateless workloads, StatefulSet for stateful workloads, and DaemonSet for node-level services.

---

# ☁️ AWS

### 8. An EC2 instance is showing high CPU utilization in production. How would you investigate it?

I would first check CloudWatch metrics to determine when the CPU increase started and whether it is a continuous spike or a temporary event. Then I would connect to the EC2 instance and use commands such as `top`, `htop`, `ps aux --sort=-%cpu`, and `uptime` to identify which process is consuming CPU. I would also check memory, disk I/O, and load average because high CPU may sometimes be related to another resource bottleneck. I would review application logs and recent deployments to determine whether there was a code change or unusual workload. I would also check traffic metrics from the ALB or application layer to see whether the instance is receiving abnormal traffic. If the CPU increase is caused by legitimate traffic, I would consider scaling horizontally using an Auto Scaling Group or vertically by moving to a larger instance type. If it is caused by a runaway process or application issue, I would address the root cause and avoid simply restarting the server without investigation.

### 9. How would you design a highly available application across multiple AWS Availability Zones?

I would design the application using multiple Availability Zones so that failure of one AZ does not make the application unavailable. I would place application instances or containers across multiple private subnets in different AZs and use an Application Load Balancer to distribute traffic across healthy targets. I would use an Auto Scaling Group to automatically maintain the required number of instances and replace unhealthy instances. For the database layer, I would use a highly available solution such as Amazon RDS Multi-AZ, depending on the application requirements. Static assets can be stored in S3 and distributed using CloudFront. I would also design redundant networking components, configure appropriate security groups and route tables, and ensure health checks are properly configured. Monitoring and alerting through CloudWatch would help detect failures quickly. This architecture ensures that the application can continue serving traffic even if one Availability Zone becomes unavailable.

### 10. An application suddenly starts receiving 10x normal traffic. How would you handle it in AWS?

First, I would determine whether the traffic is legitimate or potentially malicious by checking ALB, CloudFront, WAF, and application metrics. I would verify CPU, memory, request count, latency, error rate, and backend capacity. If the traffic is legitimate, I would rely on horizontal scaling through an Auto Scaling Group or Kubernetes HPA if the application runs on EKS. I would use an Application Load Balancer to distribute traffic and CloudFront caching where applicable to reduce load on backend services. AWS WAF can help block malicious or abnormal traffic using rules and rate-based controls. I would also check database connection limits and scaling because increasing application instances can sometimes overload the database. During the incident, I would closely monitor the system and communicate the impact to stakeholders. After stabilization, I would review the incident and improve autoscaling policies, caching, capacity planning, and alerting.

---

# 🔄 CI/CD

### 11. Your production deployment succeeded, but the application is returning 500 errors. What would you check first?

I would first confirm whether the deployment itself is healthy or whether the application is failing after deployment. I would check the load balancer target health, Kubernetes pod status if running on EKS, and application logs. I would compare the deployed version with the previous working version and check whether there were any configuration, environment variable, Secret, ConfigMap, database migration, or dependency changes. I would also verify connectivity to dependent services such as databases, APIs, queues, and caches. If the issue started immediately after the deployment and the previous version was working, I would consider rolling back to the last known good version to restore service quickly. After service restoration, I would investigate the exact root cause using logs, metrics, traces, and deployment changes before redeploying the corrected version.

### 12. How would you implement a zero-downtime deployment?

I would implement zero-downtime deployment using strategies such as rolling deployment, blue-green deployment, or canary deployment depending on the application requirements. In Kubernetes, I would use a Deployment with multiple replicas and configure appropriate readiness and liveness probes. During a rolling update, Kubernetes gradually starts new pods and terminates old pods while ensuring that the required number of healthy replicas remain available. I would also configure `maxUnavailable` and `maxSurge` appropriately. For critical applications, I could use blue-green deployment where the new version is deployed separately and traffic is switched only after validation. Canary deployment can be used to expose the new version to a small percentage of users before gradually increasing traffic. Proper health checks, monitoring, rollback mechanisms, and backward-compatible database changes are important for achieving reliable zero-downtime deployments.

### 13. How do you manage secrets and credentials in a CI/CD pipeline?

I never hardcode passwords, API keys, database credentials, or cloud access keys directly into source code or pipeline YAML files. I prefer using a dedicated secret-management solution such as AWS Secrets Manager, AWS Systems Manager Parameter Store, HashiCorp Vault, or the secret-management feature provided by the CI/CD platform. The pipeline should retrieve secrets only when required and pass them securely to the application or deployment process. Access should follow the principle of least privilege using IAM roles or service accounts rather than long-lived credentials wherever possible. Secrets should also be masked in pipeline logs and should never be printed for debugging. I would rotate credentials periodically and audit who or what is accessing them. For Kubernetes deployments, I would integrate a secret-management solution with Kubernetes rather than storing sensitive values directly in Git repositories.

---

# 🐧 Linux & Troubleshooting

### 14. What is the difference between CPU utilization and load average?

CPU utilization shows how much of the available CPU capacity is currently being used, whereas load average represents the average number of tasks that are either running or waiting for CPU or uninterruptible I/O over a specific period. In Linux, load average is commonly shown for 1, 5, and 15 minutes using the `uptime` or `top` command. For example, a server with 4 CPU cores and a load average around 4 indicates that the system is approximately fully utilized from a scheduling perspective. A load average significantly higher than the number of CPU cores may indicate CPU contention or tasks waiting on I/O. Therefore, I would not look at CPU percentage alone; I would compare CPU utilization, load average, memory, disk I/O, and process-level metrics to understand the actual bottleneck.

### 15. A Linux server has sufficient CPU and memory, but the application is slow. How would you troubleshoot it?

If CPU and memory are normal but the application is slow, I would investigate other possible bottlenecks such as disk I/O, network latency, database performance, connection pools, locks, DNS resolution, or downstream services. I would first check `top`, `iostat`, `vmstat`, `sar`, `ss`, and application logs. I would check disk latency and utilization to determine whether the application is waiting on storage. Then I would verify network connectivity and latency using commands such as `ping`, `curl`, `traceroute`, or `ss` where appropriate. If the application depends on a database, I would check database CPU, slow queries, connection counts, locks, and query latency. I would also check recent application deployments and configuration changes. For a production application, I would correlate logs, metrics, and distributed traces to identify exactly where the request is spending time.

### 16. Disk usage suddenly reaches 100% on a production server. What commands would you use to identify the problem?

I would first run `df -h` to identify which filesystem is full. Then I would use `du -sh` on relevant directories to find which directory is consuming the most space. Commands such as `du -ah /var | sort -rh | head` can help identify large files and directories. I would check `/var/log`, application logs, temporary files, Docker storage, and old backup files. If deleted files are still being held open by a process, I would use `lsof +L1` to identify them. I would also check inode exhaustion using `df -i`, because a filesystem can show available disk space but still fail due to exhausted inodes. In a production environment, I would first identify the cause before deleting files and would follow the organization's log-retention and cleanup policies. After freeing space safely, I would implement preventive measures such as log rotation, monitoring, and appropriate disk-sizing or automated cleanup.

---

# 🚨 DevOps / Production Scenario

### 17. A production issue occurs at 2 AM and multiple services are affected. How would you approach troubleshooting, communication, rollback, and root-cause analysis?

During a major production incident, my first priority would be restoring service rather than immediately trying to find the complete root cause. I would acknowledge the incident, identify the impacted services and users, and check monitoring dashboards, alerts, application logs, Kubernetes events, load balancer metrics, infrastructure metrics, and recent deployments. I would establish whether the issue is related to an application release, infrastructure, database, networking, external dependency, or traffic spike. If a recent deployment is strongly correlated with the incident and rollback is safe, I would roll back to the last known good version to reduce customer impact. At the same time, I would communicate the incident status, impact, actions being taken, and expected next update to the relevant stakeholders.

Once the service is stabilized, I would continue the investigation to identify the root cause using logs, metrics, traces, deployment history, configuration changes, and infrastructure events. I would document the timeline from detection through mitigation and recovery. Finally, I would prepare an RCA containing the root cause, customer impact, timeline, immediate resolution, contributing factors, and preventive actions. Preventive measures could include better monitoring and alerting, improved health checks, automated rollback, stronger deployment validation, capacity planning, testing, and changes to the CI/CD process. My approach would be **stabilize first, communicate clearly, investigate systematically, then prevent recurrence**.



# Infosys DevOps Interview Experience – Round 1

I’ve been receiving many messages asking about the questions from my Infosys DevOps interview, so sharing the complete list here. Hope this helps everyone preparing for similar roles. 🙌

---

## 1. Tell me about your current role and DevOps experience.

### Answer

I am currently working as a DevOps Engineer with around 3.5+ years of experience, primarily working on AWS, Docker, Kubernetes, Jenkins, Terraform, Git, Maven, and Linux. My responsibilities include managing CI/CD pipelines, containerizing applications using Docker, deploying and managing workloads on Kubernetes/EKS, provisioning infrastructure using Terraform, troubleshooting production issues, monitoring applications and infrastructure, and implementing automation using Shell and Python. I work closely with development and other infrastructure teams to improve deployment reliability, reduce manual effort, and resolve application and infrastructure issues. I have also worked with tools such as SonarQube and container security scanning as part of the DevSecOps process.

---

## 2. Is your DevOps experience primarily on AWS or other cloud platforms?

### Answer

My primary cloud experience is with AWS. I have worked with services such as EC2, VPC, EKS, EBS, EFS, S3, IAM, RDS, CloudWatch, and load balancing services. Most of my container orchestration experience is around Kubernetes and Amazon EKS, where I have worked on deployments, Services, Ingress, scaling, troubleshooting, and application connectivity. I also use Terraform to provision and manage AWS infrastructure. My focus has mainly been on AWS-based DevOps rather than working across multiple cloud platforms.

---

## 3. Have you worked with on-premises infrastructure / VMware vSphere?

### Answer

My primary hands-on experience is with AWS cloud infrastructure rather than VMware vSphere. I understand the basic concepts of on-premises infrastructure, virtualization, and VMware, but most of my production responsibilities have been around AWS. In AWS, I have worked with EC2, VPC networking, IAM, storage, EKS, load balancers, monitoring, and infrastructure provisioning through Terraform. If I encounter an on-premises or VMware-based environment, I would approach it by understanding the existing architecture, networking, compute, storage, virtualization layer, and automation requirements before making changes.

---

## 4. Did you create CI/CD pipelines from scratch or work on existing pipelines?

### Answer

I have experience working with both existing pipelines and creating or modifying CI/CD pipelines based on application requirements. A typical pipeline starts when developers push code to Git, which triggers Jenkins through a webhook. Jenkins checks out the code, performs the build using Maven, runs code-quality checks such as SonarQube, builds the Docker image, scans it for vulnerabilities, and pushes the image to Amazon ECR. The deployment stage then updates the Kubernetes workload using the required deployment mechanism. When troubleshooting an existing pipeline, I first understand each stage, its inputs and outputs, credentials, environment variables, agents, tools, and dependencies before modifying anything.

---

## 5. Explain the CI/CD pipeline you created for CSV validation and S3 upload.

### Answer

For a CSV validation and S3 upload pipeline, the process can start when a CSV file is provided or committed to the configured repository or source location. Jenkins triggers the pipeline and first validates the file format, required columns, naming conventions, data types, and any application-specific validation rules. If the validation succeeds, the pipeline authenticates securely with AWS using an IAM role or securely managed credentials and uploads the validated file to the appropriate S3 bucket and path. If validation fails, the pipeline stops and reports the reason instead of uploading invalid data. I would also maintain separate configuration for different environments and ensure that the pipeline does not expose AWS credentials or sensitive information in the Jenkins logs.

---

## 6. If an L3 team created the pipeline, how do you understand and troubleshoot it?

### Answer

If an L3 team created the pipeline, I would first understand the Jenkinsfile or pipeline configuration and identify every stage and its purpose. I would check the source repository, build tools, environment variables, credentials, agents, plugins, scripts, artifacts, and deployment steps. When a failure occurs, I would identify the exact stage where it failed and inspect the console output and relevant application or infrastructure logs. I would compare the current execution with a previously successful build to identify changes in code, dependencies, credentials, configuration, or infrastructure. I would avoid making random changes and instead isolate the failing component, reproduce the issue where possible, apply the smallest required fix, and document the root cause.

---

## 7. Have you written a Dockerfile?

### Answer

Yes, I have written Dockerfiles for containerizing applications. A typical Dockerfile starts with an appropriate base image, sets the working directory, copies the required application files, installs dependencies, builds the application if required, exposes the required port, and defines the application startup command. I prefer using multi-stage builds where applicable so that build-time dependencies are not included in the final production image. I also use `.dockerignore` to prevent unnecessary files such as `.git`, logs, local dependencies, and temporary files from being copied into the image. This helps reduce image size, build time, and the overall attack surface.

---

## 8. What is the difference between CMD and ENTRYPOINT in Docker?

### Answer

`ENTRYPOINT` defines the main executable or command that the container is intended to run, while `CMD` generally provides default arguments or a default command that can be overridden when the container starts. For example, if I use `ENTRYPOINT ["java", "-jar"]` and `CMD ["app.jar"]`, the container starts the Java application using the JAR specified by CMD. ENTRYPOINT is useful when I want the container to behave like a specific executable, while CMD is useful for providing defaults that can easily be overridden. In production Dockerfiles, I choose between them based on whether the startup executable should remain fixed or be easily replaceable.

---

## 9. What Kubernetes operations have you performed apart from deployments?

### Answer

Apart from deployments, I have worked with Pods, ReplicaSets, Services, ConfigMaps, Secrets, Ingress, StatefulSets, Persistent Volumes, Persistent Volume Claims, probes, resource requests and limits, scaling, and troubleshooting. I have also worked with Kubernetes scheduling concepts such as node selectors, node affinity, taints and tolerations. On EKS, I have worked with cluster and node-group-related operations, application connectivity, load balancer integration, and troubleshooting issues such as Pending Pods, CrashLoopBackOff, ImagePullBackOff, Service connectivity problems, and unhealthy workloads. I also use Kubernetes monitoring and logs to investigate production issues.

---

## 10. Have you troubleshooted CrashLoopBackOff? How?

### Answer

Yes. When a Pod enters `CrashLoopBackOff`, I first check the Pod status and restart count using `kubectl get pods`. Then I use `kubectl describe pod` to inspect events, container state, exit codes, probes, mounts, and scheduling information. I check the current and previous container logs using `kubectl logs` and `kubectl logs --previous` because the container may have already restarted. I also verify environment variables, ConfigMaps, Secrets, image versions, resource limits, liveness and readiness probes, application dependencies, and database connectivity. If the container is being OOMKilled, I investigate memory usage and resource limits. If the issue started after a deployment, I compare the new version with the previous stable version and roll back if necessary. My goal is to identify the actual reason for the restart instead of treating CrashLoopBackOff itself as the root cause.

---

## 11. Explain CrashLoopBackOff in simple terms.

### Answer

CrashLoopBackOff means that a container starts, crashes, and Kubernetes repeatedly tries to restart it, but because it keeps failing, Kubernetes gradually increases the delay between restart attempts. The `BackOff` portion indicates that Kubernetes is backing off before trying again. The actual root cause can be anything from an application startup failure, incorrect configuration, missing environment variables, failed dependency connection, incorrect command, failed health probe, insufficient resources, or permission issues. Therefore, CrashLoopBackOff is an indication of repeated container failure rather than a specific application error. I normally use `kubectl describe pod` and `kubectl logs --previous` to determine the underlying reason.

---

## 12. Explain PV and PVC in Kubernetes.

### Answer

A Persistent Volume, or PV, represents storage available to the Kubernetes cluster, while a Persistent Volume Claim, or PVC, is a request from an application for storage with specific requirements such as capacity and access mode. The application normally uses the PVC rather than directly interacting with the PV. Kubernetes then binds the PVC to a suitable PV. In a cloud environment such as AWS EKS, persistent storage can be provided through services such as EBS or EFS depending on the access requirements. PVs and PVCs are important for workloads where data must survive Pod restarts or Pod recreation, such as databases and stateful applications.

---

## 13. How do you handle autoscaling in Kubernetes?

### Answer

For application-level scaling, I commonly use the Horizontal Pod Autoscaler, or HPA, which adjusts the number of Pod replicas based on metrics such as CPU or memory utilization and, where configured, custom or external metrics. For example, if traffic increases and the application consumes more CPU, HPA can increase the number of Pods. If the cluster does not have enough capacity to schedule those Pods, the Cluster Autoscaler or an equivalent node autoscaling mechanism can add worker nodes. Therefore, in a production EKS environment, I generally consider both Pod-level and node-level scaling. I also configure appropriate resource requests and limits because HPA decisions depend on resource metrics and poorly configured resources can result in incorrect scaling behavior.

---

## 14. How do you achieve URL/path-based routing in Kubernetes?

### Answer

URL or path-based routing can be implemented using Kubernetes Ingress together with an Ingress Controller. The Ingress resource defines rules that map hostnames or URL paths to Kubernetes Services. For example, `/users` can route to the user service, `/orders` can route to the order service, and `/payments` can route to the payment service. In AWS EKS, an AWS Load Balancer Controller can be used to integrate Kubernetes Ingress resources with AWS load-balancing infrastructure. This provides a centralized entry point, reduces the need for a separate external load balancer for every microservice, and allows features such as TLS termination and host-based or path-based routing.

---

## 15. Are you familiar with middleware technologies like Tomcat, WebLogic, or WebSphere?

### Answer

I have an understanding of middleware technologies and their role in hosting enterprise applications. Tomcat, for example, is commonly used as a Java servlet container for Java web applications, while WebLogic and WebSphere are enterprise application servers with additional capabilities for large-scale Java applications. From a DevOps perspective, I focus on how these applications are built, packaged, configured, deployed, monitored, and troubleshooted. In a containerized environment, applications traditionally hosted on middleware servers can also be packaged into Docker images and deployed on Kubernetes, depending on application architecture and compatibility.

---

## 16. How comfortable are you with Linux? What activities do you perform?

### Answer

I am comfortable working with Linux as part of my day-to-day DevOps activities. I use Linux for application troubleshooting, log analysis, process management, file and directory operations, permissions, networking, package management, service management, and shell scripting. Common commands I use include `ps`, `top`, `df`, `du`, `free`, `netstat` or `ss`, `curl`, `grep`, `awk`, `sed`, `find`, `tail`, `journalctl`, `chmod`, and `chown`. I also troubleshoot CPU, memory, disk, process, and connectivity issues on Linux-based servers and use Shell scripting to automate repetitive operational tasks.

---

## 17. Have you worked on incident management / production incidents?

### Answer

Yes, production incident management is an important part of DevOps operations. During an incident, I first acknowledge the issue, understand the business impact, and identify the affected application or infrastructure component. I then check monitoring dashboards, logs, recent deployments, infrastructure health, Kubernetes resources, networking, databases, and other dependencies depending on the problem. For a high-severity incident, I focus first on restoring service and minimizing business impact rather than spending too much time finding the perfect root cause while the application remains unavailable. Once service is restored, I perform an RCA, document the incident timeline and root cause, and implement preventive actions such as improved monitoring, automation, configuration changes, capacity improvements, or deployment safeguards.

---

## 18. Which scripting languages have you worked with?

### Answer

I have worked primarily with Shell scripting and Python for automation and operational tasks. Shell scripting is useful for Linux administration, file processing, log analysis, deployment automation, health checks, and repetitive command execution. Python can be used for more structured automation, API integrations, AWS operations, data processing, and custom tooling. In DevOps, I use scripting to reduce repetitive manual work and make operational processes consistent and repeatable. I also integrate scripts into Jenkins pipelines or other automation workflows where required.

---

## 19. What repetitive tasks have you automated using Bash/Shell scripting?

### Answer

I have used Shell scripting to automate repetitive operational activities such as checking server health, monitoring disk and memory utilization, processing logs, validating application status, performing file operations, and executing deployment-related tasks. For example, instead of manually checking multiple servers for disk usage, a Shell script can connect to the required systems, collect disk utilization, compare it against a threshold, and generate an alert when the threshold is exceeded. I can also integrate such scripts into Jenkins pipelines so that validation or operational checks happen automatically before or after deployment. Automation reduces manual errors and saves time, especially when the same activity needs to be performed frequently.

---

## 20. Give a real-time example of automation you implemented.

### Answer

One real-time example of automation is integrating application deployment into a Jenkins CI/CD pipeline. Previously, several steps involved manual execution, but I automated the flow so that a code push triggers Jenkins through a webhook. Jenkins checks out the code, builds the application using Maven, performs quality checks, creates the Docker image, scans the image where required, pushes the image to Amazon ECR, and deploys the application to Kubernetes. This reduces manual intervention and makes deployments more consistent. I also use Shell scripting within automation workflows for validation, environment checks, and operational tasks. The main benefit is faster and more reliable deployment with a repeatable process.

---

## 21. What is Terraform state management?

### Answer

Terraform state management is the process of maintaining the `terraform.tfstate` file, which records Terraform's understanding of the infrastructure it manages. Terraform uses the state to map resources defined in the Terraform configuration to actual resources in the cloud and to determine what needs to be created, updated, or destroyed during a plan or apply. In a production team environment, I would not keep the state file only on a developer's local machine. I would use a remote backend such as an S3 bucket for centralized state storage and enable state locking using the appropriate supported locking mechanism. The state should also be protected with encryption, access control, versioning, and restricted permissions because it can contain sensitive infrastructure information.

---

## 22. How do you manage Terraform state in a team environment?

### Answer

In a team environment, I use remote Terraform state so that all engineers and CI/CD systems work with a centralized source of truth. For AWS environments, an S3 backend can be used to store the state securely, with appropriate encryption, versioning, and access control. State locking is also important because it prevents multiple Terraform operations from modifying the same state simultaneously. I separate state by environment or infrastructure boundary rather than allowing every environment to share one large state file. Access to the state is restricted using IAM, and Terraform changes are normally executed through a controlled CI/CD process with plan reviews and approvals for production. This makes infrastructure changes safer and reduces the risk of state corruption or conflicting updates.

---

## 23. What are Terraform modules and why do we use them?

### Answer

Terraform modules are reusable collections of Terraform resources and configuration that allow us to standardize infrastructure provisioning. Instead of repeatedly writing the same VPC, EKS, security group, or other infrastructure configuration for every environment, I can create a reusable module with input variables and outputs. Different environments can then call the same module with environment-specific values. Modules improve code reusability, consistency, maintainability, and standardization. For example, I could have a reusable VPC module and use it for development, QA, UAT, and production while passing different CIDR ranges and configuration values for each environment.

---

## 24. What are Terraform workspaces?

### Answer

Terraform workspaces allow multiple state files to be associated with the same Terraform configuration. They can be useful when the infrastructure configuration is essentially the same but needs separate state for different environments or instances. For example, separate workspaces could represent development and testing environments. However, for larger production environments, I prefer clear environment separation using separate configurations, directories, or state backends when that provides better isolation and control. Workspaces are useful, but they should not be treated as a complete environment isolation or security mechanism because all workspaces still use the same Terraform configuration and provider setup.

---

## 25. Do you have any questions for the interviewer?

### Answer

Yes. I would ask questions that help me understand the team's engineering practices, production responsibilities, and expectations for the role. For example, I would ask, "What does the current DevOps and cloud architecture look like, and what are the major challenges the team is trying to solve?" I would also ask, "How is the CI/CD process structured, and how much of the infrastructure and deployment process is automated?" Another useful question would be, "What would you expect someone i


# Infosys AWS DevOps Interview Questions & Answers

---

## 1. Explain the CI/CD pipeline you implemented using Jenkins, GitHub & AWS.

### Answer

In my project, we use GitHub as the source-code repository and Jenkins for CI/CD automation. When a developer pushes code to the configured branch, a GitHub webhook triggers the Jenkins pipeline.

The pipeline generally follows:

```text
Developer
   ↓
GitHub
   ↓
Webhook
   ↓
Jenkins
   ↓
Checkout
   ↓
Build
   ↓
Unit Tests
   ↓
SonarQube
   ↓
Docker Build
   ↓
Trivy Scan
   ↓
Push Image to Amazon ECR
   ↓
Deploy to EKS
   ↓
Health Check
   ↓
Monitoring
```

For Java applications, Maven is used for compilation and packaging. SonarQube performs code-quality analysis, Trivy is used for container vulnerability scanning, and the Docker image is pushed to Amazon ECR.

The Kubernetes deployment is then performed using Kubernetes manifests, Helm, or Argo CD depending on the project architecture.

### Interview Answer

> I implemented a Jenkins-based CI/CD pipeline integrated with GitHub and AWS. GitHub webhook triggers Jenkins, which checks out the code, builds and tests it, performs SonarQube analysis and security scanning, builds the Docker image, pushes it to ECR, and then deploys the application to EKS. After deployment, we perform health validation and monitor the application through CloudWatch and Prometheus/Grafana.

---

## 2. How do you troubleshoot a failed Jenkins pipeline or GitHub Actions workflow?

### Answer

I troubleshoot it stage by stage instead of assuming the entire pipeline is broken.

First, I check the Jenkins console output and identify the exact stage and command that failed.

For example:

```text
Checkout
Build
Test
SonarQube
Docker Build
Security Scan
Push
Deploy
```

Then I investigate based on the failed stage.

### If Checkout fails

I check:

- Git credentials
- Repository URL
- Branch
- Network connectivity
- Webhook configuration

### If Maven build fails

I check:

```bash
mvn clean package
```

and investigate:

- `pom.xml`
- Java version
- Dependency failures
- Repository availability

### If Docker build fails

I check:

- Dockerfile
- Build context
- Docker daemon
- Permissions
- Required files

### If ECR push fails

I check:

- AWS credentials
- IAM permissions
- ECR repository
- AWS Region
- Docker authentication

### If Kubernetes deployment fails

I check:

```bash
kubectl get pods
kubectl describe pod <pod>
kubectl get events
kubectl rollout status deployment <deployment>
```

### Interview Answer

> I first identify the exact failed stage from the Jenkins console log. Then I reproduce the failing command manually where possible and verify credentials, environment variables, dependencies, permissions, and connectivity. I avoid rerunning the complete pipeline blindly because the root cause could be a specific stage such as Maven, Docker, ECR, or Kubernetes.

---

## 3. What is the difference between ALB and NLB? When would you use each?

### Answer

**ALB — Application Load Balancer**

Works at Layer 7 and understands HTTP/HTTPS traffic.

It supports:

- Host-based routing
- Path-based routing
- HTTP headers
- TLS termination
- WebSocket
- Integration with Kubernetes Ingress

Example:

```text
example.com/users  → User Service
example.com/orders → Order Service
```

**NLB — Network Load Balancer**

Works primarily at Layer 4 and handles TCP/UDP/TLS traffic.

It provides:

- Very high throughput
- Low latency
- Static IP support
- TCP/UDP workloads

### When I choose ALB

For:

- Web applications
- REST APIs
- Microservices
- HTTP/HTTPS routing
- EKS Ingress

### When I choose NLB

For:

- TCP applications
- UDP workloads
- Extremely high throughput
- Static IP requirements
- Non-HTTP protocols

### Interview Answer

> I prefer ALB when I need Layer 7 HTTP/HTTPS routing such as host-based or path-based routing. I choose NLB when I need Layer 4 TCP/UDP traffic handling, very high throughput, low latency, or static IP requirements.

---

## 4. What is the difference between Launch Templates and Launch Configurations?

### Answer

Both are used with EC2 Auto Scaling Groups, but Launch Templates are the newer and recommended option.

### Launch Configuration

It is an older EC2 configuration mechanism.

Limitations include:

- Less flexibility
- Cannot be modified after creation
- Older feature set

### Launch Template

Supports:

- Multiple versions
- Instance types
- Spot Instances
- Network interfaces
- EBS configuration
- Metadata options
- More modern EC2 features

Example:

```text
Launch Template
      ↓
ASG
      ↓
EC2 Instances
```

### Interview Answer

> Launch Templates are the modern replacement for Launch Configurations. They support versioning and newer EC2 capabilities, so I would use Launch Templates for new production Auto Scaling architectures.

---

## 5. What happens when an EC2 instance in an ASG becomes unhealthy?

### Answer

The Auto Scaling Group continuously monitors the health of its instances using EC2 or ELB health checks.

If an instance becomes unhealthy:

```text
Unhealthy EC2
      ↓
ASG detects failure
      ↓
Terminates instance
      ↓
Launches replacement
      ↓
New instance joins target group
```

For example, if the desired capacity is:

```text
Desired = 3
```

and one instance becomes unhealthy, ASG replaces it to maintain:

```text
3 healthy instances
```

### Interview Answer

> When an EC2 instance in an ASG becomes unhealthy according to the configured health check, the ASG terminates it and launches a replacement to maintain the desired capacity. If the ASG is behind an ALB, I also verify the target-group health checks because an instance can be running but still be unhealthy from the application's perspective.

---

## 6. How do you deploy applications on EKS? What is the difference between EKS and ECS?

### Answer

For EKS, I typically follow:

```text
Dockerfile
   ↓
Docker Build
   ↓
ECR
   ↓
Kubernetes Deployment
   ↓
Service / Ingress
   ↓
EKS
```

Example:

```bash
docker build -t myapp:v1 .
docker push <ecr-repository>/myapp:v1
```

Then deploy:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### EKS

Amazon Elastic Kubernetes Service is a managed Kubernetes service.

It provides:

- Kubernetes orchestration
- Deployments
- Services
- StatefulSets
- ConfigMaps
- Secrets
- HPA
- Ingress

### ECS

Amazon Elastic Container Service is AWS's native container orchestration service.

### Interview Answer

> I choose EKS when the organization requires Kubernetes capabilities, portability, complex scheduling, Helm, operators, or a Kubernetes-based platform. ECS is simpler when the workload is primarily AWS-focused and doesn't require Kubernetes functionality.

---

## 7. Explain Blue-Green vs Canary deployment with a real-world use case.

### Answer

### Blue-Green

Two environments are maintained:

```text
Blue  → Current Production

Green → New Version
```

After testing Green, traffic is switched:

```text
Users
  ↓
Green
```

Rollback is quick:

```text
Users
  ↓
Blue
```

### Canary

The new version initially receives a small percentage of traffic.

```text
Old Version → 90%

New Version → 10%
```

If metrics are healthy:

```text
90/10
 ↓
70/30
 ↓
50/50
 ↓
0/100
```

### When I use Canary

For high-risk production releases where I want real-user validation.

### When I use Blue-Green

When fast rollback and environment isolation are more important.

### Interview Answer

> Canary gradually exposes users to a new release, reducing deployment risk, while Blue-Green maintains two environments and switches traffic between them. For a critical application, I would generally prefer Canary when gradual validation is important and Blue-Green when instant rollback is the priority.

---

## 8. What is your rollback strategy for a failed Kubernetes deployment?

### Answer

First, I monitor:

- Pod readiness
- Error rate
- HTTP status codes
- CPU/memory
- Application logs
- Health checks

If the new Deployment is unhealthy:

```bash
kubectl rollout status deployment myapp
```

I inspect:

```bash
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>
```

If rollback is required:

```bash
kubectl rollout undo deployment myapp
```

Then:

```bash
kubectl rollout status deployment myapp
```

For Helm:

```bash
helm history myapp
helm rollback myapp <revision>
```

For GitOps, I would revert the Git commit and allow Argo CD to reconcile the previous desired state.

### Interview Answer

> My rollback mechanism depends on the deployment strategy. With Kubernetes Deployments I use `kubectl rollout undo`, with Helm I use `helm rollback`, and with GitOps I revert the Git commit. I also prefer automated rollback based on health checks, error rates, and readiness rather than waiting for users to report failures.

---

## 9. How do you manage secrets securely in AWS?

### Answer

I avoid storing credentials directly in:

```text
GitHub
Jenkinsfile
Dockerfile
Terraform code
Kubernetes YAML
```

For AWS workloads, I prefer:

- AWS Secrets Manager
- AWS Systems Manager Parameter Store
- IAM Roles
- EKS Pod Identity / IRSA where applicable

For example:

```text
Application
     ↓
IAM Role
     ↓
Secrets Manager
     ↓
Secret
```

### Important Practices

- Least-privilege IAM
- Encryption at rest
- Encryption in transit
- Credential rotation
- No hardcoded credentials
- Audit access using CloudTrail

### Interview Answer

> I prefer IAM roles for workload authentication instead of static access keys. For application secrets such as database passwords and API keys, I use Secrets Manager or another approved secret-management solution and restrict access through least-privilege IAM policies.

---

## 10. Explain IAM Roles, Policies and Cross-Account Access.

### Answer

### IAM Policy

Defines what actions are allowed or denied.

Example:

```text
s3:GetObject
s3:PutObject
```

### IAM Role

An identity that can be assumed by an AWS service, user, or another account.

Example:

```text
EC2
 ↓
IAM Role
 ↓
S3
```

Instead of storing AWS credentials on EC2, the instance assumes a role.

### Cross-Account Access

For example:

```text
Account A
Developer

       ↓ AssumeRole

Account B
Production Role
```

The target role trusts the source account, and the role's permissions define what the user can do.

### Interview Answer

> Policies define permissions, while roles provide an identity that can be assumed by AWS services, users, or other accounts. For cross-account access, I use IAM roles with trust policies and least-privilege permissions rather than sharing long-lived credentials.

---

## 11. How do you configure CloudWatch metrics, logs and alerts?

### Answer

I use CloudWatch for AWS infrastructure monitoring.

For EC2 I monitor:

- CPU utilization
- Network traffic
- Disk-related metrics
- Status checks

For ALB:

- Request count
- Target response time
- HTTP 4xx
- HTTP 5xx
- Unhealthy targets

For RDS:

- CPU
- Connections
- Free storage
- Read/write latency

Logs can be collected using CloudWatch Agent or AWS service integrations.

Alarms can trigger:

```text
Metric
 ↓
CloudWatch Alarm
 ↓
SNS
 ↓
Notification
```

For Kubernetes, I also use Prometheus and Grafana for cluster and application-level monitoring.

### Interview Answer

> I configure CloudWatch metrics and logs for AWS resources and create alarms for important production indicators such as high CPU, unhealthy targets, high latency, HTTP 5xx errors, and RDS connection limits. For EKS, I complement CloudWatch with Prometheus and Grafana to monitor Kubernetes and application metrics.

---

# Quick Revision

| Topic | Key Point |
|---|---|
| Jenkins + GitHub | Webhook → Jenkins → Build → Scan → ECR → EKS |
| ALB | Layer 7, HTTP/HTTPS, path/host routing |
| NLB | Layer 4, TCP/UDP, low latency |
| Launch Template | Modern, versioned EC2 configuration |
| ASG | Replaces unhealthy instances |
| EKS | Managed Kubernetes |
| ECS | AWS-native container orchestration |
| Blue-Green | Two environments, fast rollback |
| Canary | Gradual traffic shifting |
| AWS Secrets | Secrets Manager / Parameter Store |
| IAM Role | Temporary assumed identity |
| IAM Policy | Defines permissions |
| CloudWatch | AWS metrics, logs and alarms |
| Prometheus | Metrics collection |
| Grafana | Metrics visualization |
| ECR | Container image registry |
| Trivy | Container vulnerability scanning |


---

## 12. How do you monitor and optimize AWS infrastructure costs?

### Answer

I start by identifying which AWS services and resources are contributing most to the cost.

I use:

- AWS Cost Explorer
- AWS Budgets
- Cost and Usage Reports
- CloudWatch
- AWS Trusted Advisor

I categorize costs by:

- Service
- Environment
- Application
- Team
- Region

For EC2, I look for:

- Underutilized instances
- Over-provisioned instances
- Unused EBS volumes
- Unattached Elastic IPs
- Old snapshots

For EKS, I check:

- Node utilization
- Over-provisioned workloads
- HPA configuration
- Cluster Autoscaler/Karpenter
- Idle node groups

For S3:

- Unused objects
- Storage classes
- Lifecycle policies
- Incomplete multipart uploads

For RDS:

- Instance sizing
- Storage utilization
- Read replicas
- Reserved capacity where appropriate

### Interview Answer

> I use Cost Explorer and CloudWatch to identify the major cost contributors and then optimize based on actual utilization. I look for idle or oversized EC2 instances, unused EBS resources, inefficient EKS node utilization, unnecessary S3 storage, and oversized RDS instances. I also use tagging and budgets to track costs by environment and application.

---

## 13. How do you implement Terraform modules and remote state management?

### Answer

I use Terraform modules to create reusable infrastructure components.

For example:

```text
terraform/
│
├── modules/
│   ├── vpc/
│   ├── ec2/
│   ├── eks/
│   └── rds/
│
├── environments/
│   ├── dev/
│   ├── qa/
│   ├── uat/
│   └── prod/
```

A module can contain:

```text
main.tf
variables.tf
outputs.tf
```

For team environments, I use remote state instead of keeping `terraform.tfstate` locally.

A common AWS setup is:

```text
Terraform
    ↓
S3 Backend
    +
State Locking
```

The state is stored centrally so multiple engineers and CI/CD systems can work with the same infrastructure state.

### Interview Answer

> I use Terraform modules for reusable infrastructure components such as VPC, EKS, EC2, and RDS. For team environments, I store Terraform state remotely and enable state locking so multiple engineers cannot modify the same infrastructure concurrently.

---

## 14. How do you manage Terraform state in a team environment?

### Answer

I don't keep the production Terraform state file on an engineer's local machine.

I use a remote backend.

For AWS environments, I commonly use:

```text
Terraform
    ↓
S3
    ↓
Remote State
```

State access is restricted using IAM.

Important practices include:

- Remote state
- State locking
- Encryption
- Versioning
- Restricted IAM access
- State backups
- Separate state per environment

For example:

```text
dev.tfstate
qa.tfstate
uat.tfstate
prod.tfstate
```

### Interview Answer

> In a team environment, I use a remote backend for Terraform state, with encryption, versioning, controlled IAM access, and state locking. I also separate state between environments to reduce the blast radius of an accidental change.

---

## 15. How do you scan Docker images for vulnerabilities?

### Answer

I integrate vulnerability scanning into the CI/CD pipeline.

For example:

```text
Code
 ↓
Build
 ↓
Docker Image
 ↓
Trivy Scan
 ↓
Security Gate
 ↓
Push to ECR
```

I use Trivy to identify:

- OS vulnerabilities
- Package vulnerabilities
- Dependency vulnerabilities
- Misconfigurations
- Secrets in some scanning modes

Example:

```bash
trivy image myapp:v1
```

The pipeline can fail if vulnerabilities exceed an approved severity threshold.

For example:

```text
CRITICAL → Fail
HIGH     → Fail/Review
MEDIUM   → Review
LOW      → Accept
```

The exact policy depends on organizational security requirements.

### Interview Answer

> I integrate Trivy into the CI pipeline after building the Docker image. It scans the image for known vulnerabilities and the pipeline can block the image from being pushed or deployed if it violates the defined security policy.

---

## 16. How do you secure an S3 bucket?

### Answer

I follow the principle of least privilege.

Important controls include:

- Block Public Access
- IAM policies
- Bucket policies
- Encryption
- Versioning
- Logging/auditing
- Lifecycle policies

I avoid:

```text
Public Read
Public Write
```

unless there is a very specific approved use case.

For sensitive data, I use encryption such as:

```text
SSE-S3
```

or:

```text
SSE-KMS
```

I also restrict access to only the required IAM roles or services.

### Interview Answer

> I secure S3 by blocking public access, using least-privilege IAM and bucket policies, enabling encryption, versioning where required, and monitoring access. For sensitive data, I prefer KMS-based encryption and ensure only authorized roles can access the objects.

---

## 17. How do you troubleshoot a Kubernetes Pod in CrashLoopBackOff?

### Answer

I first check the Pod status:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

I check logs:

```bash
kubectl logs <pod-name>
```

If the container has restarted, I also check the previous container logs:

```bash
kubectl logs <pod-name> --previous
```

Then I investigate:

- Application errors
- Environment variables
- ConfigMaps
- Secrets
- Image
- Resource limits
- Liveness probe
- Readiness probe
- Dependencies
- Database connectivity

I also check:

```bash
kubectl get events
```

For example, if:

```text
Reason: OOMKilled
```

I investigate memory usage and resource limits.

### Interview Answer

> CrashLoopBackOff means the container is repeatedly starting and failing. I check the current and previous logs, Pod events, exit codes, probes, configuration, Secrets, dependencies, and resource limits. I use `kubectl describe pod`, `kubectl logs --previous`, and `kubectl get events` to identify the root cause.

---

## 18. How would you dynamically scale a Kubernetes deployment?

### Answer

I generally use the Horizontal Pod Autoscaler.

For example:

```text
Normal Traffic
     ↓
2 Pods

High Traffic
     ↓
4 Pods

More Traffic
     ↓
8 Pods
```

Example:

```bash
kubectl autoscale deployment myapp \
  --cpu-percent=70 \
  --min=2 \
  --max=10
```

HPA requires resource metrics, typically provided through Metrics Server or another metrics pipeline.

For example:

```yaml
resources:
  requests:
    cpu: 250m
    memory: 256Mi
```

I can also use custom metrics when CPU alone doesn't represent application load.

For cluster-level capacity, I combine HPA with Cluster Autoscaler or Karpenter.

### Interview Answer

> I use HPA to dynamically increase or decrease Pod replicas based on CPU, memory, or custom application metrics. If the cluster doesn't have enough nodes to accommodate the additional Pods, I combine HPA with Cluster Autoscaler or Karpenter.

---

## 19. How do you know whether Kubernetes nodes are Ready?

### Answer

I use:

```bash
kubectl get nodes
```

Example:

```text
NAME       STATUS   ROLES
node-01    Ready    <none>
node-02    Ready    <none>
node-03    NotReady <none>
```

For detailed information:

```bash
kubectl describe node node-01
```

I check conditions such as:

```text
Ready
MemoryPressure
DiskPressure
PIDPressure
NetworkUnavailable
```

I also check kubelet and node-level metrics.

### Interview Answer

> I use `kubectl get nodes` to check whether nodes are Ready and `kubectl describe node` to investigate conditions such as MemoryPressure, DiskPressure, PIDPressure, and NetworkUnavailable. I also check kubelet health and node monitoring metrics when troubleshooting a NotReady node.

---

## 20. How do you handle high CPU usage in your Kubernetes cluster?

### Answer

First, I determine whether the CPU usage is coming from:

- Application Pods
- Kubernetes system components
- Nodes
- A sudden traffic spike

I check:

```bash
kubectl top nodes
kubectl top pods -A
```

Then I identify the highest consumers.

I investigate:

- Traffic increase
- CPU requests/limits
- Application behavior
- Infinite loops
- Expensive operations
- Incorrect resource configuration

If the workload needs additional replicas, I use HPA.

If the cluster lacks node capacity, I use Cluster Autoscaler or Karpenter.

### Interview Answer

> I first identify whether the CPU increase is node-level or Pod-level using `kubectl top`. Then I correlate it with traffic, application metrics, and recent deployments. Depending on the cause, I may tune resource requests, scale Pods using HPA, optimize the application, or increase cluster capacity using autoscaling.

---

## 21. How will you plan a disaster recovery?

### Answer

I start by defining:

- RTO — Recovery Time Objective
- RPO — Recovery Point Objective

For example:

```text
RTO = 30 minutes
RPO = 5 minutes
```

Then I identify critical components:

```text
Application
Database
Storage
Networking
DNS
Secrets
Infrastructure
```

For AWS, depending on requirements, I can use:

- Multi-AZ architecture
- Cross-region replication
- RDS backups
- S3 replication
- ECR image replication
- Terraform
- Route 53
- AWS Backup

Infrastructure should be reproducible through IaC.

I also conduct regular DR testing instead of assuming backups work.

### Interview Answer

> I design DR based on business-defined RTO and RPO. I ensure application infrastructure can be recreated through Terraform, databases and storage have appropriate backups or replication, container images are available in the recovery region, and DNS can redirect traffic. Most importantly, I regularly test the recovery process.

---

## 22. How will you design a microservice architecture application in Kubernetes?

### Answer

I would separate each microservice into its own Deployment and Service.

Example:

```text
                    Internet
                       ↓
                  Load Balancer
                       ↓
                  Ingress
                       ↓
       ┌───────────────┼────────────────┐
       ↓               ↓                ↓
   User Service    Order Service    Payment Service
       ↓               ↓                ↓
     Pods             Pods             Pods
```

Each service gets a stable Kubernetes Service.

I would use:

- Deployment
- Service
- ConfigMap
- Secret
- HPA
- Ingress
- NetworkPolicy
- Resource requests/limits
- Readiness probes
- Liveness probes

For observability:

```text
Prometheus
Grafana
Loki/ELK
```

For deployment:

```text
GitHub
 ↓
Jenkins
 ↓
ECR
 ↓
Argo CD
 ↓
EKS
```

### Interview Answer

> I would containerize each microservice independently and deploy it using Kubernetes Deployments with ClusterIP Services for internal communication. External traffic would enter through an Ingress or cloud load balancer. I would add HPA, probes, resource limits, NetworkPolicies, centralized logging, monitoring, and secure secret management.

---

## 23. What is Liveness probe and Readiness probe in Kubernetes?

### Answer

### Liveness Probe

Determines whether the application is still alive.

If it fails repeatedly, Kubernetes restarts the container.

Example:

```text
Application stuck
      ↓
Liveness fails
      ↓
Container restarted
```

### Readiness Probe

Determines whether the application is ready to receive traffic.

If it fails:

```text
Pod remains running
       ↓
Removed from Service endpoints
```

The container is not necessarily restarted.

### Example

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
```

### Interview Answer

> Liveness determines whether a container needs to be restarted, while readiness determines whether the Pod should receive traffic. For example, if an application is temporarily unable to connect to its dependency during startup, the readiness probe can keep it out of service without unnecessarily restarting the container.

---

## 24. What is node affinity, node selector?

### Answer

Both are Kubernetes scheduling mechanisms used to influence where Pods run.

### NodeSelector

The simpler mechanism.

Example:

```yaml
nodeSelector:
  workload: backend
```

The Pod can only run on nodes having:

```text
workload=backend
```

### Node Affinity

Provides more flexible rules.

It supports:

- Required rules
- Preferred rules
- Multiple conditions

Example:

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: workload
          operator: In
          values:
          - backend
```

### Interview Answer

> NodeSelector provides simple label-based placement, while Node Affinity provides more advanced scheduling rules including required and preferred conditions. In production, I use affinity when workload placement requires more flexibility.

---

## 25. Explain ELK cluster flow. How did you set up the cluster from scratch?

### Answer

ELK stands for:

```text
Elasticsearch
Logstash
Kibana
```

A typical architecture is:

```text
Application
     ↓
Container Logs
     ↓
Fluent Bit / Filebeat
     ↓
Logstash
     ↓
Elasticsearch
     ↓
Kibana
```

### Elasticsearch

Stores and indexes logs.

### Logstash

Processes and transforms logs.

### Kibana

Provides visualization and search.

For Kubernetes, I can deploy log collectors as DaemonSets so each node collects container logs.

Example:

```text
Node 1 → Fluent Bit
Node 2 → Fluent Bit
Node 3 → Fluent Bit
          ↓
      Elasticsearch
          ↓
        Kibana
```

I configure:

- Log collection
- Parsing
- Indexing
- Retention
- Access control
- Dashboards
- Alerts

### Interview Answer

> In a Kubernetes environment, I use a node-level log collector such as Fluent Bit or Filebeat to collect container logs. Logs are forwarded to Logstash when additional parsing or transformation is required, then stored and indexed in Elasticsearch and visualized through Kibana.

---

## 26. Scenario -- In K8s cluster, pods are restarting multiple times and application are going down, How do you troubleshoot it?

### Answer

I start by checking the Pod status:

```bash
kubectl get pods -A
```

Then identify restart counts:

```bash
kubectl get pods
```

Next:

```bash
kubectl describe pod <pod-name>
```

and:

```bash
kubectl logs <pod-name> --previous
```

I investigate:

- OOMKilled
- Liveness probe failures
- Application crashes
- Configuration errors
- Secret problems
- Dependency failures
- Image issues
- Node resource pressure

Then check:

```bash
kubectl get events --sort-by=.lastTimestamp
```

I also check:

```bash
kubectl top pods
kubectl top nodes
```

If the issue started after a deployment, I compare it with the previous version and consider rollback.

### Interview Answer

> I first identify whether the restarts are application-driven, resource-driven, probe-driven, or node-driven. I use Pod events, previous container logs, exit codes, resource metrics, and node conditions. If the issue correlates with a recent release, I roll back to the last stable version while continuing the root cause analysis.

---

## 27. How would you troubleshoot the out of memory issue in K8s cluster?

### Answer

I first identify whether the problem is:

```text
Pod-level OOM
```

or:

```text
Node-level memory pressure
```

I check:

```bash
kubectl top pods
kubectl top nodes
```

Then:

```bash
kubectl describe pod <pod-name>
```

If I see:

```text
Reason: OOMKilled
```

I investigate:

- Memory limits
- Memory requests
- Application memory usage
- Memory leaks
- Traffic patterns

If the node is under memory pressure, I check:

```bash
kubectl describe node <node>
```

Then I may:

- Scale the node group
- Add larger nodes
- Fix Pod resource configuration
- Enable autoscaling
- Optimize the application

### Interview Answer

> I first distinguish between a Pod OOMKilled condition and node-level memory pressure. I check Pod and node metrics, resource requests and limits, and application memory behavior. If the workload genuinely requires more capacity, I scale the workload or nodes, but I don't simply increase limits without understanding the underlying memory consumption.

---

## 28. Scenario -- Say your doing the deployment, but it is stuck in pending state, How will you troubleshoot?

### Answer

I first check:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

The Events section is usually very useful.

I investigate:

### Insufficient CPU/Memory

```text
Insufficient cpu
Insufficient memory
```

Check:

```bash
kubectl top nodes
```

### Taints

```bash
kubectl describe node <node>
```

Check whether the Pod has the required toleration.

### Node Affinity

Verify whether the Pod's affinity rules match available nodes.

### Persistent Volume

Check:

```bash
kubectl get pvc
kubectl describe pvc <pvc>
```

### Node Availability

```bash
kubectl get nodes
```

### Production Interview Answer

> I use `kubectl describe pod` first because Kubernetes Events normally tell me why scheduling failed. I check resource availability, taints and tolerations, node affinity, PVC binding, node readiness, and topology constraints before making changes.

---

## 29. Scenario -- Now Kubernetes cluster is running fine, but pods are not able communicate with services ? How do you troubleshoot this issue ?

### Answer

I start by checking the Service:

```bash
kubectl get svc
```

Then verify its selector:

```bash
kubectl describe svc <service-name>
```

Next check endpoints:

```bash
kubectl get endpoints <service-name>
```

If there are no endpoints, I check whether the Service selector matches the Pod labels.

Example:

```yaml
selector:
  app: backend
```

Pod:

```yaml
labels:
  app: backend
```

Then I test DNS from inside the cluster:

```bash
nslookup backend-service
```

I also investigate:

- NetworkPolicies
- kube-proxy
- CoreDNS
- CNI
- Service port
- targetPort
- Pod readiness

### Interview Answer

> I first verify that the Service selector matches the Pod labels and that healthy endpoints exist. Then I test Kubernetes DNS and connectivity from another Pod and check Service ports, targetPorts, NetworkPolicies, CoreDNS, kube-proxy, and the CNI before assuming there is a networking-plugin problem.

---

## 30. Your are upgrading the k8s cluster, after upgrade some applications are failing, how will you solve this issue ?

### Answer

I first identify whether the failure is caused by:

- Kubernetes API changes
- Deprecated APIs
- Incompatible workloads
- Ingress Controller compatibility
- CNI issues
- CSI driver issues
- Admission controllers
- Helm chart compatibility

I check:

```bash
kubectl get pods -A
kubectl get events -A
```

Then review:

```bash
kubectl describe pod <pod>
kubectl logs <pod>
```

I compare the Kubernetes version and application dependencies.

For deprecated APIs, I validate manifests before the upgrade.

I also check:

- Helm chart versions
- AWS Load Balancer Controller
- EBS CSI driver
- Metrics Server
- Custom Operators

If necessary, I restore the previous node group or use the cloud provider's supported rollback/recovery approach.

### Interview Answer

> After an upgrade, I first determine whether the failures are related to API deprecations, workload compatibility, networking, storage, ingress, or add-ons. I inspect events and logs, validate deprecated APIs, verify add-on compatibility, and restore the previous known-good configuration if the upgrade introduces a critical production issue.

---

## 31. How do you implement multi-branch CI/CD pipelines in GitLab or Jenkins?

### Answer

I use a multibranch pipeline where each Git branch can automatically create its own pipeline.

For example:

```text
main
 └── Production

develop
 └── Development

feature/*
 └── Build + Test

release/*
 └── UAT
```

Jenkins Multibranch Pipeline automatically discovers branches containing a Jenkinsfile.

The pipeline can define different behavior based on the branch.

Example:

```groovy
if (env.BRANCH_NAME == 'main') {
    // Production deployment
}
```

For feature branches:

```text
Checkout
 ↓
Build
 ↓
Uni



# Infosys DevOps Interview Experience – Round 1 (Detailed Answers)

---

# 1. Tell me about your current role and DevOps experience.

## Answer

"I'm currently working as a DevOps Engineer with around 4 years of IT experience. My primary work is on AWS cloud, Kubernetes (EKS), Docker, Jenkins, Terraform, Git, Helm, Linux, and CI/CD automation.

My day-to-day responsibilities include developing and maintaining CI/CD pipelines, containerizing applications using Docker, deploying microservices on Amazon EKS, provisioning infrastructure using Terraform, monitoring applications with Prometheus and Grafana, troubleshooting production issues, and collaborating with developers to ensure smooth application releases.

I also work on Infrastructure as Code, Kubernetes deployments, Helm charts, image management in Amazon ECR, security scanning using Trivy, and production incident support."

---

# 2. Is your DevOps experience primarily on AWS or other cloud platforms?

## Answer

Yes. My primary experience is on AWS.

Services I've worked on include:

- EC2
- VPC
- IAM
- EKS
- ECR
- S3
- CloudWatch
- Route53
- ALB
- NLB
- Auto Scaling
- EBS
- EFS
- RDS
- Secrets Manager

Apart from AWS, I have basic exposure to Kubernetes and Terraform which are cloud-agnostic.

---

# 3. Have you worked with on-premises infrastructure / VMware vSphere?

## Answer

I have primarily worked on AWS cloud infrastructure.

I have basic knowledge of VMware and understand concepts like:

- Virtual Machines
- ESXi Hosts
- Datastores
- vCenter

However, my production experience is mainly focused on AWS cloud and Kubernetes.

---

# 4. Did you create CI/CD pipelines from scratch or work on existing pipelines?

## Answer

I have done both.

Initially I worked on enhancing existing Jenkins pipelines.

Later I created pipelines from scratch for new microservices.

My pipeline generally includes:

Developer

↓

Git Push

↓

Webhook

↓

Jenkins

↓

Build

↓

Unit Testing

↓

SonarQube

↓

Trivy Scan

↓

Docker Build

↓

Push to Amazon ECR

↓

Terraform (if infrastructure changes)

↓

Helm Deployment

↓

Amazon EKS

↓

Smoke Testing

↓

Slack/Email Notification

---

# 5. Explain the CI/CD pipeline you created for CSV validation and S3 upload.

## Answer

The pipeline flow was:

CSV uploaded

↓

Git Trigger

↓

Jenkins Pipeline

↓

Validate CSV format

↓

Run Python validation script

↓

Reject if invalid

↓

Upload valid CSV to Amazon S3

↓

Send success notification

If validation failed, the pipeline stopped immediately and notified the team with detailed error logs.

---

# 6. If an L3 team created the pipeline, how do you understand and troubleshoot it?

## Answer

My approach is:

- Review the Jenkinsfile.
- Understand pipeline stages.
- Check Shared Libraries.
- Review environment variables.
- Verify credentials.
- Check Jenkins console logs.
- Review previous successful builds.
- Compare recent Git commits.
- Identify the failed stage.
- Fix the issue or coordinate with the relevant team if necessary.

---

# 7. Have you written a Dockerfile?

## Answer

Yes.

A typical Dockerfile I write includes:

- Base Image
- Working Directory
- Copy application files
- Install dependencies
- Expose required port
- Define ENTRYPOINT or CMD

Example:

```dockerfile
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

I also use multi-stage builds to reduce image size.

---

# 8. What is the difference between CMD and ENTRYPOINT in Docker?

## Answer

CMD

- Provides default command.
- Can be overridden.

Example

```
CMD ["java","-jar","app.jar"]
```

ENTRYPOINT

- Defines the main executable.
- Difficult to override.

Example

```
ENTRYPOINT ["java","-jar","app.jar"]
```

Difference:

| CMD | ENTRYPOINT |
|------|------------|
| Default command | Main executable |
| Easily overridden | Usually fixed |
| Flexible | Mandatory execution |

Production practice:

ENTRYPOINT for the application.

CMD for optional arguments.

---

# 9. What Kubernetes operations have you performed apart from deployments?

## Answer

I regularly perform:

- Pod troubleshooting
- Rollbacks
- Scaling Deployments
- Creating Namespaces
- ConfigMaps
- Secrets
- Persistent Volumes
- Persistent Volume Claims
- Ingress configuration
- Service creation
- HPA configuration
- Helm upgrades
- Pod log analysis
- Node troubleshooting
- RBAC management
- Resource optimization
- Cluster upgrades
- Monitoring and alerting

---

# 10. Have you troubleshooted CrashLoopBackOff? How?

## Answer

Yes.

My troubleshooting steps are:

```bash
kubectl get pods

kubectl describe pod

kubectl logs

kubectl logs --previous

kubectl top pod
```

Then verify:

- Image
- ConfigMaps
- Secrets
- Resource limits
- Liveness probe
- Readiness probe
- Database connectivity
- External dependencies

If caused by a deployment:

```
kubectl rollout undo deployment
```

---

# 11. Explain CrashLoopBackOff in simple terms.

## Answer

CrashLoopBackOff means:

The application inside the container starts.

↓

It crashes immediately.

↓

Kubernetes restarts it.

↓

It crashes again.

↓

After several failed attempts, Kubernetes waits longer between restart attempts.

Common reasons:

- Wrong application configuration
- Missing Secrets
- Missing ConfigMaps
- Database unavailable
- OOMKilled
- Incorrect startup command
- Probe failures

---

# 12. Explain PV and PVC in Kubernetes.

## Answer

Persistent Volume (PV)

A storage resource available in the cluster.

Persistent Volume Claim (PVC)

A request made by a Pod for storage.

Flow:

Application

↓

PVC

↓

PV

↓

AWS EBS / EFS

Difference:

| PV | PVC |
|----|-----|
| Actual storage | Storage request |
| Created by Admin/Dynamic Provisioner | Created by User |
| Supplies storage | Consumes storage |

---

# 13. How do you handle autoscaling in Kubernetes?

## Answer

I use:

Horizontal Pod Autoscaler (HPA)

- Scales Pods.

Cluster Autoscaler

- Adds worker nodes.

Vertical Pod Autoscaler (when appropriate)

- Adjusts CPU/Memory recommendations.

Metrics used:

- CPU
- Memory
- Custom Metrics
- External Metrics

This ensures applications scale automatically based on demand while optimizing infrastructure usage.


# Infosys DevOps Interview Experience – Round 1 (Part 2)

---

# 14. How do you achieve URL/Path-Based Routing in Kubernetes?

## Answer

In Kubernetes, URL or path-based routing is achieved using an **Ingress** resource along with an **Ingress Controller** (e.g., AWS Load Balancer Controller, NGINX Ingress Controller).

### Architecture

```
Internet
      │
      ▼
Application Load Balancer (ALB)
      │
      ▼
Ingress Controller
      │
 ┌────┴──────────────┐
 │                   │
 ▼                   ▼
/users          /orders
 │                   │
User Service    Order Service
 │                   │
Pods            Pods
```

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /users
        pathType: Prefix
        backend:
          service:
            name: user-service
            port:
              number: 80

      - path: /orders
        pathType: Prefix
        backend:
          service:
            name: order-service
            port:
              number: 80
```

### Benefits

- Single entry point
- Path-based routing
- Host-based routing
- SSL termination
- Lower AWS Load Balancer cost
- Easy traffic management

### Interview Answer

> In production, I use an Ingress resource with the AWS Load Balancer Controller. The ALB receives external traffic, forwards it to the Ingress Controller, and based on the URL path or hostname, the request is routed to the appropriate Kubernetes Service and Pods.

---

# 15. Are you familiar with middleware technologies like Tomcat, WebLogic, or WebSphere?

## Answer

Yes, I have worked with **Apache Tomcat** for Java-based applications.

My responsibilities included:

- Deploying WAR files
- Restarting Tomcat services
- Monitoring logs
- Troubleshooting application startup issues
- Configuring JVM options
- Managing environment variables
- Integrating Tomcat deployments into Jenkins pipelines

I have basic knowledge of WebLogic and WebSphere but my production experience is mainly with Tomcat.

### Interview Answer

> I have production experience with Apache Tomcat for deploying Java applications and troubleshooting startup issues. I also understand the basics of WebLogic and WebSphere, though my primary hands-on experience is with Tomcat.

---

# 16. How comfortable are you with Linux? What activities do you perform?

## Answer

I work with Linux daily.

Common activities include:

- User management
- File and directory permissions
- Process monitoring
- Disk usage monitoring
- Network troubleshooting
- Service management
- Log analysis
- Cron jobs
- Shell scripting

Frequently used commands:

```bash
ls
cd
pwd
cp
mv
rm
cat
grep
find
chmod
chown
ps
top
htop
free
df
du
netstat
ss
curl
wget
systemctl
journalctl
tail -f
```

### Interview Answer

> I am comfortable working with Linux and use it daily for application deployments, troubleshooting, monitoring services, analyzing logs, managing users and permissions, writing shell scripts, and performing system administration tasks.

---

# 17. Have you worked on incident management / production incidents?

## Answer

Yes.

Typical production incidents I've handled include:

- CrashLoopBackOff
- ImagePullBackOff
- High CPU usage
- High memory utilization
- Pod Pending
- Application downtime
- Jenkins pipeline failures
- Terraform deployment failures
- Ingress routing issues
- Database connectivity failures

### Incident Process

1. Receive alert from Prometheus or CloudWatch.
2. Identify impacted application.
3. Analyze logs and metrics.
4. Troubleshoot root cause.
5. Restore service (rollback if needed).
6. Validate application.
7. Conduct Root Cause Analysis (RCA).
8. Implement preventive measures.

### Interview Answer

> Yes, I regularly participate in production incident management. I investigate alerts, analyze logs and metrics, restore services quickly, communicate with stakeholders, perform RCA, and implement preventive actions to avoid future incidents.

---

# 18. Which scripting languages have you worked with?

## Answer

I have worked with:

- Bash/Shell
- Python (basic)
- Groovy (Jenkins Pipelines)

Examples:

Bash:

- Deployment automation
- Health checks
- Backup scripts
- Log cleanup

Python:

- CSV validation
- AWS automation using Boto3
- API integrations

Groovy:

- Jenkins Declarative Pipelines
- Shared Libraries

### Interview Answer

> I primarily use Bash for automation, Groovy for Jenkins pipelines, and Python for tasks such as file validation, AWS automation, and API integrations.

---

# 19. What repetitive tasks have you automated using Bash/Shell scripting?

## Answer

Examples:

- Log cleanup
- Backup scripts
- Health checks
- Service restart automation
- Docker cleanup
- Kubernetes health checks
- ECR image cleanup
- Disk usage monitoring
- Deployment validation
- User creation

Example:

```bash
docker system prune -af
```

Daily health check:

```bash
kubectl get pods

kubectl get nodes

kubectl top pods
```

### Interview Answer

> I have automated repetitive tasks such as log cleanup, service monitoring, Docker cleanup, Kubernetes health checks, backup scripts, disk monitoring, and deployment validation using Bash scripting.

---

# 20. Give a real-time example of automation you implemented.

## Answer

One automation I implemented was **Docker image cleanup** on Jenkins agents.

### Before

- Old images consumed disk space.
- Jenkins builds started failing with "No space left on device".

### Automation

Created a Bash script:

```bash
docker image prune -af

docker container prune -f

docker volume prune -f
```

Configured it as a cron job.

### Result

- Reclaimed disk space automatically.
- Prevented build failures.
- Reduced manual maintenance.

### Interview Answer

> I automated Docker cleanup on Jenkins agents using a Bash script scheduled through cron. It removed unused images, containers, and volumes, preventing disk space issues and reducing manual intervention.

---

# 21. What is Terraform State Management?

## Answer

Terraform State is a file that stores the mapping between Terraform configuration and real infrastructure.

It tracks:

- Resource IDs
- Dependencies
- Metadata
- Current infrastructure state

Example:

```
main.tf

↓

terraform apply

↓

AWS Resources

↓

terraform.tfstate
```

### Why is it important?

- Detects changes
- Plans updates
- Prevents duplicate resource creation
- Enables infrastructure tracking

### Interview Answer

> Terraform state records the current infrastructure managed by Terraform. It maps configuration to real resources, allowing Terraform to calculate changes, update existing infrastructure, and avoid recreating resources unnecessarily.

---

# 22. How do you manage Terraform State in a team environment?

## Answer

Production best practice:

Remote Backend:

- Amazon S3 (State Storage)
- DynamoDB (State Locking)

Architecture:

```
Terraform

↓

S3 Backend

↓

DynamoDB Lock

↓

AWS Infrastructure
```

Benefits:

- Shared state
- Versioning
- Encryption
- State locking
- Team collaboration

Example:

```hcl
backend "s3" {
  bucket         = "terraform-state"
  key            = "prod/terraform.tfstate"
  region         = "ap-south-1"
  dynamodb_table = "terraform-locks"
}
```

### Interview Answer

> In a team environment, I store the Terraform state in an encrypted Amazon S3 bucket with versioning enabled and use DynamoDB for state locking to prevent multiple users from modifying the infrastructure simultaneously.

---

# 23. What are Terraform Modules and why do we use them?

## Answer

A Terraform module is a reusable collection of Terraform resources.

Instead of writing EC2 creation code repeatedly, create a module once and reuse it.

Example:

```
modules/

EC2/

VPC/

EKS/

RDS/
```

Benefits:

- Reusability
- Consistency
- Easier maintenance
- Reduced code duplication
- Standardization

### Interview Answer

> Terraform modules allow us to package and reuse infrastructure code. They improve maintainability, reduce duplication, and ensure consistent infrastructure deployment across different environments.

---

# 24. What are Terraform Workspaces?

## Answer

Terraform Workspaces allow multiple environments to share the same Terraform configuration while maintaining separate state files.

Example:

```
Default

↓

Dev

↓

QA

↓

UAT

↓

Production
```

Commands:

```bash
terraform workspace list

terraform workspace new dev

terraform workspace select prod
```

Each workspace maintains its own state.

### Interview Answer

> Terraform Workspaces enable us to manage multiple environments such as Dev, QA, and Production using the same Terraform code while keeping separate state files for each environment.

---

# 25. Do you have any questions for the interviewer?

## Answer

Good questions to ask:

1. How is the DevOps team structured?

2. Which CI/CD tools and deployment strategies are currently used?

3. How do you manage Kubernetes clusters in production?

4. What monitoring and observability tools do you use?

5. How do you handle production incidents and on-call responsibilities?

6. Are you following GitOps or traditional CI/CD?

7. What are the biggest technical challenges the team is currently facing?

8. What opportunities are available for learning new cloud technologies and certifications?

9. What does success look like for this role in the first six months?

10. What are the next steps in the interview process?

### Interview Tip

Avoid saying **"No, I don't have any questions."** Asking thoughtful questions demonstrates interest in the role, the team, and the company's engineering practices.



# Infosys DevOps Interview – Round 2 (4 Years Experience)

---

## 1. Please explain your day-to-day activities as a DevOps Engineer.

### Answer

As a DevOps Engineer, my daily responsibilities include monitoring CI/CD pipelines, supporting application deployments, managing Kubernetes clusters, provisioning infrastructure using Terraform, and troubleshooting production issues.

I review Jenkins pipeline executions, resolve build failures, and ensure successful deployments to Amazon EKS using Helm and Argo CD. I monitor infrastructure and application health using Prometheus, Grafana, and CloudWatch. I also manage Docker images in Amazon ECR, optimize cloud resources, perform infrastructure changes using Terraform, and collaborate with developers to resolve deployment and application issues. Additionally, I participate in production release planning, incident management, root cause analysis (RCA), and continuously automate manual operational tasks.

---

## 2. You have cloned a Git repository. While running `git pull`, you get the error "not a git repository". How will you troubleshoot and fix it?

### Answer

First, I verify whether I'm inside the correct project directory.

```bash
pwd
ls -la
```

Then I check if the `.git` directory exists.

```bash
ls -la .git
```

If the `.git` directory is missing, it means either I'm in the wrong directory or the repository metadata has been deleted.

Next, I verify the configured remote.

```bash
git remote -v
```

If Git reports **"not a git repository"**, I navigate to the correct cloned repository.

If the repository was accidentally deleted or corrupted, I clone it again.

```bash
git clone <repository-url>
```

If necessary, I also verify the current branch.

```bash
git branch
git status
```

Once the repository is valid, I execute:

```bash
git pull origin main
```

(or the appropriate branch).

---

## 3. How do you provide access to an S3 bucket, and what permissions need to be set on the bucket?

### Answer

The recommended approach is to use **IAM Roles** instead of long-term access keys.

For EC2 instances, I attach an IAM Role containing only the required S3 permissions such as:

- s3:GetObject
- s3:PutObject
- s3:ListBucket
- s3:DeleteObject (if required)

The bucket itself can also have a Bucket Policy restricting access to specific IAM roles, AWS accounts, VPC endpoints, or IP ranges.

For Kubernetes workloads running on EKS, I use **IAM Roles for Service Accounts (IRSA)** so Pods can securely access S3 without storing AWS credentials.

Following the Principle of Least Privilege ensures applications receive only the permissions they actually require.

---

## 4. How can an application communicate with an EC2 instance that is deployed in a private subnet behind a Multi-AZ Load Balancer?

### Answer

The EC2 instances remain in private subnets without public IP addresses.

An **Application Load Balancer (ALB)** is deployed in public subnets across multiple Availability Zones.

The ALB receives incoming client requests and forwards traffic to the EC2 instances using Target Groups.

Security Groups allow inbound traffic only from the ALB Security Group rather than directly from the internet.

Applications communicate using the ALB DNS name while the backend instances remain protected inside private subnets.

This architecture provides high availability, security, and fault tolerance across multiple Availability Zones.

---

## 5. If an application is hosted in an S3 bucket and users are located in different geographic regions, how would you reduce latency?

### Answer

I would place **Amazon CloudFront** in front of the S3 bucket.

CloudFront caches static content at AWS Edge Locations located close to end users worldwide.

Instead of every request reaching the origin S3 bucket, users receive content from the nearest edge location, significantly reducing latency.

I would also enable compression, configure appropriate cache-control headers, use HTTPS, and enable Origin Access Control (OAC) so the S3 bucket remains private while CloudFront securely serves the content.

---

## 6. You encounter high latency in an application. What monitoring and troubleshooting steps would you take?

### Answer

I start by identifying whether the latency is occurring at the application, infrastructure, database, or network layer.

I review CloudWatch metrics such as CPU, Memory, Network In/Out, Disk IOPS, ALB Target Response Time, HTTP 5xx errors, and request count.

For Kubernetes workloads, I examine Prometheus metrics including Pod CPU, memory utilization, request latency, and restart count.

I inspect application logs, database performance, API response times, and distributed traces using OpenTelemetry or Jaeger if available.

I also verify Auto Scaling events, resource utilization, recent deployments, and load balancer health.

After identifying the bottleneck, I implement corrective actions such as scaling resources, optimizing queries, tuning application performance, or rolling back problematic deployments.

---

## 7. How do you manage existing (unmanaged) AWS resources using Terraform?

### Answer

Terraform can manage existing infrastructure by importing resources into its state file.

First, I define the resource block inside the Terraform configuration.

Example:

```hcl
resource "aws_instance" "web" {
}
```

Then I import the existing resource.

```bash
terraform import aws_instance.web i-0123456789abcdef
```

After importing, I execute:

```bash
terraform plan
```

Terraform compares the imported state with the configuration.

I update the Terraform code until the plan shows **No Changes**, ensuring the imported infrastructure is fully managed without recreating resources.

---

## 8. How do you import an existing VPC into Terraform?

### Answer

First, I create the VPC resource block.

```hcl
resource "aws_vpc" "main" {
}
```

Then I import the existing VPC using its VPC ID.

```bash
terraform import aws_vpc.main vpc-xxxxxxxx
```

Next, I execute:

```bash
terraform state show aws_vpc.main
```

to review the imported attributes.

Finally, I update the Terraform configuration so that it matches the actual AWS configuration.

Running:

```bash
terraform plan
```

should eventually show **No Changes**, confirming Terraform fully manages the VPC.

---

## 9. How will you upgrade a Kubernetes cluster?

### Answer

I first review the Kubernetes release notes and verify compatibility with all applications, Helm charts, CRDs, and third-party components.

I perform the upgrade in a staging environment before production.

For managed services like Amazon EKS, I upgrade the control plane first, followed by managed node groups.

Before upgrading worker nodes, I cordon and drain each node.

```bash
kubectl cordon <node>

kubectl drain <node> --ignore-daemonsets
```

Pods are automatically rescheduled onto healthy nodes.

After upgrading each node, I uncordon it.

```bash
kubectl uncordon <node>
```

Finally, I verify cluster health, application availability, monitoring dashboards, and perform post-upgrade validation before closing the maintenance window.

---

## 10. What is a Canary Deployment?

### Answer

A Canary Deployment gradually releases a new application version to a small percentage of users while the majority continue using the stable version.

For example:

- 5% Traffic → Version 2
- 95% Traffic → Version 1

If monitoring shows healthy performance, traffic is gradually increased:

- 20%
- 50%
- 100%

If issues are detected, traffic is immediately shifted back to the stable version.

Canary deployments reduce deployment risk, enable early issue detection, and provide safer production releases.

In Kubernetes, Canary deployments are commonly implemented using Argo Rollouts, Istio, Linkerd, or NGINX Ingress with weighted traffic routing.

---

## 11. During peak traffic, if the Ingress Controller fails to route requests efficiently, how would you diagnose the issue and scale the Ingress resources effectively?

### Answer

I would begin by checking the health of the Ingress Controller Pods.

```bash
kubectl get pods -n ingress-nginx
```

Then I inspect controller logs.

```bash
kubectl logs <ingress-controller-pod> -n ingress-nginx
```

Next, I verify:

- Ingress resource configuration
- Backend Service health
- Endpoint availability
- Pod readiness
- Load Balancer health
- DNS resolution
- NGINX metrics
- CPU and Memory utilization

Using Prometheus and Grafana, I monitor request rate, response time, error rate, and controller resource utilization.

If the Ingress Controller is resource-constrained, I increase its replicas using a Deployment or configure a Horizontal Pod Autoscaler (HPA) based on CPU or custom metrics.

If worker nodes become saturated, Cluster Autoscaler provisions additional nodes automatically.

Finally, I validate traffic distribution, monitor latency, and ensure the system remains stable throughout peak traffic periods.



# DevOps Engineer Interview Questions & Answers (Infosys)

## 1. In a well-designed CI/CD pipeline for a critical banking application, is it acceptable to push code directly to production without automated testing if the developer is confident and time is limited? (True/False)

**Answer:** **False**

### Explanation

No. In banking or any mission-critical application, automated testing is mandatory. Developer confidence is never a substitute for validation. Skipping testing increases the risk of production failures, security vulnerabilities, and compliance violations.

A standard CI/CD pipeline should include:

* Unit Testing
* Integration Testing
* Security Scanning
* Code Quality Checks (SonarQube)
* Approval Gates (if required)
* Automated Deployment

Only after all quality gates pass should the application be deployed to production.

---

## 2. Can adhering strictly to the Single Responsibility Principle in large distributed systems increase overall system complexity and make maintenance more difficult? (True/False)

**Answer:** **True**

### Explanation

While the Single Responsibility Principle (SRP) improves modularity and maintainability, overusing it in distributed systems can create an excessive number of microservices. This increases deployment complexity, network communication, latency, monitoring overhead, service discovery challenges, and distributed transaction management.

The goal is to find the right balance between modularity and operational simplicity.

---

## 3. As an AWS and DevOps Senior Consultant, design a secure, scalable, and highly available architecture for a global SaaS product.

### Architecture

```
Users
   │
CloudFront
   │
AWS WAF
   │
Application Load Balancer
   │
Amazon EKS Cluster
   │
Microservices
   ├── Amazon RDS Multi-AZ
   ├── Amazon ElastiCache (Redis)
   └── Amazon S3
   │
CloudWatch + Prometheus + Grafana
   │
Route 53 (Latency-Based Routing)
```

### Security

* IAM Roles
* Security Groups
* Network ACLs
* AWS Secrets Manager
* AWS KMS Encryption
* HTTPS Everywhere
* Private Subnets
* AWS Systems Manager Session Manager (or Bastion Host)

### Scalability

* Horizontal Pod Autoscaler (HPA)
* Cluster Autoscaler
* Auto Scaling Groups
* Application Load Balancer

### High Availability

* Multi-AZ Deployment
* Cross-Region Disaster Recovery
* Route 53 Failover Routing
* RDS Read Replicas
* Automated Backups and Snapshots

---

## 4. How would you structure the failover process during a regional outage?

### Architecture

```
Primary Region
      │
Route53 Health Check
      │
Region Failure Detected
      │
Traffic Redirected
      │
Secondary Region
      │
Database Replica Promotion
      │
Application Becomes Active
```

### Key Components

* Route 53 Failover Routing
* Cross-Region RDS Replication
* S3 Cross-Region Replication
* Infrastructure Provisioning using Terraform
* Automated DNS Switching
* Continuous Backup Strategy

This minimizes Recovery Time Objective (RTO) and Recovery Point Objective (RPO).

---

## 5. Your team is experiencing frequent production outages due to inconsistent environments and manual deployments. What DevOps strategy would you implement?

### Solution

Implement a complete DevOps transformation:

* Infrastructure as Code using Terraform
* Docker for consistent environments
* Kubernetes for orchestration
* Jenkins CI/CD Pipelines
* Git Branching Strategy
* Automated Unit & Integration Testing
* Blue-Green or Canary Deployments
* Configuration Management
* Continuous Monitoring with Prometheus and Grafana
* Automated Rollback Strategy

This eliminates configuration drift, reduces manual intervention, and improves deployment reliability.

---

## 6. How would you handle resistance from team members while adopting DevOps tools and practices?

### Answer

I would:

* Listen to team concerns.
* Explain the business value of DevOps.
* Start with a small pilot project.
* Provide hands-on training.
* Create proper documentation.
* Gradually introduce automation.
* Share measurable improvements such as reduced deployment time and fewer failures.
* Encourage continuous feedback.

Successful DevOps adoption is driven by collaboration and culture rather than tools alone.

---

## 7. Design an end-to-end automated deployment solution for multiple environments.

### CI/CD Flow

```
Developer
    │
Git Push
    │
Jenkins Pipeline
    │
Build Application
    │
Unit Tests
    │
SonarQube Scan
    │
Dependency Security Scan
    │
Docker Image Build
    │
Push Image to Registry
    │
Terraform Infrastructure
    │
Deploy to DEV
    │
Automated Tests
    │
Approval Gate
    │
Deploy to UAT
    │
Approval Gate
    │
Deploy to PROD
    │
Blue-Green Deployment
    │
Smoke Testing
    │
Monitoring
```

Use separate configurations for each environment through ConfigMaps, Secrets, Helm values, and Terraform Workspaces.

---

## 8. How would you measure the success of your automation initiative?

### DORA Metrics

* Deployment Frequency
* Lead Time for Changes
* Change Failure Rate
* Mean Time to Recovery (MTTR)

### Additional KPIs

* Deployment Success Rate
* Pipeline Execution Time
* Manual Effort Reduction
* Infrastructure Provisioning Time
* Rollback Frequency
* Number of Production Incidents
* Mean Time to Detect (MTTD)

---

## 9. How do Jenkins, Docker, Kubernetes, Terraform, Prometheus, and Grafana work together in a complete CI/CD pipeline?

### Workflow

```
Developer
    │
GitHub
    │
Jenkins
    │
Build & Test
    │
SonarQube
    │
Docker Build
    │
Push to Container Registry
    │
Terraform Creates Infrastructure
    │
Deploy to Kubernetes
    │
Prometheus Collects Metrics
    │
Grafana Dashboards & Alerts
```

### Tool Responsibilities

* **Jenkins:** CI/CD Automation
* **Docker:** Containerization
* **Terraform:** Infrastructure Provisioning
* **Kubernetes:** Container Orchestration
* **Prometheus:** Metrics Collection
* **Grafana:** Visualization and Alerting

---

## 10. Design the architecture of a mission-critical platform that must scale rapidly and integrate with third-party APIs.

### Architecture

```
CloudFront
      │
AWS WAF
      │
Application Load Balancer
      │
Amazon EKS
      │
API Gateway
      │
Microservices
      ├── Amazon SQS
      ├── Amazon SNS
      ├── Redis Cache
      ├── Amazon RDS
      └── Amazon S3
      │
CloudWatch
      │
Prometheus
      │
Grafana
```

### Best Practices

* Circuit Breaker Pattern
* Retry with Exponential Backoff
* API Rate Limiting
* Dead Letter Queues
* Timeout Configuration
* API Versioning

---

## 11. How would you manage data consistency and transactions across microservices deployed in multiple Availability Zones?

### Solution

Avoid distributed database transactions.

Instead use:

* Saga Pattern
* Event-Driven Architecture
* Amazon EventBridge or Kafka
* Idempotent APIs
* Retry Mechanism
* Dead Letter Queues
* Eventual Consistency
* Distributed Tracing

Each microservice should own its own database.

---

## 12. A cloud-based e-commerce application experiences unpredictable traffic spikes. How would you ensure responsiveness and reliability?

### Solution

* Application Load Balancer
* Auto Scaling Groups
* Horizontal Pod Autoscaler (HPA)
* Cluster Autoscaler
* Amazon CloudFront CDN
* Amazon ElastiCache (Redis)
* RDS Read Replicas
* Connection Pooling
* Amazon SQS for asynchronous workloads
* Circuit Breaker Pattern
* Rate Limiting
* Multi-AZ Deployment
* Continuous Monitoring with CloudWatch and Prometheus

---

## 13. Which Amazon CloudWatch metrics and alarms would you configure to detect performance bottlenecks during high-traffic periods?

### EC2 Metrics

* CPUUtilization
* MemoryUtilization (CloudWatch Agent)
* DiskReadOps
* DiskWriteOps
* DiskQueueLength
* NetworkIn
* NetworkOut
* StatusCheckFailed

### Application Load Balancer Metrics

* RequestCount
* TargetResponseTime
* HTTPCode_ELB_5XX_Count
* HTTPCode_Target_5XX_Count
* HealthyHostCount
* UnHealthyHostCount

### Amazon RDS Metrics

* CPUUtilization
* DatabaseConnections
* FreeStorageSpace
* ReadLatency
* WriteLatency
* ReadIOPS
* WriteIOPS
* ReplicaLag

### Amazon EKS / Kubernetes Metrics

* Pod CPU Usage
* Pod Memory Usage
* Pod Restarts
* Pending Pods
* Node CPU Utilization
* Node Memory Utilization
* Node Disk Pressure

### Auto Scaling Metrics

* Desired Capacity
* In-Service Instances
* Pending Instances
* Scaling Activities

### CloudWatch Alarms

Configure alarms for:

* CPU Utilization > 80%
* Memory Utilization > 80%
* ALB Response Time > 2 seconds
* HTTP 5XX Errors > Defined Threshold
* RDS CPU > 75%
* Replica Lag > Threshold
* Unhealthy Targets > 0
* Pod Restarts Increasing
* Disk Utilization > 80%
* Network Saturation
* Failed EC2 Status Checks

Integrate CloudWatch Alarms with Amazon SNS to send notifications through email, SMS, or incident management platforms such as PagerDuty or Slack for proactive monitoring.



# INFOSYS - First Round DevOps Interview Questions (4 Years Experience)

---

## 1. Customer is unable to access the application, but he has the correct credentials. How will you debug it?

### Answer

I would troubleshoot layer by layer. First, I'd verify whether the application is up and healthy. Then I'd check the authentication service logs for login failures, validate the database connection, and confirm the user's account isn't locked or disabled. Next, I'd verify IAM/LDAP/OAuth integration if used, check application and Ingress logs for authentication errors, and inspect browser/network logs. Finally, I'd reproduce the issue in a test environment to identify the root cause before implementing a fix.

---

## 2. How do you replace a string in a file in Linux?

### Answer

I use the **sed** command for in-place replacement.

```bash
sed -i 's/old_string/new_string/g' filename
```

The `-i` option updates the file directly, and `g` replaces all occurrences of the string.

---

## 3. Your pod is getting stuck in CrashLoopBackOff, but logs show no error. How will you debug the issue?

### Answer

If logs don't show any errors, I first run `kubectl describe pod` to check Events for probe failures, OOMKilled, or image issues. Then I check the **previous container logs** using `kubectl logs --previous`, verify resource limits, ConfigMaps, Secrets, mounted volumes, and environment variables. I also check node health and kubelet logs if required. In many cases, the container exits before generating logs, so Pod Events provide the actual reason.

---

## 4. Why is the Cluster Autoscaler not scaling up even though pods are in the Pending state?

### Answer

First, I'd verify whether the Pending Pods are unschedulable due to insufficient CPU or memory. Then I'd check whether the Cluster Autoscaler is running correctly, review its logs, and ensure the Auto Scaling Group has not reached its maximum node limit. I'd also verify node selectors, taints, tolerations, resource quotas, and IAM permissions because these can prevent scaling even when Pods remain Pending.

---

## 5. What is the difference between HPA and VPA?

### Answer

**HPA (Horizontal Pod Autoscaler)** scales the **number of Pods** based on metrics like CPU or memory utilization, whereas **VPA (Vertical Pod Autoscaler)** increases or decreases the **CPU and memory allocated to an individual Pod**. HPA is generally used for stateless applications to handle traffic spikes, while VPA is more suitable for applications requiring dynamic resource allocation.

---

## 6. During peak traffic, your Ingress Controller fails to route requests efficiently. How will you diagnose the issue?

### Answer

I would first check the Ingress Controller Pods for CPU and memory utilization and verify whether they are overloaded. Then I'd inspect the Ingress Controller logs for routing errors, validate Ingress rules, backend Services, and Endpoints, and ensure all backend Pods are healthy. I'd also check the Load Balancer health checks, network latency, and scaling configuration. If required, I'd increase Ingress Controller replicas using HPA to handle the traffic.

---

## 7. How will you resolve merge conflicts in Git?

### Answer

First, I'd pull the latest changes from the target branch and identify the conflicting files. Then I'd manually resolve the conflicts by reviewing both versions of the code, remove the conflict markers, test the application, stage the resolved files using `git add`, and complete the merge with `git commit`. Finally, I'd push the updated branch and create or update the Merge Request.

---

## 8. Explain how a matrix build works in GitHub Actions.

### Answer

A matrix build allows the same workflow to run multiple jobs in parallel using different configurations. For example, the application can be tested simultaneously on multiple operating systems, programming language versions, or environments. This reduces overall execution time and ensures compatibility across different platforms without duplicating workflow code.

---

## 9. How do you import an existing VPC into Terraform?

### Answer

First, I write the Terraform resource block for the existing VPC. Then I use the `terraform import` command to associate the existing AWS VPC with the Terraform state file.

```bash
terraform import aws_vpc.main vpc-xxxxxxxx
```

After importing, I run `terraform plan` and update the Terraform configuration so it matches the actual AWS resource, ensuring there are no unexpected changes.

---

## 10. How do you integrate Jenkins with a Kubernetes cluster?

### Answer

Jenkins can be integrated with Kubernetes by installing the **Kubernetes Plugin**. The plugin connects Jenkins to the Kubernetes API using a kubeconfig file or Service Account credentials. Jenkins dynamically creates Kubernetes Pods as build agents, executes the pipeline inside those Pods, and automatically deletes them after the build completes. This provides better scalability and efficient resource utilization.

---

## 11. How can you communicate with a Jenkins server running on a Kubernetes cluster?

### Answer

Jenkins can be accessed using a Kubernetes **Service** such as NodePort, LoadBalancer, or Ingress. In production, we usually expose Jenkins through an Ingress with HTTPS enabled. Internally, other Pods communicate with Jenkins using its Kubernetes Service DNS name, while external users access it through the Ingress URL.

---

## 12. Write a Jenkins Scripted Pipeline.

### Answer

```groovy
node {

    stage('Checkout') {
        git 'https://github.com/example/demo.git'
    }

    stage('Build') {
        sh 'mvn clean package'
    }

    stage('Test') {
        sh 'mvn test'
    }

    stage('Deploy') {
        sh 'kubectl apply -f deployment.yaml'
    }
}
```

This Scripted Pipeline checks out the source code, builds the application, runs tests, and deploys it to Kubernetes.

---

## 13. Write a shell script that checks CPU, memory, and disk utilization, and alerts if any of them exceed 80%.

### Answer

```bash
#!/bin/bash

CPU=$(top -bn1 | grep "Cpu(s)" | awk '{print int($2+$4)}')

MEMORY=$(free | awk '/Mem:/ {print int($3/$2 * 100)}')

DISK=$(df -h / | awk 'NR==2 {gsub("%",""); print $5}')

if [ $CPU -gt 80 ]; then
    echo "ALERT: CPU usage is ${CPU}%"
fi

if [ $MEMORY -gt 80 ]; then
    echo "ALERT: Memory usage is ${MEMORY}%"
fi

if [ $DISK -gt 80 ]; then
    echo "ALERT: Disk usage is ${DISK}%"
fi
```

This script checks CPU, memory, and disk utilization and prints an alert whenever any resource crosses the 80% threshold. In production, these alerts can be integrated with email, Slack, or monitoring tools for automated notifications.

---


# Infosys DevOps Interview Questions & Answers (4+ Years Experience)

## Q1. How would you approach migrating a monolithic application to a microservices architecture? What steps would you follow, and what key challenges might you encounter during the migration process?

Migrating a monolithic application to microservices is a gradual process and should not be done in a single release. My approach starts with understanding the existing application architecture, business domains, dependencies, database interactions, and traffic patterns. I first identify bounded contexts and separate the application into logical services such as user management, payment, notification, authentication, and reporting. Instead of rewriting everything at once, I follow the Strangler Fig Pattern where new functionality is developed as microservices while existing functionality continues to run in the monolith.

The next step is containerization using Docker and deploying services on Kubernetes or EKS. API Gateway and Ingress are introduced to manage routing, authentication, and traffic control. CI/CD pipelines are created for each microservice to enable independent deployments. Monitoring, logging, and tracing are implemented using Prometheus, Grafana, ELK, and distributed tracing tools. Database decomposition is usually the most challenging part because monoliths often share a single database. During migration, data ownership, consistency, service communication, and distributed transactions become major concerns. Other challenges include increased operational complexity, network latency, service discovery, observability, and security management. A phased migration strategy with proper testing and monitoring minimizes risk and downtime.

---

## Q2. After deploying an application, it becomes slow. How would you troubleshoot the issue and rectify it?

When an application becomes slow after deployment, my first objective is to determine whether the issue is related to infrastructure, application code, database performance, networking, or external dependencies. I start by checking monitoring dashboards such as Grafana and CloudWatch to identify spikes in CPU, memory, disk I/O, network utilization, response times, and error rates. Next, I review deployment changes and compare them with the previous stable release.

I analyze application logs, pod logs, and APM metrics to identify bottlenecks. If the application runs on Kubernetes, I verify pod health, resource requests, limits, HPA behavior, and node utilization. Database performance is checked for slow queries, connection pool exhaustion, locks, and latency. If resource utilization is normal but response times have increased, I investigate code changes, third-party API dependencies, and caching behavior. Depending on the findings, corrective actions may include scaling resources, rolling back the deployment, tuning database queries, adjusting resource limits, fixing application code, or optimizing caching layers. Once the issue is resolved, I perform a root cause analysis and implement preventive measures to avoid recurrence.

---

## Q3. What happens if the Terraform state file stored in an Amazon S3 bucket is accidentally deleted? How would you recover it and prevent such incidents in the future?

The Terraform state file is the source of truth that maps Terraform-managed resources to actual cloud infrastructure. If the state file stored in S3 is accidentally deleted, Terraform loses track of existing resources, and future deployments may attempt to recreate or modify resources incorrectly.

In production environments, I always enable S3 versioning for Terraform state storage. If the state file is deleted, I recover it by restoring the most recent version from S3 version history. After restoration, I verify the integrity of the state file and perform a terraform plan to ensure consistency. If versioning was not enabled, recovery becomes more complex and may require rebuilding the state using terraform import commands.

To prevent such incidents, I enable S3 versioning, MFA Delete where applicable, KMS encryption, IAM least-privilege access, and CloudTrail auditing. DynamoDB state locking is also configured to prevent concurrent modifications. Regular state backups and restricted access policies significantly reduce the risk of accidental deletion.

---

## Q4. What Jenkins strategy and pipeline type did you use?

In my current project, I primarily use Jenkins Declarative Pipelines because they are easier to maintain, standardize, and review across teams. The pipeline is defined as code using Jenkinsfiles stored in Git repositories. A typical pipeline includes stages such as Source Code Checkout, Build, Unit Testing, SonarQube Analysis, Security Scanning, Docker Image Build, Image Push to ECR, Terraform Validation, Deployment to Kubernetes, Smoke Testing, and Notifications.

For execution, I use a Master-Agent architecture where build workloads run on dedicated Jenkins agents instead of the controller. Agents are dynamically provisioned when required to improve scalability and resource utilization. Shared Libraries are used to standardize reusable pipeline logic across multiple projects. For production deployments, approval gates are included before release stages. This approach improves maintainability, consistency, and governance across CI/CD workflows.

---

## Q5. What Kubernetes deployment strategy did you use in your project?

The primary deployment strategy used in my project is Rolling Update because it provides zero downtime while gradually replacing old application versions with new ones. Kubernetes ensures that a minimum number of healthy pods remain available throughout the deployment process. Readiness Probes prevent traffic from reaching pods until they are fully initialized and healthy.

For business-critical services such as payment or customer-facing applications, we also use Blue-Green and Canary deployment strategies when additional risk mitigation is required. Blue-Green deployment creates a parallel environment and shifts traffic only after validation. Canary deployment releases changes to a small percentage of users before full rollout. These strategies reduce deployment risk and allow rapid rollback if issues are detected. The choice depends on application criticality, release risk, and business requirements.

---

## Q6. What is immutable infrastructure in Terraform? How does Terraform support the concept of immutability?

Immutable infrastructure is a practice where existing infrastructure is not modified after deployment. Instead of updating resources in place, new infrastructure is created and old infrastructure is replaced. This eliminates configuration drift, ensures consistency across environments, and improves deployment reliability.

Terraform supports immutability through its declarative approach and lifecycle management capabilities. Features such as create_before_destroy allow Terraform to provision replacement resources before removing existing ones, minimizing downtime. For example, instead of modifying an EC2 instance directly, Terraform can create a new instance with updated configuration and then terminate the old one. This approach ensures predictable deployments, easier rollbacks, and improved infrastructure consistency. Immutable infrastructure is commonly combined with Auto Scaling Groups, Launch Templates, and containerized workloads to achieve highly reliable deployments.

---

## Q7. Do you maintain a single CI/CD pipeline for all environments (Development, SIT, UAT, and Production), or do you use separate pipelines? How do you manage environment-specific configurations and deployments?

In my projects, I generally maintain a single reusable CI/CD pipeline with environment-specific configurations rather than creating completely separate pipelines for each environment. The pipeline logic remains the same, but deployment behavior changes based on parameters, environment variables, configuration files, and secrets.

For example, Kubernetes deployments use different values files for Development, SIT, UAT, and Production environments. Terraform uses separate backend configurations and variable files for each environment. Secrets are stored securely in AWS Secrets Manager, HashiCorp Vault, or Kubernetes Secrets rather than being hardcoded in the pipeline. Deployment approvals are required only for higher environments such as UAT and Production.

This approach reduces duplication, improves maintainability, ensures consistency across environments, and simplifies governance. At the same time, each environment remains isolated through separate infrastructure, state files, namespaces, accounts, and access controls.



# Infosys DevOps Interview Questions & Answers (4+ Years Experience)

# 1. An application hosted behind an ALB is returning 503 errors. How would you troubleshoot the issue?

When an Application Load Balancer returns HTTP 503 errors, it usually means the load balancer cannot find any healthy backend targets to forward requests to. My first step is checking the Target Group health status in AWS. If targets are marked unhealthy, I review the configured health check path, response codes, timeout values, and intervals. I then verify whether EC2 instances, ECS tasks, or Kubernetes pods are actually listening on the expected ports. Next, I inspect Security Groups, NACLs, and routing rules to ensure traffic can reach the backend. I also review application logs and CloudWatch metrics to identify application-level failures. If the issue started after a deployment, I verify the latest release and perform a rollback if necessary. My troubleshooting flow is ALB → Target Group → Backend Service → Application Logs → Network Configuration.

---

# 2. Users are reporting slow application performance. Which AWS services and metrics would you check first?

I begin by checking Amazon CloudWatch because it provides visibility into infrastructure and application performance. I review EC2 CPU utilization, memory usage, disk I/O, network throughput, and load balancer latency. For databases, I inspect RDS metrics such as CPU utilization, connections, free memory, read latency, and write latency. If the application runs on EKS, I analyze pod resource utilization and HPA activity. I also examine ALB Target Response Time and HTTP error rates. If infrastructure metrics appear normal, I move to application monitoring tools such as Prometheus, Grafana, or APM solutions to identify slow database queries, external API latency, thread contention, or inefficient code paths.

---

# 3. An Auto Scaling Group is not launching new instances during traffic spikes. How would you investigate?

I first verify whether scaling policies are configured correctly. Then I review CloudWatch metrics to determine whether scaling thresholds are being breached. Next, I inspect scaling activities within the Auto Scaling Group to identify failed launch attempts. Common issues include insufficient subnet IP addresses, EC2 capacity shortages, invalid launch templates, IAM permission problems, or reaching AWS service limits. I also verify whether cooldown periods are delaying scaling events. If scaling policies are based on custom metrics, I ensure those metrics are being published correctly. Finally, I review AWS CloudTrail and ASG activity history to determine the exact reason scaling did not occur.

---

# 4. An RDS database becomes unavailable during business hours. What would be your recovery approach?

The first priority is minimizing business impact. I immediately check RDS events and CloudWatch metrics to determine whether the issue is caused by resource exhaustion, storage limitations, network connectivity, failover events, or database engine problems. If Multi-AZ is enabled, I verify whether automatic failover has occurred successfully. If the primary database is unavailable, I initiate recovery procedures using snapshots, read replicas, or failover mechanisms depending on the architecture. Simultaneously, I communicate incident status to stakeholders and application teams. Once service is restored, I perform root cause analysis and implement preventive measures such as performance tuning, scaling, monitoring improvements, or architecture enhancements.

---

# 5. How would you perform a zero-downtime deployment in AWS?

For zero-downtime deployments, I typically use Blue-Green Deployment or Rolling Deployment strategies. In a Blue-Green approach, a new environment is deployed alongside the existing production environment. After validating functionality, traffic is switched using Route 53 or Load Balancer routing rules. In Kubernetes environments, I use rolling updates with proper readiness probes, PodDisruptionBudgets, and multiple replicas. During deployment, healthy instances continue serving traffic while new instances gradually replace old ones. If issues are detected, rollback can be performed immediately by redirecting traffic to the previous version. This approach minimizes user impact and deployment risk.

---

# 6. A production deployment caused downtime. How would you perform rollback and identify the root cause?

When a deployment causes downtime, my first objective is service restoration. If the deployment introduced the issue, I immediately perform a rollback using ArgoCD, Helm, Kubernetes rollout undo, Jenkins pipeline rollback, or previous container images depending on the deployment platform. Once service is restored, I analyze deployment logs, application logs, monitoring dashboards, configuration changes, infrastructure modifications, and release notes. I compare the working version with the failed version to identify differences. Finally, I document findings through a Root Cause Analysis report and implement preventive controls such as deployment validation checks, canary releases, automated testing, or enhanced monitoring.

---

# 7. Monitoring alerts show increased response time but infrastructure appears healthy. How would you investigate?

If infrastructure metrics look healthy, the issue is likely occurring at the application layer. I begin by reviewing application response time metrics, error rates, database query performance, cache hit ratios, external API dependencies, thread pool utilization, and application logs. I also inspect distributed tracing data if available. Many latency issues are caused by inefficient queries, dependency bottlenecks, application-level memory pressure, or third-party service delays. My approach is to trace the complete request path and identify where latency is being introduced rather than focusing solely on infrastructure health.

---

# 8. Disk utilization on production servers reaches 95%. What immediate and long-term actions would you take?

When disk utilization reaches 95%, immediate action is required because applications may soon fail to write data. I first identify the largest consumers using commands such as du, df, find, and log analysis tools. Temporary files, old logs, container images, and backup files are common causes. If necessary, I archive or remove unnecessary data to free space. Long-term improvements include implementing log rotation, monitoring disk growth trends, expanding storage volumes, enforcing retention policies, and automating cleanup procedures. I also configure alerts so the team is notified before disk utilization reaches critical levels.

---

# 9. A sudden traffic spike causes application degradation. How would you stabilize the platform?

The first step is understanding whether the platform is scaling correctly. I check load balancer metrics, application metrics, pod utilization, Auto Scaling Groups, HPA behavior, database performance, and cache effectiveness. If capacity is insufficient, I scale horizontally by increasing pod replicas or adding infrastructure resources. I also verify rate limiting, caching mechanisms, CDN performance, and queue processing systems. If the traffic spike is legitimate, scaling and optimization are prioritized. If it appears malicious, such as a DDoS attack, I activate AWS WAF protections, rate limiting policies, and security controls. Throughout the incident, I continuously monitor recovery metrics and communicate status updates to stakeholders.

# 10. The Jenkins master is running out of disk space. What actions would you take?

When Jenkins starts running out of disk space, my first priority is preventing build failures and service disruption. I begin by identifying what is consuming storage using operating system commands such as df, du, and find. In most cases, old build artifacts, workspace directories, archived logs, Docker images, and temporary files are responsible for excessive disk usage.

As an immediate action, I clean unused workspaces, remove old build histories, delete unnecessary artifacts, clear temporary files, and prune unused Docker images. If Jenkins is hosted on EC2, I verify whether the underlying EBS volume can be expanded safely.

For long-term prevention, I configure build retention policies, artifact expiration policies, external artifact repositories such as Nexus or Artifactory, automated workspace cleanup, monitoring alerts, and storage utilization dashboards. This ensures Jenkins remains stable and scalable over time.

---

# 11. Build artifacts are not getting uploaded to the artifact repository. How would you investigate?

I start by reviewing the pipeline logs to identify the exact stage where the upload fails. The most common causes are authentication failures, incorrect repository URLs, expired credentials, network connectivity issues, insufficient permissions, or storage limitations on the repository server.

I verify that the generated artifact exists and matches the expected file path. Next, I validate connectivity between Jenkins and the artifact repository using curl or network diagnostics. I check repository credentials stored in Jenkins credentials management and confirm that permissions allow artifact uploads.

If everything appears correct, I inspect repository-side logs in Nexus or Artifactory to determine whether uploads are being rejected. Once the root cause is identified, I rerun the pipeline and validate successful artifact publication.

---

# 12. A Docker container keeps restarting in production. How would you identify the root cause?

When a Docker container continuously restarts, I first inspect container logs because application failures are the most common cause. I review startup logs, runtime exceptions, dependency failures, configuration issues, and connectivity errors.

Next, I check the container exit code using Docker inspection commands. Exit codes often indicate whether the container failed because of application crashes, out-of-memory conditions, permission issues, or invalid startup commands.

I also review resource utilization metrics such as CPU and memory consumption. Containers frequently restart because memory limits are too low, causing OOM kills. If the application depends on databases, APIs, or external services, I verify connectivity and authentication. My goal is to identify whether the issue originates from the application, configuration, infrastructure, or dependency layer.

---

# 13. Docker image size has grown from 300MB to 3GB. How would you optimize it?

A sudden increase in image size usually indicates unnecessary dependencies, build artifacts, temporary files, or large base images. I start by reviewing the Dockerfile and identifying layers contributing the most storage consumption.

The first optimization is selecting a smaller base image such as Alpine or Distroless when appropriate. I then implement multi-stage builds so that compilation tools remain in the build stage while only runtime components are included in the final image.

I remove unnecessary packages, caches, package manager metadata, temporary files, and development dependencies. I also consolidate Dockerfile layers where possible to reduce image overhead. Finally, I scan the image using tools such as Docker Scout or Trivy to identify unnecessary content. These optimizations significantly reduce image size and improve deployment speed.

---

# 14. A container works perfectly on a developer machine but fails in Kubernetes. What would you check?

The first step is understanding that local Docker execution differs significantly from Kubernetes environments. I begin by reviewing pod logs and Kubernetes events to identify startup errors.

I verify environment variables, ConfigMaps, Secrets, mounted volumes, resource limits, network policies, DNS resolution, service discovery, and image versions. Many applications work locally because developers use local configurations that are missing in Kubernetes.

I also inspect readiness probes, liveness probes, service accounts, RBAC permissions, and dependency connectivity. If the application communicates with external services, I verify DNS resolution and network accessibility within the cluster. The objective is identifying differences between the developer environment and the Kubernetes environment.

---

# 15. How would you recover if the Docker daemon becomes unavailable on a production server?

The first step is confirming whether the Docker daemon process has stopped or become unresponsive. I inspect Docker service status, system logs, and daemon logs to determine the cause.

Common reasons include resource exhaustion, disk space issues, corrupted Docker metadata, failed upgrades, or operating system problems. If the issue is isolated to the Docker service, I restart the daemon and verify container recovery.

If the underlying server is unhealthy, I fail workloads over to healthy nodes or instances depending on the architecture. In highly available environments such as Kubernetes or ECS, workloads are automatically rescheduled onto healthy nodes. After recovery, I investigate the root cause and implement preventive monitoring and capacity planning measures.

---

# 16. A pod is stuck in CrashLoopBackOff. How would you troubleshoot it?

CrashLoopBackOff indicates Kubernetes is repeatedly attempting to restart a failing container. My first step is reviewing pod logs to identify application-level errors. Next, I inspect pod events to determine whether failures are caused by image issues, probe failures, configuration errors, missing secrets, insufficient resources, or dependency connectivity problems.

I verify resource requests and limits, ConfigMaps, Secrets, startup commands, environment variables, and external service availability. If the application depends on databases or APIs, I test connectivity from inside the cluster.

Once the root cause is identified, I apply the necessary fix and monitor the pod until it stabilizes successfully.

---

# 17. A deployment rollout is stuck and new pods are not becoming ready. What steps would you follow?

If a rollout becomes stuck, I first examine the deployment status and pod events. The most common reason is readiness probes failing. I review readiness probe configurations, application startup times, resource availability, and dependency connectivity.

Next, I inspect pod logs to identify application startup failures. I verify ConfigMaps, Secrets, mounted volumes, database access, and external API connectivity. If resource constraints exist, I check whether pods can be scheduled successfully and whether nodes have sufficient capacity.

If the rollout cannot proceed safely, I perform a rollback to restore service. After stabilization, I analyze the failed deployment and implement corrective actions before attempting another rollout.

---

# 18. Users cannot access an application exposed via Ingress. How would you debug the issue?

I troubleshoot the request path from the user to the application. First, I verify DNS resolution to ensure the hostname points to the correct load balancer or ingress endpoint.

Next, I inspect the Ingress resource configuration, routing rules, TLS settings, and annotations. I verify that the Ingress Controller is running correctly and examine controller logs for routing errors.

I then validate Service configuration, endpoint population, and pod readiness. If Services have no endpoints, traffic cannot reach the application even though the Ingress is configured correctly. By tracing the complete path—DNS → Load Balancer → Ingress → Service → Pod—I can identify the exact failure point.

---

# 19. One worker node becomes NotReady. What actions would you take?

When a node enters the NotReady state, I first determine whether the issue is temporary or persistent. I inspect node conditions, kubelet status, operating system logs, and resource utilization.

Common causes include network failures, kubelet crashes, disk pressure, memory pressure, CPU exhaustion, or cloud infrastructure issues. If workloads are affected, Kubernetes typically reschedules pods onto healthy nodes automatically.

If the node cannot recover quickly, I cordon and drain it to prevent additional workloads from being scheduled. After troubleshooting and remediation, I return the node to service or replace it entirely depending on the severity of the issue.

---

# 20. etcd becomes unavailable in a production cluster. What is your recovery strategy?

etcd is the source of truth for Kubernetes cluster state, making it one of the most critical components. If etcd becomes unavailable, cluster operations may stop because the API Server cannot retrieve or update cluster information.

My first step is determining whether the issue affects a single etcd member or the entire cluster. If quorum still exists, Kubernetes may continue functioning while recovery is performed. I inspect etcd logs, storage health, networking, and resource utilization.

If recovery is not possible through member restoration, I restore etcd from a recent backup snapshot. After recovery, I validate API Server functionality, cluster health, workloads, and data consistency. Regular etcd backups are essential because they significantly reduce recovery time during critical incidents.

---


# 21. Pods are getting evicted frequently due to resource pressure. How would you prevent this?

Frequent pod evictions usually indicate that worker nodes are running out of resources such as memory, CPU, ephemeral storage, or disk space. My first step is identifying the specific eviction reason using pod events and node conditions. Common messages include MemoryPressure, DiskPressure, PIDPressure, or EphemeralStoragePressure.

Once the root cause is identified, I review resource requests and limits configured for workloads. Many organizations either under-allocate resources or completely skip defining requests and limits, causing scheduling and resource contention issues. I analyze historical utilization metrics using Prometheus and Grafana to determine actual workload requirements.

To prevent future evictions, I implement proper resource requests and limits, enable Horizontal Pod Autoscaler for workload scaling, and configure Cluster Autoscaler to add worker nodes during resource shortages. I also monitor node utilization proactively and establish alerts before resource pressure reaches critical levels. In production environments, capacity planning and continuous monitoring are essential for avoiding repeated pod evictions.

---

# 22. A service is running but application requests are timing out. How would you troubleshoot networking issues?

When requests are timing out, I troubleshoot the entire network path rather than focusing on a single component. My approach starts from the application and moves outward through each networking layer.

First, I verify that the application is actually listening on the expected port and responding correctly. Next, I check Kubernetes Service configuration, Service selectors, and Endpoint objects to confirm traffic is reaching the correct pods.

I then review Network Policies, Security Groups, NACLs, Ingress configurations, Load Balancer health checks, DNS resolution, and routing rules. If the application communicates with databases or external APIs, I verify outbound connectivity and latency.

I also perform connectivity tests from inside the cluster using tools such as curl, wget, nslookup, dig, traceroute, and netcat. Throughout the investigation, I analyze application logs, ingress controller logs, and network metrics to identify where packets are being dropped or delayed. This layered approach helps isolate the exact networking bottleneck.

---

# 23. Terraform state file is accidentally deleted. How would you recover?

If a Terraform state file is deleted, my first action is stopping all Terraform deployments immediately to prevent additional inconsistencies. The next step depends on the backend configuration.

In production environments, Terraform state files are typically stored in S3 with versioning enabled. I access the S3 bucket and restore the most recent valid version of the state file. After restoration, I verify the integrity of the recovered state and compare it against the actual infrastructure.

I then execute terraform plan to identify any drift between the restored state and existing resources. If certain resources are missing from the state, I use terraform import to bring them back under Terraform management.

Once the environment is stabilized, I investigate why the state file was deleted and implement preventive controls such as access restrictions, backup validation, versioning enforcement, and stronger governance policies.

---

# 24. A Terraform apply fails midway after creating some resources. What would be your next steps?

When Terraform apply fails midway, some resources may already exist while others were never created. My first step is carefully reviewing the error output to understand exactly where the deployment failed.

Next, I inspect the Terraform state file and compare it with the actual infrastructure to determine whether partial changes were successfully recorded. I run terraform plan again to evaluate the current state of the environment.

In many cases, Terraform can continue from where it left off once the underlying issue is resolved. However, if resources were created manually or the state became inconsistent, I may need to use terraform import, state manipulation commands, or resource cleanup procedures.

The goal is never to delete everything and start over. Instead, I safely reconcile the infrastructure and state file while minimizing downtime and avoiding unintended resource destruction.

---

# 25. Multiple engineers are modifying infrastructure simultaneously. How would you handle state locking?

In team environments, state locking is critical to prevent concurrent infrastructure modifications. I use a remote backend such as AWS S3 for state storage and DynamoDB for state locking.

Before Terraform performs any operation, it attempts to acquire a lock in DynamoDB. If one engineer already holds the lock, additional users receive an error indicating the state is locked. This prevents race conditions and state corruption.

I also enforce deployment through CI/CD pipelines rather than allowing direct execution from personal workstations. This creates a controlled deployment process and reduces the risk of conflicting infrastructure changes.

If a lock becomes stuck because a deployment crashed, I first verify that no active deployment is running and then release the lock safely using terraform force-unlock.

---

# 26. A resource was manually changed in AWS but Terraform is showing drift. How would you resolve it?

Infrastructure drift occurs when the actual cloud infrastructure no longer matches the Terraform configuration. The first step is understanding whether the manual change was intentional or accidental.

If the change was approved and should remain, I update the Terraform code to reflect the new configuration. After updating the code, I execute terraform plan and verify that Terraform no longer attempts to revert the change.

If the resource was created manually outside Terraform, I use terraform import to bring it under Terraform management.

If the manual modification was unauthorized, I allow Terraform to restore the infrastructure to its desired state through terraform apply.

The key principle is ensuring Terraform remains the single source of truth for infrastructure management.

---

# 27. How would you safely deploy infrastructure changes to production using Terraform?

Production infrastructure deployments require a controlled and repeatable process. I never apply Terraform changes directly without validation.

The process begins with peer review of infrastructure code through pull requests. After approval, terraform fmt, terraform validate, security scanning, and policy checks are executed automatically.

Next, terraform plan generates an execution plan that is reviewed before approval. Deployments are typically performed through CI/CD pipelines rather than manually from developer machines.

For critical environments, manual approval gates are added before terraform apply. State locking, remote state storage, backups, monitoring, and rollback procedures are verified beforehand.

This approach minimizes deployment risk and provides full auditability for production infrastructure changes.

---

# 28. Kubernetes pods are healthy but users are receiving errors. How would you troubleshoot the complete request flow?

Healthy pods do not necessarily mean users can access the application successfully. I troubleshoot the complete request path from the user to the application.

The flow I investigate is:

User → DNS → Load Balancer → Ingress → Service → Endpoint → Pod → Application

First, I verify DNS resolution and ensure users are reaching the correct endpoint. Next, I check load balancer health, ingress rules, TLS configuration, and ingress controller logs.

I then validate Kubernetes Services and Endpoint objects to ensure traffic is routed correctly. If traffic reaches the pods successfully, I analyze application logs, database connectivity, external API dependencies, and application response codes.

By following the entire request flow systematically, I can identify whether the issue originates from networking, routing, infrastructure, or the application itself.

---

# 29. CI/CD pipelines are successful but application changes are not visible in production. What would you check?

When pipelines complete successfully but changes are not visible, I verify whether the deployment actually reached production.

First, I check the generated container image and confirm a new image version was built and pushed successfully. Next, I verify deployment logs from Jenkins, GitLab CI, ArgoCD, or the deployment platform.

I inspect Kubernetes Deployments, ReplicaSets, and running pods to confirm the new image is actually running. One common issue is using the latest image tag, which can result in stale images being reused.

I also review ArgoCD synchronization status, Helm release history, caching layers, CDN behavior, browser cache, and configuration updates. The investigation focuses on identifying where the deployment process stopped despite reporting success.

---

# 30. A sudden traffic spike causes application degradation. How would you stabilize the platform?

My first objective is stabilizing user experience and preventing complete service failure. I immediately review monitoring dashboards to identify bottlenecks in the application, infrastructure, database, and networking layers.

I verify whether Horizontal Pod Autoscaler and Cluster Autoscaler are functioning correctly. If capacity is insufficient, I scale application replicas and worker nodes. I also evaluate database performance, cache utilization, queue backlogs, and external dependencies.

If the traffic surge is legitimate, I optimize scaling policies and increase available resources. If the spike appears malicious, I activate AWS WAF protections, rate limiting rules, DDoS mitigation controls, and traffic filtering mechanisms.

Throughout the incident, I continuously monitor error rates, latency, throughput, and resource utilization while communicating updates to stakeholders. Once stability is restored, I perform a post-incident review and implement improvements to handle future traffic surges more effectively.
```

# 31. 𝗣𝗶𝗽𝗲𝗹𝗶𝗻𝗲 𝗽𝗮𝘀𝘀𝗲𝗱. 𝗕𝘂𝘁 𝗽𝗿𝗼𝗱𝘂𝗰𝘁𝗶𝗼𝗻 𝘀𝘁𝗶𝗹𝗹 𝗵𝗮𝘀 𝗼𝗹𝗱 𝗰𝗼𝗱𝗲. 𝗘𝘅𝗽𝗹𝗮𝗶𝗻 𝘄𝗵𝘆. 𝗬𝗼𝘂 𝗵𝗮𝘃𝗲 𝟮 𝗺𝗶𝗻𝘂𝘁𝗲𝘀.

► Deployment stage skipped — pipeline has test and build but deploy step is commented out or conditional.
  Check pipeline logs — did deploy stage actually run?

► Wrong environment — pipeline deployed to staging not production.
  Check environment variable in deploy script. Which env is set?

► CDN cache — CloudFront is serving old cached version to users.
  Fix: invalidate CloudFront cache after deployment.
  aws cloudfront create-invalidation --paths "/*"

► Health check rollback — new version failed health check silently.
  Load balancer rolled back to old version automatically.
  Check target group — which version is marked healthy?

► Wrong image tag — deployment used :latest but latest was not updated in ECR.
  Always use specific tags. Never use :latest in production.

► Blue-Green switch missed — new version deployed but traffic not switched to it.
  Check ALB listener rules — still pointing to old target group.

𝗛𝗼𝘄 𝗜 𝗺𝗮𝗸𝗲 𝗱𝗲𝗽𝗹𝗼𝘆𝗺𝗲𝗻𝘁𝘀 𝗯𝘂𝗹𝗹𝗲𝘁𝗽𝗿𝗼𝗼𝗳:
• Add smoke test step after deploy — verify new version is actually live

• Always use specific image tags — never :latest

• Add deployment verification in pipeline before marking success

• Alert on deployment events in CloudWatch
