# Docker & Kubernetes Interview Questions and Answers

## 1. What is docker and how does it solve a problem?

Docker is a containerization platform used to package applications along with their dependencies, libraries, runtime, and configurations into lightweight containers. Before Docker, applications used to run directly on servers or virtual machines, and many times developers faced issues where the application worked in one environment but failed in another due to dependency mismatch or configuration differences. Docker solves this problem by providing consistent environments across development, testing, staging, and production. It also improves deployment speed, scalability, portability, and resource utilization compared to traditional virtual machines.

---

## 2. What existed before docker ? Compare before vs after.

Before Docker, organizations mainly used physical servers and virtual machines such as VMware and VirtualBox. In virtual machines, each VM required a complete operating system, which consumed more CPU, memory, and storage resources. VM startup time was also slower. Docker introduced containers, which share the host OS kernel and are much lighter and faster. Before Docker, deployment consistency and scaling were difficult, whereas after Docker, applications became portable, scalable, and easy to deploy through container images.

---

## 3. How is a Docker client different from a Docker daemon?

Docker Client is the command-line interface used by users to interact with Docker. Commands like `docker build`, `docker run`, and `docker ps` are executed through the Docker client. Docker Daemon, also called `dockerd`, is the backend service running in the background which actually builds images, creates containers, manages networking, volumes, and handles all Docker operations. The Docker client communicates with the Docker daemon using REST APIs.

---

## 4. Explain Docker Architecture and its key components.

Docker architecture consists of multiple components. Docker Client is used by users to execute commands. Docker Daemon is the core engine responsible for building images, running containers, and managing Docker objects. Docker Registry is used to store Docker images and can be public like Docker Hub or private like AWS ECR. Docker Images are read-only templates used to create containers. Docker Containers are running instances of images. Docker also includes networking and storage components such as Docker Networks and Docker Volumes for communication and persistent storage.

---

## 5. What does "pack your app into a container" mean?

Packing an application into a container means bundling the application code along with all required dependencies, libraries, binaries, runtime, and configuration files into a single portable package called a container image. This ensures the application behaves consistently in every environment without dependency conflicts or configuration issues.

---

## 6. Difference between a Docker image and a container.

A Docker image is a read-only template or blueprint used to create containers. It contains application code, dependencies, libraries, and configurations. A Docker container is the running instance of an image where the application actually executes. Images are static and immutable, whereas containers are dynamic and can be started, stopped, or deleted.

---

## 7. Do you know about layering while building an image?

Yes. Docker images are built in layers. Every instruction in a Dockerfile such as `FROM`, `RUN`, `COPY`, or `ADD` creates a separate layer. Docker uses layer caching to optimize builds. If one layer does not change, Docker reuses the cached layer instead of rebuilding it. This improves build speed and storage efficiency because common layers can be shared across multiple images.

---

## 8. How can you share your container?

Containers are usually shared by pushing Docker images to a container registry such as Docker Hub, AWS ECR, Azure ACR, or Google Artifact Registry. Once the image is pushed, other users or systems can pull the image and run containers from it.

---

## 9. What is Docker Hub?

Docker Hub is a public cloud-based container registry provided by Docker. It is used to store, manage, and distribute Docker images. It contains official images for many technologies like Nginx, MySQL, Redis, Ubuntu, and Node.js. Developers can also upload their own custom images to Docker Hub and share them with teams or applications.

---

## 10. Can you create multiple Docker Containers at once?

Yes. Multiple Docker containers can be created and managed together using Docker Compose, Docker Swarm, or Kubernetes. Docker Compose allows defining multiple services inside a YAML file and launching all containers together using a single command.

---

## 11. How can you persist data in Docker?

Data persistence in Docker is achieved using Docker Volumes or Bind Mounts. Volumes are managed by Docker and are the preferred approach for persistent storage because data remains even if the container is deleted. Volumes are commonly used for databases and application storage.

---

## 12. What are bind mounts? Why prefer volumes over bind mounts?

Bind mounts map a directory or file from the host machine directly into the container. While bind mounts are useful during development, Docker volumes are preferred in production because they are managed by Docker, more secure, portable, easier to back up, and independent of the host filesystem structure.

---

## 13. Do you know about Docker Networking?

Yes. Docker networking enables communication between containers, between containers and host systems, and external communication. Docker provides multiple network types such as bridge, host, overlay, macvlan, and none. Networking allows applications running in different containers to communicate securely.

---

## 14. Explain how Docker bridge networking works.

Bridge networking is the default Docker network type. When containers are launched without specifying a network, they are connected to the default bridge network. Containers inside the same bridge network can communicate with each other using container names or IP addresses. Docker internally creates virtual interfaces and manages routing between containers.

---

## 15. Two containers need to talk to each other – how?

Two containers can communicate by connecting them to the same Docker network. Docker provides internal DNS resolution, so containers can communicate using container names instead of IP addresses. Communication can also happen using exposed ports and service discovery.

---

## 16. Explain different commands inside a Dockerfile?

Dockerfile commands are used to define how an image should be built. `FROM` specifies the base image. `RUN` executes commands during image build. `COPY` copies files from host to container image. `ADD` is similar to COPY but also supports extraction and URLs. `CMD` defines the default command executed when the container starts. `ENTRYPOINT` specifies the main executable. `WORKDIR` sets the working directory. `ENV` defines environment variables, and `EXPOSE` documents application ports.

---

## 17. Can you write a Dockerfile for Node.js Application?

A Node.js Dockerfile usually starts with a Node base image, sets a working directory, copies package files, installs dependencies using npm install, copies application code, exposes the required port, and finally starts the application using CMD or ENTRYPOINT.

---

## 18. Difference between CMD and ENTRYPOINT in Dockerfile.

CMD provides the default command or arguments that can be overridden when starting the container. ENTRYPOINT defines the main executable that always runs when the container starts. CMD is more flexible for overriding commands, while ENTRYPOINT is used when the container should behave like a dedicated executable.

---

## 19. What is .dockerignore? Why use it?

`.dockerignore` is used to exclude unnecessary files and directories from being copied into the Docker build context. Common exclusions include `.git`, `node_modules`, logs, and temporary files. Using `.dockerignore` reduces image size, improves build speed, and enhances security.

---

## 20. Write your most used Docker commands?

Commonly used Docker commands include `docker build`, `docker run`, `docker ps`, `docker images`, `docker logs`, `docker exec -it`, `docker stop`, `docker rm`, `docker pull`, `docker push`, and `docker inspect`. These commands are frequently used for building images, managing containers, troubleshooting, and registry operations.


## 21. How to copy files from host to container?

Files can be copied from the host machine to a running container using the `docker cp` command. This command is useful when logs, configuration files, scripts, or patches need to be moved into a container without rebuilding the image. Similarly, files can also be copied from the container back to the host. This is commonly used during debugging or troubleshooting activities in production and lower environments.

---

## 22. How do you debug a failed container?

To debug a failed container, the first step is checking container logs using `docker logs`. If the container is repeatedly crashing, I check the exit code, application logs, environment variables, port conflicts, mounted volumes, and dependency connectivity such as database or APIs. I also use `docker inspect` to verify container configuration and `docker exec -it` to access the container shell for live troubleshooting. In production, I additionally check resource utilization, restart counts, and container health checks.

---

## 23. Can a container restart by itself? Explain restart: always vs default policies?

Yes, Docker containers can restart automatically using restart policies. By default, containers do not restart after failure. The `restart: always` policy ensures the container restarts automatically even after Docker daemon or server reboot. The `on-failure` policy restarts the container only when it exits with a non-zero status code. The `unless-stopped` policy behaves like always but does not restart if manually stopped. Restart policies are important in production environments to improve application availability.

---

## 24. Port already in use error : how to fix?

This error occurs when another process or container is already using the same port on the host machine. To fix it, I first identify which process is using the port using commands like `netstat`, `ss`, or `lsof`. Then I either stop the conflicting process/container or change the port mapping while starting the new container. In Kubernetes environments, I also verify NodePort or LoadBalancer conflicts.

---

## 25. Container exits immediately : possible reasons?

A container may exit immediately if the main application process inside the container finishes or crashes. Common reasons include incorrect CMD or ENTRYPOINT, missing dependencies, application startup failure, permission issues, configuration errors, or invalid environment variables. To troubleshoot, I check container logs, inspect exit codes, and verify application startup commands and configurations.

---

## 26. Your Docker image size is too big and the system is slow : how do you fix it?

To reduce Docker image size, I use lightweight base images such as Alpine Linux, implement multi-stage builds, remove unnecessary packages, and avoid copying unwanted files using `.dockerignore`. I also combine RUN commands to minimize layers and clean package manager caches after installation. Smaller images improve deployment speed, storage efficiency, and security scanning performance.

---

## 27. How can you scan a Docker image for vulnerabilities?

Docker images can be scanned using tools such as Trivy, Docker Scout, Snyk, Clair, or Anchore. These tools identify known CVEs and security vulnerabilities in OS packages and application dependencies. In CI/CD pipelines, image scanning is often integrated before deployment so vulnerable images are blocked from reaching production environments.

---

## 28. Best practices to keep a Docker container secure.

To secure Docker containers, I use minimal base images, avoid running containers as root users, regularly patch and update images, scan images for vulnerabilities, use read-only file systems wherever possible, and avoid storing secrets directly inside images. Sensitive information should be managed using secret managers like AWS Secrets Manager or HashiCorp Vault. I also restrict unnecessary ports and capabilities and follow least-privilege principles.

---

## 29. What are dangling images? How do you remove them?

Dangling images are unused Docker images that do not have tags and are no longer associated with active containers. These images consume unnecessary disk space. They can be removed using cleanup commands such as `docker image prune` or `docker system prune`. Regular cleanup is important to avoid disk utilization issues on Docker hosts.

---

## 31. How can you manage sensitive data like passwords in Docker?

Sensitive data such as passwords, API keys, and tokens should not be hardcoded inside Docker images or Dockerfiles. They can be managed using Docker Secrets, environment variables, external secret management systems like AWS Secrets Manager, Azure Key Vault, or HashiCorp Vault. In Kubernetes environments, Secrets objects are commonly used for securely managing credentials.

---

## 32. What is Docker Swarm? How is it different from Kubernetes?

Docker Swarm is Docker’s native container orchestration tool used for managing multiple Docker nodes and containers. It is simpler to set up and suitable for smaller environments. Kubernetes is a more advanced and feature-rich orchestration platform providing better scalability, self-healing, autoscaling, rolling updates, and ecosystem support. Kubernetes is widely used in enterprise production environments, whereas Docker Swarm is less commonly used now.

---

## 33. Name different Docker services on various cloud platforms.

Different cloud providers offer managed container and registry services. AWS provides ECR for image storage, ECS for container orchestration, and EKS for Kubernetes. Azure provides ACR and AKS. Google Cloud provides Artifact Registry and GKE. These services help organizations manage containerized applications in cloud environments.

---

## 34. Difference between Podman and Docker?

Podman is a daemonless container engine that can run containers without requiring a background service, whereas Docker depends on the Docker daemon. Podman supports rootless containers more securely and follows OCI standards. Docker has a larger ecosystem and is widely adopted, while Podman is often preferred in security-focused Linux environments.

---

## 35. What is the Container Runtime Interface (CRI)?

CRI is the Container Runtime Interface used by Kubernetes to communicate with container runtimes. It allows Kubernetes to work with different runtimes such as containerd and CRI-O. Earlier Kubernetes used Docker directly, but now containerd and CRI-O are commonly used because of CRI compatibility and better Kubernetes integration.

---

## 36. Difference between Docker and Kubernetes ?

Docker is a containerization platform used to build and run containers. Kubernetes is a container orchestration platform used to manage containers at scale across multiple nodes. Docker handles individual containers, while Kubernetes manages deployment, scaling, networking, self-healing, service discovery, rolling updates, and load balancing for large distributed applications.

---

## 37. What is a Pod in Kubernetes? How is it different from a container?

A Pod is the smallest deployable unit in Kubernetes. It can contain one or more containers that share networking and storage. Containers are individual application runtime units, whereas Pods provide a wrapper around containers for orchestration purposes. Containers inside the same Pod communicate using localhost and share the same IP address.

---

## 38. Difference between containerization and virtualization.

Containerization uses the host operating system kernel and isolates applications at the process level, making containers lightweight and fast. Virtualization uses hypervisors to run separate operating systems for each VM, which consumes more resources. Containers start faster and use fewer resources, while VMs provide stronger isolation and support different operating systems.

---

## 39. Do you know How to Containerize a 3-tier application and How can you manage Networking and Volume?

Yes. A 3-tier application usually contains frontend, backend, and database layers. Each component is containerized separately using dedicated Dockerfiles. Docker Compose or Kubernetes is used for orchestration. Networking is managed using Docker bridge networks or Kubernetes Services for communication between components. Persistent volumes are used for databases to ensure data durability. In production Kubernetes environments, Ingress or LoadBalancer services are used to expose applications externally.

---

## 40. Explain the lifecycle of Docker containers.

The Docker container lifecycle starts with image creation using a Dockerfile. A container is then created and started from the image. During execution, the container can be paused, restarted, stopped, or killed. Once no longer required, the container can be removed. Throughout its lifecycle, Docker manages networking, storage, resource allocation, and restart policies. In production, orchestration tools like Kubernetes automate much of the container lifecycle management.

