# 🚀 DevOps Interview Guide – SquareOps (Kubernetes + AWS)

---

## What is the current Kubernetes version you are working with?

In my current environment, I usually work with a recent stable Kubernetes version (for example 1.27/1.28 depending on project upgrades). I make sure clusters are not too far behind because older versions miss security patches and features. I also follow Kubernetes release notes to stay updated on deprecations and API changes.

---

## How do you perform a Kubernetes cluster upgrade?

For managed services like EKS, I upgrade the control plane first using AWS console or CLI, ensuring compatibility with workloads. Then I upgrade worker nodes using rolling updates or node group upgrades. I ensure zero downtime by using PodDisruptionBudgets, proper readiness probes, and draining nodes before upgrade. I always test in lower environments before production.

---

## How does Kubernetes Ingress route traffic to services and pods?

Ingress acts as an entry point for HTTP/HTTPS traffic. It defines rules based on hostnames or paths and routes requests to Kubernetes Services. The Ingress Controller (like NGINX or AWS ALB controller) reads these rules and forwards traffic to the appropriate service, which then load balances traffic to pods via endpoints.

---

## How does a pod get created when one replica is in a failed/pending state?

Kubernetes uses controllers (like Deployment controller) to maintain the desired state. If one pod fails or is pending, the controller detects that the desired replica count is not met and creates a new pod. The scheduler then assigns the pod to a node, and kubelet ensures it runs on that node.

---

## How do you configure Horizontal Pod Autoscaler (HPA)?

HPA is configured based on metrics like CPU or memory usage. I define min and max replicas and target utilization. Kubernetes automatically scales pods up or down based on load. Metrics are collected via Metrics Server or Prometheus. This helps handle varying traffic efficiently.

---

## What is NLB (Network Load Balancer), and when would you use it?

NLB is a Layer 4 load balancer in AWS that operates at TCP/UDP level. It is used when we need high performance, low latency, and support for non-HTTP traffic. It is ideal for handling large volumes of traffic or when static IPs are required.

---

## How do you expose Kubernetes services externally?

Services can be exposed using:

* NodePort (basic external access)
* LoadBalancer (cloud-managed load balancer)
* Ingress (HTTP/HTTPS routing with rules)

In production, Ingress with a load balancer is preferred for better routing and scalability.

---

## In a Kubernetes Deployment with three replicas, if one pod enters a failed or stuck state (e.g., Pending, CrashLoopBackOff), how does Kubernetes detect and handle this situation? Can you explain the end-to-end flow, including how different components (kubelet, controller manager, scheduler) interact to evict and replace the pod?

When a pod fails, kubelet on the node detects issues like container crashes or health check failures. It reports status to the API server. The controller manager continuously monitors desired vs actual state. If one replica is unhealthy, it triggers creation of a new pod. The scheduler assigns the new pod to a suitable node. If the old pod is stuck, it may be restarted or terminated based on policies. This ensures the system always maintains the desired number of replicas.

---

## How ingress is redirecting the traffic to target pods? (follow up: Since pods are ephemeral in nature how traffic is redirected to the target pods)

Ingress routes traffic to services, not directly to pods. Services maintain a list of pod endpoints dynamically. Even if pods are recreated, the service updates endpoints automatically. This ensures traffic is always routed to healthy pods.

---

## Which problem does ingress solve & How? (In my answer I mentioned NLB so I got a follow up)

Ingress solves the problem of managing multiple services behind a single entry point. Instead of exposing each service separately, ingress allows routing based on path or domain. It simplifies traffic management and reduces cost by using a single load balancer.

---

## If ingress is already there why do you need NLB?

Ingress works at application layer (L7), but it still needs an external load balancer to receive traffic. NLB provides external entry point at network layer (L4). Ingress controller sits behind it. So NLB handles incoming traffic and ingress manages routing inside the cluster.

---

## What are the different types of load balancers in AWS?

AWS provides:

* Application Load Balancer (ALB) – Layer 7 (HTTP/HTTPS)
* Network Load Balancer (NLB) – Layer 4 (TCP/UDP)
* Classic Load Balancer – legacy

Each serves different use cases based on protocol and performance needs.

---

## We can use NLB to direct the external traffic as well then why do we need to use ALB ?

NLB works at Layer 4 and cannot perform advanced routing like path-based or host-based routing. ALB operates at Layer 7 and supports features like URL routing, SSL termination, and authentication. For microservices and web apps, ALB is preferred.

---

## Say I have an EC2 and I have lost my pem key how will I connect to it? (I mentioned Session Manager then a follow up ) How to enable Session manager on an EC2?

If PEM key is lost, I can use AWS Systems Manager Session Manager to connect. To enable it, the instance must have SSM agent installed, an IAM role with SSM permissions attached, and connectivity to SSM endpoints. Once configured, I can connect via AWS console without SSH keys.

---

## A client provides a GitHub repository and wants the application deployed on an Amazon EKS cluster. What Kubernetes manifests/resources would you create to achieve this deployment?

I would create:

* Deployment (to manage pods)
* Service (to expose application internally)
* Ingress (for external access)
* ConfigMap and Secret (for configuration and sensitive data)
* HPA (for scaling)
  Additionally, I would build a CI/CD pipeline to automate build, push Docker image to ECR, and deploy to EKS using these manifests.

---

## 🚀 Final Tip

For interviews like this:

* Focus on **how components interact (not just definitions)**
* Explain **flow (end-to-end thinking)**
* Be honest if unsure (like you did 👍) and still attempt logically

---
