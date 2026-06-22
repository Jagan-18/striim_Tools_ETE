# Striim Sources & Targets:
In Striim, a **Source (Reader)** reads data from a system, and a **Target (Writer)** writes data to another system.

```text
Source (Reader) --> Stream --> CQ/Transformation --> Target (Writer)
```

#### Example:

```text
Oracle --> Striim --> BigQuery
MySQL  --> Striim --> Kafka
SQL Server --> Striim --> Snowflake
```

---
# What is a Source?

A **Source** is responsible for capturing data from a database, application, file, or messaging system.

Types of data capture:

* CDC (Change Data Capture)
* Initial Load (Snapshot)
* Real-time Streaming

---
# Common Source Readers:
## 1. OracleReader

Reads:

* Oracle Database
* Oracle CDC (Redo Logs)

```text
Oracle DB
    |
OracleReader
```

Use Cases:

* Oracle → BigQuery
* Oracle → Kafka
* Oracle → Snowflake

---
## 2. MySQLReader:

Reads:

* MySQL Binary Logs (Binlogs)

```text
MySQL
   |
MySQLReader
```

Use Cases:

* MySQL CDC
* Database migration

---

## 3. SQLServerReader

Reads:

* SQL Server Transaction Logs

```text
SQL Server
      |
SQLServerReader
```

Captures:

* INSERT
* UPDATE
* DELETE

---

## 4. PostgreSQLReader

Reads:

* PostgreSQL WAL Logs

```text
PostgreSQL
      |
PostgreSQLReader
```

---

## 5. KafkaReader

Reads messages from Kafka topics.

```text
Kafka Topic
      |
KafkaReader
```

Use Cases:

* Event streaming
* Real-time analytics

---

## 6. FileReader

Reads:

* CSV
* JSON
* XML
* Log files

```text
CSV File
    |
FileReader
```

---

## 7. Cloud Storage Readers

Examples:

* GCS (Google Cloud Storage)
* AWS S3
* Azure Blob Storage

```text
GCS Bucket
     |
FileReader
```

---

# What is a Target?

A **Target** writes processed data to a destination system.

```text
Source
   |
 Stream
   |
 Target
```

---

# Common Target Writers

## 1. BigQueryWriter

Most common in GCP projects.

```text
Striim
   |
BigQueryWriter
   |
BigQuery
```

Use Cases:

* Data Warehouse
* Analytics

---

## 2. SnowflakeWriter

Writes to:

```text
Snowflake
```

Use Cases:

* Cloud Data Warehouse
* Reporting

---

## 3. KafkaWriter

Publishes events to Kafka.

```text
Striim
   |
KafkaWriter
   |
Kafka Topic
```

Use Cases:

* Microservices
* Event Streaming

---

## 4. DatabaseWriter

Writes to:

* Oracle
* MySQL
* PostgreSQL
* SQL Server

```text
Striim
   |
DatabaseWriter
   |
Target DB
```

---

## 5. FileWriter

Writes:

* CSV
* JSON
* XML

```text
Striim
   |
FileWriter
```

---

## 6. Cloud Storage Writers

Writes to:

### GCP

```text
Google Cloud Storage (GCS)
```

### AWS

```text
Amazon S3
```

### Azure

```text
Azure Blob Storage
```

---

# Source and Target Flow Examples

## Oracle → BigQuery

```text
Oracle
   |
OracleReader
   |
OrderStream
   |
BigQueryWriter
   |
BigQuery
```

---

## MySQL → Kafka

```text
MySQL
   |
MySQLReader
   |
CustomerStream
   |
KafkaWriter
   |
Kafka Topic
```

---

## SQL Server → Snowflake

```text
SQL Server
      |
SQLServerReader
      |
SalesStream
      |
SnowflakeWriter
      |
Snowflake
```

---

# Initial Load + CDC

Many projects require:

### Initial Load

```text
Existing Data
```

### CDC

```text
New Changes
```

Example:

```text
Oracle
   |
OracleReader
   |
Initial Load
   +
CDC
   |
BigQuery
```

This ensures:

* Historical data migration
* Real-time synchronization

---

# GCP-Focused Striim Pipeline

```text
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

Other GCP targets:

```text
Cloud SQL
Pub/Sub
BigQuery
Cloud Storage
```

---

# Reader vs Writer

| Reader (Source)  | Writer (Target) |
| ---------------- | --------------- |
| Reads data       | Writes data     |
| OracleReader     | OracleWriter    |
| MySQLReader      | DatabaseWriter  |
| KafkaReader      | KafkaWriter     |
| FileReader       | FileWriter      |
| SQLServerReader  | SnowflakeWriter |
| PostgreSQLReader | BigQueryWriter  |

---

# Interview Questions

### What is a Source in Striim?

A Source is a component that captures data from databases, files, Kafka topics, or cloud storage systems and sends the data into Striim streams for processing.

### What is a Target in Striim?

A Target is a component that writes processed data from Striim streams to destination systems such as BigQuery, Snowflake, Kafka, databases, or cloud storage.

### What are the most commonly used readers and writers?

**Readers:**

* OracleReader
* MySQLReader
* SQLServerReader
* PostgreSQLReader
* KafkaReader
* FileReader

**Writers:**

* BigQueryWriter
* SnowflakeWriter
* KafkaWriter
* DatabaseWriter
* FileWriter

---

## Quick Revision

```text
Readers (Sources)
-----------------
OracleReader
MySQLReader
SQLServerReader
PostgreSQLReader
KafkaReader
FileReader

Writers (Targets)
-----------------
BigQueryWriter
SnowflakeWriter
KafkaWriter
DatabaseWriter
FileWriter
```

For a GCP-based project, the most common interview scenario is:

```text
Cloud SQL (MySQL)
       |
   MySQLReader
       |
     Stream
       |
 BigQueryWriter
       |
    BigQuery
```

This demonstrates real-time CDC from Cloud SQL into BigQuery using Striim.
