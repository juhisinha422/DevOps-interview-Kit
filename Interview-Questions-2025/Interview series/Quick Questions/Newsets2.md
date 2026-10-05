Here's a copy-paste ready `README.md` with detailed 4+ years DevOps interview answers.

# DevOps Interview Questions & Answers (4+ Years Experience)

## 1. What is multi-stage build in Docker?

A multi-stage build is a Docker feature used to reduce image size and improve security by separating the build environment from the runtime environment. In a typical Java application, the first stage uses a Maven image to compile the application and generate the JAR file. The second stage uses a lightweight runtime image such as OpenJDK or Eclipse Temurin and copies only the generated artifact from the build stage. This removes unnecessary build tools, source code, and dependencies from the final image. Multi-stage builds significantly reduce image size, improve deployment speed, and minimize security vulnerabilities.

---

## 2. How will you handle secrets in Docker image?

Secrets should never be hardcoded into Dockerfiles or embedded inside container images. Instead, secrets should be injected at runtime using Kubernetes Secrets, HashiCorp Vault, AWS Secrets Manager, CyberArk, or environment variables. In Kubernetes, I typically create Secrets and mount them as environment variables or volumes inside containers. This ensures credentials remain separate from application code and can be rotated without rebuilding container images.

---

## 3. What happens to the application if the Docker container is stopped?

When a Docker container stops, all processes running inside that container stop immediately. The application becomes unavailable and stops serving requests. If the container is managed by Kubernetes, Docker Swarm, ECS, or another orchestration platform, the platform may automatically restart or recreate the container depending on the configured restart policy. If persistent storage is not configured, any data stored inside the container filesystem may also be lost.

---

## 4. How to see the running containers in Docker?

To view running containers:

```bash
docker ps
```

This command displays container IDs, image names, status, ports, and container names. To view all containers including stopped containers:

```bash
docker ps -a
```

---

## 5. How to run a stopped container?

To start an existing stopped container:

```bash
docker start <container_id>
```

If interactive access is required:

```bash
docker start -i <container_id>
```

This allows the container to resume execution using its previous configuration.

---

## 6. How can you troubleshoot a failed container?

I first check container status using `docker ps -a`. Then I inspect container logs using:

```bash
docker logs <container_id>
```

If necessary, I inspect container configuration using:

```bash
docker inspect <container_id>
```

I verify environment variables, network connectivity, mounted volumes, resource limits, and application startup errors. If the container exits immediately, I review the ENTRYPOINT and CMD instructions to ensure the main process starts correctly.

---

## 7. What is the difference between ConfigMap and Secret in Kubernetes?

ConfigMap stores non-sensitive configuration data such as application settings, URLs, and feature flags. Secrets store sensitive information such as passwords, API keys, certificates, and tokens. Although both can be mounted as environment variables or volumes, Secrets are base64 encoded and have additional access controls. In production environments, Secrets are often integrated with external secret management systems such as Vault or AWS Secrets Manager.

---

## 8. What is the use of etcd in Kubernetes?

etcd is the distributed key-value datastore used by Kubernetes to store cluster state information. It stores details about pods, nodes, deployments, services, ConfigMaps, Secrets, RBAC policies, and other cluster resources. The Kubernetes API Server continuously interacts with etcd to retrieve and update cluster information. Without etcd, Kubernetes cannot maintain or manage cluster state.

---

## 9. Explain different types of Kubernetes Services and use cases.

ClusterIP is the default service type and is used for internal communication within the cluster. NodePort exposes the application on a specific port across all worker nodes and is often used for testing environments. LoadBalancer provisions an external load balancer and is commonly used in cloud environments to expose applications publicly. ExternalName maps a service to an external DNS name. In production environments, ClusterIP combined with Ingress is the most common architecture.

---

## 10. If a Kubernetes node is in NotReady state, what steps would you take to troubleshoot it?

I begin by checking node status using:

```bash
kubectl get nodes
kubectl describe node <node-name>
```

I review node conditions such as MemoryPressure, DiskPressure, and NetworkUnavailable. Next, I verify kubelet status, container runtime status, and network connectivity. I inspect system logs, resource utilization, disk availability, and cloud provider health checks. Once the root cause is identified, I either recover the node or cordon and replace it if necessary.

---

## 11. If you commit changes in Git, Jenkins jobs should automatically trigger. How can you do it?

I configure webhooks between Git repositories and Jenkins. Whenever code is pushed to a repository, the webhook sends an HTTP request to Jenkins, triggering the configured pipeline automatically. Jenkins can also poll SCM periodically, but webhooks are preferred because they provide immediate execution and reduce unnecessary polling overhead.

---

## 12. Difference between Declarative and Scripted Pipeline?

Declarative Pipeline follows a structured syntax with predefined sections such as pipeline, agent, stages, and post actions. It is easier to maintain and recommended for most projects. Scripted Pipeline uses Groovy-based scripting and provides greater flexibility and advanced programming capabilities. Declarative Pipelines are generally preferred for standard CI/CD workflows, while Scripted Pipelines are used for highly customized automation requirements.

---

## 13. Explain VPC and its components in AWS.

A Virtual Private Cloud (VPC) is a logically isolated network within AWS where cloud resources are deployed securely. Components include subnets, route tables, Internet Gateways, NAT Gateways, Security Groups, Network ACLs, VPC Endpoints, Elastic IPs, and Transit Gateways. Together, these components control network segmentation, routing, security, and communication between resources.

---

## 14. How will you establish communication between two VPCs?

Communication can be established using VPC Peering, Transit Gateway, Site-to-Site VPN, AWS PrivateLink, or Direct Connect depending on the architecture. VPC Peering is suitable for direct communication between a small number of VPCs, while Transit Gateway is preferred for large-scale multi-VPC environments because it simplifies routing management.

---

## 15. Different types of Load Balancers and when do you use them?

Application Load Balancer (ALB) operates at Layer 7 and supports HTTP and HTTPS traffic with advanced routing capabilities. Network Load Balancer (NLB) operates at Layer 4 and provides high-performance TCP and UDP traffic handling. Classic Load Balancer (CLB) is the older generation load balancer and is largely replaced by ALB and NLB. ALB is commonly used for web applications, while NLB is preferred for low-latency or non-HTTP workloads.

---

## 16. How will the load be distributed in load balancers?

Load balancers distribute incoming requests across multiple healthy backend targets using algorithms such as round robin, least outstanding requests, flow hashing, or weighted routing. Health checks continuously monitor backend availability and remove unhealthy targets from traffic distribution automatically.

---

## 17. Difference between Git Rebase and Git Merge?

Git Merge combines changes from multiple branches while preserving branch history and creating a merge commit. Git Rebase rewrites commit history by moving commits onto a new base branch, resulting in a cleaner and linear history. Merge is safer for shared branches, while Rebase is commonly used to maintain clean feature branch history before merging.

---

## 18. In Linux, if you want to kill a specific port number, what command do you use?

First identify the process:

```bash
lsof -i :8080
```

Then terminate it:

```bash
kill -9 <PID>
```

This stops the process using the specified port.

---

## 19. How can you give execute permission for a file?

```bash
chmod +x filename.sh
```

This grants execute permission to the file owner, group, and others based on existing permission settings.

---

## 20. What is the use of SonarQube Quality Gate?

A Quality Gate enforces predefined code quality and security standards. It evaluates metrics such as vulnerabilities, bugs, code smells, duplication, and test coverage. If the Quality Gate fails, the CI/CD pipeline is blocked from progressing further, ensuring only compliant code reaches higher environments.

---

## 21. Say you have 1 worker node and 1 master node. Traffic should only go to the worker node. How can you design the manifest?

I would use nodeSelector, node affinity, or taints and tolerations. Worker nodes would be labeled appropriately, and the deployment manifest would specify scheduling only on worker nodes. Master nodes remain tainted by default, preventing application workloads from being scheduled there.

---

## 22. What is Ingress in Kubernetes?

Ingress is a Kubernetes resource used to manage external HTTP and HTTPS traffic into the cluster. It provides centralized routing, SSL termination, path-based routing, host-based routing, and integration with load balancers. Ingress Controllers such as NGINX, AWS Load Balancer Controller, and Traefik process Ingress rules and route traffic to backend services.

---

## 23. How do you handle outgoing traffic in Kubernetes?

Outgoing traffic is handled through cluster networking and NAT mechanisms. Pods typically communicate externally through worker node networking or cloud provider NAT Gateways. Network Policies can be used to control egress traffic and restrict communication to approved destinations.

---

## 24. Which pod is used to deploy metrics-related tools in Kubernetes?

Metrics Server is used for basic resource metrics and enables HPA functionality. For advanced monitoring, Prometheus pods collect metrics while Grafana pods visualize them. Together they provide observability, alerting, and performance monitoring across the Kubernetes cluster.

---

## 25. What deployment strategy were you using?

In production, I primarily use Rolling Updates because they provide zero downtime and controlled application upgrades. For critical services, I also use Blue-Green and Canary deployment strategies to minimize risk and enable safe validation before full rollout.

---

## 26. Tell me about yourself.

I am a DevOps Engineer with approximately 4 years of experience working on AWS Cloud, Kubernetes, Docker, Jenkins, GitLab CI/CD, Terraform, Helm, ArgoCD, Linux, and monitoring tools such as Prometheus and Grafana. My responsibilities include infrastructure automation, CI/CD implementation, Kubernetes administration, cloud optimization, security integration, and production support. I have worked extensively on EKS, containerized microservices, GitOps practices, and cloud-native deployments.

---

## 27. Suppose you have a Java-based project and build images using Docker and deploy using Kubernetes. How will you integrate everything with Jenkins?

The Jenkins pipeline starts by checking out source code from Git. Maven builds the application and generates a JAR file. SonarQube performs code analysis, followed by security scanning. Docker builds the image and pushes it to a container registry. Jenkins then updates Kubernetes manifests or Helm chart values and deploys the application using kubectl or ArgoCD. Finally, smoke tests and monitoring validations confirm successful deployment.

---

## 28. Difference between Docker Image and Docker Container?

A Docker Image is a read-only template containing application code, libraries, dependencies, and runtime configuration. A Docker Container is a running instance of an image. Multiple containers can be created from a single image, and containers provide the runtime environment where applications execute.

---

## 29. When Terraform Apply gives an error, how will you troubleshoot?

I start by reviewing the exact error message and Terraform logs. Then I verify provider authentication, state file consistency, backend connectivity, resource dependencies, variable values, and cloud provider limits. Running `terraform validate` and `terraform plan` often helps identify configuration issues before reattempting deployment.

---

## 30. How do you perform Rolling Updates and Rollbacks?

Rolling Updates gradually replace old pods with new pods while maintaining application availability. If issues occur after deployment, Kubernetes allows rollback using:

```bash
kubectl rollout undo deployment <deployment-name>
```

This restores the previous stable version with minimal downtime.



## 31. What are the types of Ansible roles?

Ansible roles are a way to organize automation code into reusable and modular components. A role typically contains directories such as tasks, handlers, templates, files, vars, defaults, meta, and tests. There are no officially defined "types" of roles, but in enterprise environments we commonly create Application Roles for deploying applications, Infrastructure Roles for configuring servers, Database Roles for database setup, Security Roles for hardening systems, Monitoring Roles for installing monitoring agents, and Middleware Roles for configuring services such as Nginx, Apache, or Tomcat. Roles improve code reusability, maintainability, and standardization across environments.

---

## 32. Can you write a playbook for anything?

Yes. A playbook defines automation tasks to be executed on target hosts. For example, installing Nginx:

```yaml
---
- hosts: webservers
  become: yes

  tasks:
    - name: Install Nginx
      yum:
        name: nginx
        state: present

    - name: Start Nginx
      service:
        name: nginx
        state: started
        enabled: yes
```

In production environments, I usually place such tasks inside reusable roles rather than writing everything directly in playbooks.

---

## 33. What is the branching strategy in your project?

In most projects, we follow GitFlow or a simplified feature branch strategy. Developers create feature branches from the develop branch and raise pull requests after completing development. Code reviews and automated CI validations are performed before merging. Once testing is completed, changes move to release branches and eventually to the main or master branch for production deployment. This strategy provides isolation, controlled releases, traceability, and easier rollback capabilities.

---

## 34. Have you worked on Linux? Tell me some troubleshooting commands that you used.

Yes. Linux troubleshooting is part of my daily responsibilities. Common commands I use include `top`, `htop`, `free -m`, `vmstat`, `iostat`, `sar`, `df -h`, `du -sh`, `ps -ef`, `netstat`, `ss`, `journalctl`, `systemctl status`, `dmesg`, `tail`, `grep`, and `find`. These commands help analyze CPU usage, memory consumption, disk utilization, network connectivity, service status, and application logs during production incidents.

---

## 35. What is the use of chmod?

The chmod command is used to modify file and directory permissions in Linux. It controls read, write, and execute permissions for owners, groups, and others. For example:

```bash
chmod 755 script.sh
```

This grants read, write, and execute permissions to the owner while granting read and execute permissions to the group and others. Proper permission management is essential for security and controlled access.

---

## 36. Container is running but application is not available. How do you troubleshoot?

I begin by verifying whether the application process inside the container is running. Next, I inspect container logs using `docker logs` or `kubectl logs`. I validate port mappings, service configurations, health probes, environment variables, DNS resolution, and network connectivity. If the application is deployed on Kubernetes, I check Services, Endpoints, Ingress rules, and load balancer health checks. In many cases, containers are healthy while the application process fails to bind to the expected port or readiness probes prevent traffic routing.

---

## 37. How will you troubleshoot the issue when a deployment fails at midnight? What actions will you take?

My first priority is incident management and service restoration. I assess business impact, notify stakeholders, and review monitoring dashboards and alerts. Then I investigate recent deployments, logs, events, and infrastructure changes. If the deployment is responsible for the outage, I execute a rollback immediately to restore service. Once the environment stabilizes, I perform detailed root cause analysis, document findings, and implement preventive measures. Communication throughout the incident is as important as technical troubleshooting.

---

## 38. Your memory utilization is 90%. What will you do?

I first identify which processes or containers are consuming memory using tools such as `top`, `htop`, `free -m`, and Kubernetes resource metrics. I determine whether the increase is caused by expected workload growth, memory leaks, caching behavior, or runaway processes. If memory pressure threatens service availability, I scale the application, restart affected workloads if necessary, or increase resource allocations. Long-term actions include profiling the application, tuning garbage collection, adjusting limits, and implementing proactive monitoring alerts.

---

## 39. Suppose your team lead is on leave and there is an issue. The client or stakeholders are angry. What will you do?

I would take ownership of the incident and maintain professional communication. First, I acknowledge the issue and assure stakeholders that investigation is underway. Then I gather technical details, assess impact, coordinate with available team members, and work toward service restoration. I provide regular status updates, avoid speculation, and focus on facts. Once the issue is resolved, I share a detailed RCA and preventive action plan. During critical incidents, calm communication and leadership are just as important as technical skills.

---

## 40. Write a YAML for Service.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

  type: ClusterIP
```

This Service exposes pods labeled `app: nginx` internally within the Kubernetes cluster.

---

## 41. Apart from Git branching strategy, what other methodology do you use in your project?

We follow Agile and DevOps methodologies. Agile enables iterative development through sprint planning, daily standups, backlog refinement, sprint reviews, and retrospectives. DevOps practices integrate development, testing, security, and operations through automation, CI/CD, Infrastructure as Code, monitoring, and continuous feedback. Together, these methodologies improve delivery speed, collaboration, and reliability.

---

## 42. How do you achieve High Availability in Jenkins?

High Availability can be achieved by separating the Jenkins Controller and Agents, using external databases where required, storing Jenkins home on highly available shared storage, and implementing regular backups. In cloud environments, Jenkins Controllers can run behind load balancers with standby instances. Agent-based architectures distribute build workloads and reduce single points of failure. Backup and disaster recovery strategies are also critical for maintaining availability.

---

## 43. Do you know about Agile methodology?

Yes. Agile is a software development methodology focused on iterative delivery, collaboration, and continuous improvement. Work is organized into short development cycles called sprints. Teams conduct sprint planning, daily standups, sprint reviews, and retrospectives. Agile promotes rapid feedback, adaptability to changing requirements, and incremental value delivery. In DevOps environments, Agile and CI/CD practices complement each other by enabling frequent and reliable releases.

---

## 44. A container is crashing again and again. How will you troubleshoot?

I first examine container logs to identify application errors. Next, I inspect container status, restart counts, exit codes, and resource utilization. In Kubernetes, I review events using `kubectl describe pod`, check liveness and readiness probes, verify environment variables, mounted volumes, and dependency availability. Common causes include application crashes, configuration issues, insufficient resources, failed health checks, and missing dependencies. Root cause analysis determines whether the issue is application-related or infrastructure-related.

---

## 45. What AWS services have you used? Did you use any database service?

I have worked extensively with AWS services including EC2, VPC, S3, IAM, EKS, ECS, ALB, NLB, Route 53, CloudWatch, CloudTrail, Lambda, Auto Scaling Groups, SNS, SQS, Secrets Manager, Systems Manager, ECR, RDS, DynamoDB, and AWS Backup. For databases, I have worked with Amazon RDS for relational databases such as MySQL and PostgreSQL, as well as DynamoDB for NoSQL workloads.

---

## 46. How many pods are you running for your application?

The number of pods depends on application requirements, traffic volume, availability objectives, and scaling policies. In production environments, I generally ensure at least two or more replicas for high availability. Critical microservices often run multiple replicas across different nodes and availability zones, while HPA automatically adjusts pod counts based on traffic and resource utilization.

---

## 47. Different types of Services in Kubernetes?

Kubernetes supports four primary Service types. ClusterIP provides internal cluster communication and is the default type. NodePort exposes applications on a port across worker nodes. LoadBalancer integrates with cloud providers to expose applications externally using managed load balancers. ExternalName maps a Kubernetes Service to an external DNS name. In modern production environments, ClusterIP combined with Ingress is the most commonly used architecture.



