# 🚀 DevOps Interview Guide – MNC Experience (Docker, AWS, Kubernetes, Monitoring)

---

## What are the kinds of Docker volumes that are available?

Docker supports three main types of volumes. Bind mounts map a directory from the host directly into the container, giving full control but less portability. Named volumes are managed by Docker and stored in Docker’s internal directory, making them easier to manage and portable across containers. Anonymous volumes are similar to named volumes but without a specific name, usually created automatically. Named volumes are preferred in production because they are decoupled from host paths and easier to manage.

---

## Can you tell me with Docker volume on the host /app/db and your container it should be mounted to /opt/data. What is the command that you use to run the Docker container with this mapping?The container name should be test. Can you give that command?

To mount a host directory to a container, I use a bind mount with the `-v` option. The command would be:

```
docker run -d --name test -v /app/db:/opt/data <image-name>
```

This maps the host directory `/app/db` to `/opt/data` inside the container.

---

## In Docker, how do you inspect a container?

I use the `docker inspect <container_name_or_id>` command. It provides detailed information in JSON format, including configuration, network settings, volumes, and runtime details. For quick checks, I also use `docker ps`, `docker logs`, and `docker stats`.

---

## Suppose you have high CPU (60%), high memory (70%), and some network traffic. What else will you check before stopping an EC2 instance?

Before stopping the instance, I check disk I/O usage, running processes, application logs, and load average to understand if the load is expected or abnormal. I also verify whether the instance is part of an autoscaling group or load balancer to avoid impacting production traffic. Additionally, I check recent deployments or cron jobs that might be causing spikes.

---

## How will you check if the instance is behind a load balancer or part of an autoscaling group?

I check in the AWS console under EC2 → Load Balancers to see target group attachments. For autoscaling, I check Auto Scaling Groups to see if the instance is registered. From CLI, I can use `aws elbv2 describe-target-health` and `aws autoscaling describe-auto-scaling-instances`.

---

## How do you check which processes or services are running on that instance? Which commands do you use?

I use commands like `ps -ef`, `top`, or `htop` to view running processes. For services, I use `systemctl list-units --type=service` or `service --status-all`. These help identify resource-consuming processes.

---

## How do you check the active ports on that instance?

I use commands like `netstat -tulnp`, `ss -tulnp`, or `lsof -i` to check active ports and which processes are listening on them.

---

## How do you migrate objects from one S3 bucket to another?

I use AWS CLI command:

```
aws s3 sync s3://source-bucket s3://destination-bucket
```

This copies all objects efficiently. For large-scale migration, I can use S3 replication or AWS DataSync.

---

## If an instance is stopped, how do you check in AWS Cloud which instance was stopped and when?

I check AWS CloudTrail logs, which record all API activity. By filtering for `StopInstances` events, I can identify which instance was stopped, by whom, and at what time.

---

## What are static pods in Kubernetes?

Static pods are managed directly by the kubelet on a node, not by the Kubernetes API server. They are defined using manifest files on the node and are mainly used for running critical components like control plane services.

---

## You mentioned ingress controller. If you are getting 431 or 413 errors, how do you troubleshoot?

These errors are usually related to request size limits. I check ingress controller configurations (like NGINX annotations) for body size limits and header size limits. I update settings like `client_max_body_size` and reload the controller. I also verify application-level limits.

---

## What is status code 413?

HTTP status code 413 means “Payload Too Large”. It occurs when the request body exceeds the allowed size configured on the server or ingress.

---

## What are the major challenges you faced recently?

A good answer is scenario-based. For example, I handled a production issue where application latency increased due to resource bottlenecks. I analyzed metrics, identified CPU saturation, and resolved it by tuning autoscaling and optimizing resource allocation.

---

## How do you get the metrics of your pods, worker nodes, and traffic? How do you get visibility of those?

I use monitoring tools like Prometheus to collect metrics from pods and nodes, and Grafana to visualize them. Kubernetes metrics server provides basic metrics, while Prometheus provides detailed insights into CPU, memory, and request traffic.

---

## How do Prometheus and Grafana work together for observability?

Prometheus collects and stores metrics from various targets. Grafana connects to Prometheus as a data source and visualizes the data through dashboards. Together, they provide monitoring, alerting, and observability.

---

## If there is a DB connectivity issue from pods, what steps do you take to troubleshoot?

I first check if the pod can reach the DB using tools like `kubectl exec` and `curl` or `telnet`. Then I verify service endpoints, DNS resolution, and network policies. I also check security groups and firewall rules.

---

## How do you check Kubernetes networking and namespace communication for DB connectivity?

I verify if pods can communicate across namespaces using service DNS. I check network policies that might block traffic and ensure proper service configuration. Tools like `kubectl get svc`, `kubectl get endpoints`, and `nslookup` help debug connectivity.

---

## If the issue is from DB side, what do you check?

I check if the DB is running, accepting connections, and not overloaded. I verify connection limits, logs, and authentication settings. I also check firewall rules and ensure the DB allows connections from the Kubernetes cluster.

---

## How do you get the deployments and services which have label app=test?

I use:

```
kubectl get deploy,svc -l app=test
```

This filters resources based on label.

---

## Can you print numbers 1 to 10 using a for loop?

In bash:

```
for i in {1..10}
do
  echo $i
done
```

---

## 🚀 Final Tip

For interviews:

* Always combine **commands + explanation**
* Show **real troubleshooting mindset**
* Speak in **step-by-step approach**

---
