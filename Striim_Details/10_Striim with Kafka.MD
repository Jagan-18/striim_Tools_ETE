## What is Kafka?
**Apache Kafka** is a distributed event streaming platform used to publish, store, and process real-time data streams.

Think of Kafka as a **high-speed messaging system** that allows applications to exchange data in real time.

---
## Kafka Architecture:
```text
Producer --> Topic --> Consumer
```

Example:

```text
Application
     |
     v
 Producer
     |
     v
 Kafka Topic
     |
     v
 Consumer
     |
     v
 Database / Application
```

---
## Core Components:
### 1. Producer

Sends messages to Kafka.

Example:

```text
Online Shopping App
       |
       v
    Producer
```

---
### 2. Topic:
A logical channel where messages are stored.

Example:

```text
customer-topic
order-topic
payment-topic
```

---
### 3. Consumer:
Reads messages from Kafka topics.

Example:

```text
Kafka Topic
      |
      v
 Consumer
```

---
### 4. Broker:
A Kafka server that stores messages.

```text
Broker-1
Broker-2
Broker-3
```

Multiple brokers form a Kafka Cluster.

---
## Kafka Flow:
```text
Order Application
       |
       v
   Producer
       |
       v
  order-topic
       |
       v
 Consumer
       |
       v
 Database
```

---
## Why Kafka?
✅ Real-time data streaming
✅ High throughput (millions of messages)
✅ Fault tolerant
✅ Scalable
✅ Distributed architecture

---
## Kafka with Striim:
### Kafka as Source

```text
Kafka
   |
KafkaReader
   |
Striim
   |
BigQuery
```

### Kafka as Target:
```text
Oracle
   |
Striim
   |
KafkaWriter
   |
Kafka Topic
```

---
## Common Kafka Commands:
### Create Topic

```bash
kafka-topics.sh --create \
--topic customer-topic \
--bootstrap-server localhost:9092
```

### List Topics
```bash
kafka-topics.sh --list \
--bootstrap-server localhost:9092
```

### Produce Messages
```bash
kafka-console-producer.sh \
--topic customer-topic \
--bootstrap-server localhost:9092
```

### Consume Messages:
```bash
kafka-console-consumer.sh \
--topic customer-topic \
--from-beginning \
--bootstrap-server localhost:9092
```

---
**What is Kafka?**
- Apache Kafka is a distributed event streaming platform used for real-time data ingestion, processing, and messaging. It uses Producers to publish messages to Topics and Consumers to read those messages. Kafka is widely used for log processing, event-driven architectures, CDC pipelines, and real-time analytics.

### One-Line Definition:
**Kafka = Producer → Topic → Consumer (Real-Time Data Streaming Platform)**

For a DevOps engineer, Kafka is commonly used with:
* Striim
* Kubernetes
* Microservices
* Real-time analytics
* Event-driven applications
* CDC pipelines (Oracle → Striim → Kafka → Consumers)

---
---

# Striim with Kafka:
**Apache Kafka** is commonly used with Striim for real-time event streaming.

Striim can work with Kafka in two ways:

1. **Kafka as a Source** (KafkaReader)
2. **Kafka as a Target** (KafkaWriter)

---
# Kafka as a Source:
In this scenario, Kafka produces messages and Striim consumes them.

```text
Kafka Topic
      |
      v
 KafkaReader
      |
      v
   Stream
      |
      v
 BigQuery/Snowflake/DB
```

### Example Use Case:
* Application sends events to Kafka.
* Striim reads Kafka messages.
* Striim loads data into BigQuery.

```text
Application
     |
     v
Kafka Topic
     |
KafkaReader
     |
CustomerStream
     |
BigQueryWriter
     |
BigQuery
```

---
# Kafka as a Target:
In this scenario, Striim publishes events to Kafka.

```text
Oracle
   |
OracleReader
   |
CDC Stream
   |
KafkaWriter
   |
Kafka Topic
```

### Example Use Case:
* Oracle CDC captured by Striim.
* Changes published to Kafka.
* Microservices consume events.

```text
Oracle DB
     |
Redo Logs
     |
OracleReader
     |
CustomerStream
     |
KafkaWriter
     |
customer-topic
```

---
# Kafka Components:
```text
Producer --> Topic --> Consumer
```

### In Striim:
When Kafka is a Target:

```text
Striim = Producer
Kafka = Topic
Application = Consumer
```

When Kafka is a Source:

```text
Application = Producer
Kafka = Topic
Striim = Consumer
```

---
# KafkaReader Configuration:
Example TQL:

```sql
CREATE SOURCE KafkaSource
USING KafkaReader (
    brokerAddress:'kafka01:9092',
    Topic:'customer-topic'
)
OUTPUT TO CustomerStream;
```

Flow:

```text
Kafka Topic
      |
KafkaReader
      |
CustomerStream
```

---
# KafkaWriter Configuration:
Example TQL:

```sql
CREATE TARGET KafkaTarget
USING KafkaWriter (
    brokerAddress:'kafka01:9092',
    Topic:'employee-topic'
)
INPUT FROM EmployeeStream;
```

Flow:

```text
EmployeeStream
      |
KafkaWriter
      |
Kafka Topic
```

---
# Oracle CDC → Kafka:
One of the most common enterprise use cases.

```text
Oracle
   |
OracleReader
   |
CDC Stream
   |
KafkaWriter
   |
Kafka Topic
```

Benefits:

* Real-time event distribution
* Decoupled architecture
* Multiple consumers

---
# Kafka → BigQuery:
Common GCP architecture.

```text
Kafka
  |
KafkaReader
  |
CustomerStream
  |
BigQueryWriter
  |
BigQuery
```

Use Cases:

* Analytics
* Reporting
* Data Warehousing

---
# Kafka → Snowflake:
```text
Kafka
  |
KafkaReader
  |
OrderStream
  |
SnowflakeWriter
  |
Snowflake
```

---
# Multiple Targets from Kafka:
Striim can read once and write to multiple targets.

```text
Kafka Topic
      |
KafkaReader
      |
CustomerStream
      |
  +--------+--------+
  |        |        |
  v        v        v
BigQuery Kafka   Snowflake
```

This follows the **Read Once, Stream Anywhere** pattern.

---
# Message Formats:
Kafka messages can be:

* JSON
* CSV
* XML
* Avro

Example JSON:

```json
{
  "customer_id": 1001,
  "name": "Jagadeesh",
  "city": "Bangalore"
}
```

Striim parses and processes these records.

---
# Kafka Monitoring in Striim:
Monitor:

### Throughput:
```text
Messages/sec
```

Example:

```text
10000 messages/sec
```

### Consumer Lag:
```text
Kafka Offset
      -
Processed Offset
```

Example:

```text
Lag = 500 messages
```

### Errors:

```text
Broker Unavailable
Authentication Failed
Topic Not Found
```

---
# Kafka Security:
Common configurations:

### SSL

```text
SSL Enabled
```

### SASL

```text
SASL_PLAINTEXT
SASL_SSL
```

### Authentication

```text
Username
Password
Certificates
```

---
# Kafka + Striim Production Architecture:
```text
                  Oracle DB
                       |
                  OracleReader
                       |
                CustomerStream
                       |
                  KafkaWriter
                       |
                Kafka Cluster
              /      |      \
             /       |       \
            v        v        v
      Service-A Service-B Analytics

                       |
                  KafkaReader
                       |
                  BigQuery
```

---
### Can Kafka be both Source and Target in Striim?

Yes.

* KafkaReader → Kafka as Source
* KafkaWriter → Kafka as Target

---
### What is KafkaReader?
KafkaReader is a Striim source adapter that consumes messages from Kafka topics and publishes them into Striim streams.

---
### What is KafkaWriter?
KafkaWriter is a Striim target adapter that publishes Striim stream events into Kafka topics.

---
### Why use Kafka with Striim?
* Event-driven architecture
* Real-time streaming
* Decoupled systems
* Multiple consumers
* High throughput and scalability

---
# Quick Revision:
```text
Kafka as Source
---------------
Kafka Topic
    |
KafkaReader
    |
Stream
    |
Target

Kafka as Target
---------------
Source
   |
Stream
   |
KafkaWriter
   |
Kafka Topic

Adapters
--------
KafkaReader
KafkaWriter

Common Pipelines
----------------
Oracle -> Kafka
Kafka -> BigQuery
Kafka -> Snowflake
Oracle -> Kafka -> Microservices
```

# How is Kafka integrated with Striim?
- Striim integrates with Kafka using KafkaReader and KafkaWriter adapters. KafkaReader consumes messages from Kafka topics and streams them into Striim for transformation and delivery to targets like BigQuery or Snowflake. KafkaWriter publishes CDC events or transformed data from Striim streams into Kafka topics, enabling real-time event-driven architectures and downstream processing by multiple consumers.

---

