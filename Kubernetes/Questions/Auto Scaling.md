What are Horizontal Pod Autoscaling & Vertical Pod Autoscaling? 

Autoscaling in Kubernetes is key to managing workloads efficiently, unlike Replica Sets, which maintain a fixed number of pods without adjusting for demand.

Horizontal Pod Autoscaling (HPA) automatically changes the number of pods based on real-time metrics like CPU or memory usage. It’s like adding or removing team members depending on how busy things get.

Vertical Pod Autoscaling (VPA) adjusts the CPU and memory resources assigned to each pod without changing the pod count. Think of it as giving each team member more or less capacity to handle their workload.

Together, HPA and VPA help optimise resource use and keep performance steady in ways Replica Sets alone can’t.



The tricky part with using HPA and VPA together is how they both influence pod behavior in different ways.

HPA scales the number of pods based on resource utilization (like CPU%), while VPA adjusts the actual resource requests. So when VPA increases CPU requests, it can make HPA think the pod is underutilized and scale down — even if the actual load hasn’t changed. If not handled carefully, they kind of fight each other.

Best workaround I've seen is keeping VPA in "initial" mode so it sets sensible defaults without interfering with autoscaling

![Image](https://github.com/user-attachments/assets/1bcdecb1-2233-4ad5-b39d-bda3fca181bd)
