# CDC (Change Data Capture) in Striim:
## What is CDC?

**Change Data Capture (CDC)** is a technique used to identify and capture changes made to a database in real time.

Instead of reading the entire table repeatedly, CDC captures only:

* INSERT
* UPDATE
* DELETE

operations from database transaction logs.

---
## Why CDC?
### Traditional Approach:

```text
Database
   |
Full Table Scan
   |
Target System
```

Problems:

* High database load
* Slow performance
* Large network traffic
* Not real-time

---
### CDC Approach:

```text
Database
   |
Transaction Logs
   |
Striim CDC Reader
   |
Target System
```

Benefits:

* Real-time replication
* Minimal database impact
* Faster processing
* Reduced network usage

---
# How CDC Works:

When a user performs:

```sql
INSERT INTO EMPLOYEE VALUES(101,'John');
```

or

```sql
UPDATE EMPLOYEE
SET SALARY = 50000
WHERE EMP_ID = 101;
```

or

```sql
DELETE FROM EMPLOYEE
WHERE EMP_ID = 101;
```

the database writes these changes to transaction logs.

Striim reads those logs and streams the changes.

---
# Log-Based CDC Architecture:

```text
+----------------+
| Source DB      |
| (Oracle/MySQL) |
+--------+-------+
         |
         | Transaction Logs
         v
+----------------+
| Striim Reader  |
| CDC Engine     |
+--------+-------+
         |
         v
+----------------+
| Stream         |
+--------+-------+
         |
         v
+----------------+
| Target System  |
| BigQuery/Kafka |
+----------------+
```

---
# Supported Databases for CDC:

| Database   | Log Type              |
| ---------- | --------------------- |
| Oracle     | Redo Logs             |
| MySQL      | Binary Logs (Binlogs) |
| SQL Server | Transaction Logs      |
| PostgreSQL | WAL (Write Ahead Log) |
| MariaDB    | Binlogs               |

---
# Oracle CDC Example:

```text
Oracle Database
      |
      v
Redo Logs
      |
      v
OracleReader
      |
      v
EmployeeStream
      |
      v
BigQueryWriter
      |
      v
BigQuery
```

### Flow:
1. User updates Oracle table.
2. Oracle writes change to Redo Log.
3. Striim OracleReader reads Redo Log.
4. Event enters Stream.
5. Data reaches BigQuery.

---
# MySQL CDC Example;

```text
MySQL
   |
Binlogs
   |
MySQLReader
   |
CustomerStream
   |
KafkaWriter
   |
Kafka
```

---
# SQL Server CDC Example:

```text
SQL Server
     |
Transaction Log
     |
SQLServerReader
     |
SalesStream
     |
SnowflakeWriter
```

---
# Initial Load + CDC

Most real projects use:

### Initial Load

Copies existing data.

```text
Historical Records
```

### CDC
Captures future changes.

```text
New Changes
```

Architecture:

```text
Oracle Table
      |
Initial Load
      +
CDC
      |
Striim
      |
BigQuery
```

Example:

```text
1 Million Existing Records
+
New Inserts/Updates/Deletes
```

---
# CDC Event Example

Source Table:

```text
EMPLOYEE
```

Insert:

```sql
INSERT INTO EMPLOYEE
VALUES(101,'John');
```

CDC Event:

```json
{
  "Operation":"INSERT",
  "EMP_ID":101,
  "NAME":"John"
}
```

Update:

```sql
UPDATE EMPLOYEE
SET NAME='David'
WHERE EMP_ID=101;
```

CDC Event:

```json
{
  "Operation":"UPDATE",
  "EMP_ID":101,
  "NAME":"David"
}
```

Delete:

```sql
DELETE FROM EMPLOYEE
WHERE EMP_ID=101;
```

CDC Event:

```json
{
  "Operation":"DELETE",
  "EMP_ID":101
}
```

---
# CDC in Striim Flow Designer:

### Step 1

Add Source

```text
Oracle Reader
```

### Step 2

Enable CDC

```text
CDC = Enabled
```

### Step 3

Select Tables

```text
EMPLOYEE
ORDERS
CUSTOMERS
```

### Step 4

Add Target

```text
BigQuery
Snowflake
Kafka
```

### Step 5

Deploy

Striim starts reading logs automatically.

---
# CDC Checkpoints:

Striim maintains checkpoints.

```text
Oracle
   |
Checkpoint
   |
BigQuery
```

Benefits:

* No data loss
* Recovery after restart
* Resume from last processed position

---

# GCP Example

```text
Cloud SQL (MySQL)
        |
     Binlogs
        |
  MySQLReader
        |
CustomerStream
        |
BigQueryWriter
        |
BigQuery
```

Use Case:

* Real-time analytics
* Reporting dashboards
* Data warehouse synchronization

---
# Advantages of Log-Based CDC:

✅ Real-time replication

✅ Low source database impact

✅ High performance

✅ No full table scans

✅ Supports INSERT, UPDATE, DELETE

✅ Scalable for large databases

---
### What is CDC?
- CDC (Change Data Capture) is a technique that captures database changes such as INSERT, UPDATE, and DELETE operations and delivers them to downstream systems in real time.

### What is Log-Based CDC?
- Log-based CDC reads database transaction logs (Redo Logs, Binlogs, WAL, Transaction Logs) instead of querying tables directly, resulting in low latency and minimal impact on the source database.

### Why is CDC preferred over Full Load?
| Full Load            | CDC                  |
| -------------------- | -------------------- |
| Reads entire table   | Reads only changes   |
| High DB load         | Low DB load          |
| Slow                 | Real-time            |
| More network traffic | Less network traffic |

### Which logs are used by different databases?
* Oracle → Redo Logs
* MySQL → Binlogs
* SQL Server → Transaction Logs
* PostgreSQL → WAL Logs

---
## Quick Revision

```text
CDC = Change Data Capture

Captures:
✓ INSERT
✓ UPDATE
✓ DELETE

Reads:
✓ Oracle Redo Logs
✓ MySQL Binlogs
✓ SQL Server Logs
✓ PostgreSQL WAL

Flow:
Database
   ↓
Transaction Logs
   ↓
Striim Reader
   ↓
Stream
   ↓
Target (BigQuery/Kafka/Snowflake)
```

This is one of the most important Striim concepts because almost every real-time migration, replication, and analytics pipeline is built using **log-based CDC**.
---
# Oracle CDC Setup in Striim (LogMiner + Supplemental Logging)

For Oracle CDC, Striim typically uses **Oracle Reader (LogMiner-based CDC)** to capture changes from Oracle Redo Logs and Archive Logs in real time. Striim reads the transaction logs instead of querying tables, which minimizes impact on the source database. ([Striim][1])

---

# Oracle CDC Architecture

```text
Oracle Database
      |
      | Redo Logs / Archive Logs
      v
   LogMiner
      |
      v
Oracle Reader (Striim)
      |
      v
   Stream
      |
      v
BigQuery / Kafka / Snowflake
```

---

# Prerequisites

Before configuring Oracle CDC:

### Oracle Database Requirements

* Oracle 11g or later
* ARCHIVELOG mode enabled
* Supplemental Logging enabled
* Oracle user with LogMiner privileges
* Access to Redo Logs and Archive Logs
* Network connectivity between Striim and Oracle ([Microsoft Learn][2])

---

# Step 1: Verify ARCHIVELOG Mode

Connect as SYSDBA:

```sql
SELECT LOG_MODE FROM V$DATABASE;
```

Expected:

```text
ARCHIVELOG
```

If not enabled:

```sql
SHUTDOWN IMMEDIATE;

STARTUP MOUNT;

ALTER DATABASE ARCHIVELOG;

ALTER DATABASE OPEN;
```

LogMiner requires archived redo logs to capture historical changes. ([Microsoft Learn][2])

---

# Step 2: Enable Supplemental Logging

## Verify Current Status

```sql
SELECT SUPPLEMENTAL_LOG_DATA_MIN
FROM V$DATABASE;
```

Expected:

```text
YES
```

or

```text
IMPLICIT
```

([Oracle Documentation][3])

---

## Enable Minimal Supplemental Logging

Required for LogMiner.

```sql
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
```

Oracle recommends enabling at least minimal supplemental logging for LogMiner-based CDC. ([Oracle Documentation][3])

---

## Enable Primary Key Logging

Recommended for CDC replication.

```sql
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA
(PRIMARY KEY) COLUMNS;
```

This helps identify rows during UPDATE and DELETE operations. ([Oracle Documentation][3])

---

## Table-Level Supplemental Logging (Optional)

For specific tables:

```sql
ALTER TABLE EMPLOYEE
ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
```

Useful when tables don't have reliable primary keys or when full before-images are required. ([Debezium][4])

---

# Step 3: Create Oracle CDC User

Example:

```sql
CREATE USER STRIIM_CDC
IDENTIFIED BY Password123;

GRANT CONNECT TO STRIIM_CDC;
GRANT RESOURCE TO STRIIM_CDC;
```

Additional LogMiner-related privileges are typically required:

```sql
GRANT SELECT ANY TRANSACTION TO STRIIM_CDC;
GRANT SELECT_CATALOG_ROLE TO STRIIM_CDC;
GRANT EXECUTE_CATALOG_ROLE TO STRIIM_CDC;
```

Production environments often grant additional access to LogMiner views and redo log metadata. ([Striim][5])

---

# Step 4: Verify Redo Logs

Check redo log groups:

```sql
SELECT
GROUP#,
STATUS,
MEMBERS
FROM V$LOG;
```

Check archived logs:

```sql
SELECT
NAME,
SEQUENCE#
FROM V$ARCHIVED_LOG;
```

Striim reads these logs through LogMiner. ([Striim][1])

---

# Step 5: Configure Oracle Reader in Striim

Flow Designer:

```text
Applications
   |
Create Application
   |
Add Source
   |
Oracle Reader
```

Configure:

```text
Host: oracle-server
Port: 1521
Service Name: ORCL
Username: STRIIM_CDC
Password: ********
```

---

# Step 6: Enable CDC

In Oracle Reader configuration:

```text
CDC Mode = LogMiner
```

Select tables:

```text
HR.EMPLOYEE
HR.DEPARTMENT
HR.ORDERS
```

---

# Step 7: Configure Target

Example:

```text
Oracle Reader
      |
      v
EmployeeStream
      |
      v
BigQuery Writer
```

or

```text
Oracle Reader
      |
      v
Kafka Writer
```

---

# Example TQL

### Source

```sql
CREATE SOURCE OracleSource
USING OracleReader (
    ConnectionURL:'jdbc:oracle:thin:@host:1521:ORCL',
    Username:'STRIIM_CDC',
    Password:'password'
)
OUTPUT TO EmployeeStream;
```

### Target

```sql
CREATE TARGET BigQueryTarget
USING BigQueryWriter (...)
INPUT FROM EmployeeStream;
```

---

# CDC Flow Example

Suppose Oracle executes:

```sql
INSERT INTO EMPLOYEE
VALUES (1001,'Jagadeesh');
```

Oracle writes:

```text
Redo Log
```

Then:

```text
Redo Log
   |
LogMiner
   |
Oracle Reader
   |
EmployeeStream
   |
BigQuery
```

The record reaches the target in near real time. ([Striim][1])

---
# Important Production Checks

### Check Supplemental Logging

```sql
SELECT SUPPLEMENTAL_LOG_DATA_MIN
FROM V$DATABASE;
```

### Check Archive Mode

```sql
SELECT LOG_MODE
FROM V$DATABASE;
```

### Check Active Redo Logs

```sql
SELECT *
FROM V$LOG;
```

### Check Archive Log Generation

```sql
ARCHIVE LOG LIST;
```

---
**How do you configure Oracle CDC in Striim using LogMiner?**

1. Enable ARCHIVELOG mode in Oracle.
2. Enable supplemental logging using:

```sql
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
```

3. Enable primary key or table-level supplemental logging if required.
4. Create a CDC user with LogMiner privileges.
5. Configure Oracle Reader in Striim.
6. Select CDC mode (LogMiner).
7. Choose source tables.
8. Configure a target such as BigQuery, Kafka, or Snowflake.
9. Deploy the application.

Striim then reads Oracle Redo Logs and Archive Logs through LogMiner and streams INSERT, UPDATE, and DELETE changes in real time with minimal impact on the source database. 
