Kafka 101

1 - What is Kafka? 

Kafka is a distributed event store and a streaming platform. It began as an internal project at LinkedIn and now powers some of the largest data pipelines in the world in orgs like Netflix, Uber, etc. 

2 - Kafka Messages 

Message is the basic unit of data in Kafka. It consists of headers, key, and value. 

3 - Kafka Topics and Partitions 

Every message goes to a particular Topic. Topics have multiple partitions. 

4 - Advantages of Kafka 

Kafka can handle multiple producers and consumers, while providing disk-based data retention and high scalability. 

5 - Kafka Producer 

Producers in Kafka create new messages, batch them, and send them to a Kafka topic.

6 - Kafka Consumer 

Kafka consumers work together as a consumer group to read messages from the broker. 

7 - Kafka Cluster 

A Kafka cluster consists of several brokers where each partition is replicated across multiple brokers to provide high availability and redundancy. 

8 - Use Cases of Kafka 

Kafka can be used for log analysis, data streaming, change data capture, and system monitoring.

![Image](https://github.com/user-attachments/assets/3ca27e4e-02a9-460d-a9fd-0455cf8370fe)
