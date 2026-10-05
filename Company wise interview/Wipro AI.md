1. **Describe a complex DevOps project you worked on where you had to design and implement a CI/CD pipeline from scratch. Walk through the key decisions you made, the tools you selected, the challenges you encountered, and how you ensured the pipeline was both secure and reliable.** 

2. **When designing a CI/CD pipeline for a large-scale environment, what are the key stages you would include to ensure both speed of delivery and security compliance? How would you enforce quality gates between those stages?** 

3. **You build CI/CD pipelines using Jenkins and Argo CD together. What was the specific responsibility of each tool in your GitOps workflow, and how did you handle environment promotion between staging and production?** 

4. **When managing secrets in a secure pipeline, what strategies and tools do you use to ensure secrets are never exposed in logs, source code, or pipeline artifacts? How would you rotate them without causing downtime?** 

5. **Describe how you structure a Git branching strategy for a team managing multiple microservices, remote actions, and automation scripts, including how you would handle hotfixes, future development, and release tagging.** 

6. **When integrating with a REST API that uses OAuth 2.0 for authentication, what steps would you take to troubleshoot a scenario where your pipeline integration is intermittent and receiving 401 Unauthorized errors despite valid credentials being configured?** 

7. **You use Terraform to manage AWS infrastructure. How did you structure your Terraform state management across multiple environments, and what approach did you take to prevent state file conflicts in a team setting?** 

8. **When building a self-heal automation workflow, how would you design the logic to detect a recurring endpoint issue, trigger a remote action, validate the remediation outcome, and escalate to an ITSM incident if the self-heal fails?** 

9. **How would you validate remediation outcomes in a self-heal automation workflow and escalate to ITSM if the self-heal fails?** 

10. **How would you escalate to an ITSM incident if the self-heal automation fails?** 

11. **You mentioned reducing production vulnerabilities by 60% by embedding SonarQube and OWASP ZAP into your pipelines. How did you configure these tools to run as blocking quality gates, and what thresholds did you define to distinguish a pipeline failure from a warning?** 

12. **When designing a PowerShell script intended to be deployed as a Nexthink remote action across thousands of endpoints, what practices would you follow to ensure the script is idempotent, handles errors gracefully, and produces structured output that can be parsed by the Nexthink platform?** 

13. **How would you produce structured output in a PowerShell script that can be parsed by the next platform step?** 

14. **In a scenario where a deployment automation rollback is triggered in production due to a failed health check, walk through the exact steps your pipeline would execute to safely roll back, notify stakeholders, and initiate a root cause analysis process aligned with change management.** 

15. **How would you initiate a root cause analysis (RCA) process aligned with change management after a deployment rollback triggered by a failed health check?** 

16. **You built Grafana and Prometheus observability dashboards that improved MTTR by 50%. What specific alerting rules and dashboard panels did you configure to detect infrastructure degradation early, and how did you ensure alerts were actionable rather than generating noise?** 

17. **How did you ensure that the alerts you configured in Grafana and Prometheus were actionable and did not generate noise?** 


