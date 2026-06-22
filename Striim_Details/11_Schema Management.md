# Schema Management in Striim:

## What is Schema Management?

**Schema Management** is the process of handling the structure of data (tables, columns, data types) between source and target systems.

It ensures that data captured from the source is correctly mapped and written to the target.

Example:

### Source Table:
```text
EMPLOYEE
--------
EMP_ID      NUMBER
EMP_NAME    VARCHAR2
SALARY      NUMBER
```

### Target Table:
```text
EMPLOYEE
--------
EMP_ID      INTEGER
EMP_NAME    STRING
SALARY      FLOAT
```

Striim maps source schema to target schema during data processing.

---
# Why Schema Management is Important:
Without schema management:

```text
Source Table Changed
        |
        v
Pipeline Failure
```

With proper schema management:

```text
Source Change
      |
Schema Mapping
      |
Target Updated
```

**Benefits:**
✅ Prevents pipeline failures

✅ Handles schema evolution

✅ Supports data type conversions

✅ Maintains data consistency

---
# Schema Flow in Striim:
```text
Oracle Table
      |
OracleReader
      |
Stream Schema
      |
CQ Transformation
      |
Target Schema
      |
BigQuery/Snowflake
```

---
# Schema Discovery:
Striim automatically discovers source table schemas.

Example:

```text
EMPLOYEE
--------
EMP_ID
EMP_NAME
SALARY
DEPARTMENT
```

OracleReader reads metadata and creates corresponding stream structures.

---
# Stream Schema:
Example:

```sql
CREATE STREAM EmployeeStream (
   EMP_ID Integer,
   EMP_NAME String,
   SALARY Double,
   DEPARTMENT String
);
```

The stream schema acts as the data contract inside the pipeline.

---
# Data Type Mapping:
### Oracle → BigQuery

| Oracle    | BigQuery      |
| --------- | ------------- |
| NUMBER    | INTEGER/FLOAT |
| VARCHAR2  | STRING        |
| DATE      | DATE          |
| TIMESTAMP | TIMESTAMP     |
| CLOB      | STRING        |

---
### Oracle → Kafka:
```text
Oracle
   |
Striim
   |
JSON
   |
Kafka
```

Data is often serialized as JSON.

---
# Schema Mapping:
Example:

### Source

```text
EMP_ID
EMP_NAME
```

### Target
```text
ID
NAME
```

Using TQL:

```sql
CREATE CQ EmployeeMapping
INSERT INTO EmployeeTargetStream
SELECT
    EMP_ID AS ID,
    EMP_NAME AS NAME
FROM EmployeeStream;
```

---
# Adding New Columns (Schema Evolution):

### Original Table

```text
EMP_ID
EMP_NAME
SALARY
```

Later:

```text
EMP_ID
EMP_NAME
SALARY
EMAIL
```

This is called **Schema Evolution**.

---
# Handling Schema Evolution:
Options:

### Option 1: Auto Schema Evolution

Striim detects:

```text
New Column Added
```

and updates schema automatically (depending on source/target support).

---
### Option 2: Manual Update:
Update stream definition:

```sql
CREATE STREAM EmployeeStream (
   EMP_ID Integer,
   EMP_NAME String,
   SALARY Double,
   EMAIL String
);
```

---
# Dropping Columns:
Example:

Old:

```text
EMP_ID
EMP_NAME
EMAIL
```

New:

```text
EMP_ID
EMP_NAME
```

Update mappings and targets accordingly.

---
# Data Type Conversion:
Example:

Convert String to Integer.

```sql
CREATE CQ ConvertData
INSERT INTO NewStream
SELECT
   TO_INT(EMP_ID) AS EMP_ID
FROM EmployeeStream;
```

---
# Null Handling:
Example:

```sql
CREATE CQ NullHandling
INSERT INTO NewStream
SELECT
   NVL(EMP_NAME,'UNKNOWN') AS EMP_NAME
FROM EmployeeStream;
```

Output:

```text
NULL
```

becomes:

```text
UNKNOWN
```

---
# Schema Validation:
Before deployment:

Validate:

```text
Source Columns

Target Columns

Data Types

Mappings
```

Example:

```text
EMP_ID Integer
      |
      v
ID Integer
```

Valid

---
# Common Schema Issues:
## Column Missing

Error:

```text
EMAIL column not found
```

Cause:

```text
Target expects EMAIL
Source does not provide it
```

---
## Data Type Mismatch:
Error:

```text
STRING cannot be converted to INTEGER
```

Example:

```text
ABC123
```

into:

```text
INTEGER
```

---
## New Column Added:
Source:

```text
EMAIL
```

Target:

```text
EMAIL not present
```

May require schema update.

---
# Schema Management with Kafka:
### Without Schema Registry

```text
Kafka Topic
     |
JSON Payload
```

Risk:

* Producers and consumers may use different schemas.

---
### With Schema Registry:
```text
Producer
    |
Schema Registry
    |
Kafka
    |
Consumer
```

Benefits:

* Schema validation
* Versioning
* Compatibility checks

---
# GCP Example:
```text
Cloud SQL
    |
MySQLReader
    |
CustomerStream
    |
BigQueryWriter
    |
BigQuery Table
```

When a new column is added:

```text
CUSTOMER_EMAIL
```

Verify:

* Stream schema
* BigQuery schema
* Mapping logic

---
# Best Practices:
### Use Explicit Mappings

Good:

```sql
SELECT
EMP_ID AS ID,
EMP_NAME AS NAME
```

Avoid:

```sql
SELECT *
```

in production pipelines.

---
### Validate Schema Changes:
Before deployment:

✔ New columns

✔ Dropped columns

✔ Data types

✔ Target compatibility

---
### Version-Control Schema Changes:
Store:

```text
TQL Scripts
Schema Definitions
Mapping Files
```

in Git.

---
### Test Schema Evolution:
Always test:

```text
Add Column

Drop Column

Change Data Type
```

in lower environments first.

---
### What is Schema Management?
Schema Management is the process of managing source and target data structures, mappings, data types, and schema changes to ensure successful data movement through Striim pipelines.

---
### What is Schema Evolution?
Schema Evolution refers to changes in the source schema, such as adding, removing, or modifying columns, while maintaining pipeline functionality.

---
### How does Striim handle schema changes?
Striim can detect schema changes, support schema evolution, and allow manual updates to stream definitions, mappings, and target schemas depending on the source and target systems.


>Schema Management in Striim involves discovering source schemas, mapping them to stream and target schemas, handling data type conversions, validating compatibility, and managing schema evolution such as adding or removing columns. Proper schema management ensures reliable CDC processing and prevents pipeline failures caused by schema mismatches.

---
# Handling Schema Changes, Schema Evolution & DDL Propagation in Striim:
In real-world CDC pipelines, source databases frequently change:

* New columns are added
* Columns are dropped
* Data types are modified
* Tables are renamed
* New tables are created

These changes are called **Schema Changes** or **DDL (Data Definition Language) Changes**.

---
# What is DDL?
DDL commands modify database structure.

Examples:

```sql
CREATE TABLE EMPLOYEE (
    EMP_ID NUMBER,
    EMP_NAME VARCHAR2(100)
);
```

```sql
ALTER TABLE EMPLOYEE
ADD EMAIL VARCHAR2(100);
```

```sql
ALTER TABLE EMPLOYEE
DROP COLUMN EMAIL;
```

```sql
ALTER TABLE EMPLOYEE
MODIFY SALARY NUMBER(15,2);
```

```sql
DROP TABLE EMPLOYEE;
```

---
# What is Schema Evolution?

Schema Evolution means handling database structure changes without breaking the pipeline.

Example:

### Before:
```text
EMPLOYEE
---------
EMP_ID
EMP_NAME
SALARY
```

### After:
```text
EMPLOYEE
---------
EMP_ID
EMP_NAME
SALARY
EMAIL
```

The pipeline should continue working even after the new column is added.

---
# Architecture:
```text
Oracle Database
       |
   DDL Change
       |
OracleReader
       |
Schema Evolution
       |
Stream
       |
BigQuery/Snowflake
```

---
# Common Schema Changes:
## 1. Add Column

Example:

```sql
ALTER TABLE EMPLOYEE
ADD EMAIL VARCHAR2(100);
```

### Before:
```text
EMP_ID
EMP_NAME
```

### After:
```text
EMP_ID
EMP_NAME
EMAIL
```

---
## 2. Drop Column:
```sql
ALTER TABLE EMPLOYEE
DROP COLUMN EMAIL;
```

---
## 3. Rename Column:
```sql
ALTER TABLE EMPLOYEE
RENAME COLUMN EMP_NAME TO EMPLOYEE_NAME;
```

---
## 4. Change Data Type:
```sql
ALTER TABLE EMPLOYEE
MODIFY SALARY NUMBER(15,2);
```

---
## 5. Create New Table:
```sql
CREATE TABLE CUSTOMER (...);
```

---
# DDL Propagation:
DDL Propagation means:

```text
Source DDL
      |
      v
Striim Detects
      |
      v
Target Updated
```

Example:

```text
Oracle
   |
ALTER TABLE ADD EMAIL
   |
Striim
   |
BigQuery Table Updated
```

---
# Without DDL Propagation:
```text
Oracle
   |
EMAIL Added
   |
Striim
   |
BigQuery
```

Result:

```text
Pipeline Error
Column Mismatch
```

---
# With DDL Propagation:
```text
Oracle
   |
EMAIL Added
   |
Striim
   |
BigQuery Schema Updated
```

Pipeline continues successfully.

---
# Oracle CDC Example:
### Source

```sql
ALTER TABLE CUSTOMER
ADD PHONE VARCHAR2(20);
```

Oracle Redo Logs contain:

```text
DDL Event
```

OracleReader detects:

```text
ALTER TABLE
```

Striim updates metadata and propagates the change.

---
# Handling Add Column:
### Source

```text
EMP_ID
EMP_NAME
EMAIL
```

### CQ
Instead of:

```sql
SELECT *
```

Use:

```sql
SELECT
EMP_ID,
EMP_NAME,
EMAIL
```

Update the CQ when new columns are required.

---
# Handling Drop Column:
Before:

```sql
SELECT
EMP_ID,
EMP_NAME,
EMAIL
FROM EmployeeStream;
```

After EMAIL is dropped:

```sql
SELECT
EMP_ID,
EMP_NAME
FROM EmployeeStream;
```

Otherwise the CQ may fail.

---
# Handling Data Type Changes:
Example:

### Before
```text
SALARY NUMBER(10)
```

### After
```text
SALARY NUMBER(15,2)
```

Verify:

```text
Source Type
Stream Type
Target Type
```

Update mappings if necessary.

---
# BigQuery Schema Evolution:
Example:

### Existing Table

```text
EMP_ID
EMP_NAME
```

Source adds:

```text
EMAIL
```

BigQuery supports:

```text
Add Column
```

without recreating the table.

---
# Kafka Schema Evolution:
### Without Schema Registry

```text
Producer
    |
Kafka
    |
Consumer
```

Risk:

```text
Different Schemas
```

---
### With Schema Registry:
```text
Producer
     |
Schema Registry
     |
Kafka
     |
Consumer
```

Benefits:

* Version control
* Compatibility validation
* Controlled evolution

---
# Best Practices for Schema Evolution:
## 1. Avoid SELECT *

Bad:

```sql
SELECT *
FROM EmployeeStream;
```

Good:

```sql
SELECT
EMP_ID,
EMP_NAME,
SALARY
FROM EmployeeStream;
```

This gives better control during schema changes.

---
## 2. Version-Control TQL:
Store:

```text
source.tql
cq.tql
target.tql
```

in GitHub/GitLab.

---
## 3. Test Schema Changes:
Always validate:

```text
Add Column
Drop Column
Rename Column
Change Type
```

in Dev or QA before Production.

---
## 4. Monitor DDL Events:
Monitor:

```text
ALTER TABLE
CREATE TABLE
DROP TABLE
```

through Striim logs and dashboards.

---
## 5. Coordinate with DB Teams:
Before production deployment:

```text
DBA Team
     |
Schema Change Notification
     |
Striim Team
```

---
# Common Issues:
## Column Not Found

Error:

```text
EMAIL column not found
```

Cause:

```text
Column dropped from source
```

---
## Type Mismatch:
Error:

```text
Cannot convert STRING to INTEGER
```

Cause:

```text
Source datatype changed
```

---
## Target Schema Not Updated:
Error:

```text
Target table missing column EMAIL
```

Cause:

```text
DDL propagated from source
but target schema not updated
```

---
### What is Schema Evolution?

Schema Evolution is the ability to handle changes in database structure, such as adding, removing, or modifying columns, without disrupting the data pipeline.

---
### What is DDL Propagation?
DDL Propagation is the automatic detection and propagation of source database DDL changes (CREATE, ALTER, DROP) to downstream systems through the CDC pipeline.

---
### What are common schema changes?
* Add Column
* Drop Column
* Rename Column
* Change Data Type
* Create/Drop Table

---
### How do you handle schema changes in Striim?
1. Detect DDL events through CDC.
2. Validate stream and target compatibility.
3. Update CQ mappings if needed.
4. Propagate schema changes to targets.
5. Test changes before production deployment.
6. Monitor pipeline health after deployment.

---
> Schema evolution in Striim refers to handling structural changes in source databases while keeping CDC pipelines operational. DDL propagation allows Striim to detect CREATE, ALTER, and DROP operations and update downstream schemas when supported. Best practices include explicit column mappings, version control of TQL scripts, testing schema changes in lower environments, and monitoring DDL events to prevent pipeline failures.
