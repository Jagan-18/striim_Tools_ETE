# Pipeline Creation in Striim

A **Striim Pipeline** is an end-to-end data flow that captures data from a source, optionally transforms it using Continuous Queries (CQ), and delivers it to a target system in real time. In Striim, a pipeline is built using **Sources → Streams → CQs → Targets** within an Application/Flow. ([Striim][1])

---

# End-to-End Pipeline Architecture

```text
Source Database
      |
      v
Source Reader
      |
      v
Stream
      |
      v
CQ (Optional)
      |
      v
Target Writer
      |
      v
Target System
```

Example:

```text
Oracle
  |
OracleReader
  |
OrderStream
  |
Filter CQ
  |
BigQueryWriter
  |
BigQuery
```

---

# Pipeline Components

### 1. Source

Reads data from:

* Oracle
* MySQL
* SQL Server
* PostgreSQL
* Kafka
* Files

Example:

```text
OracleReader
```

Captures CDC events from the source database. ([Striim][1])

---

### 2. Stream

A stream carries events between components.

```text
OracleReader
      |
      v
OrderStream
```

Streams are the backbone of Striim data flows. ([Striim][1])

---

### 3. Continuous Query (CQ)

Used for:

* Filtering
* Transformations
* Aggregations
* Enrichment
* Joins

Example:

```sql
CREATE CQ HighValueOrders
INSERT INTO FilteredOrders
SELECT *
FROM OrderStream
WHERE amount > 10000;
```

CQs continuously process incoming events in real time. ([Striim][2])

---

### 4. Target

Writes data to:

* BigQuery
* Snowflake
* Kafka
* Cloud Storage
* Databases

Example:

```text
BigQueryWriter
```

---

# Building a Pipeline Using Flow Designer

## Step 1: Create Application

```text
Applications
    |
Create Application
```

Example:

```text
Oracle_To_BigQuery
```

Applications contain one or more flows. ([Striim][1])

---

## Step 2: Add Source

Drag:

```text
Source
```

Select:

```text
Oracle Reader
```

Configure:

```text
Host
Port
Service Name
Username
Password
```

Enable CDC:

```text
CDC = Enabled
```

---

## Step 3: Select Tables

Example:

```text
CUSTOMERS
ORDERS
EMPLOYEE
```

---

## Step 4: Create Stream

```text
OracleReader
      |
      v
OrderStream
```

---

## Step 5: Add Transformation (Optional)

Example:

```text
Filter Orders > 10000
```

Flow:

```text
OrderStream
      |
      v
FilterCQ
      |
      v
FilteredStream
```

---

## Step 6: Add Target

Choose:

```text
BigQuery
Snowflake
Kafka
```

Example:

```text
BigQuery Writer
```

Configure:

```text
Project ID
Dataset
Table
```

---

## Step 7: Validate

```text
Validate
```

Checks:

* Connectivity
* Credentials
* Schema
* Permissions

---

## Step 8: Deploy

```text
Deploy
```

Pipeline begins processing data immediately.

---

# GCP Example: Cloud SQL → BigQuery

```text
Cloud SQL (MySQL)
        |
        v
MySQLReader
        |
        v
CustomerStream
        |
        v
CustomerCQ
        |
        v
BigQueryWriter
        |
        v
BigQuery
```

This is a common real-time analytics architecture on GCP. ([Google Cloud Documentation][3])

---

# Oracle → Kafka Pipeline

```text
Oracle
   |
OracleReader
   |
CDCStream
   |
KafkaWriter
   |
Kafka Topic
```

---

# Oracle → Snowflake Pipeline

```text
Oracle
   |
OracleReader
   |
SalesStream
   |
SalesCQ
   |
SnowflakeWriter
   |
Snowflake
```

---

# Read Once, Stream Anywhere Pattern

A common Striim design pattern is:

```text
Oracle
   |
OracleReader
   |
Persistent Stream
   |
   +----> Kafka
   |
   +----> BigQuery
   |
   +----> Snowflake
```

The source database is read only once, and the stream can feed multiple downstream targets. ([Striim][4])

---

# Monitoring the Pipeline

Navigate:

```text
Monitoring
```

Track:

```text
Status      : Running
Events/sec  : 5000
Latency     : 100 ms
Errors      : 0
CDC Lag     : Low
```

---

# Best Practices

✅ Use CDC instead of full table scans

✅ Enable Recovery and Checkpointing

✅ Use Persistent Streams for multiple consumers

✅ Monitor CDC lag and throughput

✅ Separate source ingestion and target delivery applications for large workloads

✅ Validate source and target connectivity before deployment ([Striim][4])

---
**How do you create an end-to-end pipeline in Striim?**

1. Create a Striim Application.
2. Add a Source Reader (Oracle, MySQL, SQL Server, etc.).
3. Enable CDC and select tables.
4. Create a Stream to carry events.
5. Add Continuous Queries (CQ) for filtering or transformation if needed.
6. Add a Target Writer such as BigQuery, Kafka, or Snowflake.
7. Validate the configuration.
8. Deploy and monitor the pipeline.

The data flows continuously from the source through streams and transformations to the target with low latency and real-time CDC support. 