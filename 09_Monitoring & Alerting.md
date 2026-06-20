# Monitoring & Alerting in Striim

Monitoring and Alerting are critical in Striim to ensure CDC pipelines are running smoothly, data is flowing correctly, and there is no data loss or excessive lag.

---
# Why Monitoring is Important:

Monitor:
✅ Pipeline Health
✅ CDC Lag
✅ Throughput
✅ Errors
✅ Source Connectivity
✅ Target Connectivity
✅ Node Health
✅ CPU & Memory Usage

---
# Striim Monitoring Architecture:S

```text id="9g0m3a"
Oracle Database
       |
       v
   Striim Pipeline
       |
       v
  Monitoring Dashboard
       |
       v
   Alerts & Notifications
```

---
# Access Monitoring Dashboard:

Login to Striim UI:

```text id="5wz8gf"
http://<Striim_Server>:9080
```

Navigate:

```text id="r8z1sr"
Dashboard
    |
Monitoring
```

---
# Key Monitoring Metrics:
## 1. Application Status

Shows pipeline state.

```text id="66sht4"
Running
Stopped
Paused
Failed
```

Example:

```text id="kltjlwm"
Oracle_To_BigQuery

Status: Running
```

---
## 2. Throughput:

Measures events processed per second.

```text id="avswtm"
Events/sec
```

Example:

```text id="f6m7jv"
5000 Events/sec
```

Meaning:

```text id="8n4nwd"
5000 CDC events processed every second
```

Higher throughput generally indicates healthy processing.

---
## 3. CDC Lag:

Most important metric.

Lag indicates the difference between:

```text id="ivgyh2"
Changes generated in source
            -
Changes processed by Striim
```

Example:

```text id="cl1eik"
Source Events : 100000

Processed     : 99500

Lag           : 500
```

---
### Healthy Lag:

```text id="p2h1hm"
0 - 100 events
```

### Warning

```text id="v4pv85"
1000+ events
```

### Critical

```text id="w6t95e"
10000+ events
```

---
# Pipeline Monitoring View:

Example:

```text id="bb4ylx"
Application Name:
Oracle_BigQuery_CDC

Status:
Running

Throughput:
5000 Events/sec

Latency:
150 ms

Lag:
50 Events

Errors:
0
```

---
# Latency Monitoring:

Measures:

```text id="ydb1xj"
Source Change
       |
       v
Target Arrival Time
```

Example:

```text id="gq5gdh"
Latency = 100 ms
```

Meaning:

```text id="r98hzz"
Data reaches target in 100 milliseconds
```

---
# Source Monitoring:

Check:

```text id="s0xq0z"
OracleReader
MySQLReader
SQLServerReader
```

Dashboard shows:

```text id="9ws4tu"
Connection Status

Connected
Disconnected
Reconnecting
```

---
# Target Monitoring:

Monitor:

```text id="4g0zst"
BigQuery Writer
Kafka Writer
Snowflake Writer
```

Check:

```text id="72b7uu"
Write Success
Write Failure
Retries
```

---
# Node Monitoring:

Production clusters:

```text id="r1i8pw"
Node1
Node2
Node3
```

Monitor:

```text id="cbxqk9"
CPU
Memory
Disk
Network
```

Example:

```text id="ew8ezi"
CPU      40%
Memory   60%
Disk     30%
```

---
# Error Monitoring:

Dashboard displays:

```text id="3xjlwm"
Connection Failed
Authentication Failed
Target Timeout
CDC Reader Error
```

Example:

```text id="6hzbqj"
BigQuery Connection Failed
```

Immediate investigation required.

---
# Common Alert Types:

## Source Disconnected

```text id="a5x98f"
Oracle Connection Lost
```

Alert Generated:

```text id="0k4jxj"
Source Connectivity Alert
```

---
## Target Failure:

```text id="m6vc0v"
BigQuery Not Reachable
```

Alert Generated:

```text id="jlwmko"
Target Failure Alert
```

---
## High CDC Lag:

```text id="u9ggkp"
Lag > 5000 Events
```

Alert Generated:

```text id="m7r2lx"
CDC Lag Alert
```

---
## Node Failure:

```text id="yl39nd"
Node-2 Down
```

Alert Generated:

```text id="3b8e4g"
Cluster Health Alert
```

---
# GCP Monitoring Example:
Pipeline:

```text id="jlwmzo"
Cloud SQL
     |
MySQLReader
     |
CustomerStream
     |
BigQueryWriter
     |
BigQuery
```

Monitor:

```text id="lxvwk6"
Cloud SQL Connectivity

CDC Lag

BigQuery Load Errors

Pipeline Throughput
```

---
# Troubleshooting High Lag:
### Possible Causes

```text id="z6hnh7"
Heavy Source Activity

Slow Network

BigQuery Throttling

Kafka Backpressure

Insufficient CPU
```

### Resolution:
```text id="gl1wio"
Increase Striim Nodes

Tune Batch Size

Scale Target

Increase VM Resources
```

---
# Monitoring Best Practices:
### Monitor Daily

```text id="ayjlwm"
Application Status
CDC Lag
Throughput
Latency
```

### Configure Alerts:
```text id="67dzlv"
Lag Threshold

Node Failure

Source Disconnect

Target Failure
```

### Capacity Planning:
Monitor:

```text id="rwm3ch"
CPU

Memory

Disk
```

before they become bottlenecks.

---
# Typical Dashboard View:

```text id="s8zjqf"
+--------------------------------+
| Pipeline Health Dashboard      |
+--------------------------------+
| Status      : Running          |
| Throughput  : 5000/sec         |
| Lag         : 50 events        |
| Latency     : 120 ms           |
| Errors      : 0                |
+--------------------------------+
```

---
### What metrics do you monitor in Striim?

* Pipeline Status
* Throughput
* CDC Lag
* Latency
* Source Connectivity
* Target Connectivity
* CPU & Memory
* Errors

---
### What is CDC Lag?

CDC Lag is the difference between the number of changes generated in the source database and the number of changes processed by Striim.

---
### How do you identify a healthy pipeline?

A healthy pipeline has:

* Running status
* Low latency
* Minimal CDC lag
* No errors
* Stable throughput

---
# Quick Revision
```text id="b4mfdv"
Monitoring Metrics
------------------
Pipeline Status
Throughput
CDC Lag
Latency
Errors
CPU
Memory

Common Alerts
-------------
Source Disconnect
Target Failure
High CDC Lag
Node Failure

Healthy Pipeline
----------------
Running
Low Lag
Low Latency
No Errors
Stable Throughput
```
---
**How do you monitor Striim pipelines?**
- Using the Striim Monitoring Dashboard, I monitor application status, CDC lag, throughput, latency, source and target connectivity, node health, CPU/memory utilization, and error rates. I configure alerts for source disconnections, target failures, high lag, and node outages to ensure real-time pipelines remain healthy and meet SLA requirements.
