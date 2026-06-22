# Striim UI / Flow Designer:
**Striim Flow Designer** is a **web-based graphical interface** used to create, configure, deploy, and monitor real-time data pipelines without writing much TQL code.

It allows you to build CDC (Change Data Capture) pipelines visually by connecting:

* Sources
* Streams
* Transformations
* Targets

---
## Accessing Striim UI;

Open:

```text
http://<server-ip>:9080
```

Login with your Striim credentials.

---
## Main Components of Striim UI:

```text
+--------------------------------------+
| Striim Dashboard                     |
+--------------------------------------+
| Applications                         |
| Flow Designer                        |
| Sources                              |
| Targets                              |
| Monitoring                           |
| Cluster Management                   |
| Security                             |
+--------------------------------------+
```

---
## Flow Designer Architecture:

```text
Oracle DB
    |
    v
Oracle Reader
    |
    v
Stream
    |
    v
Transformation
    |
    v
BigQuery Target
```

In Flow Designer, these appear as drag-and-drop components.

---
## Creating a Pipeline:

### Step 1: Create Application:
1. Login to Striim UI
2. Click **Applications**
3. Select **Create Application**
4. Enter application name

Example:

```text
Oracle_To_BigQuery
```

---
### Step 2: Add Source:

Choose source type:

```text
Oracle
MySQL
SQL Server
PostgreSQL
Kafka
Files
```

Example:

```text
Source: Oracle
Host: oracle-server
Port: 1521
Database: PRODDB
```

---
### Step 3: Configure CDC:

```text
Enable CDC = Yes
Start Position = Latest
Tables = EMPLOYEE, ORDERS
```

Striim reads transaction logs continuously.

---
### Step 4: Create Stream:

```text
OracleReader
      |
      v
EmployeeStream
```

The stream carries events through the pipeline.

---
### Step 5: Add Transformation (Optional):

Examples:

### Filter

```sql
Salary > 50000
```

### Rename Columns

```sql
EMP_NAME -> EMPLOYEE_NAME
```

### Aggregate

```sql
COUNT(*)
GROUP BY DEPARTMENT
```

---

### Step 6: Add Target

Supported targets:

```text
BigQuery
Snowflake
Kafka
Cloud Storage
S3
PostgreSQL
```

Example:

```text
Target: BigQuery
Dataset: EmployeeData
Table: Employee
```

---

### Step 7: Validate

Click:

```text
Validate
```

Checks:

* Connectivity
* Schema
* Permissions
* Configuration

---

### Step 8: Deploy

Click:

```text
Deploy
```

Flow starts running immediately.

---

## Monitoring Pipelines

### Dashboard

```text
Application Status
Source Status
Target Status
Latency
Throughput
Errors
```

Example:

```text
Status: Running
Events/sec: 5000
Latency: 150ms
```

---

## Monitoring Cluster

```text
Cluster
 ├── Node-1
 ├── Node-2
 └── Node-3
```

UI displays:

* Node health
* CPU usage
* Memory usage
* Active applications

---

## Error Handling

UI shows:

```text
Application Failed
Connection Lost
Authentication Error
Target Unavailable
```

Logs can be viewed directly from the dashboard.

---

## Flow Designer Example

### Oracle → BigQuery

```text
+------------+
| Oracle DB  |
+------------+
       |
       v
+------------+
| CDC Reader |
+------------+
       |
       v
+------------+
| Stream     |
+------------+
       |
       v
+------------+
| BigQuery   |
+------------+
```

No coding required except advanced transformations.

---

## Flow Designer vs TQL

| Flow Designer          | TQL                     |
| ---------------------- | ----------------------- |
| GUI Based              | Code Based              |
| Easy to Learn          | More Flexible           |
| Drag & Drop            | SQL-like Scripts        |
| Faster Setup           | Advanced Customization  |
| Suitable for Beginners | Suitable for Developers |

---
## Typical DevOps Tasks in Striim UI;

### Application Management

* Start application
* Stop application
* Restart application

### Monitoring

* Check pipeline health
* Check CDC lag
* Monitor throughput

### User Management

* Create users
* Assign roles
* Configure permissions

### Cluster Management
* Add nodes
* Remove nodes
* Check cluster status

---
## Interview Answer:

**What is Striim Flow Designer?**

Striim Flow Designer is a web-based graphical interface that allows users to build, deploy, and monitor real-time data pipelines using a drag-and-drop approach. It enables users to connect sources, configure CDC, apply transformations, and load data into targets such as BigQuery, Snowflake, Kafka, and databases without extensive TQL coding. It also provides monitoring, application management, and cluster administration capabilities.
---
# Navigating the Striim Interface & Building Pipelines Using Flow Designer

This is one of the most common topics for Striim beginners and interviews.

---
# 1. Login to Striim UI:

Open:

```text
http://<Striim-Server-IP>:9080
```

Enter:

* Username
* Password

After login, you'll see the Striim Dashboard.

---
# 2. Striim UI Navigation:

```text
+-----------------------------------+
| Dashboard                         |
+-----------------------------------+
| Applications                      |
| Flow Designer                     |
| Monitoring                        |
| Sources                           |
| Targets                           |
| Cluster                           |
| Security                          |
| Logs                              |
+-----------------------------------+
```

## Dashboard:
Displays:

* Running applications
* Active pipelines
* Cluster health
* Event throughput
* Error notifications

---
## Applications:

Used to:

* Create applications
* Start applications
* Stop applications
* Delete applications

Example:

```text
Applications
 ├─ Oracle_To_BigQuery
 ├─ MySQL_To_Kafka
 └─ SQLServer_To_Snowflake
```

---
## Flow Designer:

This is where you build pipelines visually.

```text
Source → Stream → Transform → Target
```

No coding required for basic pipelines.

---
## Monitoring:

Monitor:

* CDC lag
* Events/sec
* Memory
* CPU
* Errors

---
## Cluster:

Shows:

```text
Cluster
 ├─ Node-1
 ├─ Node-2
 └─ Node-3
```

Useful in production environments.

---
# 3. Creating a Pipeline Using Drag-and-Drop

Example:

```text
Oracle → BigQuery
```

---
## Step 1: Create Application:

Navigate:

```text
Applications
    ↓
Create Application
```

Example:

```text
Application Name:
Oracle_BigQuery_CDC
```

Click **Create**.

---
## Step 2: Open Flow Designer:

Select:

```text
Oracle_BigQuery_CDC
```

Click:

```text
Open Flow Designer
```

Canvas appears.

---
## Step 3: Add Source:

Drag **Source** component.

```text
Palette
 ├─ Source
 ├─ Stream
 ├─ CQ
 └─ Target
```

Select:

```text
Oracle Reader
```

Configure:

```text
Host: oracle-server
Port: 1521
Database: PROD
Username: admin
Password: ****
```

Enable CDC:

```text
CDC = Enabled
```

---
## Step 4: Select Tables:

Example:

```text
EMPLOYEE
ORDERS
CUSTOMERS
```

Striim automatically discovers schemas.

---
## Step 5: Create StreamL:

Connect:

```text
Oracle Reader
       |
       v
EmployeeStream
```

The stream carries events in real time.

---
## Step 6: Add Transformation (Optional)

Drag **Transformation** or **CQ**.

### Example 1: Filter Records

```text
Salary > 50000
```

Flow:

```text
Oracle Reader
       |
EmployeeStream
       |
Filter
       |
FilteredStream
```

---
### Example 2: Rename Columns:

```text
EMP_NAME
      ↓
EMPLOYEE_NAME
```

---
### Example 3: Data Enrichment:

Join with another stream:

```text
EmployeeStream
      +
DepartmentStream
```

---
## Step 7: Add Target:

Drag Target component.

Examples:

```text
BigQuery
Kafka
Snowflake
Cloud Storage
PostgreSQL
```

Choose:

```text
BigQuery
```

Configure:

```text
Project ID
Dataset
Table Name
```

---
## Step 8: Connect Components:

Final flow:

```text
Oracle Reader
       |
EmployeeStream
       |
Transformation
       |
BigQuery Writer
```

Visual Designer:

```text
+---------+    +---------+    +---------+
| Oracle  | -> | Filter  | -> |BigQuery |
+---------+    +---------+    +---------+
```

---
## Step 9: Validate Pipeline:

Click:

```text
Validate
```

Checks:

* Database connectivity
* Credentials
* Table existence
* Target permissions

---
## Step 10: Deploy Pipeline:

Click:

```text
Deploy
```

Pipeline starts.

---
# 4. Monitoring Pipeline:

Navigate:

```text
Monitoring
```

Example:

```text
Application: Oracle_BigQuery_CDC

Status: Running
Events/sec: 3500
Latency: 120ms
Errors: 0
```

---
# 5. Checking CDC Lag:

Very important in production.

Example:

```text
Source Changes: 100000

Processed: 99500

Lag: 500 events
```

Lower lag is better.

---
# 6. Managing Applications:

### Start

```text
Application
   ↓
Start
```

### Stop

```text
Application
   ↓
Stop
```

### Restart

```text
Application
   ↓
Restart
```

---
# Example End-to-End GCP Pipeline:

```text
Cloud SQL (MySQL)
        |
        v
   MySQL Reader
        |
        v
     Stream
        |
        v
   Transformation
        |
        v
BigQuery Writer
```

Flow Designer Steps:

```text
Source:
 Cloud SQL

Stream:
 CustomerStream

Transformation:
 Filter Active Customers

Target:
 BigQuery
```

---
