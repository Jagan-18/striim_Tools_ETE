# High Availability (HA) & Failover Setup in Striim:
## What is High Availability (HA)?

**High Availability (HA)** ensures that Striim applications continue running even if a node, server, or process fails.

Goal:

```text id="qj0j6z"
Minimize Downtime
Avoid Data Loss
Automatic Recovery
```

---
# Why HA is Important?

Without HA:

```text id="p9d9k7"
Node Failure
     |
Pipeline Stops
     |
Data Loss Risk
```

With HA:

```text id="1s4xgg"
Node Failure
     |
Automatic Failover
     |
Pipeline Continues
```

---
# HA Architecture

```text id="m6d5hj"
            Striim Cluster

      +------------------------+
      |                        |
      v                        v

   Node-1                 Node-2
 (Active)               (Standby)

      |
      |
 Application Running
```

If Node-1 fails:

```text id="3igwpi"
Node-1 Down
      |
Failover Triggered
      |
Node-2 Becomes Active
```

---
# Single Node vs HA Cluster:
## Single Node

```text id="2z26vi"
Node-1
  |
Application
```

Risk:

```text id="9e4h3m"
Node Failure = Downtime
```

---
## Multi-Node Cluster:
```text id="avr41s"
Node-1
Node-2
Node-3
```

**Benefits:**
✅ Failover

✅ Load Distribution

✅ High Availability

✅ Better Throughput

---
# HA Components:
## 1. Cluster Nodes

Production deployment:

```text id="68wdzj"
Node-1
Node-2
Node-3
```

Each node participates in:

* Application execution
* Metadata synchronization
* Recovery

---
## 2. Metadata Store:
Stores:

```text id="k8pq9k"
Application Definitions
Checkpoints
Cluster State
```

Important for failover recovery.

---
## 3. Checkpoints:
Striim continuously records processing positions.

Example:

```text id="x3n4ii"
Oracle SCN
123456789
```

Stored checkpoint:

```text id="bpn74i"
Last Processed Position
```

---
# Failover Process:
## Step 1

Application running on:

```text id="09uy6r"
Node-1
```

---
## Step 2

Node crash occurs.

```text id="cw2vyd"
Hardware Failure
OS Failure
Network Failure
```

---
## Step 3

Cluster detects failure.

```text id="r0w9b4"
Heartbeat Missing
```

---
## Step 4

Application moved to:

```text id="fykklm"
Node-2
```

---
## Step 5

Processing resumes.

```text id="k87jmx"
Checkpoint Recovery
```

---
# Failover Example:

```text id="zqwj2m"
Oracle
   |
OracleReader
   |
OrderStream
   |
BigQuery
```

Running on:

```text id="cjlwm0"
Node-1
```

Failure:

```text id="nnx0k6"
Node-1 Down
```

Recovery:

```text id="mfmbh5"
Node-2 Starts Application
```

Resume from:

```text id="d9es9v"
Last Checkpoint
```

---
# Active-Standby Model:

```text id="m3n4wu"
Node-1 (Active)

Node-2 (Standby)
```

Normal:

```text id="qf1n86"
Node-1 Processing
```

Failure:

```text id="9g3ixc"
Node-2 Takes Over
```

---
# Active-Active Model:
```text id="icrn0m"
Node-1
Node-2
Node-3
```

All nodes process workloads.

Benefits:

* Better utilization
* Load balancing
* Higher throughput

---
# Checkpoint Recovery:
Suppose:

```text id="zjmt9z"
Oracle SCN
100000
```

Striim processed:

```text id="mjlwmj"
SCN 95000
```

Checkpoint stored:

```text id="jlr16s"
95000
```

Node fails.

Recovery starts from:

```text id="c5vbly"
95000
```

Not from:

```text id="sp84ff"
0
```

This prevents data loss.

---
# Source HA Considerations:
## Oracle CDC

Ensure:

```text id="0ql85v"
ARCHIVELOG Enabled
Redo Logs Retained
```

Failover node must access:

```text id="a1izwr"
Redo Logs
Archive Logs
```

---
## Kafka:
Kafka itself should be highly available.

```text id="8d70m8"
Broker-1
Broker-2
Broker-3
```

Monitor:

```text id="h4cq9f"
Consumer Lag
Broker Health
```

---
# Target HA Considerations:
### BigQuery

Managed by GCP.

Built-in HA.

---
### Snowflake:
Built-in HA.

---
### Kafka:
Use replicated topics.

```text id="0bjlwm"
Replication Factor = 3
```

---
# Monitoring HA:

Monitor:

```text id="1r0w8z"
Node Health
Application Health
Checkpoint Status
Cluster Status
```

Dashboard:

```text id="pb66l5"
Node-1 : Healthy
Node-2 : Healthy
Node-3 : Healthy
```

---
# HA Best Practices:
## Minimum 3 Nodes

Recommended:

```text id="aov4np"
Node-1
Node-2
Node-3
```

Avoid:

```text id="wdf3du"
Single Node Production
```

---
## Monitor Heartbeats

Check:

```text id="vjlwmf"
Node Connectivity
Cluster Membership
```

---
## Test Failover

Regularly simulate:

```text id="zgjlwm"
Node Shutdown
Process Kill
Network Failure
```

Verify recovery.

---
## Maintain Checkpoints

Ensure:

```text id="gcj79m"
Checkpoint Persistence
```

is enabled.

---
## Backup Metadata

Backup:

```text id="ihjnm4"
Metadata Store
Configuration Files
TQL Scripts
```

---
# Failover Testing Scenario

### Before Failure

```text id="vjlwmu"
Node-1 Running

Lag = 0

Status = Healthy
```

### Simulate Failure
```bash id="6jz9yx"
kill -9 <process>
```

### Expected
```text id="jjlwmm"
Node-2 Takes Over

Application Running

Checkpoint Restored
```

---
### What is High Availability in Striim?
High Availability is a cluster-based deployment model that allows Striim applications to continue processing data even if a node fails, minimizing downtime and preventing data loss.

---
### How does failover work in Striim?
When a node fails, another cluster node automatically takes over application processing and resumes from the latest checkpoint.

---
### What is the role of checkpoints?
Checkpoints store the last processed position so applications can recover after failures without reprocessing all data.

---
### Why are multiple nodes required?
Multiple nodes provide:

* Redundancy
* Failover
* Load balancing
* Higher throughput

---
> Striim High Availability is achieved by deploying applications across a multi-node cluster. If a node fails, another node automatically takes over processing using checkpoint information stored in the metadata store. Failover minimizes downtime and prevents data loss by resuming CDC processing from the last committed position. Production environments typically use three or more nodes, checkpoint persistence, metadata backups, and regular failover testing to ensure reliability.

---
# Configuring Striim for High Availability (HA)
## What is HA in Striim?

High Availability (HA) ensures Striim continues processing data even when a node fails.

Main objectives:

* No data loss
* Automatic failover
* Fast recovery
* Continuous CDC processing

---
# HA Cluster Architecture

A typical production setup uses **3 or more nodes**.

```text
                Striim Cluster

        +-------------------------+
        |      Quorum Service     |
        +-------------------------+

           |         |         |
           v         v         v

        Node-1    Node-2    Node-3
       (Active)  (Active)  (Standby)

           \        |        /
            \       |       /
             \      |      /
               Metadata Store
```

---
# What are Quorum Nodes?

A **quorum** is the minimum number of healthy nodes required for the cluster to operate safely.

Example:

### 3 Node Cluster

```text
Node-1
Node-2
Node-3
```

Quorum:

```text
2 Nodes Required
```

If:

```text
Node-1 = Up
Node-2 = Up
Node-3 = Down
```

Cluster continues running.

---
### Split Brain Prevention

Without quorum:

```text
Node-1 thinks it's Active

Node-2 thinks it's Active
```

This creates:

```text
Duplicate Processing
Data Corruption
```

Quorum prevents this situation.

---
# Recommended Production Setup:
## Small Production

```text
Node-1
Node-2
Node-3
```

---
## Enterprise Production

```text
Node-1
Node-2
Node-3
Node-4
Node-5
```

Benefits:

* Better HA
* More throughput
* Better workload distribution

---
# HA Deployment Strategy:
### Node Roles

```text
Node-1 → Source Processing

Node-2 → CQ Processing

Node-3 → Target Processing
```

Or:

```text
All Nodes Share Workload
```

depending on cluster configuration.

---
# Checkpointing Strategy

Checkpointing is the foundation of recovery.

Example:

```text
Oracle SCN
100000
```

Processed:

```text
SCN 98000
```

Checkpoint stored:

```text
98000
```

If failure occurs:

```text
Restart from SCN 98000
```

instead of:

```text
Restart from beginning
```

---
# Failover Workflow

## Normal Operation

```text
Oracle
   |
Node-1
   |
BigQuery
```

---
## Failure

```text
Node-1 Crash
```

---
## Cluster Detection

Heartbeat lost.

```text
Heartbeat Timeout
```

---
## Automatic Failover

```text
Application Moved

Node-1
   ↓

Node-2
```

---
## Recovery

```text
Checkpoint Restored
```

Application resumes automatically.

---
# Heartbeat Monitoring

Nodes continuously exchange heartbeats.

```text
Node-1 <--> Node-2

Node-2 <--> Node-3

Node-3 <--> Node-1
```

If heartbeat stops:

```text
Node Failure Suspected
```

Failover begins.

---
# Recovery Strategies:
## 1. Application Recovery

Application restarts automatically.

```text
Application Crash
      |
Restart
      |
Checkpoint Recovery
```

---
## 2. Node Recovery

Failed node rejoins cluster.

```text
Node Repaired
      |
Join Cluster
      |
Synchronization
```

---
## 3. Source Recovery

Oracle Example:

```text
Oracle
   |
Redo Logs
   |
Archive Logs
```

Striim reads from:

```text
Last Checkpoint SCN
```

after failover.

---
## 4. Target Recovery

BigQuery Example:

```text
Write Failure
     |
Retry
     |
Success
```

Many targets support automatic retry mechanisms.

---
# Oracle CDC HA Considerations

Ensure:

### ARCHIVELOG Enabled

```sql
ARCHIVE LOG LIST;
```

Expected:

```text
Database log mode
Archive Mode
```

---
### Supplemental Logging Enabled

```sql
SELECT SUPPLEMENTAL_LOG_DATA_MIN
FROM V$DATABASE;
```

Expected:

```text
YES
```

---
### Archive Log Retention

Keep logs long enough for recovery.

```text
24 Hours
48 Hours
72 Hours
```

depending on SLA.

---
# Kafka HA Considerations

Kafka should also be highly available.

### Multiple Brokers

```text
Broker-1
Broker-2
Broker-3
```

---
### Replication Factor

```text
Replication Factor = 3
```

Example:

```text
Topic: customer-topic

Partition-1
   |
Replica-1
Replica-2
Replica-3
```

---
# Monitoring HA

Monitor:

## Cluster Health:
```text
Node Status
```

Example:

```text
Node-1 Healthy
Node-2 Healthy
Node-3 Healthy
```

---
## Application Health

```text
Running
Stopped
Failed
```

---
## Checkpoint Health

```text
Checkpoint Updated
```

---

## CDC Lag

```text
Lag < 100 Events
```

preferred.

---
# HA Best Practices

## Use Odd Number of Nodes

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

because quorum becomes harder to manage.

---
## Store Metadata Separately

Protect:

```text
Metadata Store
Checkpoint Data
```

with backups.

---
## Enable Persistent Checkpoints

Required for:

```text
Failover
Recovery
```

---
## Test Failover Regularly

Simulate:

```bash
kill -9 process
```

or

```text
Node Shutdown
```

Verify automatic recovery.

---
## Backup Configuration

Backup:

```text
TQL Scripts
Applications
UDF JARs
Metadata
```

Store in Git/Nexus/Backup Storage.

---
# Example Production HA Setup

```text
                 Oracle CDC
                       |
                Load Balancer
                       |
       --------------------------------
       |              |              |
       v              v              v

    Node-1         Node-2         Node-3
   (Active)       (Active)       (Standby)

       \             |             /
        \            |            /
         \           |           /

          Shared Metadata Store

                     |
                     v

                 BigQuery
```

---
### What is quorum in Striim?
A quorum is the minimum number of active cluster nodes required to maintain cluster availability and prevent split-brain scenarios.

---
### Why use 3 nodes instead of 2?
With 3 nodes, quorum is 2. The cluster can tolerate one node failure while maintaining majority consensus.

---
### How does failover work?
When a node becomes unavailable, the cluster detects the failure through heartbeat monitoring. Another node takes ownership of the application and resumes processing from the latest checkpoint.

---
### What is the role of checkpoints?
Checkpoints store the last processed offset/position (such as Oracle SCN) and allow recovery without data loss after a failure.

---

> Striim HA is configured using a multi-node cluster, typically with 3 or more nodes. Quorum ensures cluster consistency and prevents split-brain conditions. Nodes exchange heartbeats to detect failures. When a node fails, applications automatically fail over to another node and resume processing from the latest checkpoint. Recovery strategies include checkpoint-based restart, metadata synchronization, archive log retention for CDC sources, and regular failover testing to ensure business continuity.
