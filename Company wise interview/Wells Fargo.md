# Wells Fargo DevOps Interview Questions & Answers (4+ Years Experience)

## 1. How do you build docker image.

Docker images are built using a Dockerfile, which contains instructions required to package an application along with its dependencies. In my projects, I first create a Dockerfile specifying the base image, application code, dependencies, environment variables, and startup commands. Once the Dockerfile is ready, I execute the docker build command and assign a version tag to the image. After successful image creation, I validate it locally by running a container and testing application functionality. Finally, the image is pushed to a container registry such as Docker Hub, Amazon ECR, GitHub Container Registry, or Harbor, from where Kubernetes or deployment pipelines can pull it. In production environments, image building is usually automated through Jenkins, GitLab CI/CD, or GitHub Actions.

---

## 2. Can you build a docker image in kubernetes?

Yes, Docker images can be built inside Kubernetes, although it is not a common production practice. Traditionally, images are built in CI/CD pipelines and then pushed to a registry. However, Kubernetes supports image building using tools such as Kaniko, Buildah, Tekton, and BuildKit. These tools build container images without requiring privileged Docker daemon access. For example, Kaniko runs as a Kubernetes pod and builds images directly from source code repositories before pushing them to a registry. This approach is commonly used in cloud-native environments where Kubernetes itself hosts the CI/CD workloads.

---

## 3. Where do you write images in kubernetes?

In Kubernetes, container images are specified in the Pod, Deployment, StatefulSet, DaemonSet, or Job manifest under the image field. Kubernetes does not store the image itself; it only stores the image reference. When a pod is created, the kubelet running on the worker node pulls the specified image from the configured container registry. For example, the image field may contain values such as nginx:latest, company/payment-service:v2.3, or an ECR repository URL. Kubernetes then downloads the image and creates the container using the container runtime.

---

## 4. Pod is continuously restarting what will you do?

If a pod is continuously restarting, the first thing I check is the pod status and events using kubectl describe pod. Then I examine application logs using kubectl logs and kubectl logs --previous if the container has already restarted. Common causes include application crashes, incorrect environment variables, database connectivity failures, missing ConfigMaps or Secrets, insufficient memory causing OOMKilled errors, failed readiness or liveness probes, or incorrect container startup commands. I also verify resource limits, network connectivity, and application dependencies. Once the root cause is identified, I implement the fix, redeploy the application, and monitor the pod stability before closing the incident.

---

## 5. Pod running in dev but not in prod why?

When a pod runs successfully in development but fails in production, I first compare configuration differences between environments. The issue is often related to environment variables, Secrets, ConfigMaps, network policies, resource limits, IAM permissions, storage configurations, or database endpoints. I verify whether the same container image is being used across both environments and inspect logs for application-specific errors. I also check namespace-level restrictions, RBAC permissions, ingress configurations, service connectivity, and external dependencies. In production environments, stricter security controls and infrastructure differences frequently expose issues that may not be visible in development environments.

---

## 6. Can you build docker image in terraform? Why is terraform used?

Although Terraform can invoke scripts or external commands using provisioners such as local-exec, it is not recommended to use Terraform for building Docker images. Terraform's primary purpose is Infrastructure as Code, meaning it is designed to provision and manage infrastructure resources such as VPCs, EC2 instances, EKS clusters, security groups, load balancers, databases, and storage. Image building is better handled by CI/CD tools such as Jenkins, GitLab CI/CD, GitHub Actions, or Azure DevOps. In production, Terraform manages infrastructure while CI/CD pipelines handle application packaging and image creation, maintaining a clear separation of responsibilities.

---

## 7. Which CI/CD tools you have used. What is CI/CD?

I have worked extensively with Jenkins, GitLab CI/CD, GitHub Actions, ArgoCD, and AWS CodePipeline. Continuous Integration (CI) is the process of automatically building, testing, and validating code whenever developers commit changes to a source code repository. Continuous Delivery or Continuous Deployment (CD) automates the deployment of validated code into different environments such as development, UAT, staging, and production. CI/CD reduces manual effort, improves deployment consistency, enables faster releases, and helps detect defects early in the software delivery lifecycle. In my projects, CI pipelines build Docker images, run tests, perform security scans, and publish artifacts, while CD pipelines deploy applications to Kubernetes and cloud environments.

---

## 8. How can you optimize docker images?

Docker image optimization is important for reducing build time, storage consumption, security risks, and deployment time. I start by selecting lightweight base images such as Alpine Linux whenever possible. I use multi-stage builds to separate build dependencies from runtime dependencies so that only the final application artifacts are included in the production image. I remove unnecessary packages, temporary files, caches, and development tools from the final image. I also combine Dockerfile layers efficiently, use .dockerignore to exclude unwanted files, and pin package versions to ensure consistent builds. Additionally, I perform vulnerability scanning using tools such as Trivy, Snyk, or Clair to reduce security exposure. These practices help keep images small, secure, and efficient.

---

## 9. Deployment is failing. How do you investigate?

When a deployment fails, I follow a structured troubleshooting process. First, I identify where the failure occurred by examining CI/CD pipeline logs, Kubernetes deployment events, and application logs. I verify whether the container image was built and pushed successfully. Next, I inspect the Deployment resource, ReplicaSets, and pod status to determine whether pods were created and whether they became healthy. I review readiness and liveness probe configurations, resource requests and limits, Secrets, ConfigMaps, network policies, and service dependencies. If the deployment introduced application errors, I analyze logs and compare the new version against the previous stable release. In production environments, if the issue affects users, I initiate a rollback to restore service quickly while conducting root cause analysis in parallel. After resolving the issue, I document findings and implement preventive measures to avoid recurrence.

