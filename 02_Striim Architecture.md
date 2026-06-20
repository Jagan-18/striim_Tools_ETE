# Striim Architecture

Striim is built on a **streaming-first architecture** that captures data changes in real time, processes them in memory, and delivers them to multiple target systems with low latency. It uses **Change Data Capture (CDC)**, streams, Continuous Queries (CQ), and targets to create end-to-end real-time data pipelines. 

## High-Level Architecture

```text
+------------------+
| Source Systems   |
| Oracle, MySQL,   |
| SQL Server, etc. |
+--------+---------+
         |
         | CDC / Readers
         v
+------------------+
| Source Adapter   |
+--------+---------+
         |
         v
+------------------+
| Stream           |
| (Event Pipeline) |
+--------+---------+
         |
         v
+------------------+
| CQ (Continuous   |
| Query)           |
| Filter/Transform |
+--------+---------+
         |
         v
+------------------+
| Target Adapter   |
+--------+---------+
         |
         v
+------------------+
| Kafka, Snowflake,|
| BigQuery, S3, DB |
+------------------+
```

## Core Components:

### 1. Source:
The Source component captures data from databases, files, message queues, applications, and cloud services. For databases, Striim commonly uses CDC to read transaction logs and capture INSERT, UPDATE, and DELETE operations in real time without impacting the source database. 

Examples:

* OracleReader
* MySQLReader
* SQLServerReader
* KafkaReader

---
### 2. Stream:
A Stream is a continuous flow of events moving between components. It acts as the transport layer that carries captured data through the pipeline. Streams can be in-memory or persistent for recovery and replay purposes. 

---
### 3. Continuous Query (CQ):
CQ is Striim's processing engine.

It performs:

* Filtering
* Data enrichment
* Joins
* Aggregations
* Transformations

CQ uses SQL-like syntax called TQL and runs continuously on streaming data. 

Example:

```sql
CREATE CQ HighValueOrders
INSERT INTO FilteredOrders
SELECT *
FROM OrdersStream
WHERE amount > 1000;
```

---
### 4. Target:
Targets write processed data to external systems. A single stream can feed multiple targets simultaneously. 

Examples:

* SnowflakeWriter
* KafkaWriter
* BigQueryWriter
* S3Writer
* OracleWriter

---
### 5. Dashboard & Monitoring:
Striim provides:

* Pipeline monitoring
* Throughput metrics
* Latency monitoring
* Error tracking
* Alerts and observability

This helps monitor the health of real-time data pipelines. 

---
## End-to-End Example:
### Oracle → Striim → Snowflake

```text
Oracle DB
    |
    | CDC
    v
OracleReader
    |
    v
OrdersStream
    |
    v
CQ (Filter/Transform)
    |
    v
SnowflakeWriter
    |
    v
Snowflake
```

### Data Flow:
1. Oracle transaction log captures changes.
2. OracleReader reads CDC events.
3. Events enter OrdersStream.
4. CQ filters and transforms data.
5. SnowflakeWriter loads data into Snowflake in real time. 

---
## Architecture Layers:

```text
┌─────────────────────────┐
│ Presentation Layer      │
│ Dashboard, Monitoring   │
└───────────┬─────────────┘
            │
┌───────────▼─────────────┐
│ Processing Layer        │
│ CQ, Windows, Caches     │
└───────────┬─────────────┘
            │
┌───────────▼─────────────┐
│ Streaming Layer         │
│ Streams, Routers        │
└───────────┬─────────────┘
            │
┌───────────▼─────────────┐
│ Ingestion Layer         │
│ CDC Readers, Sources    │
└───────────┬─────────────┘
            │
┌───────────▼─────────────┐
│ Source Systems          │
│ DBs, Apps, Files, Kafka │
└─────────────────────────┘
```

## Important Interview Question

**Q: Explain Striim Architecture.**

**Answer:**
Striim architecture consists of Sources, Streams, Continuous Queries (CQ), and Targets. Sources capture data using CDC or adapters, Streams transport events in real time, CQ components perform filtering and transformation using TQL, and Targets deliver processed data to databases, data warehouses, Kafka, or cloud platforms. The entire architecture is designed for low-latency real-time data integration and streaming analytics. 

As a DevOps engineer, you can think of Striim as a **real-time data pipeline platform**, similar to how Jenkins automates CI/CD pipelines, but for streaming data.


---
# Striim Architecture – DevOps Engineer Point of View:

As a **DevOps Engineer**, you usually focus less on the business logic (CQ, transformations) and more on:

* Installation & Deployment
* Infrastructure Management
* High Availability
* Monitoring & Logging
* Security
* Backup & Recovery
* Performance Tuning

---
## Striim Deployment Architecture:

```text
+----------------------+
| Source Databases     |
| Oracle, MySQL, SQL   |
+----------+-----------+
           |
           v
+----------------------+
| Striim Server        |
| (Application Layer)  |
+----------+-----------+
           |
           v
+----------------------+
| Kafka/Snowflake      |
| BigQuery/S3          |
+----------------------+
```

### DevOps Responsibilities:

✔ Install Striim

✔ Configure JVM Memory

✔ Configure HA

✔ Monitor CPU, Memory, Disk

✔ Manage Backups

✔ Upgrade Striim Versions

✔ Troubleshoot Logs

---
# Striim Components from DevOps Perspective:

## 1. Striim Server

The core application server.

### Similar To:

* Jenkins Master
* Nexus Server
* SonarQube Server

### Tasks:

* Installation
* Startup/Shutdown
* Version Upgrades
* Service Monitoring

Example:

```bash
service striim start
service striim stop
service striim status
```

---
## 2. Metadata Repository:

Stores:

* Applications
* Sources
* Targets
* User Configurations

Usually backed by:

* PostgreSQL
* Oracle

### DevOps Tasks:

* Database Backup
* Database Monitoring
* Disaster Recovery

---
## 3. CDC Layer:

Reads database transaction logs.

Examples:

* Oracle Redo Logs
* MySQL Binlogs
* SQL Server Transaction Logs

### DevOps Tasks:

* Verify DB Connectivity
* Open Firewall Ports
* Check Network Latency
* Monitor CDC Lag

---

## 4. Processing Engine

Executes Continuous Queries (CQ).

### DevOps Focus:

* JVM Heap Size
* CPU Utilization
* Garbage Collection

Example JVM Settings:

```bash
-Xms8G
-Xmx8G
```

---
## 5. Target Systems:

Examples:

* Kafka
* Snowflake
* BigQuery
* S3

### DevOps Tasks:

* Credential Management
* Secret Rotation
* Network Connectivity
* SSL/TLS Configuration

---
# High Availability (HA):
Production environments usually use multiple Striim nodes.

```text
            Load Balancer
                   |
        -----------------------
        |                     |
   Striim Node-1        Striim Node-2
        |                     |
        -----------------------
                   |
          Metadata DB
```

### DevOps Tasks:

* Configure HA
* Health Checks
* Failover Testing
* Load Balancer Configuration

---
# Monitoring:

### What to Monitor?

| Component    | Metrics         |
| ------------ | --------------- |
| Server       | CPU, Memory     |
| JVM          | Heap Usage, GC  |
| CDC          | Replication Lag |
| Network      | Latency         |
| Disk         | Storage Usage   |
| Applications | Running/Failed  |

### Tools:
* Prometheus
* Grafana
* Splunk
* ELK Stack
* Datadog

---
# Security:

### DevOps Responsibilities

* SSL Certificates
* User Access Management
* LDAP Integration
* Secret Management
* Firewall Rules

Example:

```text
Source DB --> Striim --> Kafka
     SSL        SSL       SSL
```

---
# Backup & Recovery:

### Backup Items

1. Metadata Database
2. Striim Configuration
3. Application TQL Scripts
4. SSL Certificates

### Recovery Flow

```text
Backup
   |
Restore Metadata DB
   |
Install Striim
   |
Import Applications
   |
Start Pipelines
```

---
# Striim on Kubernetes:

Many companies run Striim on Kubernetes.

```text
+------------------+
| Kubernetes       |
| Cluster          |
+--------+---------+
         |
   +-----+-----+
   | Striim Pod|
   +-----------+
```

### DevOps Tasks:
* Create Deployments
* Configure Services
* Configure Ingress
* Set Resource Limits
* Autoscaling

Example:

```yaml
resources:
  requests:
    memory: "4Gi"
    cpu: "2"
  limits:
    memory: "8Gi"
    cpu: "4"
```

---
**Q: What is your role in Striim as a DevOps Engineer?**

**Answer:**
> As a DevOps Engineer, I manage Striim infrastructure, deployment, monitoring, security, and high availability. My responsibilities include installing and upgrading Striim, configuring CDC connectivity, monitoring JVM and system resources, managing backups and disaster recovery, securing integrations with databases and target systems, and ensuring reliable real-time data pipelines in production environments.

### Easy Comparison
| DevOps Tool        | Similar Role in Striim  |
| ------------------ | ----------------------- |
| Jenkins            | Striim Server           |
| Jenkins Pipeline   | Striim Application      |
| Git Commit Trigger | CDC Event               |
| Build Artifact     | Stream Data             |
| Nexus              | Target Storage          |
| Prometheus         | Striim Monitoring       |
| Kubernetes         | Striim Hosting Platform |

For your DevOps interviews, focus on:
**Installation → HA → Monitoring → Security → Backup → Kubernetes Deployment → Troubleshooting**, rather than deep TQL development.

---
# Understanding Striim Nodes, Cluster Setup, Agents, and Core Components

If you're coming from a DevOps background (Kubernetes, Jenkins, GCP), think of Striim as a distributed platform that runs applications across multiple servers (nodes) for scalability and high availability.

## 1. Striim Node:
A **Node** is a single Striim server instance.

```text
+------------------+
| Striim Node 1    |
|------------------|
| Source Adapter   |
| CQ Engine        |
| Target Adapter   |
+------------------+
```

A node can:
* Run Striim applications
* Process streams
* Execute Continuous Queries (CQ)
* Read from sources
* Write to targets

### Example:
```text
Node-1
 ├── Oracle Reader
 ├── Stream Processor
 └── Snowflake Writer
```

---
## 2. Striim Cluster:
A **Cluster** is a group of Striim nodes working together.

```text
          +-------------+
          | Cluster     |
          +-------------+
                 |
     -------------------------
     |           |           |
     v           v           v
  Node-1      Node-2      Node-3
```

### Benefits:
* High Availability (HA)
* Load Distribution
* Fault Tolerance
* Scalability

If one node fails, another node can continue processing.

---
## 3. Cluster Setup:
### Single Node Setup

```text
+----------------+
| Striim Server  |
+----------------+
```

Used for:

* Development
* Testing
* Small workloads

### Multi-Node Setup:
```text
         +-------------+
         | Cluster     |
         +-------------+
               |
      -------------------
      |        |        |
      v        v        v
   Node1    Node2    Node3
```

Used for:

* Production
* High Availability
* Large-scale CDC workloads

---
## 4. Agents in Striim:
An **Agent** is a lightweight service that runs close to the source system and helps collect or forward data.

```text
Oracle DB
    |
    v
Striim Agent
    |
    v
Striim Cluster
```

### Agent Responsibilities:
* Collect source data
* Read logs (CDC)
* Forward events
* Reduce load on central servers

### Example:
```text
Oracle Database
      |
      v
Forwarding Agent
      |
      v
Striim Cluster
```

Useful when:

* Databases are in private networks
* Security restrictions exist
* Source systems are geographically distributed

---
# Core Components:
## 1. Source Adapter

Reads data from source systems.

Examples:

* OracleReader
* MySQLReader
* SQLServerReader
* KafkaReader

```text
Oracle DB
    |
OracleReader
```

---
## 2. Stream:
Temporary in-memory event pipeline.

```text
OracleReader
      |
      v
 OrderStream
```

Carries events between components.

---
## 3. Continuous Query (CQ):
Processes streaming data.

Functions:

* Filter
* Join
* Aggregate
* Transform

```sql
CREATE CQ FilterData
INSERT INTO NewStream
SELECT *
FROM OrderStream
WHERE amount > 1000;
```

---
## 4. Target Adapter:
Writes data to target systems.

Examples:

* KafkaWriter
* SnowflakeWriter
* BigQueryWriter
* S3Writer

```text
ProcessedStream
       |
       v
SnowflakeWriter
```

---
## 5. Metadata Store:
Stores:

* Application definitions
* Cluster configuration
* Checkpoints
* User information

```text
+----------------+
| Metadata Store |
+----------------+
```

This allows Striim to recover after failures.

---
## 6. Checkpointing:
Tracks processing progress.

```text
Source --> Stream --> Checkpoint --> Target
```

Benefits:

* Recovery after restart
* Prevents data loss
* Supports exactly-once or near-exactly-once processing

---
## 7. Monitoring Dashboard:
Provides:

* Application status
* Throughput
* Latency
* Node health
* Error logs

```text
+---------------------+
| Striim Dashboard    |
+---------------------+
| App Status          |
| Node Health         |
| CDC Lag             |
| Errors              |
+---------------------+
```

---
# Production Architecture Example:
```text
                Oracle DB
                     |
                     |
             Forwarding Agent
                     |
                     v

      +--------------------------------+
      |        Striim Cluster          |
      |--------------------------------|
      | Node-1 (Source Processing)     |
      | Node-2 (CQ Processing)         |
      | Node-3 (Target Processing)     |
      +--------------------------------+
                     |
          ----------------------
          |                    |
          v                    v
       Kafka              Snowflake
```

---
# Kubernetes Analogy (Easy to Remember):
| Striim         | Kubernetes Equivalent   |
| -------------- | ----------------------- |
| Node           | Worker Node             |
| Cluster        | K8s Cluster             |
| Agent          | Daemon/Collector        |
| Stream         | Message Queue/Data Flow |
| CQ             | Processing Service      |
| Target         | External Service        |
| Metadata Store | etcd                    |
| Dashboard      | Kubernetes Dashboard    |

---
**What are Nodes, Cluster, Agents, and Core Components in Striim?**

A Striim node is an individual server running Striim services. Multiple nodes form a cluster for high availability and scalability. Agents can be deployed near source systems to capture and forward CDC data to the cluster. The core components include Source Adapters, Streams, Continuous Queries (CQ), Target Adapters, Metadata Store, Checkpointing, and Monitoring services, which together enable real-time data integration and streaming.
---
