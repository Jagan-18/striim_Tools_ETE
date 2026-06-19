# Striim:
**Striim** is a **real-time data integration and streaming platform** used to move, process, and synchronize data between databases, applications, cloud platforms, and data warehouses with minimal latency.

### What Striim Does
* Captures data changes from source databases in real time (CDC - Change Data Capture)
* Streams data continuously to target systems
* Performs real-time data transformation and enrichment
* Supports data migration and replication
* Enables real-time analytics and reporting

### Key Features
1. **Change Data Capture (CDC):**
- Captures INSERT, UPDATE, and DELETE operations from databases.
- No need for full table refreshes.

**Benefits:**
1. Real-time replication
2. Reduced network traffic
3. Faster synchronization

2. **Real-Time Data Replication:**
* Replicates data between:

  1. Oracle
  2. SQL Server
  3. MySQL
  4. PostgreSQL
  5. MongoDB
  6. Snowflake
  7. Kafka
  8. BigQuery
  9. Azure Synapse

3. **Data Transformation:**
- Filter, enrich, aggregate, and modify data while streaming.

4. **High Availability:**
- Supports fault tolerance and recovery.

### Common Use Cases:
1. Database Migration

   * Oracle → Snowflake
   * SQL Server → GCP BigQuery

2. Real-Time Data Warehousing

   * Database → Snowflake

3. Cloud Migration

   * On-Prem → AWS/GCP/Azure

4. Event Streaming

   * Database → Kafka

5. Real-Time Dashboards

   * Operational reporting with live data

---
## Striim Architecture:
```text
Source Database
      |
      v
   CDC Reader
      |
      v
   Striim Server
      |
      v
 Transformations
      |
      v
 Target System
```

#### Example:

```text
Oracle
   |
   v
Striim
   |
   v
Kafka
   |
   v
Snowflake
```

---
## Important Components:
### Source
Reads data from:
* Oracle
* SQL Server
* MySQL
* PostgreSQL
* Kafka

### Stream
In-memory continuous flow of events.

### CQ (Continuous Query)

Processes and transforms streaming data.

Example:

```sql
CREATE CQ FilterOrders
INSERT INTO FilteredStream
SELECT *
FROM InputStream
WHERE amount > 1000;
```

### Target:
Writes data to:

* Snowflake
* Kafka
* S3
* BigQuery
* Databases

---
## What is TQL in Striim?
**TQL (Tungsten Query Language)** is Striim's SQL-like language used to create applications, streams, sources, targets, and transformations.

Example:

```sql
CREATE SOURCE OracleSource USING OracleReader (
    ConnectionURL:'jdbc:oracle:thin:@host:1521:orcl',
    Username:'admin',
    Password:'password'
)
OUTPUT TO OracleStream;
```

Example Target:

```sql
CREATE TARGET SnowflakeTarget
USING SnowflakeWriter (
    ConnectionURL:'jdbc:snowflake://account.snowflakecomputing.com',
    Username:'user',
    Password:'password'
)
INPUT FROM OracleStream;
```



#### Q1: What is Striim?
**Answer:**
- Striim is a real-time data integration and streaming platform that uses Change Data Capture (CDC) to continuously capture and replicate data changes from source systems to target systems such as Kafka, Snowflake, BigQuery, and cloud databases with low latency.

- For a DevOps engineer, think of Striim as a tool that enables **real-time data pipelines**, similar to how Jenkins automates CI/CD pipelines.

#### Q2. What is CDC?
- Change Data Capture captures INSERT, UPDATE, and DELETE operations from database transaction logs.

#### Q3. What is TQL?
- Tungsten Query Language, a SQL-like language used to create Striim applications, sources, streams, queries, and targets.

#### Q4. What is a Continuous Query (CQ)?
- A query that continuously processes streaming data in real time.

#### Q5. What are common Striim targets?
- Snowflake
- Kafka
- BigQuery
- AWS S3
- Databases

#### Q6. What are Sources and Targets?
1. Source = Data producer
2. Target = Data consumer

#### Q7 What is the use of Striim in a project?
- Database migration
- Data replication
- Streaming analytics
- Real-time reporting
- Cloud migration

Since you're learning GCP, Kafka, Snowflake, Kubernetes, and DevOps, Striim is mainly used in Data Engineering and Real-Time Data Pipelines, especially for:

- Oracle → Kafka
- Oracle → Snowflake
- SQL Server → BigQuery
- On-Prem → Cloud migrations.