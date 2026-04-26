# 🚀 Apache Kafka Interview Guide (0–2 Years DevOps)

---

## What is Apache Kafka and why is it used?

Apache Kafka is a distributed event streaming platform used to publish, store, and process real-time data streams. It is widely used for building data pipelines, event-driven architectures, and real-time analytics systems. Kafka is preferred because it is highly scalable, fault-tolerant, and can handle large volumes of data with low latency.

---

## What is a message broker?

A message broker is a system that enables communication between different applications by sending and receiving messages. It acts as an intermediary that decouples producers (senders) and consumers (receivers). Kafka is a type of message broker designed for high-throughput and distributed messaging.

---

## What are topics in Kafka?

A topic in Kafka is a logical channel where messages are stored and organized. Producers send messages to topics, and consumers read messages from them. Topics help categorize and manage data streams efficiently.

---

## What is a partition in Kafka?

A partition is a subset of a topic that allows Kafka to distribute data across multiple servers. Each partition is ordered and can be processed independently, enabling parallel processing and scalability. More partitions mean higher throughput.

---

## What is a producer in Kafka?

A producer is a client application that sends data (messages) to Kafka topics. Producers decide which topic and partition the message should go to and are responsible for publishing data.

---

## What is a consumer in Kafka?

A consumer is an application that reads data from Kafka topics. Consumers process the messages for tasks like analytics, logging, or triggering other workflows.

---

## What is a consumer group?

A consumer group is a group of consumers that work together to read data from a topic. Each partition is consumed by only one consumer in the group, which ensures load balancing and parallel processing.

---

## How does Kafka ensure high availability?

Kafka ensures high availability through replication. Each partition has multiple replicas stored on different brokers. If one broker fails, another replica takes over as the leader, ensuring continuous availability.

---

## What is replication in Kafka?

Replication means copying data across multiple brokers to ensure fault tolerance. Each partition has a leader and follower replicas. The leader handles read/write requests, while followers replicate the data for backup.

---

## What is a broker in Kafka?

A broker is a Kafka server that stores data and handles requests from producers and consumers. A Kafka cluster consists of multiple brokers working together to manage data distribution.

---

## What is ZooKeeper in Kafka?

ZooKeeper is used in older Kafka versions to manage cluster metadata, leader election, and configuration management. It helps coordinate brokers and maintain cluster state. (Note: newer Kafka versions are moving away from ZooKeeper using KRaft mode.)

---

## What is offset in Kafka?

An offset is a unique identifier for each message within a partition. It helps consumers track which messages have been read and ensures reliable message processing.

---

## How do you monitor Kafka?

Kafka can be monitored using tools like Prometheus, Grafana, and Kafka Manager. Key metrics include broker health, consumer lag, throughput, and disk usage. Monitoring helps detect performance issues and failures early.

---

## What are common Kafka use cases?

Common use cases include log aggregation, real-time analytics, event-driven microservices, data streaming pipelines, and messaging between distributed systems.

---

## How do you troubleshoot Kafka issues?

Troubleshooting involves checking broker logs, monitoring consumer lag, verifying topic configurations, and ensuring network connectivity. Tools like `kafka-topics.sh` and `kafka-consumer-groups.sh` help diagnose issues.

---

## What are common Kafka errors?

Common errors include broker not available, leader not available, high consumer lag, disk space issues, and message timeout errors. These are usually related to configuration, resource limits, or network problems.

---

## How do you scale Kafka?

Kafka is scaled by adding more brokers and increasing the number of partitions in topics. This allows better load distribution and higher throughput. Scaling consumers also helps process data faster.

---

## What is data retention in Kafka?

Data retention defines how long Kafka stores messages. It can be configured based on time or size. After the retention period, old messages are deleted automatically.

---

## How do you secure Kafka?

Kafka security includes authentication (SSL/SASL), authorization (ACLs), and encryption (TLS). Access control ensures only authorized users can produce or consume data.

---

## What are best practices for Kafka?

Best practices include using proper partitioning, monitoring consumer lag, enabling replication, securing the cluster, optimizing retention policies, and regularly checking broker health. Proper configuration ensures high performance and reliability.

---

## 🚀 Final Tip

For 0–2 years experience:

* Focus on **basic concepts clearly**
* Explain with **simple real examples**
* Show understanding of **how Kafka is used in projects**

---
