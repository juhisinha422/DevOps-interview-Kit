"My pod can't reach the service by name!"

 Ah yes — the classic Kubernetes mystery. One moment everything works like a charm, and the next… total silence. No error. No traffic. Just… nothing.

Here’s what’s actually happening behind the scenes:

 How DNS works inside a pod:

 When your pod tries to connect to a service like my-service, Kubernetes doesn’t rely on magic. Instead, it builds a full DNS name like:

 my-service.namespace.svc.cluster.local

This is resolved using a nameserver defined in the pod's /etc/resolv.conf, which points to CoreDNS or kube-dns running inside your cluster. That service is responsible for translating names into IP addresses of services/endpoints — so your pod knows where to go.

 But when name resolution fails, here’s what to check:

Is CoreDNS (or kube-dns) running and healthy in the kube-system namespace?

Can you nslookup or dig from within the pod? (Install if needed)

Are you using the full or short service name correctly? (my-service vs my-service.namespace)

Are NetworkPolicies or security groups blocking DNS or service traffic?

Is the pod’s /etc/resolv.conf misconfigured or overridden by a custom DNS policy?

Did the service even get created successfully in the first place?

Pro tip:

 Misconfigured DNS is one of the most frustrating and invisible problems in Kubernetes. It won’t throw a stack trace or crash your container — it just makes your app sit there... waiting forever.

So next time a pod acts like it doesn't know its neighbors — it’s probably not being rude. It just needs some DNS help.


![Image](https://github.com/user-attachments/assets/e919513d-2837-4602-9d4a-fc53c0ad0763)
