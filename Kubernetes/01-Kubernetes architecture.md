Kubernetes architecture seems big at first, but it’s just a set of components working together.

Here’s the simplest way to understand the core components 👇

kubectl ➜ How you talk to Kubernetes
API Server ➜  The brain that receives and processes every request
Controller Manager ➜  Adjusts cluster state to match the desired state
Scheduler ➜  Finds the best node for each workload
Kubelet ➜  Runs workloads on each machine
etcd ➜  Stores everything about the cluster (its memory)
Kube Proxy ➜  Sends traffic to the right pods
Pod ➜  Where your workloads actually run
Container Runtime ➜  Runs the containers inside pods

Kubernetes can feel huge - but once these pieces click, the whole system starts to make sense.

<img width="800" height="503" alt="Image" src="https://github.com/user-attachments/assets/5008ca2d-9c1d-4649-9460-16e923915657" />
