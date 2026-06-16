# DevOps Interview Questions & Answers (4+ Years Experience)

## 1. Some of the applications are in AWS and some are on Azure, How can u achieve the communication between these 2 ?

In enterprise environments, applications are often distributed across multiple cloud providers for business continuity, compliance, or acquisition-related reasons. To enable secure communication between AWS and Azure workloads, I would first identify the traffic pattern and security requirements. The most common approach is establishing a Site-to-Site VPN between the AWS VPC and Azure Virtual Network. This creates encrypted communication over the internet and allows private IP communication between resources. For production workloads requiring low latency and high throughput, organizations typically use AWS Direct Connect and Azure ExpressRoute through a common network provider. If the applications are microservices exposed through APIs, secure HTTPS communication using API Gateways, Load Balancers, OAuth, JWT tokens, and TLS encryption may also be used. In production, I always ensure route tables, security groups, network security groups, firewalls, DNS resolution, and monitoring are configured properly to maintain secure and reliable cross-cloud communication.

---

## 2. My jobs should run on particular nodes, How can you do that in Jenkins?

In Jenkins, job execution can be controlled using node labels. Every Jenkins agent can be assigned one or more labels based on its purpose, such as docker-agent, terraform-agent, kubernetes-agent, windows-agent, or production-deployment-agent. In the Jenkins pipeline, I specify the required label in the agent section so that Jenkins schedules the build only on matching nodes. This is especially useful when specific tools, credentials, network access, or hardware resources are available only on certain agents. For example, infrastructure deployment jobs may run only on Terraform-enabled agents while application builds run on generic build agents. In production environments, this segregation improves security, resource utilization, and operational consistency.

---

## 3. What is the default port number of Jenkins, Is it changeable, If Yes How ?

The default Jenkins port is 8080. Yes, it can be changed based on organizational requirements. When Jenkins is installed as a service on Linux, the port can be modified in the Jenkins configuration file or service startup arguments. After updating the configuration, Jenkins must be restarted for the changes to take effect. In enterprise environments, Jenkins is commonly placed behind a reverse proxy such as Nginx or Apache or behind an Application Load Balancer. Users access Jenkins through ports 80 or 443 while Jenkins continues listening internally on port 8080. This approach provides SSL termination, security controls, and easier integration with enterprise authentication systems.

---

## 4. Is s3 can be used to install an application, If Yes How, If no why ?

Amazon S3 cannot directly install applications because it is an object storage service rather than a compute service. However, S3 is commonly used as a software distribution repository. Application binaries, deployment packages, shell scripts, configuration files, Docker images, and installation artifacts can be stored in S3. During EC2 launch, User Data scripts, Ansible playbooks, Jenkins pipelines, or cloud-init processes can download the required packages from S3 and perform installation automatically. In my projects, we often store deployment artifacts in S3 and automate installation through CI/CD pipelines. This approach provides centralized artifact storage, versioning, scalability, and high availability.

---

## 5. How can we take the backup the particular data in the resource like EC2, at some intervals ?

For EC2 instances, I generally use Amazon EBS snapshots to create backups at scheduled intervals. AWS Backup service can be configured to automate snapshot creation, retention policies, encryption, and lifecycle management. Backup schedules can be hourly, daily, weekly, or monthly depending on business requirements. For application-specific data, I may also use file-level backups to S3 using cron jobs, AWS DataSync, or backup agents. In production environments, I ensure backups are encrypted, replicated across regions if required, regularly tested for restoration, and aligned with Recovery Point Objective (RPO) and Recovery Time Objective (RTO) requirements.

---

## 6. Write a terraform script, to create an EC2 instance and run tomcat server on the it, but the script should run the tomcat server only ONCE when the EC2 launches.

In production, I would avoid provisioners and instead use EC2 User Data because User Data executes automatically during the first boot of the instance. Terraform provisions the EC2 instance and passes a bootstrap script through User Data. The script installs Java, downloads Tomcat, configures the service, and starts Tomcat. Since User Data runs only during initial launch, the installation occurs only once. This approach is more reliable, scalable, and aligned with Infrastructure as Code best practices than using remote-exec provisioners.

---

## 7. Explain the k8s deployment strategy used by your project ?

In my current project, we primarily use Rolling Updates because they provide zero downtime while minimizing infrastructure overhead. During deployment, Kubernetes gradually creates new Pods and removes old Pods based on the configured maxSurge and maxUnavailable values. For critical applications, we combine rolling updates with readiness probes, startup probes, Pod Disruption Budgets, monitoring validation, and automated rollback mechanisms. In some high-risk releases, we also use Blue-Green or Canary deployments through ArgoCD and Ingress routing to reduce deployment risk and validate changes before exposing them to all users.

---

## 8. Code works in one environment but fails in others due to what ? and How do you fix it ?

This issue is typically caused by environment drift. Common causes include differences in application configuration, environment variables, operating system versions, library dependencies, network connectivity, IAM permissions, DNS settings, database versions, Secrets, ConfigMaps, or resource allocations. My troubleshooting approach starts by comparing configurations across environments. I verify deployment manifests, environment variables, Helm values, application logs, infrastructure configurations, and external service dependencies. To prevent future occurrences, I standardize infrastructure using Terraform, containerize applications using Docker, manage deployments through Kubernetes manifests or Helm charts, and follow build-once-promote-everywhere principles to maintain consistency.

---

## 9. Explain some of the Security Vulnerabilities you faced in automation.

In automation pipelines, I have encountered vulnerabilities such as hardcoded credentials in repositories, excessive IAM permissions, exposed secrets in Jenkins logs, vulnerable Docker base images, outdated dependencies, unrestricted security group rules, and missing container image scanning. To mitigate these risks, we implemented secret management using AWS Secrets Manager and Vault, integrated SonarQube, Trivy, Aqua Security, and Snyk scans into CI/CD pipelines, enforced least-privilege IAM policies, enabled MFA, restricted network access, and established automated security gates that block deployments when critical vulnerabilities are detected.

---

## 10. Long build times slow down the development and deployment process , Why ? How to take care of this issue ?

Long build times reduce developer productivity, delay feedback loops, slow down releases, and increase infrastructure costs. Common causes include large codebases, inefficient Dockerfiles, repeated dependency downloads, slow test execution, serialized pipeline stages, and resource-constrained build agents. To optimize build performance, I use dependency caching, Docker layer caching, parallel execution, selective testing, incremental builds, distributed Jenkins agents, optimized Docker images, and artifact reuse. Monitoring build durations and continuously optimizing bottlenecks helps maintain fast delivery cycles and improves overall developer experience.

---

## 11. Difference between Docker Service , Docker Stack and Docker Swarm

Docker Swarm is Docker's native container orchestration platform that manages clustering, scheduling, scaling, and service discovery across multiple Docker hosts. A Docker Service defines how a containerized application should run within the Swarm cluster, including replica count, update strategy, and networking. Docker Stack is a higher-level construct that allows multiple services to be deployed together using a Docker Compose file. In simple terms, Swarm manages the cluster, Services define individual workloads, and Stacks deploy entire application ecosystems consisting of multiple services.

---

## 12. What is the best way to connect to portal ?

The best connection method depends on the portal and security requirements. For administrative access to cloud environments, I prefer secure methods such as VPN connectivity, Bastion Hosts, AWS Systems Manager Session Manager, Azure Bastion, or federated Single Sign-On integrated with Identity Providers. Direct public access should be minimized. In enterprise environments, secure access solutions combined with MFA, audit logging, role-based access control, and centralized identity management provide the most secure and compliant approach.

---

## 13. Explain pod affinity and node affinity in Kubernetes ?

Node Affinity controls which worker nodes a Pod can run on based on node labels. For example, database workloads can be restricted to high-memory nodes while compute-intensive applications run on CPU-optimized nodes. Pod Affinity controls scheduling decisions based on the location of other Pods. It can be used to place related services close together to reduce network latency. Pod Anti-Affinity does the opposite by ensuring similar Pods are distributed across multiple nodes for high availability. In production environments, affinity rules help optimize resource utilization, improve application performance, and increase resilience against node failures.


# DevOps / Cloud Engineer Interview Questions & Answers

## 1. Docker Image Size Increased from 200MB to 2GB

### Question

Docker image sizes have grown from 200MB to 2GB over 6 months of development, causing deployment times to increase significantly. How would you investigate and fix this without disrupting the current deployment pipeline?

### Answer

> “First, I would investigate the image growth without disrupting the current pipeline by comparing older and newer Docker images using commands like `docker history`, `docker image inspect`, and tools such as Dive. This helps identify which layers or dependencies caused the increase from 200MB to 2GB.
>
> Common reasons are:
>
> * Large dependencies or unused packages added over time
> * Copying unnecessary files into the image
> * Multiple layers created by separate RUN commands
> * Build artifacts, logs, or cache files inside the image
> * Using heavy base images like full Ubuntu instead of Alpine/slim variants
>
> To fix it safely, I would:
>
> * Introduce multi-stage builds to separate build and runtime environments
> * Switch to lightweight base images like Alpine or slim images
> * Add a `.dockerignore` file to exclude unnecessary files
> * Combine RUN commands and clean package cache during build
> * Store logs and temp files outside the container
> * Scan for unused dependencies
>
> Since we should not disrupt the current deployment pipeline, I would create an optimized image in a separate branch or parallel CI pipeline, test it in lower environments, compare deployment time and application behavior, then gradually roll it out using blue-green or canary deployment.
>
> I’d also add image size monitoring in CI/CD with threshold alerts so future image bloat is detected early.”

---

# 2. AI Tools Used in Day-to-Day Work

### Question

What AI tools do you actually use in your day-to-day work, and what do you use them for?

### Answer

> “In my day-to-day work, I mainly use AI tools to improve productivity, automate repetitive tasks, and speed up troubleshooting.
>
> I use ChatGPT for:
>
> * Writing and optimizing shell scripts, Terraform, Kubernetes manifests, and CI/CD pipelines
> * Troubleshooting errors quickly by analyzing logs and configurations
> * Generating automation ideas and documentation
> * Learning new AWS or DevOps concepts faster
>
> I use GitHub Copilot inside VS Code for:
>
> * Auto-completing code and scripts
> * Writing Dockerfiles, YAML files, and Python/Bash scripts faster
> * Reducing repetitive coding effort
>
> I’ve also used Claude for:
>
> * Summarizing large documentation
> * Reviewing configurations and getting detailed explanations
> * Comparing different implementation approaches
>
> But I don’t rely on AI blindly. I always validate outputs, especially for production infrastructure, security, Terraform changes, and deployment scripts before applying them.”

---

# 3. Top Technical Strengths

### Question

What are your top two technical abilities that make you a strong engineer — things that actually give you an edge when working with engineering or data teams?

### Answer

> “I think my top two technical strengths are troubleshooting complex production issues and building reliable automation.
>
> First, I’m strong in troubleshooting and root cause analysis. In cloud and DevOps environments, issues can come from infrastructure, networking, Kubernetes, CI/CD, or application layers. I’m good at analyzing logs, metrics, monitoring dashboards, and system behavior to quickly identify the root cause and reduce downtime. That helps engineering teams resolve incidents faster.
>
> Second, I’m strong in automation and infrastructure optimization. I’ve worked with Terraform, Docker, Kubernetes, Jenkins, and AWS services to automate deployments, infrastructure provisioning, and operational tasks. I focus on reducing manual effort, improving deployment reliability, and making systems more scalable and consistent.
>
> I think these two skills give me an edge because they help both engineering and data teams move faster while maintaining stability and reliability in production environments.”

---

# 4. Why Looking for New Opportunities

### Question

What’s making you want to look for other opportunities right now? What’s driving it?

### Answer

> “I’ve learned a lot in my current role, especially around AWS, Kubernetes, CI/CD, and production support. Now I’m looking for opportunities where I can work on more large-scale cloud infrastructure, automation, and modern DevOps practices.
>
> What’s really driving me is the chance to grow technically, take on more ownership, and work in an environment where I can contribute to scalable and reliable systems. I’m especially interested in teams that are focused on cloud-native technologies, automation, and continuous improvement.
>
> I also want exposure to more challenging projects where I can expand my skills in areas like Kubernetes, infrastructure automation, observability, and system reliability engineering.”

---

# 5. 6-Day Work Week

### Question

Some companies follow a 6-day work week. Is that something you’re open to?

### Answer (Balanced Response)

> “I’m open to it depending on the work culture, learning opportunities, and overall role responsibilities. My main focus is being part of a strong engineering environment where I can grow technically and contribute effectively. At the same time, I also value productivity and long-term sustainability, so I’d prefer a balanced and efficient work culture.”

### Alternative Answer (If Not Comfortable)

> “I generally prefer a 5-day work week because I believe it helps maintain long-term productivity and work-life balance. However, I’m flexible during critical releases, production incidents, or important project phases when extra effort is needed.”
