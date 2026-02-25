# DevOps Interview Preparation Guide (4+ Years Experience)

This `README.md` provides a comprehensive guide for DevOps interview preparation, covering key concepts, tools, and commands frequently asked in interviews for professionals with 4+ years of experience.

## 1. Explain your experience and understanding of DevOps.

Over the past 4+ years in the DevOps domain, I have been involved in automating the software development lifecycle (SDLC) from code commit to deployment. I have hands-on experience in Continuous Integration (CI), Continuous Delivery (CD), Infrastructure as Code (IaC), monitoring, containerization, and cloud deployments. Key tools I have worked with include Jenkins, Docker, Kubernetes, Terraform, GitLab CI, and AWS. My focus has been on optimizing and automating pipelines, managing cloud infrastructure, and ensuring efficient collaboration between development and operations teams.

## 2. What command is used to list all listening ports in Linux?

To list all listening ports in Linux, you can use:

```bash
netstat -tuln
```

```bash
Or if netstat is not available, use:

ss -tuln
```

## 3. How do you check which process is using the most CPU or memory?

```bash
To check which process is using the most CPU:

top

Or:

ps aux --sort=-%cpu | head -n 10

To check the most memory-consuming processes:

top

Press M to sort by memory usage.

Alternatively:

ps aux --sort=-%mem | head -n 10
```
## 4. Command to generate an SSH key?

```bash
To generate an SSH key pair:

ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
```

## 5. Name some popular Linux distributions.

Ubuntu

CentOS

Red Hat Enterprise Linux (RHEL)

Debian

Fedora

Arch Linux

Alpine Linux

Kali Linux

## 6. How can you access private instances inside a VPC?

To access private instances in a VPC, you can:

Bastion Host: Set up a bastion host in a public subnet and SSH into private instances from there.

VPN: Establish a VPN connection to access private instances.

AWS Systems Manager Session Manager: Use AWS Systems Manager to access EC2 instances without SSH access.

## 7. How do you SSH into an EC2 instance using your key?

```
To SSH into an EC2 instance using a private key:

ssh -i /path/to/your-key.pem ec2-user@<EC2_PUBLIC_IP>

Ensure the private key has appropriate permissions:

chmod 400 your-key.pem
```

## 8. Explain Security Groups vs Network ACLs.

Security Groups: Stateful firewall rules applied to EC2 instances to control inbound and outbound traffic.

Network ACLs: Stateless firewall rules applied at the subnet level to control traffic entering or leaving a subnet.

## 9. How can you block an IP using only Security Groups?

To block an IP using Security Groups:

Go to the EC2 Dashboard > Security Groups.

Select the security group and click Edit inbound rules.

Add an inbound rule with type "All traffic", source as the IP you want to block, and set it to "Deny".

Note: Security Groups are stateful, so blocking inbound traffic doesn't automatically block outbound traffic.

## 10. What is the default port of Jenkins?

The default port for Jenkins is 8080.

## 11. How do you secure your Jenkins server?

To secure Jenkins:

Use Authentication (LDAP, Active Directory, or Jenkins internal database).

Enable Authorization based on roles (Matrix or Project-based).

Implement SSL/TLS encryption (HTTPS).

Regularly update Jenkins and plugins.

Use the Credentials Plugin for secret management.

Enable Audit logging to monitor user activities.

## 12. Explain your CI/CD pipeline and how you optimize it.

My CI/CD pipeline typically includes the following stages:

Code Commit: Code is pushed to a repository (e.g., GitHub, GitLab).

Build: A CI tool (Jenkins, GitLab CI) automates the build.

Test: Automated tests (unit, integration, UI) are run.

Deploy: Deployment to staging or production using Kubernetes, AWS, or Terraform.

Optimization strategies:

Parallelizing jobs to minimize build time.

Caching dependencies to avoid redundant downloads.

Using containerized environments for consistency.

Integrating monitoring and alerting to quickly catch issues.

## 13. How do you store secret keys in a pipeline securely?

Secrets can be securely stored using:

Environment Variables in CI tools.

AWS Secrets Manager, HashiCorp Vault, or Azure Key Vault for centralized secret management.

Jenkins Credentials Plugin for securely managing secrets in Jenkins pipelines.

## 14. What are SAST and DAST? Explain both.

SAST (Static Application Security Testing): A white-box approach that analyzes source code or binaries for security flaws before execution.

DAST (Dynamic Application Security Testing): A black-box approach that analyzes an application during runtime to identify security vulnerabilities.

## 15. What is a DaemonSet and where is it used?

A DaemonSet ensures that a specific pod runs on every node in the Kubernetes cluster. It is typically used for:

Running logging agents (e.g., Fluentd, Logstash).

Running monitoring agents (e.g., Prometheus).

Ensuring that system services are available on all nodes.

## 16. What are taints and tolerations in Kubernetes?

Taints: Applied to nodes to repel specific pods from being scheduled on them.

Tolerations: Applied to pods to allow them to be scheduled on nodes with certain taints.

They are used together to manage pod scheduling in a Kubernetes cluster.

## 17. What does an Ingress controller do?

An Ingress controller manages access to services in a Kubernetes cluster, typically via HTTP or HTTPS. It provides features such as SSL termination, load balancing, and routing based on defined rules.

## 18. Explain Common Kubernetes pod errors.

CrashLoopBackOff: Pod is crashing repeatedly due to missing files, misconfigurations, or resource limits.

ImagePullBackOff: Kubernetes is unable to pull the container image due to incorrect image names or missing authentication credentials.

Pending: The pod cannot be scheduled due to insufficient resources or other constraints.

## 19. How would you deploy a three-tier application in Kubernetes?

To deploy a three-tier application (e.g., frontend, backend, and database):

Create a Deployment for each tier (frontend, backend, and database).

Expose the backend and frontend using Services.

Use Ingress for routing external traffic to the frontend.

Manage sensitive data with Secrets and configurations using ConfigMaps.

For the database, use Persistent Volumes (PVs) and Persistent Volume Claims (PVCs).

## 20. What is Terraform?

Terraform is an open-source Infrastructure as Code (IaC) tool that enables you to define and provision cloud infrastructure through declarative configuration files. It supports multi-cloud environments and allows for automated resource management.

## 21. What is the purpose of backend.tf?

backend.tf is used to configure the backend where Terraform stores its state file. It can be a local file or a remote backend like S3, Consul, or others. A remote backend enables state locking and collaboration in team environments.

## 22. How do you reduce the size of a Docker image?

To reduce Docker image size:

Use smaller base images like alpine instead of larger ones (e.g., ubuntu).

Remove unnecessary dependencies and files after installation.

Use multi-stage builds to separate the build environment from the runtime environment.

## 23. Difference between Dockerfile COPY and ADD & CMD and ENTRYPOINT.

COPY: Copies files from the host system into the container.

ADD: Similar to COPY but also supports remote URLs and unpacking compressed files.

CMD: Specifies the default command to run when the container starts.

ENTRYPOINT: Defines the main command to run and allows arguments to be passed in.

## 24. Explain any project mentioned in your resume.

During my time at XYZ Company, I led the implementation of a CI/CD pipeline for a microservices-based application deployed on AWS. We used Jenkins for continuous integration, Docker for containerization, and Kubernetes for orchestration. I automated the entire deployment process using Terraform and AWS CloudFormation, reducing deployment time by 60% and improving stability.
