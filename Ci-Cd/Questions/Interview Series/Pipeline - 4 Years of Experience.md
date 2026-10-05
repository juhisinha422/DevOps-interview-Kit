### CI/CD Pipeline - 4 Years of Experience

## 1. What happens when a Jenkins pipeline fails and how do you troubleshoot it?

When a Jenkins pipeline fails, Jenkins logs the error output in the console log. To troubleshoot:

Check logs: Look at the console output for error messages that can pinpoint the failure point.
Inspect individual steps: Check if the failure occurs in build, test, deploy, or post steps.
Re-run with increased verbosity: Use the --debug flag for more detailed logs if needed.
Investigate Jenkins configuration: Ensure there are no misconfigurations, missing dependencies, or wrong environment settings.
Check node status: Sometimes, the failure may be due to the build node being offline or improperly configured.

## 2. How do you rollback a failed deployment in production?

To rollback a failed deployment:

Use Version Control: Ensure you have versioned releases using Git tags or commits.
Rollback scripts: Write rollback scripts that can redeploy the last known stable version.
Blue/Green Deployment: If using blue/green or canary deployments, switch traffic back to the stable environment.
Automated rollbacks: If the failure is caught early, implement automatic rollback as part of the CI/CD pipeline.

## 3. How do you manage multiple environments like dev, QA, and prod in CI/CD?

Multiple environments can be managed with:

Environment-specific configuration files: Store environment variables in .env files or use Jenkins Environment Variables.
Branching strategies: Use different branches like develop for dev, staging for QA, and main/master for production.
Pipeline parameters: Define parameters for different stages (dev, QA, prod) and select environment-specific actions (e.g., deploying to specific servers).
Docker/Kubernetes: Containerize environments to ensure consistency across them.

## 4. What Git branching strategy do you follow in real projects and why?

In real projects, I typically use the Gitflow strategy with the following branches:

main: Holds production-ready code, only merged after approval.
develop: The integration branch where features are merged after development and testing.
Feature branches: Created from develop, used for new features or fixes.
Release branches: Used to prepare for a new release, including testing and final bug fixes.
Hotfix branches: Created from main for urgent fixes in production.

Why? Gitflow provides a structured workflow that clearly separates different stages of development, making it easy to manage complex releases.

## 5. What is the difference between Git merge and rebase, and which one did you use in production?
Merge: Combines changes from two branches, preserving the commit history. Suitable for large teams to maintain full commit history.
Rebase: Reapplies changes from one branch onto another, creating a linear history. Great for keeping the history clean.

In production, I prefer merge when dealing with feature branches and releases, as it maintains a complete commit history. I use rebase in smaller teams or personal projects to keep the commit history tidy before merging into develop.

## 6. How do you secure Jenkins credentials and secrets?

To secure Jenkins credentials:

Jenkins Credentials Plugin: Store secrets in Jenkins using the built-in Credentials plugin.
Environment Variables: Use environment variables to inject secrets into the pipeline, never hard-code them.
Vault Integration: Integrate Jenkins with HashiCorp Vault for centralized secret management.
Restrict Permissions: Ensure that only authorized users can access or modify credentials.

## 7. How do you trigger CI/CD pipelines automatically?

To trigger pipelines automatically:

Git Webhooks: Use Git webhooks to trigger Jenkins jobs when code is pushed to specific branches.
Scheduled Builds: Configure Jenkins to run pipelines at fixed intervals (e.g., nightly builds).
Pipeline Triggers: Trigger pipelines manually via REST API or based on events (e.g., pull request creation).
Merge hooks: Trigger pipelines when a pull request is merged into certain branches.

## 8. Explain all the stages present in your CI/CD pipeline?

A typical CI/CD pipeline might include the following stages:

Checkout: Pull the latest code from the Git repository.
Build: Compile the code, install dependencies, and package the application.
Test: Run unit, integration, and UI tests.
Static Analysis: Run tools like SonarQube for code quality and security checks.
Deploy to Dev: Deploy to a dev environment for manual testing.
Deploy to QA: Deploy to a QA environment for validation.
Approval Stage: Manual or automated approval to move to production.
Deploy to Production: Deploy the application to production.
Post-deploy validation: Check logs and run health checks to ensure the app is running correctly.

## 9. How do you handle build failures in Jenkins?

For build failures:

Auto-retry: Implement retries for flaky builds to avoid unnecessary failures.
Detailed Reporting: Use the JUnit or Allure plugins to provide clear failure reports and logs.
Notifications: Integrate Slack or email to notify teams when a build fails.
Pipeline Isolation: Isolate failing steps to minimize the impact on the rest of the pipeline.

## 10. How do you manage secrets inside CI/CD pipelines?

Secrets are managed by:

Jenkins Credentials Plugin for securely storing API keys, passwords, etc.
Secret Manager/HashiCorp Vault to pull secrets dynamically during the build process.
Environment Variables injected during pipeline execution.

## 11. What do you do if deployment succeeds but the application crashes?

In case of a crash:

Check Logs: Review logs from the application and server to identify the root cause.
Rollback: Use a rollback strategy to revert to a stable version.
Automated Health Checks: Implement automated health checks that monitor the system post-deployment.
Alerting: Set up proactive monitoring tools (e.g., Prometheus, Grafana) to alert the team about application health.

## 12. How do you ensure that only approved code is deployed to production?

To ensure approved code is deployed:

Code Reviews: Implement mandatory code reviews via Pull Requests (PRs).
Approval Gates: Use manual or automated approval steps in the CI/CD pipeline before production deployment.
Feature Flags: Use feature flags to test new features safely in production without exposing them.

## 13. How do you notify teams when a pipeline or deployment fails?
Slack Notifications: Integrate Jenkins with Slack to send alerts on failure.
Email Alerts: Configure Jenkins to send email alerts on failure.
Custom Webhooks: Use webhooks to trigger custom notifications to other systems or platforms.
JIRA Issues: Automatically create JIRA tickets for pipeline failures.

## 14. Where and how do you store build artifacts?

Artifacts are stored in:

Artifact Repositories: Use tools like Nexus or Artifactory to store built artifacts.
Cloud Storage: Store artifacts in S3, GCS, or Azure Blob Storage for scalability.
GitHub Releases: Store release artifacts in GitHub’s release section if using GitHub for version control.

## 15. How do you manage versioning of application releases?

Versioning is managed by:

Semantic Versioning (SemVer): Use major.minor.patch versioning.
Git Tags: Tag releases in Git to track the version.
CI/CD Variables: Use environment variables to automate versioning and increment the version number with each build.

## 16. How do you test code before deploying it to production?

Before deployment:

Unit Tests: Run unit tests as part of the pipeline.
Integration Tests: Test interactions between different modules of the application.
UI Tests: Use tools like Selenium to automate UI testing.
Static Code Analysis: Run linting and code quality tools to catch potential issues early.
Load Testing: Test the application under load to ensure it can handle production traffic.

## 17. How do you prevent accidental deployment from the wrong Git branch?

To prevent accidental deployments:

Branch Protection: Use branch protection rules to prevent direct pushes to main or prod branches.
CI/CD Approval Gates: Use approval gates to ensure that only validated code is deployed.
Environment-specific Pipelines: Define separate pipelines for different environments like dev, qa, and prod.

## 18. How do you handle long-running pipelines?

For long-running pipelines:

Parallel Execution: Break the pipeline into smaller stages and run independent tasks in parallel.
**Timeout
in readme

Certainly! Here's the continuation of the README format with the answers for the remaining questions:

## 19. How do you deploy hotfixes in production?

To deploy hotfixes in production:

Create a Hotfix Branch: Branch off from main or production to create a hotfix branch.
Implement the Fix: Apply the required changes to the hotfix branch.
Test: Ensure the fix is tested in a staging or dev environment before deployment.
Merge: Merge the hotfix branch back into main (or production) and develop to maintain consistency.
Deploy: Deploy the hotfix to production using the CI/CD pipeline.
Monitor: Monitor application performance closely post-deployment to ensure the fix works as expected.

## 20. How do you monitor deployments after release?

Post-deployment monitoring includes:

Application Logs: Continuously monitor logs (e.g., using ELK Stack or Splunk) for errors or performance issues.
Health Checks: Implement automated health checks that verify if the app is running properly (e.g., HTTP status codes, DB connectivity).
Monitoring Tools: Use tools like Prometheus, Grafana, or New Relic to monitor system performance and track critical metrics (CPU, memory usage, response times).
Alerts: Set up alerts in case of abnormal behavior (e.g., high error rates, slow response times).
User Feedback: Collect user feedback from both internal teams and end-users to identify issues not covered by automated monitoring.
Continuous Testing: Perform regression and smoke tests in the production environment to catch unexpected issues.


