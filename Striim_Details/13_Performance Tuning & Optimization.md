# Performance Tuning & Optimization in Striim

Performance tuning in Striim focuses on improving:

* Throughput (events/sec)
* Latency
* CDC Lag
* Memory utilization
* CPU utilization
* Pipeline stability

---
# Performance Monitoring Metrics:
Monitor these metrics regularly:

| Metric            | Description                      |
| ----------------- | -------------------------------- |
| Throughput        | Events processed per second      |
| Latency           | Time taken from source to target |
| CDC Lag           | Delay between source and Striim  |
| CPU Usage         | Processing utilization           |
| Memory Usage      | JVM memory consumption           |
| Target Write Rate | Records written to target        |

Example:

```text
Application Status : Running
Throughput         : 5000 events/sec
Latency            : 120 ms
CDC Lag            : 50 events
CPU                : 45%
Memory             : 60%
```

---
# Common Performance Bottlenecks:
## 1. High CDC Lag

```text
Oracle
   |
OracleReader
   |
Lag Increasing
```

Causes:

* Heavy source transactions
* Slow CQ processing
* Slow target writes
* Insufficient resources

Solution:

* Increase CPU/RAM
* Scale cluster
* Optimize queries

---
## 2. Slow Continuous Queries (CQ):
Bad Example:

```sql
SELECT *
FROM OrderStream
```

Good Example:

```sql
SELECT
ORDER_ID,
CUSTOMER_ID,
AMOUNT
FROM OrderStream
```

Select only required columns.

---
# Optimize Filtering:
Filter as early as possible.

Bad:

```sql
JOIN LargeStream1
JOIN LargeStream2
WHERE AMOUNT > 10000
```

Good:

```sql
SELECT *
FROM OrderStream
WHERE AMOUNT > 10000
```

Then perform joins.

This reduces processing volume significantly.

---
# Join Optimization:
## Avoid Large Stream-to-Stream Joins

Bad:

```sql
CustomerStream
      JOIN
OrderStream
```

Both streams contain millions of records.

---
## Better Approach:
Use:

* Reference data
* Smaller datasets
* Proper join keys

Example:

```sql
ON CUSTOMER_ID
```

instead of joining on text fields.

---
# Window Optimization:
Bad:

```sql
GROUP BY TIME_WINDOW(HOUR,24)
```

Large memory usage.

Better:

```sql
GROUP BY TIME_WINDOW(MINUTE,5)
```

Smaller windows consume fewer resources.

---
# Source Optimization:
## Oracle CDC

Ensure:

```text
ARCHIVELOG Enabled
Supplemental Logging Enabled
```

Monitor:

```text
Redo Log Generation
Archive Log Growth
```

Too many logs may increase CDC processing time.

---
## Table Selection:
Avoid capturing unnecessary tables.

Good:

```text
CUSTOMER
ORDER
PAYMENT
```

Avoid:

```text
Entire Database
```

if not required.

---
# Target Optimization:
## BigQuery

Use batch writes when possible.

Benefits:

```text
Lower API Calls
Better Throughput
```

---

## Kafka
Monitor:

```text
Consumer Lag
Topic Partitions
Broker Health
```

Increase partitions if throughput is limited.

---
## Snowflake:
Optimize:

```text
Warehouse Size
Batch Commit Size
```

---
# Memory Tuning:
Striim runs on JVM.

Monitor:

```text
Heap Usage
Garbage Collection
```

Example:

```text
Heap Size = 8 GB
Used      = 5 GB
```

High GC activity can impact performance.

---
# CPU Tuning:
Monitor:

```text
CPU Usage
```

Healthy:

```text
40% - 70%
```

Warning:

```text
Above 85%
```

Solution:

* Increase VM size
* Add cluster nodes
* Optimize CQs

---
# Multi-Node Scaling:
Single Node:

```text
Node-1
```

Production:

```text
Node-1
Node-2
Node-3
```

Benefits:

✅ High Availability

✅ Better throughput

✅ Load distribution

---
# Parallel Processing:
Example:

```text
OracleReader
      |
CustomerStream
      |
   +------+------+
   |             |
BigQuery     Kafka
```

One source feeding multiple targets.

---
# Checkpoint Optimization:
Checkpoints help recovery.

Too frequent:

```text
Higher overhead
```

Too infrequent:

```text
Long recovery time
```

Balance checkpoint frequency according to workload.

---
# TQL Optimization Best Practices:
## Avoid SELECT *

Bad:

```sql
SELECT *
FROM CustomerStream
```

Good:

```sql
SELECT
CUSTOMER_ID,
CUSTOMER_NAME
FROM CustomerStream
```

---
## Apply Filters Early:
```sql
WHERE AMOUNT > 10000
```

before joins and aggregations.

---
## Use Aggregations Carefully

Bad:

```sql
GROUP BY CUSTOMER_NAME
```

Large cardinality.

Better:

```sql
GROUP BY CUSTOMER_ID
```

---
# Monitoring Dashboard Checks:
Daily checks:

```text
Application Status
Throughput
Latency
CDC Lag
CPU
Memory
Errors
```

---
# Troubleshooting Examples:
## Scenario 1: High CDC Lag

Symptoms:

```text
Lag = 100000 events
```

Actions:

* Check source activity
* Check target write speed
* Review CQ complexity
* Scale cluster

---
## Scenario 2: High CPU:
Symptoms:

```text
CPU = 95%
```

Actions:

* Optimize joins
* Reduce window size
* Increase VM resources

---
## Scenario 3: BigQuery Write Delay:
Symptoms:

```text
Pipeline Running
Target Slow
```

Actions:

* Increase batch size
* Verify BigQuery quotas
* Check network latency

---
# Production Best Practices:
✅ Use dedicated Striim nodes

✅ Monitor lag continuously

✅ Filter early

✅ Avoid unnecessary joins

✅ Scale horizontally

✅ Use explicit column selection

✅ Monitor JVM heap

✅ Tune Kafka partitions

✅ Test performance in lower environments

---
### What are the key performance metrics in Striim?
* Throughput
* Latency
* CDC Lag
* CPU Usage
* Memory Usage
* Target Write Rate

---
### How do you reduce CDC lag?
1. Optimize TQL queries.
2. Apply filters early.
3. Scale Striim nodes.
4. Increase system resources.
5. Improve target write performance.

---
### How do you optimize Continuous Queries?
* Avoid `SELECT *`
* Use efficient joins
* Filter early
* Limit aggregation windows
* Select only required columns

---
> Performance tuning in Striim involves monitoring throughput, latency, CDC lag, CPU, and memory utilization. Optimization techniques include filtering data early, selecting only required columns, optimizing joins and window functions, tuning source and target configurations, scaling Striim clusters, and monitoring JVM performance. The goal is to achieve low latency, minimal CDC lag, and high throughput while maintaining pipeline stability.