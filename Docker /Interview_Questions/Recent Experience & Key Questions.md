# Docker Interview Prep — My Recent Experience & Key Questions

---

## 1. Docker images vs Docker containers - what's the difference?

* **Image**: Read-only template (blueprint)
* **Container**: Running instance of an image

---

## 2. What are the advantages of containerizing applications?

* Portability
* Consistency across environments
* Faster deployment
* Scalability
* Resource efficiency

---

## 3. What is a Dockerfile and what instructions do you use in it?

* A script to build Docker images
* Common instructions:

  * `FROM`, `RUN`, `COPY`, `ADD`, `CMD`, `ENTRYPOINT`, `WORKDIR`, `EXPOSE`

---

## 4. How do you reduce Docker image size using multi-stage builds?

* Use separate build & runtime stages
* Copy only required artifacts to final image
* Avoid unnecessary dependencies

---

## 5. Why should containers run as non-root?

* Improves security
* Limits damage if container is compromised

---

## 6. What is image immutability?

* Images are not modified after creation
* Any change → new image version

---

## 7. Difference between ADD and COPY in Dockerfile

| COPY             | ADD               |
| ---------------- | ----------------- |
| Simple file copy | Advanced features |
| No extraction    | Can extract tar   |
| Preferred        | Less predictable  |

---

## 8. Difference between CMD and ENTRYPOINT - can we use both?

* CMD → default command
* ENTRYPOINT → fixed executable
* Yes, both can be used together

---

## 9. What happens if both CMD and ENTRYPOINT are defined?

* ENTRYPOINT runs first
* CMD acts as arguments to ENTRYPOINT

---

## 10. What challenges do you face when containerizing legacy monolithic applications?

* Tight coupling
* Stateful design
* Hardcoded configs
* Dependency issues

---

## 11. How do you make a legacy application stateless for containers?

* Externalize state (DB, Redis)
* Use environment variables
* Store files in external storage

---

## 12. How do you handle filesystem dependencies and background jobs in containers?

* Use volumes for persistent data
* Move background jobs to separate containers or schedulers

---

## 13. What is the "one process per container" principle?

* Each container should run a single main process
* Improves isolation and scalability

---

## 14. How do sidecars help in containerizing legacy applications?

* Add supporting features (logging, monitoring)
* Without modifying main app

---

## 15. What security considerations do you take while containerizing old applications?

* Run as non-root
* Scan images for vulnerabilities
* Use minimal base images
* Avoid hardcoded secrets

---

## 16. How is the Docker client different from the Docker daemon?

* Client → CLI interface
* Daemon → background service that builds/runs containers

---

## 17. What is Docker networking and which commands create bridge/overlay networks?

* Enables communication between containers

```bash id="7gh2kq"
docker network create --driver bridge my_bridge
docker network create --driver overlay my_overlay
```

---

## 18. What is the difference between Docker, Dockerfile, and Docker Compose?

| Tool           | Purpose                       |
| -------------- | ----------------------------- |
| Docker         | Container runtime             |
| Dockerfile     | Image build instructions      |
| Docker Compose | Multi-container orchestration |

---

## 19. What are three best practices to secure a Docker container?

* Use non-root user
* Use trusted base images
* Limit container capabilities

---

## 20. What is the difference between a Dockerfile and a Docker registry?

* Dockerfile → builds image
* Registry → stores images (like Docker Hub)

---

## 21. Have you worked with Docker volumes? What happens to volume if container is removed?

* Volumes persist even after container removal
* Must delete manually if not needed

---

## 22. Can you create a Dockerfile for a Python application?

```dockerfile id="x91pqr"
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

---

## 23. If requirements.txt is at remote location, how will you copy it in Dockerfile?

```dockerfile id="v5r8tm"
ADD https://example.com/requirements.txt .
```

OR

```dockerfile id="k3n9wx"
RUN curl -o requirements.txt https://example.com/requirements.txt
```

---
