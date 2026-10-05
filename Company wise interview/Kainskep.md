# 🚀 Cloud DevOps / SRE Interview Notes (Kubernetes + Docker + DevSecOps)

---

## how scheduling works in K8s

Kubernetes scheduling is handled by the kube-scheduler, which is responsible for assigning pods to suitable nodes based on multiple factors. When a pod is created, it enters a Pending state until the scheduler finds a node. The scheduling process happens in two main phases: filtering and scoring. In the filtering phase, the scheduler removes nodes that do not meet the pod’s requirements, such as insufficient CPU/memory, node selectors, taints/tolerations, and affinity/anti-affinity rules. In the scoring phase, the remaining nodes are ranked based on criteria like resource availability, spreading pods evenly, and minimizing latency. The scheduler then binds the pod to the highest-ranked node. After assignment, the kubelet on that node pulls the container image and starts the pod. In real-world scenarios, misconfigured resource requests or strict affinity rules are common reasons pods remain unscheduled.

---

## what happens when you run docker runs

When you execute the `docker run` command, Docker performs several steps internally to create and start a container. First, it checks if the specified image exists locally; if not, it pulls it from a registry like Docker Hub. Then Docker creates a writable container layer on top of the image using a union file system. It sets up namespaces (PID, network, mount) and cgroups to isolate and limit resources such as CPU and memory. Next, it configures networking by assigning an IP address and connecting the container to a bridge network. If ports are specified, Docker maps container ports to host ports. Finally, it executes the container’s entrypoint or command and starts the process. From an SRE perspective, issues often arise due to incorrect entrypoints, missing environment variables, or port conflicts.

---

## network policies

Kubernetes network policies are used to control traffic flow between pods at the IP and port level, acting like a firewall for pod communication. By default, all pods can communicate with each other, but once a network policy is applied, it follows a deny-by-default model. Policies define ingress (incoming) and egress (outgoing) rules based on pod selectors, namespace selectors, and IP blocks. For example, you can allow only specific pods from a particular namespace to access a database service. In production, network policies are essential for enforcing security boundaries and zero-trust architecture. Debugging usually involves checking applied policies, verifying labels, and testing connectivity using tools like curl or netcat.

---

## left shift Devsecops

Left shift DevSecOps means integrating security practices early in the software development lifecycle instead of addressing them at the end. This approach reduces vulnerabilities and improves overall system reliability. In practice, this involves adding security checks in CI/CD pipelines, such as static code analysis, dependency scanning, and container image scanning. Infrastructure as Code (IaC) templates are also validated for security misconfigurations before deployment. Kubernetes security can be enforced using policies like RBAC, network policies, and admission controllers. By shifting security left, teams detect and fix issues earlier, reducing risk and cost while improving deployment speed and confidence.

---

## how egress works for a request in K8s, from pod to end user

When a pod initiates an outbound request (egress), the traffic flows through several layers. First, the application inside the pod sends a request, which goes through the container network interface (CNI). The pod’s IP is used, and traffic is routed to the node’s network stack. If the destination is external (internet), the request typically passes through a NAT mechanism (like a NAT Gateway in cloud environments) because pods usually have private IPs. The node’s route table determines how traffic exits the VPC or cluster. If network policies are defined, they are evaluated to allow or block the traffic. The request then reaches the external service, and the response follows the reverse path back to the pod. In production, debugging egress issues involves checking network policies, route tables, NAT configuration, DNS resolution, and firewall rules.

---

## 🚀 Final Tip

For this level of interview:

* Always explain **flow (step-by-step)**
* Add **real-world debugging scenarios**
* Mention **security + networking + scaling together**

---
