Not every node should run every Pod 

In Kubernetes, Taints and Tolerations work together to control where Pods can be scheduled.
Think of taints as “keep out” signs for nodes; they repel Pods unless those Pods have matching tolerations.

How it works:

- Taints are applied to nodes to restrict which Pods can land there.

- Tolerations are added to Pods to let them “ignore” specific taints and still run.

- Together, they ensure sensitive workloads stay isolated and resources are used efficiently.

Example:
Run system-critical Pods only on specific nodes while keeping general workloads elsewhere.


![Image](https://github.com/user-attachments/assets/d4814592-4bb9-4b20-8eeb-648241f1da87)
