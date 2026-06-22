# Striim Troubleshooting Guide (Interview + Production Support)

As a DevOps/Support Engineer, most Striim issues fall into these categories:

1. Pipeline Failure
2. CDC Lag Spikes
3. CDC Breaks / Data Not Flowing
4. Oracle CDC Issues
5. Kafka Issues
6. BigQuery/Snowflake Write Failures
7. Connectivity Issues
8. High CPU / Memory
9. Node Failure
10. Schema Change Issues
11. Checkpoint Issues

---
# 1. Pipeline Failure

## Symptoms

```text
Application Status = Failed
```

or

```text
Application Status = Stopped
```

---

## Root Causes

* Source unavailable
* Target unavailable
* Invalid TQL
* Authentication failure
* Network issue

---

## Example

```text
OracleReader
      |
Connection Refused
```

Error:

```text
ORA-12541: TNS No Listener
```

---

## Troubleshooting

### Check Application Logs

```text
Monitoring
    |
Application Logs
```

---

### Verify Source Connectivity

```bash
tnsping ORCL
```

or

```bash
telnet oracle-server 1521
```

---

### Verify Credentials

```text
Username
Password
```

---

## Solution

```text
Start Oracle Listener

Correct Credentials

Restart Pipeline
```

---
# 2. CDC Lag Spike

## Symptoms

```text
Lag = 100000 Events
```

Expected:

```text
Lag < 100
```

---

## Root Causes

### Heavy Source Transactions

Example:

```text
10 Million Updates
```

---

### Slow Target

Example:

```text
BigQuery Slow
```

---

### CPU Bottleneck

```text
CPU = 95%
```

---

## Troubleshooting

Check:

```text
Throughput

CPU

Memory

Target Health
```

---

## Solution

### Scale Cluster

```text
3 Nodes -> 5 Nodes
```

---

### Optimize CQ

Bad:

```sql
SELECT *
FROM OrderStream
```

Good:

```sql
SELECT
ORDER_ID,
AMOUNT
FROM OrderStream
```

---

### Apply Filters Early

```sql
WHERE AMOUNT > 10000
```

---
# 3. CDC Break (No Data Flow)

## Symptoms

```text
Pipeline Running

No Records Processing
```

---

## Example

Oracle CDC:

```text
Redo Logs Not Available
```

---

## Root Causes

* Archive logs deleted
* Supplemental logging disabled
* CDC user permissions removed

---

## Troubleshooting

### Check Oracle Logs

```sql
SELECT LOG_MODE
FROM V$DATABASE;
```

Expected:

```text
ARCHIVELOG
```

---

### Check Supplemental Logging

```sql
SELECT SUPPLEMENTAL_LOG_DATA_MIN
FROM V$DATABASE;
```

Expected:

```text
YES
```

---

## Solution

```sql
ALTER DATABASE
ADD SUPPLEMENTAL LOG DATA;
```

Enable archive mode if disabled.

---
# 4. Oracle CDC Failure

## Symptoms

```text
OracleReader Failed
```

---

## Common Errors

### ORA-01017

```text
Invalid Username/Password
```

Solution:

```text
Verify Credentials
```

---

### ORA-12514

```text
Listener Doesn't Know Service
```

Solution:

```text
Verify Service Name
```

---

### ORA-12541

```text
No Listener
```

Solution:

```text
Start Listener
```

---
# 5. Kafka Issues

## Symptoms

```text
KafkaReader Not Receiving Data
```

---

## Root Causes

### Topic Missing

```text
customer-topic
```

does not exist.

---

### Broker Down

```text
Broker-1 Offline
```

---

## Troubleshooting

List topics:

```bash
kafka-topics.sh --list
```

---

Check consumer lag:

```bash
kafka-consumer-groups.sh
```

---

## Solution

```text
Create Topic

Restart Broker

Increase Partitions
```

---
# 6. BigQuery Write Failures

## Symptoms

```text
BigQueryWriter Errors
```

---

## Common Errors

### Permission Denied

```text
403 Forbidden
```

Solution:

```text
Verify Service Account Roles
```

---

### Dataset Missing

```text
Dataset Not Found
```

Solution:

```text
Create Dataset
```

---

### Quota Exceeded

```text
API Limit Reached
```

Solution:

```text
Increase Batch Size
```

---
# 7. Connectivity Issues

## Symptoms

```text
Source Disconnected

Target Disconnected
```

---

## Troubleshooting

### Ping Server

```bash
ping hostname
```

---

### Port Check

Oracle:

```bash
telnet host 1521
```

Kafka:

```bash
telnet host 9092
```

---

### Firewall Check

Verify:

```text
Ingress Rules

Egress Rules
```

---

## Solution

```text
Open Ports

Update Firewall

Fix DNS
```

---
# 8. High CPU Usage

## Symptoms

```text
CPU > 90%
```

---

## Root Causes

### Large Joins

```sql
CustomerStream
JOIN
OrderStream
```

---

### Huge Aggregations

```sql
GROUP BY CUSTOMER_NAME
```

---

## Solution

### Optimize Query

```sql
GROUP BY CUSTOMER_ID
```

---

### Increase Resources

```text
8 CPU -> 16 CPU
```

---
# 9. High Memory Usage

## Symptoms

```text
Memory > 85%
```

---

## Causes

* Large windows
* Large joins
* Huge event volume

---

## Example

Bad:

```sql
GROUP BY TIME_WINDOW(HOUR,24)
```

Good:

```sql
GROUP BY TIME_WINDOW(MINUTE,5)
```

---

## Solution

```text
Increase JVM Heap

Reduce Window Size
```

---
# 10. Node Failure

## Symptoms

```text
Node-1 Down
```

---

## Recovery

Cluster detects:

```text
Heartbeat Lost
```

Application moves to:

```text
Node-2
```

---

## Verify

```text
Application Running

Checkpoint Restored
```

---
# 11. Schema Change Failure

## Symptoms

```text
Column Not Found
```

---

## Example

Source:

```text
EMAIL Added
```

Target:

```text
EMAIL Missing
```

---
## Solution

### Update Target Schema

```text
Add Column
```

---

### Update CQ

```sql
SELECT
EMP_ID,
EMP_NAME,
EMAIL
```

---
# 12. Checkpoint Issues

## Symptoms

After restart:

```text
Data Reprocessed
```

or

```text
Data Missing
```

---
## Root Causes

* Corrupt checkpoint
* Metadata issue
* Storage issue

---
## Solution

```text
Verify Checkpoint Store

Restore Backup

Rebuild Checkpoint
```

---
# Production Troubleshooting Flow

```text
Issue Reported
      |
      v

Check Application Status
      |
      v

Check Logs
      |
      v

Source Healthy?
      |
      +--> No --> Fix Source

      |
      v

Target Healthy?
      |
      +--> No --> Fix Target

      |
      v

Check CPU/Memory
      |
      v

Check CDC Lag
      |
      v

Verify Recovery
```

---
### Question:

**Pipeline is running but no data is reaching BigQuery. What will you do?**

### Answer:

1. Verify application status.
2. Check Striim logs for errors.
3. Verify Oracle CDC is active.
4. Check ARCHIVELOG and Supplemental Logging.
5. Verify BigQueryWriter connectivity.
6. Validate service account permissions.
7. Check CDC lag and throughput metrics.
8. Verify checkpoint status.
9. Restart application if required.
10. Monitor data flow after recovery.

---
> When troubleshooting Striim issues, I first check application status, logs, source and target connectivity, CDC lag, throughput, CPU, memory, and checkpoint health. For Oracle CDC issues, I verify ARCHIVELOG mode, supplemental logging, and redo log availability. For Kafka, I check broker health, topics, and consumer lag. For BigQuery or Snowflake, I verify permissions, quotas, and target availability. My approach is to identify whether the issue is at the source, Striim processing layer, infrastructure, or target, then apply corrective actions while ensuring no data loss through checkpoint-based recovery.
