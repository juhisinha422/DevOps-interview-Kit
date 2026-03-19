# 🧑‍💻 Automation Engineer Interview Preparation (Deloitte - Hyderabad)

---

## 1. Introduce  yourself and tell me about your project.

Hi, I’m Juhi Sinha, a DevOps/Automation Engineer with around 4 years of experience in cloud and DevOps technologies.

I have worked extensively on AWS, Kubernetes (EKS), Jenkins, Docker, Terraform, and Ansible.

In my recent project, I worked on a microservices-based application where my responsibilities included:

* Automating infrastructure using Terraform
* Building CI/CD pipelines using Jenkins
* Containerizing applications using Docker
* Deploying applications on Kubernetes
* Monitoring using Prometheus and Grafana

The main goal of the project was to reduce manual effort, improve deployment speed, and ensure high availability.

---

## 2. Please tell me about the toughest tasks that you have done in your projects and what errors that you have encountered in the process.

One of the toughest tasks I handled was resolving a production issue in Kubernetes.

### Errors Encountered:

* Pods going into CrashLoopBackOff
* OOMKilled errors due to high memory usage
* Application downtime

### What I Did:

* Checked logs using kubectl logs
* Analyzed resource usage
* Identified memory leak issue

### Resolution:

* Increased memory limits
* Rolled back the faulty deployment
* Coordinated with developers to fix the issue

---

## 3. How did you implemented automation in your project.tell me the steps.

### Steps:

1. Requirement analysis to identify repetitive tasks
2. Selected tools like Jenkins, Terraform, Docker, Kubernetes
3. Created CI/CD pipeline:

   * Code checkout
   * Build Docker image
   * Push image to registry
   * Deploy to Kubernetes
4. Automated infrastructure using Terraform
5. Added automated testing and validation
6. Integrated monitoring tools

---

## 4. How did you integrate ansible in your project and what kind of tasks that you have performed using ansible.

I integrated Ansible with Jenkins for configuration management and automation.

### Tasks Performed:

* Server provisioning
* Application deployment
* Configuration management
* Patch updates
* User and permission management

Example:

* Automated installation of packages across multiple servers
* Deployed application builds to target servers

---

## 5. What methodology have you used in your project.

We followed Agile methodology (Scrum).

### Practices:

* Sprint planning
* Daily stand-ups
* Sprint reviews
* Continuous feedback

---

## 6. What is the difference between agile and kanban.

| Feature  | Agile (Scrum)     | Kanban          |
| -------- | ----------------- | --------------- |
| Workflow | Sprint-based      | Continuous flow |
| Roles    | Defined roles     | No strict roles |
| Changes  | Limited in sprint | Allowed anytime |
| Board    | Scrum board       | Kanban board    |

---

## 7. What is the difference between terraform and cloud formation.

| Feature          | Terraform      | CloudFormation |
| ---------------- | -------------- | -------------- |
| Cloud Support    | Multi-cloud    | AWS only       |
| Language         | HCL            | JSON/YAML      |
| Flexibility      | High           | Moderate       |
| State Management | External state | Managed by AWS |

---

## 8. There is a monitoring tool called dynatrace, alerts are coming from the tool ,you need to automate the process like alert came--> then incident has be created in service now--> and assigned to certain team like devops ,cloud, linux teams. how do you automate this.

### Approach:

1. Configure webhook in Dynatrace to send alerts
2. Use middleware like AWS Lambda or API Gateway
3. Trigger ServiceNow REST API to create incident
4. Add logic for assignment:

   * Infra issues → Cloud team
   * Application issues → DevOps team
   * OS issues → Linux team

### Flow:

Dynatrace Alert → Webhook → Lambda → ServiceNow Incident Creation → Auto Assignment

---

## 9. You and team are almost ready with the production deployment everything is perfect,but the client made changes in the last minute,how do you handle this situation,then tell me the deployment process of this.

### Handling Situation:

1. Perform impact analysis
2. Discuss with stakeholders
3. Implement change in staging
4. Test thoroughly
5. Get approvals

### Deployment Process:

1. Code pushed to Git
2. Jenkins pipeline triggered
3. Build Docker image
4. Push image to registry
5. Deploy to Kubernetes
6. Validate deployment
7. Monitor application

---

## 10. you have studied electrical engineering but why did choose this IT industry,what motivates you to choose cloud and devops path.

Although I come from an Electrical Engineering background, I developed interest in IT during my studies.

### Motivation:

* Passion for problem-solving
* Interest in automation and cloud technologies
* Continuous learning and growth

DevOps attracted me because:

* It focuses on automation and efficiency
* Combines development and operations
* Plays a critical role in modern application delivery

---
