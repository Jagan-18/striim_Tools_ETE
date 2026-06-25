# Topic 16: Cloud Spanner Performance Tuning (Complete End-to-End Professional Guide)

# Chapter 1: What is Performance Tuning?

## Definition

Performance Tuning is the process of improving the speed, efficiency, scalability, and reliability of a database or application.

Goal:

* Faster response time
* Lower latency
* Better throughput
* Lower resource usage
* Better user experience

---

# Chapter 2: Why is Performance Tuning Needed?

Imagine an e-commerce website.

Normal day:

```text
100 Users
```

Festival sale:

```text
100,000 Users
```

Without tuning:

* Slow website
* Timeouts
* Failed payments
* Customer complaints

With proper tuning:

* Stable performance
* Fast queries
* Better scalability

---

# Chapter 3: Performance Architecture

```text
Application

↓

Cloud Spanner

↓

Storage

↓

Network

↓

Monitoring

↓

Datadog

↓

DevOps Team
```

Performance depends on every layer.

---

# Chapter 4: What Affects Performance?

Cloud Spanner performance depends on many factors.

```text
Performance

│

├── Primary Key Design

├── Schema Design

├── Indexes

├── Queries

├── Transactions

├── CPU

├── Nodes

├── Storage

├── Network

├── Application

└── Traffic
```

One slow component affects the entire system.

---

# Chapter 5: Performance Metrics

Monitor these regularly.

## CPU Utilization

Example:

```text
CPU

20%

↓

45%

↓

70%

↓

95%
```

High CPU may indicate:

* Heavy traffic
* Expensive queries
* Too few nodes

---

## Read Latency

Time required to read data.

Healthy:

```text
10 ms
```

Poor:

```text
500 ms
```

---

## Write Latency

Time required to write data.

High write latency may result from:

* Contention
* Large transactions
* Heavy write load

---

## Commit Latency

Measures transaction completion time.

Higher commit latency impacts application response times.

---

## Storage Growth

Monitor:

```text
100 GB

↓

250 GB

↓

500 GB
```

Unexpected growth may require investigation.

---

# Chapter 6: CPU Optimization

Example:

```text
CPU = 95%
```

Do **not** immediately increase nodes.

First investigate.

Possible causes:

* Slow queries
* Missing indexes
* High traffic
* Poor schema
* Large transactions

Only scale after understanding the root cause.

---

# Chapter 7: Query Optimization

One of the biggest performance factors.

Bad query:

```sql
SELECT *
FROM Employee;
```

If the table contains 100 million rows, this reads far more data than necessary.

Better:

```sql
SELECT Name, Department
FROM Employee
WHERE EmployeeID = 101;
```

Benefits:

* Reads less data
* Faster execution
* Lower CPU

---

## Use Indexes

Without index:

```sql
SELECT *
FROM Employee
WHERE Email='abc@gmail.com';
```

Table scan.

With index:

```text
Email Index

↓

Employee Found
```

Much faster.

---

# Chapter 8: Schema Optimization

Good schema:

* Correct data types
* Good primary keys
* Appropriate indexes
* Proper relationships

Poor schema causes:

* Slow queries
* Large scans
* Hotspots

---

# Chapter 9: Primary Key Optimization

One of the biggest Cloud Spanner topics.

Poor primary key:

```text
1

2

3

4

5
```

Sequential writes may create hotspots under heavy write workloads.

Better designs distribute writes more evenly when appropriate for the workload.

---

# Chapter 10: Index Optimization

Don't create indexes on every column.

Good:

```text
Index

Email

Phone
```

if those columns are frequently searched.

Bad:

Indexing every column.

That increases write overhead.

---

# Chapter 11: Transaction Optimization

Bad transaction:

```text
Update

10,000 Rows
```

Long-running transactions:

* Increase contention
* Increase latency
* Increase aborts

Better:

Smaller transactions whenever possible.

---

# Chapter 12: Scaling

Cloud Spanner allows scaling compute capacity.

```text
1 Node

↓

2 Nodes

↓

4 Nodes
```

Scaling helps with increased workload, but it is not a replacement for good schema and query design.

---

# Chapter 13: Oracle → Striim → Cloud Spanner

Migration:

```text
Oracle

↓

Striim

↓

Cloud Spanner
```

During migration monitor:

* CDC lag
* Write latency
* CPU
* Transaction throughput
* Error rate

Performance problems during migration often appear first in monitoring dashboards.

---

# Chapter 14: Performance Monitoring

Use:

* Google Cloud Monitoring
* Datadog
* Cloud Logging

Monitor:

* CPU
* Storage
* Query latency
* Transaction latency
* Error rate
* Node utilization
* Read/write operations

---

# Chapter 15: DevOps Engineer Responsibilities

This is where your role becomes very important.

---

## Daily Health Check

Review:

* CPU
* Storage
* Read latency
* Write latency
* Commit latency
* Transaction aborts
* CDC lag
* Error logs

---

## Capacity Planning

Watch long-term trends.

Example:

```text
Week 1

CPU 35%

↓

Week 4

CPU 70%

↓

Week 8

CPU 88%
```

Plan scaling before users experience issues.

---

## Performance Incident Response

Example:

Application becomes slow.

Steps:

1. Check Datadog dashboard.
2. Review CPU and latency.
3. Check Cloud Logging.
4. Compare with recent deployments.
5. Identify root cause.
6. Involve developers or DBAs if schema/query changes are required.
7. Scale resources only if justified.

---

## Go-Live Monitoring

Immediately after deployment:

Monitor:

* Query latency
* Transactions
* CPU
* Errors
* CDC
* Application response time

---

## CI/CD Validation

After deployment:

Run validation queries.

Verify:

* Tables
* Indexes
* Response time
* Application health

---

## Performance Dashboard

Typical dashboard:

```text
Cloud Spanner

CPU

Read Latency

Write Latency

Storage

Transaction Latency

Errors

CDC Lag
```

---

# Chapter 16: Production Scenarios

## Scenario 1

CPU = 95%

Possible causes:

* Heavy workload
* Missing index
* Inefficient SQL
* Too few nodes

Actions:

* Review dashboards.
* Analyze slow queries.
* Check recent deployments.
* Scale only if needed.

---

## Scenario 2

Application Response = 5 Seconds

Possible causes:

* Query scans too much data.
* Missing index.
* Network delay.
* Transaction contention.

---

## Scenario 3

Striim Lag = 15 Minutes

Possible causes:

* Cloud Spanner under heavy write load.
* Network issues.
* Striim bottleneck.

---

# Chapter 17: Troubleshooting Workflow

A systematic approach helps.

```text
Alert

↓

Identify affected metric

↓

Review dashboards

↓

Check logs

↓

Analyze recent changes

↓

Identify root cause

↓

Implement fix

↓

Monitor results
```

Avoid changing multiple things at once.

---

# Chapter 18: Best Practices

✔ Design good primary keys.

✔ Create indexes based on actual query patterns.

✔ Keep transactions small.

✔ Monitor continuously.

✔ Scale proactively.

✔ Validate performance after every deployment.

✔ Review long-term trends.

---

# Chapter 19: Common Mistakes

❌ Scaling before investigating.

❌ Ignoring slow query patterns.

❌ Creating unnecessary indexes.

❌ Using poor primary keys.

❌ Running large transactions unnecessarily.

❌ Monitoring only CPU.

---

# Chapter 20: Interview Questions & Answers

### Q1. What is Performance Tuning?

**Answer:**
Performance tuning is the process of improving database efficiency, reducing latency, increasing throughput, and ensuring the system performs well under different workloads.

---

### Q2. What metrics should be monitored?

**Answer:**

* CPU utilization
* Read latency
* Write latency
* Commit latency
* Storage usage
* Transaction aborts
* Error rate
* Query performance

---

### Q3. What are common causes of poor Cloud Spanner performance?

**Answer:**

* Poor primary key design
* Missing indexes
* Inefficient queries
* Large transactions
* Heavy workload
* Insufficient compute capacity

---

### Q4. Should you always add more nodes when performance is poor?

**Answer:**
No. First identify the root cause using monitoring, logs, and workload analysis. Scaling should be based on evidence, not assumptions.

---

### Q5. What is the DevOps role in performance tuning?

**Answer:**

A DevOps engineer:

* Monitors production metrics
* Investigates alerts
* Reviews logs
* Performs capacity planning
* Supports scaling
* Validates deployments
* Collaborates with developers and database teams to resolve performance issues

---

# Real-Time Project Example

Suppose your Oracle → Striim → Cloud Spanner migration goes live.

```text
Oracle
      │
      ▼
Striim
      │
      ▼
Cloud Spanner
      │
      ▼
Application
```

Thirty minutes after go-live, Datadog sends alerts:

```text
CPU = 92%
Read Latency = 750 ms
CDC Lag = 8 minutes
```

As the DevOps engineer, your investigation might look like this:

1. Open Datadog dashboards.
2. Verify whether traffic increased after go-live.
3. Check Cloud Monitoring for CPU, latency, and transaction metrics.
4. Review Cloud Logging for errors.
5. Confirm whether a new deployment introduced inefficient queries.
6. Check if a required secondary index is missing.
7. If workload has genuinely increased, discuss scaling the Cloud Spanner instance.
8. Continue monitoring until metrics stabilize.

---

# Summary

```text
Performance Tuning
│
├── Query Optimization
├── Schema Optimization
├── Primary Key Design
├── Index Optimization
├── Transaction Optimization
├── CPU Optimization
├── Scaling
├── Monitoring
├── Capacity Planning
└── Incident Response
```

## Final Takeaways

* **Performance tuning is about finding and fixing the root cause—not just adding more resources.**
* Cloud Spanner performance depends on **schema design, primary keys, indexes, queries, transactions, and workload**, in addition to compute capacity.
* As a DevOps Engineer in your Oracle → Striim → Cloud Spanner project, you'll spend much of your time monitoring performance, investigating incidents, validating deployments, and planning capacity to keep production systems healthy.
