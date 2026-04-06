# 🚀 Docker Interview Questions & Answers (DevOps | 3–4 Years Experience)

This document contains practical Docker interview questions and answers focused on real-world scenarios for mid-level DevOps engineers.

---

## 🐳 Scenario-Based Questions

### 1. Your Docker image size is very large. How do you reduce it?

- Use lightweight base images (e.g., alpine)
- Use multi-stage builds
- Remove unnecessary packages and cache
- Combine RUN commands to reduce layers
- Use .dockerignore to exclude unwanted files

💡 Smaller images improve performance and deployment speed

---

### 2. Explain the difference between a Docker image and a Docker container with a real example.

- Docker Image → Blueprint (read-only template)
- Docker Container → Running instance of an image

Example:
- Image = Java app packaged as Docker image
- Container = Running application from that image

💡 Image is static, container is dynamic

---

### 3. A Docker container exits immediately after starting. How do you debug this issue?

- Check logs:
  docker logs <container_id>
- Verify ENTRYPOINT or CMD
- Check if main process is exiting
- Run interactively:
  docker run -it <image> /bin/sh
- Inspect container:
  docker inspect <container_id>

💡 Container stops when main process stops

---

### 4. Docker build works locally but fails in CI pipeline. What could be the reasons?

- Missing environment variables
- Different Docker versions
- Network issues (dependency downloads)
- Files excluded via .dockerignore
- Permission issues
- Cache differences

💡 Ensure consistency between local and CI environments

---

### 5. What is a multi-stage Docker build and why do you use it?

- Uses multiple stages in Dockerfile
- Separates build and runtime environments

Example:
- Stage 1 → Build application
- Stage 2 → Run application

💡 Reduces image size and improves security

---

### 6. Where do you store Docker images in your project?

- Docker Hub
- AWS ECR
- Azure ACR
- Private registries

💡 Enables versioning and easy deployment

---

### 7. How do you tag Docker images properly for different environments?

- app:dev
- app:staging
- app:prod
- app:v1.0.0

Example:
docker tag app:latest app:v1.0.0

💡 Avoid using only 'latest' in production

---

### 8. How do you handle secrets securely in Docker?

- Use environment variables
- Use Docker/Kubernetes secrets
- Use external tools (AWS Secrets Manager, Vault)

💡 Never hardcode secrets in Dockerfile

---

### 9. What happens if a container exceeds its memory limit?

- Container gets OOMKilled
- Process is terminated

💡 Set proper limits and monitor usage

---

### 10. How do you troubleshoot Docker networking issues?

- Check connectivity:
  docker exec -it <container> ping <service>
- Inspect network:
  docker network inspect <network>
- Verify port mappings
- Check DNS resolution

💡 Most issues are due to network misconfiguration

---

### 11. Why did you choose Docker over virtual machines?

- Lightweight
- Faster startup
- Better resource utilization
- Portable
- Ideal for microservices

💡 Containers are more efficient than VMs

---

### 12. How do you clean unused Docker images and containers?

docker system prune -a

Other commands:
docker container prune
docker image prune
docker volume prune

💡 Frees disk space

---

### 13. How do you scan Docker images for vulnerabilities?

- Trivy
- Clair
- Anchore
- Docker scan

Example:
trivy image <image_name>

💡 Important for security

---

### 14. What happens if the Docker daemon goes down?

- All containers stop
- Docker commands fail

Solution:
- Restart Docker service
- Use restart policy:
  --restart=always

---

### 15. How do you deploy Docker containers to Kubernetes?

- Build Docker image
- Push to registry
- Create Deployment and Service YAML
- Apply using kubectl

Example flow:
docker build → docker push → kubectl apply

💡 Kubernetes handles scaling and availability

---

## 🎯 Key Takeaways

- Focus on real-world scenarios
- Strong understanding of debugging
- Optimize images and ensure security
- Explain answers with use cases

---

## ✅ Interview Level

- Experience: 3–4 Years DevOps Engineer
- Focus Areas:
  - Docker fundamentals
  - Troubleshooting
  - CI/CD integration
  - Kubernetes integration

---

## 🚀 Pro Tip

Always explain:
- What you did
- Why you did it
- Impact of your solution

👉 That’s what interviewers expect
