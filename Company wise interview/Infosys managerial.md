# Infosys Managerial Round – Sample Answers (DevOps/Cloud Engineer | 4+ Years Experience)

## 1. Tell me about yourself.

I am a DevOps Engineer with around 4 years of experience in Cloud and DevOps technologies, primarily working on AWS, Kubernetes, Docker, Jenkins, GitLab CI/CD, Terraform, Helm, ArgoCD, Linux, and monitoring tools like Prometheus and Grafana. Currently, I am working on managing cloud-native applications deployed on Kubernetes, automating infrastructure using Terraform, and building end-to-end CI/CD pipelines. My responsibilities include infrastructure provisioning, deployment automation, monitoring, troubleshooting production incidents, and ensuring application reliability and scalability. I enjoy solving complex operational challenges and continuously improving deployment processes through automation.

---

## 2. Explain your current project and your responsibilities.

My current project involves supporting and managing cloud-native microservices deployed on AWS EKS. The application consists of multiple microservices running in Kubernetes, integrated with databases, messaging systems, and external APIs. My responsibilities include creating and maintaining CI/CD pipelines, managing Kubernetes deployments, provisioning infrastructure using Terraform, implementing monitoring and alerting solutions, troubleshooting production issues, performing cluster upgrades, managing secrets, and ensuring high availability and security compliance across environments. I also collaborate closely with development, QA, and operations teams to ensure smooth software delivery.

---

## 3. What is the architecture of your current project?

Our application follows a microservices architecture deployed on AWS EKS. Source code is stored in Git repositories and changes trigger Jenkins or GitLab CI/CD pipelines. Docker images are built and pushed to container registries. Kubernetes manages deployment and scaling of workloads. AWS services such as ALB, Route 53, IAM, CloudWatch, S3, and RDS support the application infrastructure. Monitoring is implemented using Prometheus and Grafana, while ArgoCD manages GitOps-based deployments. Terraform is used for infrastructure provisioning and management. This architecture provides scalability, reliability, and automated deployments.

---

## 4. What is the most critical production issue you have handled?

One of the most critical incidents I handled involved a payment microservice outage during a production deployment. A Kubernetes deployment configuration was updated with an incorrect rolling update strategy that allowed all running pods to be terminated simultaneously. Since the new application version required significant startup time, the service temporarily had no healthy endpoints, causing payment API failures and customer transaction issues. The incident impacted business operations and required immediate attention.

---

## 5. How did you troubleshoot and resolve that issue?

As soon as the alerts were triggered, I assessed the impact and informed stakeholders about the outage. I reviewed Kubernetes events, deployment history, pod status, and monitoring dashboards. The investigation revealed that all existing pods had been terminated before new pods became ready. I immediately rolled back the deployment using Kubernetes rollout commands, restoring service availability. After service recovery, we updated deployment strategies, improved readiness probes, implemented PodDisruptionBudgets, and strengthened deployment validation processes to prevent similar incidents.

---

## 6. How do you prioritize tasks when multiple incidents occur at the same time?

I prioritize incidents based on business impact, customer impact, severity level, and service criticality. Production issues affecting customer-facing applications or revenue-generating services receive the highest priority. I assess the scope of each incident, delegate tasks where possible, and coordinate with team members to ensure parallel investigation of multiple issues. Effective communication and structured incident management help maintain focus and reduce resolution time during high-pressure situations.

---

## 7. Tell me about a situation where a deployment failed. What actions did you take?

During one deployment, application pods repeatedly failed readiness checks due to a configuration mismatch introduced in the new release. I immediately paused the rollout, analyzed logs and deployment changes, and confirmed the root cause. Since production stability was the priority, I executed a rollback to the previous stable version. After service recovery, we corrected the configuration, validated it in lower environments, and redeployed successfully. We also introduced additional validation checks into the pipeline to catch similar issues earlier.

---

## 8. Have you ever disagreed with a team member? How did you handle it?

Yes. During a project discussion, there was disagreement regarding whether a deployment process should remain manual or be fully automated. Instead of focusing on opinions, I gathered data about deployment failures, release delays, and operational effort. I presented the benefits and risks of both approaches and facilitated a discussion with stakeholders. After reviewing the evidence, the team agreed on a phased automation strategy. The experience reinforced the importance of collaboration, communication, and data-driven decision-making.

---

## 9. How do you manage communication with stakeholders during a production outage?

During a production incident, I believe communication is just as important as technical troubleshooting. I first assess the impact and establish a communication channel. Stakeholders are informed about the issue, affected services, estimated impact, and ongoing actions. I provide regular updates at defined intervals, avoid speculation, and communicate verified facts only. Once the issue is resolved, I share recovery confirmation, root cause analysis, and preventive measures. Transparent communication helps maintain trust and reduces confusion during critical situations.

---

## 10. What automation initiatives have you implemented in your project?

I have implemented several automation initiatives, including CI/CD pipelines for application deployments, Infrastructure as Code using Terraform, automated Kubernetes deployments using Helm and ArgoCD, automated monitoring and alerting configurations, infrastructure compliance checks, backup validation jobs, security scanning integrations, and automated rollback mechanisms. These initiatives significantly reduced manual effort, improved deployment consistency, and increased overall system reliability.

---

## 11. How do you ensure the reliability of your CI/CD pipelines?

Pipeline reliability is achieved through automated testing, code quality checks, security scanning, environment validation, artifact versioning, and deployment approvals for production environments. I also implement monitoring and alerting for pipeline failures, maintain rollback procedures, and regularly review pipeline performance. Build-once-deploy-everywhere principles ensure consistent artifacts across environments and reduce deployment-related risks.

---

## 12. What challenges have you faced while working with Kubernetes?

Some common challenges include troubleshooting CrashLoopBackOff issues, handling resource constraints, debugging networking problems, managing cluster upgrades, implementing secure RBAC policies, optimizing autoscaling configurations, and maintaining application availability during deployments. Through monitoring, automation, and operational best practices, these challenges can be effectively managed while maintaining production stability.

---

## 13. How do you handle pressure during critical incidents?

During critical incidents, I focus on structured problem-solving rather than reacting emotionally. I assess the impact, establish priorities, gather relevant information, and coordinate with team members. Breaking the problem into manageable steps helps maintain clarity. Regular communication with stakeholders and documenting actions during the incident also help maintain control. Staying calm and methodical is essential for effective incident management.

---

## 14. Have you mentored junior engineers? How do you guide them?

Yes. I regularly assist junior engineers by explaining infrastructure concepts, reviewing code changes, conducting knowledge-sharing sessions, and helping them troubleshoot issues. I encourage them to understand the reasoning behind solutions rather than simply following instructions. Providing practical examples, documentation, and hands-on guidance helps them build confidence and technical expertise over time.

---

## 15. How do you perform Root Cause Analysis (RCA)?

I begin by collecting logs, metrics, alerts, deployment records, and timeline information related to the incident. I identify the sequence of events leading to the failure and determine the underlying root cause rather than focusing only on symptoms. The RCA document typically includes incident summary, impact assessment, timeline, root cause, contributing factors, corrective actions, and preventive measures. The goal is continuous improvement and prevention of recurrence.

---

## 16. Tell me about a time when you took ownership beyond your regular responsibilities.

During a major production outage, our team lead was unavailable. I coordinated incident response activities, communicated with stakeholders, managed troubleshooting efforts, and ensured service restoration. After resolving the issue, I prepared the RCA, led follow-up discussions, and implemented preventive improvements. Although these activities extended beyond my normal responsibilities, taking ownership helped minimize business impact and demonstrated leadership under pressure.

---

## 17. How do you handle tight deadlines and competing priorities?

I begin by evaluating business impact, urgency, dependencies, and available resources. Tasks are categorized based on priority, and I focus first on activities that directly affect production stability or customer experience. Clear communication with stakeholders helps manage expectations. Breaking large tasks into smaller deliverables and tracking progress systematically enables efficient execution even under tight timelines.

---

## 18. What improvements would you suggest for your current project?

I would focus on expanding automation, improving observability, strengthening security controls, increasing deployment frequency through GitOps adoption, optimizing infrastructure costs, and enhancing disaster recovery processes. Continuous improvement in monitoring, automation, and reliability engineering practices can significantly improve operational efficiency and application resilience.

---

## 19. Why are you looking for a job change?

I am grateful for the opportunities and experience I have gained in my current role. However, I am looking for new challenges that will allow me to work on larger-scale environments, expand my technical expertise, and contribute to more complex cloud and DevOps initiatives. I believe this opportunity aligns well with my career goals and provides strong learning and growth potential.

---

## 20. Why do you want to join Infosys?

Infosys is recognized globally for its technology expertise, large-scale transformation projects, and strong focus on innovation. The opportunity to work with diverse clients, modern cloud technologies, and highly skilled teams is very appealing to me. I believe Infosys provides an environment where I can contribute meaningfully while continuing to grow both technically and professionally.

---

## 21. What are your short-term and long-term career goals?

My short-term goal is to deepen my expertise in cloud-native technologies, Kubernetes, automation, and platform engineering while contributing effectively to business-critical projects. My long-term goal is to evolve into a senior cloud or DevOps architect role where I can design scalable platforms, mentor teams, drive engineering best practices, and contribute to strategic technology decisions.

---

## 22. Where do you see yourself in the next five years?

In five years, I see myself taking on greater technical leadership responsibilities, designing enterprise-scale cloud platforms, leading DevOps transformation initiatives, mentoring engineers, and contributing to architectural decisions. I aim to become a trusted technical leader who combines strong engineering skills with business understanding.

---

## 23. What are your strengths and areas of improvement?

My strengths include problem-solving, ownership, automation mindset, troubleshooting abilities, adaptability, and effective collaboration across teams. One area I continuously work on is improving my ability to delegate tasks during large-scale projects. While I naturally take ownership, I have learned that effective delegation improves team efficiency and helps develop team capabilities.

---

## 24. Why should we hire you for this role?

I bring a combination of hands-on technical expertise, production support experience, automation skills, and ownership mindset. Over the past four years, I have worked extensively with AWS, Kubernetes, Terraform, CI/CD, monitoring, and incident management. I am comfortable handling both day-to-day operations and critical production incidents. Beyond technical skills, I focus on communication, collaboration, and continuous improvement, which are essential for success in modern DevOps environments. I believe I can contribute quickly, take ownership of responsibilities, and add value to your engineering team from day one.


