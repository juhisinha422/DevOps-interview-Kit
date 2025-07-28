Kubernetes (K8s) Architecture:-

Kubernetes (K8s) is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications. Its architecture is distributed and follows a client-server model, consisting of control plane (master) nodes and worker (data plane) nodes.

1. Kubernetes Control Plane (Master Node)

The control plane manages the cluster and makes global decisions about scheduling, scaling, and maintaining the desired state. It consists of the following key components:

a) kube-apiserver

The front-end of the control plane, exposing the Kubernetes API.
Handles REST operations, validates requests, and updates etcd.
Acts as the communication hub between all components.

b) etcd

A distributed key-value store that stores the cluster’s state (configurations, secrets, and metadata).

Ensures high availability with leader-election and consensus algorithms (Raft).

Only the API server interacts with etcd for security reasons.

c) kube-scheduler

Watches for newly created Pods with no assigned node and selects the best node for them.

Considers factors like resource requirements, affinity/anti-affinity rules, and node health.

d) kube-controller-manager

Runs controller processes that regulate the cluster state:

Node Controller: Monitors node status and handles failures.

Replication Controller: Ensures the correct number of Pod replicas are running.

Deployment Controller: Manages rolling updates and rollbacks.

Endpoint Controller: Updates Services with Pod IPs.

Service Account & Token 

Controllers: Manages API access tokens.

e) cloud-controller-manager (Optional)

Integrates with cloud provider APIs (AWS, GCP, Azure) for load balancers, storage, and node management.

2. Worker Nodes (Data Plane)

Worker nodes run containerized applications and are managed by the control plane. Each node includes:

a) kubelet

The primary node agent that communicates with the control plane.
Ensures Pods are running as specified in PodSpecs.

Reports node and Pod status back to the API server.

b) kube-proxy

Manages network rules on nodes to enable communication between Pods and Services.

Implements ClusterIP, NodePort, and LoadBalancer services using iptables/IPVS.

c) Container Runtime

Software responsible for running containers (e.g., containerd, CRI-O, Docker).

Interfaces with Kubernetes via the Container Runtime Interface (CRI).


![Image](https://github.com/user-attachments/assets/c3523f94-db98-452c-98d6-f1d1da1dba33)
