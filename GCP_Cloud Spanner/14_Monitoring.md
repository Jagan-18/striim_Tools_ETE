# This is one of the most important topics for your project.

**Monitoring** is probably the **#1 day-to-day responsibility** after deployment.

Many beginners think monitoring means:

> "Checking CPU and Memory."

That's only about **5%** of real monitoring.

In production, monitoring means answering questions like:

* Is Cloud Spanner healthy?
* Is Striim replicating data?
* Is CDC lag increasing?
* Is CPU too high?
* Are transactions failing?
* Are queries slow?
* Is storage growing too quickly?
* Did the latest deployment introduce errors?
* Are users experiencing problems?
* Are IAM permission failures occurring?

As a DevOps Engineer, you'll be responsible for detecting these issues **before users notice them**.

---

# Topic 14: Cloud Spanner Monitoring (Complete End-to-End Professional Guide)

---

# Table of Contents

We'll cover:

1. What is Monitoring?
2. Why Monitoring is Important
3. Monitoring Architecture
4. What Should We Monitor?
5. Cloud Spanner Metrics
6. Infrastructure Monitoring
7. Database Monitoring
8. Application Monitoring
9. Migration Monitoring (Oracle → Striim → Cloud Spanner)
10. Google Cloud Monitoring
11. Datadog Integration
12. Logging
13. Alerting
14. Dashboards
15. DevOps Responsibilities
16. Production Scenarios
17. Troubleshooting
18. Best Practices
19. Common Mistakes
20. Interview Questions

---

# Chapter 1: What is Monitoring?

Monitoring is the continuous observation of your systems to ensure they are healthy, performant, and available.

Monitoring helps answer questions such as:

* Is the system working?
* Is it fast enough?
* Is it secure?
* Is it failing?
* Do users experience issues?

Without monitoring, problems are often discovered only after customers report them.

---

# Chapter 2: Real-Life Example

Imagine driving a car.

The dashboard shows:

* Speed
* Fuel level
* Engine temperature
* Battery status

These indicators tell you if something needs attention.

Cloud Spanner monitoring works the same way.

Instead of a car dashboard, you monitor:

* CPU utilization
* Storage
* Transactions
* Latency
* Errors
* Replication

---

# Chapter 3: Monitoring Architecture

A typical architecture looks like this:

```text
Application
      │
      ▼
Cloud Spanner
      │
      ▼
Google Cloud Monitoring
      │
      ├── Dashboards
      ├── Alerts
      ├── Logs
      └── Metrics
      │
      ▼
Datadog
      │
      ▼
DevOps Team
```

Monitoring data flows from Cloud Spanner into observability tools, where engineers can visualize and respond to issues.

---

# Chapter 4: Types of Monitoring

There are several layers.

```text
Monitoring
│
├── Infrastructure Monitoring
├── Database Monitoring
├── Application Monitoring
├── Security Monitoring
├── Migration Monitoring
├── Network Monitoring
└── CI/CD Monitoring
```

---

# Chapter 5: Infrastructure Monitoring

Infrastructure monitoring focuses on the underlying resources.

Typical metrics:

* CPU utilization
* Memory (where applicable to compute resources)
* Storage usage
* Network activity
* Disk performance (for supporting infrastructure)

Example:

```text
CPU

20%

↓

60%

↓

95%

↓

Alert
```

High CPU can indicate increased workload or inefficient queries.

---

# Chapter 6: Cloud Spanner Database Monitoring

These metrics are specific to Cloud Spanner.

## CPU Utilization

Measures how busy the instance is.

Example:

```text
CPU

10%

↓

30%

↓

50%

↓

90%
```

If CPU remains high for a long period, investigate:

* Traffic growth
* Slow queries
* Insufficient nodes or processing units

---

## Storage

Monitor storage growth.

Example:

```text
100 GB

↓

200 GB

↓

500 GB
```

Rapid growth may indicate:

* Business growth
* Data retention issues
* Unexpected data duplication

---

## Read Latency

How long read operations take.

Healthy:

```text
10 ms
```

Problem:

```text
500 ms
```

Possible causes:

* Missing indexes
* Poor schema design
* Heavy workload

---

## Write Latency

Measures the time required to write data.

High write latency can result from:

* Heavy traffic
* Contention
* Large transactions
* Insufficient compute capacity

---

## Commit Latency

Measures how long it takes to commit a transaction.

High commit latency may indicate:

* Busy workloads
* Network delays
* High contention

---

# Chapter 7: Transaction Monitoring

Monitor:

* Transaction count
* Commit latency
* Aborted transactions
* Retry rates

Example:

```text
Transaction

↓

Aborted

↓

Retry

↓

Success
```

A growing abort rate may indicate concurrent updates to the same data.

---

# Chapter 8: Query Monitoring

Slow queries are one of the most common production issues.

Example:

```sql
SELECT *

FROM Employee

WHERE Email='rahul@gmail.com';
```

If this query is slow, possible reasons include:

* Missing index
* Large data volume
* Poor schema design

---

# Chapter 9: Migration Monitoring (Your Project)

Project flow:

```text
Oracle
   │
   ▼
Striim
   │
   ▼
Cloud Spanner
```

During migration, monitor:

* CDC lag
* Replication status
* Failed events
* Throughput
* Transaction consistency
* Row count validation
* Error logs

Example:

```text
Oracle

↓

100 Transactions

↓

Striim

↓

100 Transactions

↓

Cloud Spanner
```

Counts should remain consistent.

---

# Chapter 10: Google Cloud Monitoring

Google Cloud Monitoring collects metrics from Cloud Spanner and other Google Cloud services.

Common uses:

* View dashboards
* Create alerts
* Analyze trends
* Troubleshoot incidents

Examples of dashboards:

* CPU
* Storage
* Latency
* API requests
* Errors

---

# Chapter 11: Datadog Integration

From your project details, Datadog is an important monitoring tool.

Typical architecture:

```text
Cloud Spanner
      │
      ▼
Cloud Monitoring
      │
      ▼
Datadog
      │
      ▼
Alerts
```

Datadog helps you:

* Build dashboards
* Correlate infrastructure and application metrics
* Set alerts
* Investigate incidents

---

# Chapter 12: Logging

Metrics tell you **that** something is wrong.

Logs help explain **why**.

Example log:

```text
Permission denied

Service Account XYZ

Missing IAM Role
```

Logs are essential during troubleshooting.

---

# Chapter 13: Alerting

Monitoring without alerts isn't enough.

Examples:

```text
CPU > 80%

↓

Alert
```

```text
CDC Lag > 5 Minutes

↓

Alert
```

```text
Transaction Failure

↓

Alert
```

Alerts should notify the appropriate on-call team.

---

# Chapter 14: Dashboards

A good dashboard includes:

* CPU utilization
* Storage usage
* Read latency
* Write latency
* Commit latency
* Transaction aborts
* Error counts
* API request rates
* CDC lag
* Striim status

---

# Chapter 15: DevOps Engineer Responsibilities

This is where you'll spend much of your time.

## Daily Health Check

Typical morning checklist:

* Is Cloud Spanner healthy?
* Are all instances available?
* Is CPU normal?
* Is storage growing as expected?
* Are there transaction errors?
* Is Striim healthy?
* Is CDC lag acceptable?
* Are there failed deployments?
* Did any alerts trigger overnight?

---

## Production Monitoring

Monitor:

* CPU
* Storage
* Latency
* Errors
* Replication
* Query performance
* IAM failures

---

## Alert Management

Receive alerts.

Determine:

* False alarm?
* Real incident?
* Business impact?
* Escalation needed?

---

## Incident Response

Example:

```text
Application Slow

↓

Check Datadog

↓

CPU = 95%

↓

Check Queries

↓

Missing Index

↓

Developer Adds Index

↓

Performance Restored
```

---

## Go-Live Monitoring

After production deployment:

Monitor:

* Errors
* Transactions
* CPU
* Latency
* CDC
* Application health
* User traffic

The first few hours after release are critical.

---

## Capacity Planning

Review long-term trends.

Example:

```text
Month 1

CPU 30%

↓

Month 2

CPU 45%

↓

Month 3

CPU 70%
```

Plan additional capacity before performance degrades.

---

# Chapter 16: Real-Time Production Scenarios

### Scenario 1: High CPU

Possible causes:

* Increased user traffic
* Poor queries
* Too few nodes

Action:

* Review dashboards
* Analyze workload
* Optimize queries
* Scale compute if needed

---

### Scenario 2: High CDC Lag

Possible causes:

* Striim issue
* Network problem
* Cloud Spanner under heavy load

Action:

* Check Striim health
* Review logs
* Validate replication

---

### Scenario 3: Slow Application

Possible causes:

* Missing index
* High read latency
* Transaction contention

Action:

* Review query performance
* Check latency metrics
* Work with developers if optimization is needed

---

# Chapter 17: Best Practices

✔ Create meaningful dashboards.

✔ Configure actionable alerts.

✔ Monitor trends, not just current values.

✔ Investigate repeated alerts.

✔ Correlate metrics with logs.

✔ Test alerts regularly.

✔ Monitor every production environment.

---

# Chapter 18: Common Mistakes

❌ Monitoring only CPU.

❌ No alert configuration.

❌ Ignoring logs.

❌ Not monitoring migration progress.

❌ Failing to review dashboards after deployments.

❌ Alert fatigue caused by poorly tuned thresholds.

---

# Interview Questions & Answers

### Q1. Why is monitoring important?

**Answer:**
Monitoring helps detect performance issues, failures, capacity problems, and security events before they significantly impact users.

---

### Q2. What metrics should you monitor for Cloud Spanner?

**Answer:**

* CPU utilization
* Storage usage
* Read latency
* Write latency
* Commit latency
* Transaction aborts
* Query performance
* Error rates

---

### Q3. What is the difference between metrics and logs?

| Metrics                                        | Logs                                            |
| ---------------------------------------------- | ----------------------------------------------- |
| Numerical measurements (CPU, latency, storage) | Detailed event records explaining what happened |
| Good for trends and alerts                     | Good for root cause analysis                    |

---

### Q4. How would you monitor an Oracle → Striim → Cloud Spanner migration?

**Answer:**

I would monitor:

* Striim health
* CDC lag
* Replication errors
* Row counts
* Transaction consistency
* Cloud Spanner latency
* Error logs
* Infrastructure health

---

### Q5. What is the DevOps role in monitoring?

**Answer:**

A DevOps engineer designs dashboards, configures alerts, monitors production health, investigates incidents, validates deployments, performs capacity planning, and collaborates with developers and database teams to resolve issues.

---

# Real-Time Project Example

Suppose your team deploys a new release.

```text
Developer
      │
      ▼
Git
      │
      ▼
Harness
      │
      ▼
Deploy to Cloud Spanner
      │
      ▼
DevOps Monitoring
      │
      ├── CPU = Normal ✓
      ├── Read Latency = Normal ✓
      ├── Write Latency = Normal ✓
      ├── CDC Lag = 0 ✓
      ├── Error Rate = 0 ✓
      └── User Traffic = Healthy ✓
```

Two hours later, Datadog alerts:

```text
Read Latency = 800 ms
CPU = 92%
```

As the DevOps engineer, your steps would be:

1. Acknowledge the alert.
2. Open the Datadog dashboard.
3. Compare CPU, latency, and traffic trends.
4. Check Cloud Logging for errors.
5. Determine whether the issue started after the deployment.
6. If query-related, involve the database/development team.
7. If capacity-related, evaluate scaling the Cloud Spanner instance.
8. Monitor until metrics return to normal.

---

# Summary

```text
Monitoring
│
├── Infrastructure
├── Cloud Spanner Metrics
│     ├── CPU
│     ├── Storage
│     ├── Read Latency
│     ├── Write Latency
│     ├── Commit Latency
│     └── Transaction Aborts
├── Migration Monitoring
├── Logging
├── Alerting
├── Dashboards
└── Incident Response
```

## Final Takeaways

* **Monitoring is one of the core responsibilities of a DevOps Engineer** in a Cloud Spanner project.
* Monitoring is much more than CPU and memory—it includes **database performance, transactions, migration health, logs, alerts, dashboards, and capacity planning**.
* In your Oracle → Striim → Cloud Spanner migration project, you should monitor **Cloud Spanner**, **Striim**, **Google Cloud Monitoring**, and **Datadog** together to get a complete view of system health.
* A good DevOps engineer doesn't just react to alerts—they build monitoring that detects problems early and helps the team resolve them quickly.
