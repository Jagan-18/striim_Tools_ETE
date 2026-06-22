# Multi-Node Cluster Management in Striim:

## What is a Multi-Node Cluster?

A **Multi-Node Cluster** is a group of Striim servers (nodes) working together as a single Striim platform.

Instead of running everything on one server:

```text
Node-1
```

Production environments use:

```text
Node-1
Node-2
Node-3
```

**Benefits:**
✅ High Availability (HA)

✅ Failover Support

✅ Load Balancing

✅ Better Performance

✅ Scalability

---
# Cluster Architecture

```text
                Striim Cluster

      +--------------------------------+
      |         Metadata Store         |
      +--------------------------------+

          |          |          |
          v          v          v

       Node-1     Node-2     Node-3
```

Each node can run:

* Sources
* Streams
* Continuous Queries (CQ)
* Targets

---
# Why Multi-Node Clusters?

## Single Node

```text
Oracle
   |
Node-1
   |
BigQuery
```

Problem:

```text
Node-1 Failure
      |
Pipeline Stops
```

---
## Multi-Node

```text
Oracle
   |
Cluster
   |
BigQuery
```

If one node fails:

```text
Node-1 Down
     |
Node-2 Takes Over
```

Pipeline continues.

---
# Typical Production Setup:
### Small Environment

```text
Node-1
Node-2
Node-3
```

---
### Enterprise Environment

```text
Node-1
Node-2
Node-3
Node-4
Node-5
```

---
# Node Roles:
## Source Processing Node

Handles:

```text
OracleReader
MySQLReader
KafkaReader
```

Example:

```text
Node-1

OracleReader
```

---
## Processing Node

Handles:

```text
CQ
Aggregation
Join
Filtering
```

Example:

```text
Node-2

OrderCQ
AnalyticsCQ
```

---
## Target Node

Handles:

```text
BigQueryWriter
KafkaWriter
SnowflakeWriter
```

Example:

```text
Node-3

BigQueryWriter
```

---
# Distributed Processing

Example:

```text
OracleReader
      |
      v

   Node-1

      |
      v

AnalyticsCQ

   Node-2

      |
      v

BigQueryWriter

   Node-3
```

Workload is distributed across nodes.

---
# Cluster Communication

Nodes communicate using:

```text
Heartbeats
Metadata Synchronization
Checkpoint Sharing
```

Example:

```text
Node-1 <--> Node-2

Node-2 <--> Node-3

Node-3 <--> Node-1
```

---
# Cluster Health Monitoring

Monitor:

### Node Status

```text
Healthy
Down
Joining
Recovering
```

Example:

```text
Node-1 Healthy

Node-2 Healthy

Node-3 Healthy
```

---
### CPU Usage

```text
Node-1 40%

Node-2 50%

Node-3 45%
```

---
### Memory Usage

```text
Node-1 60%

Node-2 55%

Node-3 70%
```

---
# Adding a New Node

Current Cluster:

```text
Node-1
Node-2
Node-3
```

Add:

```text
Node-4
```

Result:

```text
Node-1
Node-2
Node-3
Node-4
```

Benefits:

* Increased capacity
* Better throughput
* Additional failover support

---
# Removing a Node

Before maintenance:

```text
Node-3
```

Steps:

1. Stop applications running on Node-3.
2. Move workload to other nodes.
3. Remove node from cluster.
4. Perform maintenance.
5. Rejoin cluster.

---
# Load Balancing

Example:

```text
Node-1 → 1000 events/sec

Node-2 → 950 events/sec

Node-3 → 1100 events/sec
```

Balanced workload improves performance.

---
# Failover in Multi-Node Clusters

### Before Failure

```text
Node-1 Running OracleReader
```

### Failure

```text
Node-1 Down
```

### Recovery

```text
Node-2 Starts OracleReader
```

Processing resumes using checkpoints.

---
# Checkpoint Sharing

Checkpoint Example:

```text
Oracle SCN
123456789
```

Stored in cluster metadata.

If a node fails:

```text
New Node
    |
Reads Checkpoint
    |
Resumes Processing
```

---
# Scaling Strategies:

## Vertical Scaling

Increase:

```text
CPU
Memory
Disk
```

Example:

```text
8 CPU → 16 CPU
```

---
## Horizontal Scaling

Add nodes:

```text
Node-1
Node-2
Node-3
Node-4
```

Preferred in large production environments.

---
# Monitoring Dashboard

Monitor:

```text
Cluster Nodes
Application Health
CPU
Memory
Latency
CDC Lag
```

Example:

```text
Cluster Status : Healthy

Nodes          : 3

Applications   : Running

Lag            : 20 Events
```

---
# Best Practices
## Use Odd Number of Nodes

Recommended:

```text
3 Nodes
5 Nodes
7 Nodes
```

Helps maintain quorum.

---
## Separate Workloads

Example:

```text
Node-1 → Sources

Node-2 → CQ Processing

Node-3 → Targets
```

---
## Monitor Resource Usage

Watch:

```text
CPU > 80%

Memory > 80%
```

Scale before bottlenecks occur.

---
## Test Failover

Regularly test:

```bash
kill -9 <process>
```

or

```text
Node Shutdown
```

Verify application recovery.

---
## Backup Metadata

Backup:

```text
Metadata Store
TQL Scripts
Applications
UDF JARs
```

---
### What is a Multi-Node Cluster?
A Multi-Node Cluster is a set of Striim servers working together to provide scalability, high availability, failover, and distributed processing.

---
### Why use multiple nodes?
* High Availability
* Failover
* Load Balancing
* Scalability
* Improved Performance

---
### How do applications recover after node failure?
Applications fail over to another cluster node and resume processing from the latest checkpoint stored in cluster metadata.

---
### What is the preferred production cluster size?
Typically:

```text
3 Nodes Minimum
```

for quorum and high availability.

---
> A Multi-Node Striim Cluster consists of multiple Striim servers working together to provide high availability, failover, and scalable stream processing. Workloads such as source ingestion, CQ processing, and target delivery can be distributed across nodes. Cluster health is maintained through heartbeat communication, checkpoint sharing, and metadata synchronization. Production environments typically use at least three nodes to ensure quorum, automatic failover, and uninterrupted CDC processing.

---
# Setting Up and Managing Striim in a Multi-Node Cluster Environment

## What is a Multi-Node Striim Cluster?
A multi-node cluster consists of multiple Striim servers working together as a single platform.

Example:

```text
Node-1 (Primary)
Node-2
Node-3
```

**Benefits:**
✅ High Availability (HA)

✅ Failover Support

✅ Load Balancing

✅ Scalability

✅ Better Performance

---
# Recommended Production Architecture:

```text
                    Striim Cluster

        +-----------------------------------+
        |         Cluster Metadata          |
        +-----------------------------------+

               |        |        |
               v        v        v

           Node-1   Node-2   Node-3
```

### Minimum Production Setup

```text
3 Nodes
```

Recommended because:

```text
Quorum = 2
```

Cluster can survive one node failure.

---
# Infrastructure Requirements:
Example:

| Component | Recommended         |
| --------- | ------------------- |
| CPU       | 8-16 vCPU           |
| Memory    | 16-32 GB RAM        |
| Disk      | SSD                 |
| OS        | Linux (RHEL/Ubuntu) |
| Network   | Low Latency         |

---
# Example GCP Deployment

Using GCP VMs:

Google Cloud Platform

```text
striim-node-1
10.10.1.10

striim-node-2
10.10.1.11

striim-node-3
10.10.1.12
```

Open required ports between nodes.

---
# Step 1: Install Striim on All Nodes

Install Striim on:

```text
Node-1
Node-2
Node-3
```

Example path:

```bash
/opt/striim
```

Verify installation:

```bash
ls /opt/striim
```

---
# Step 2: Configure Hostname Resolution

Each node must resolve all cluster members.

Example:

```bash
cat /etc/hosts
```

```text
10.10.1.10 striim-node-1
10.10.1.11 striim-node-2
10.10.1.12 striim-node-3
```

Test:

```bash
ping striim-node-2
```

---
# Step 3: Configure Cluster Settings

On Node-1:

```bash
cd /opt/striim/conf
```

Configure cluster properties.

Example:

```text
Cluster Name : prod-cluster

Node Name    : node1
```

Node-2:

```text
Node Name : node2
```

Node-3:

```text
Node Name : node3
```

All nodes must belong to the same cluster.

---
# Step 4: Start First Node

Start Node-1:

```bash
cd /opt/striim/bin

./server.sh start
```

Verify:

```bash
./server.sh status
```

Expected:

```text
Running
```

---
# Step 5: Join Additional Nodes

Start Node-2:

```bash
./server.sh start
```

Node-2 joins:

```text
prod-cluster
```

Start Node-3:

```bash
./server.sh start
```

---
# Step 6: Verify Cluster Membership

Login to Striim UI.

Navigate:

```text
Administration
      |
Cluster
```

Expected:

```text
Node-1 Healthy

Node-2 Healthy

Node-3 Healthy
```

---
# Cluster Topology

```text
OracleReader
      |
Node-1

CustomerCQ
      |
Node-2

BigQueryWriter
      |
Node-3
```

Processing can be distributed across nodes.

---
# Application Deployment in Cluster

Deploy application:

```text
Oracle_To_BigQuery
```

Cluster automatically decides where components run.

Example:

```text
OracleReader   → Node-1

CustomerCQ     → Node-2

BigQueryWriter → Node-3
```

---
# Monitoring Cluster Health

Monitor:

### Node Status

```text
Healthy
Down
Recovering
```

---
### CPU Usage

```text
Node-1 : 45%

Node-2 : 50%

Node-3 : 40%
```

---
### Memory Usage

```text
Node-1 : 60%

Node-2 : 55%

Node-3 : 58%
```

---
### CDC Lag

```text
Lag = 25 Events
```

Healthy cluster:

```text
Low Lag
```

---
# Failover Example:
### Before Failure

```text
OracleReader
     |
Node-1
```

### Failure

```text
Node-1 Down
```

### Automatic Failover

```text
OracleReader
     |
Node-2
```

Recovery starts from:

```text
Last Checkpoint
```

No full reload required.

---
# Checkpoint Management

Checkpoint Example:

```text
Oracle SCN
1250000
```

Stored in cluster metadata.

If failover occurs:

```text
Resume from SCN 1250000
```

instead of restarting from the beginning.

---
# Adding a New Node

Current:

```text
Node-1
Node-2
Node-3
```

Add:

```text
Node-4
```

Steps:

1. Install Striim.
2. Configure cluster settings.
3. Start service.
4. Verify node joins cluster.

Result:

```text
Node-1
Node-2
Node-3
Node-4
```

---
# Removing a Node

Before maintenance:

1. Stop applications on node.
2. Drain workload.
3. Remove node from cluster.
4. Perform maintenance.
5. Rejoin node later.

---
# Best Practices:
### Use Odd Number of Nodes

Good:

```text
3 Nodes
5 Nodes
7 Nodes
```

Avoid:

```text
2 Nodes
4 Nodes
```

---
### Separate Workloads

Example:

```text
Node-1 → CDC Readers

Node-2 → CQ Processing

Node-3 → Writers
```

---
### Monitor Resources

Alert when:

```text
CPU > 80%

Memory > 80%
```

---
### Backup Cluster Metadata

Backup:

```text
Metadata Store
Applications
TQL Scripts
UDF JARs
```

---
### Test Failover Regularly

Simulate:

```bash
systemctl stop striim
```

Verify:

```text
Applications Fail Over
```

---
# Common Commands:
### Start Striim

```bash
./server.sh start
```

### Stop Striim

```bash
./server.sh stop
```

### Restart Striim

```bash
./server.sh restart
```

### Status

```bash
./server.sh status
```

---
### Why use a multi-node cluster?

To provide:

* High Availability
* Failover
* Scalability
* Load Balancing

---
### What is the recommended minimum cluster size?

```text
3 Nodes
```

for quorum and failover support.

---
### How does failover work?
When a node fails, another node automatically takes ownership of the application and resumes processing using checkpoint information.

---
### How do you scale a Striim cluster?

By adding more nodes (horizontal scaling) and redistributing workloads across the cluster.

---
> To set up Striim in a multi-node environment, install Striim on all nodes, configure cluster membership, start the primary node, and join additional nodes to the cluster. Applications are distributed across nodes for scalability and high availability. Cluster management includes monitoring node health, CPU, memory, CDC lag, checkpoint status, and performing failover testing. Production environments typically use at least three nodes to support quorum-based failover and uninterrupted CDC processing.
