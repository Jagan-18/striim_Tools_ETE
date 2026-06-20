# TQL (Transformation Query Language) in Striim:
**TQL (Transformation Query Language)** is Striim's SQL-like language used to:

* Create Sources
* Create Streams
* Create Targets
* Filter data
* Transform data
* Join streams
* Aggregate data
* Build complete CDC pipelines

Think of TQL as **SQL for streaming data**.

---
# TQL Architecture:

```text
Oracle Reader
      |
      v
OrderStream
      |
      v
TQL (CQ)
      |
      v
FilteredStream
      |
      v
BigQuery Writer
```

---
# Basic TQL Components:

## 1. CREATE SOURCE

Reads data from source systems.

Example:

```sql
CREATE SOURCE OracleSource
USING OracleReader (
    ConnectionURL:'jdbc:oracle:thin:@localhost:1521:ORCL',
    Username:'admin',
    Password:'password'
)
OUTPUT TO OrderStream;
```

---
## 2. CREATE STREAM:

Defines a stream structure.

Example:

```sql
CREATE STREAM EmployeeStream (
    EMP_ID Integer,
    EMP_NAME String,
    SALARY Double
);
```

---
## 3. CREATE CQ (Continuous Query):

Used for transformations.

```sql
CREATE CQ EmployeeCQ
INSERT INTO NewStream
SELECT *
FROM EmployeeStream;
```

---
# Common TQL Transformations:

## 1. Filtering Data

Only employees with salary greater than 50,000.

```sql
CREATE CQ HighSalaryEmployees
INSERT INTO HighSalaryStream
SELECT *
FROM EmployeeStream
WHERE SALARY > 50000;
```

Input:

```text
EMP_ID | NAME  | SALARY
101    | John  | 30000
102    | David | 60000
```

Output:

```text
102 | David | 60000
```

---
## 2. Selecting Specific Columns:

```sql
CREATE CQ EmployeeNames
INSERT INTO NameStream
SELECT EMP_ID, EMP_NAME
FROM EmployeeStream;
```

---
## 3. Rename Columns:

```sql
CREATE CQ EmployeeRename
INSERT INTO NewStream
SELECT
    EMP_ID,
    EMP_NAME AS EMPLOYEE_NAME
FROM EmployeeStream;
```

---
## 4. Data Transformation:

Increase salary by 10%.

```sql
CREATE CQ SalaryIncrement
INSERT INTO SalaryStream
SELECT
    EMP_ID,
    EMP_NAME,
    SALARY * 1.10 AS NEW_SALARY
FROM EmployeeStream;
```

---
## 5. String Functions:

Convert names to uppercase.

```sql
CREATE CQ EmployeeUpper
INSERT INTO UpperStream
SELECT
    UPPER(EMP_NAME) AS NAME
FROM EmployeeStream;
```

Result:

```text
john
```

becomes

```text
JOHN
```

---
# Aggregation:

## Count Records

```sql
CREATE CQ EmployeeCount
INSERT INTO CountStream
SELECT COUNT(*) AS TOTAL
FROM EmployeeStream;
```

---
## Group By:

```sql
CREATE CQ DepartmentCount
INSERT INTO DeptStream
SELECT
    DEPARTMENT,
    COUNT(*) AS EMP_COUNT
FROM EmployeeStream
GROUP BY DEPARTMENT;
```

Output:

```text
IT      50
HR      20
Finance 15
```

---
# Stream Joins:

Join Employee and Department streams.

```sql
CREATE CQ EmployeeDeptJoin
INSERT INTO JoinedStream
SELECT
    E.EMP_ID,
    E.EMP_NAME,
    D.DEPT_NAME
FROM EmployeeStream E
JOIN DepartmentStream D
ON E.DEPT_ID = D.DEPT_ID;
```

---

# CDC Event Filtering

Capture only INSERT operations.

```sql
CREATE CQ InsertOnly
INSERT INTO InsertStream
SELECT *
FROM CDCStream
WHERE METADATA(OperationName)='INSERT';
```

---

Capture only UPDATE operations.

```sql
CREATE CQ UpdateOnly
INSERT INTO UpdateStream
SELECT *
FROM CDCStream
WHERE METADATA(OperationName)='UPDATE';
```

---

Capture only DELETE operations.

```sql
CREATE CQ DeleteOnly
INSERT INTO DeleteStream
SELECT *
FROM CDCStream
WHERE METADATA(OperationName)='DELETE';
```

---
# Date Functions:

Current timestamp:

```sql
CREATE CQ AuditStream
INSERT INTO AuditOutput
SELECT
    EMP_ID,
    CURRENT_TIMESTAMP AS LOAD_TIME
FROM EmployeeStream;
```

---
# Creating a Target

Example BigQuery Target:

```sql
CREATE TARGET BigQueryTarget
USING BigQueryWriter (
    ProjectID:'my-project',
    Dataset:'employee'
)
INPUT FROM EmployeeStream;
```

---
# Complete End-to-End TQL Pipeline:

### Oracle → Filter → BigQuery

```sql
CREATE SOURCE OracleSource
USING OracleReader (
    ConnectionURL:'jdbc:oracle:thin:@server:1521:ORCL',
    Username:'admin',
    Password:'password'
)
OUTPUT TO EmployeeStream;

CREATE CQ HighSalaryEmployees
INSERT INTO FilteredStream
SELECT *
FROM EmployeeStream
WHERE SALARY > 50000;

CREATE TARGET BigQueryTarget
USING BigQueryWriter (
    ProjectID:'gcp-project',
    Dataset:'employee'
)
INPUT FROM FilteredStream;
```

Flow:

```text
Oracle
   |
OracleReader
   |
EmployeeStream
   |
HighSalaryEmployees CQ
   |
FilteredStream
   |
BigQueryWriter
   |
BigQuery
```

---
# Common TQL Keywords:
| Keyword           | Purpose               |
| ----------------- | --------------------- |
| CREATE SOURCE     | Define source         |
| CREATE STREAM     | Create stream         |
| CREATE CQ         | Create transformation |
| CREATE TARGET     | Define target         |
| SELECT            | Retrieve fields       |
| WHERE             | Filter data           |
| GROUP BY          | Aggregate             |
| JOIN              | Join streams          |
| COUNT             | Count records         |
| CURRENT_TIMESTAMP | Current time          |

---
### What is TQL?

TQL (Transformation Query Language) is Striim's SQL-like language used to create data pipelines, perform stream transformations, filtering, aggregations, joins, and route data between sources and targets.

### What is a CQ in TQL?

A Continuous Query (CQ) is a continuously running transformation that processes streaming data in real time.

### How do you filter CDC events?

```sql
WHERE METADATA(OperationName)='INSERT'
```

or

```sql
WHERE METADATA(OperationName)='UPDATE'
```

---
# Quick Revision

```text
TQL Components
--------------
CREATE SOURCE
CREATE STREAM
CREATE CQ
CREATE TARGET

Common Operations
-----------------
SELECT
WHERE
GROUP BY
JOIN
COUNT
UPPER
CURRENT_TIMESTAMP

CDC Filtering
-------------
INSERT
UPDATE
DELETE
```

For interviews, remember:

**Source → Stream → CQ → Target**

and **CQ (Continuous Query)** is where most TQL transformations and filtering logic are implemented.
---

