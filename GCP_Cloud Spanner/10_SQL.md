# Topic 11: Cloud Spanner SQL (Complete End-to-End Professional Guide)

---

# Chapter 1: Introduction

## What is SQL?

SQL stands for **Structured Query Language**.

It is the standard language used to communicate with relational databases.

Using SQL, you can:

* Create databases
* Create tables
* Insert data
* Update data
* Delete data
* Read data
* Create indexes
* Modify schema
* Manage permissions (with supporting tools/features)
* Validate migration results

Think of SQL as the language you use to "talk" to the database.

---

# Why Do We Need SQL?

Imagine your application stores employee data.

The database contains:

```text
Employee Table

EmployeeID

Name

Department

Salary
```

Now you want:

* Find Employee 101
* Increase Rahul's salary
* Add a new employee
* Delete an employee

How do you tell the database what to do?

Using SQL.

---

# Real-Life Example

Imagine a bank.

The database contains:

```text
Accounts

Customers

Transactions

Loans
```

A customer checks their balance.

The banking application sends an SQL query to Cloud Spanner.

```text
Customer

↓

Bank Application

↓

SQL Query

↓

Cloud Spanner

↓

Result

↓

Customer
```

---

# SQL Categories

SQL is divided into different categories.

```text
SQL

│

├── DDL

├── DML

├── DQL

├── DCL

└── TCL
```

Let's understand each one.

---

# 1. DDL (Data Definition Language)

DDL defines the database structure.

Used for:

* CREATE
* ALTER
* DROP

Example:

Create a table.

```sql
CREATE TABLE Employee (
    EmployeeID INT64 NOT NULL,
    Name STRING(100),
    Department STRING(50)
)
PRIMARY KEY(EmployeeID);
```

---

# ALTER

Modify an existing table.

```sql
ALTER TABLE Employee
ADD COLUMN Email STRING(100);
```

Now Employee table has a new column.

---

# DROP

Delete an object.

```sql
DROP TABLE Employee;
```

Be careful.

All data will be removed.

---

# 2. DML (Data Manipulation Language)

DML works with data.

Commands:

* INSERT
* UPDATE
* DELETE

---

## INSERT

Add new data.

```sql
INSERT INTO Employee
(EmployeeID, Name, Department)

VALUES

(101, 'Rahul', 'IT');
```

---

## UPDATE

Modify existing data.

```sql
UPDATE Employee

SET Department='HR'

WHERE EmployeeID=101;
```

---

## DELETE

Remove data.

```sql
DELETE FROM Employee

WHERE EmployeeID=101;
```

---

# 3. DQL (Data Query Language)

Used to read data.

Main command:

SELECT

Example:

```sql
SELECT *

FROM Employee;
```

Returns all rows.

---

Select specific columns.

```sql
SELECT Name, Department

FROM Employee;
```

---

Find one employee.

```sql
SELECT *

FROM Employee

WHERE EmployeeID=101;
```

---

# 4. DCL (Data Control Language)

Controls permissions.

Examples:

* GRANT
* REVOKE

Conceptually:

```sql
GRANT SELECT
ON Employee
TO reporting_user;
```

> **Note:** In Cloud Spanner, access control is primarily managed using **Google Cloud IAM**, not traditional database users and roles like many on-premises databases. SQL permission commands are more limited than in Oracle or SQL Server.

---

# 5. TCL (Transaction Control Language)

Controls transactions.

Commands:

* BEGIN
* COMMIT
* ROLLBACK

Example:

Bank Transfer.

```text
Withdraw ₹500

↓

Deposit ₹500

↓

COMMIT
```

If anything fails,

```text
ROLLBACK
```

Everything returns to its previous state.

---

# SQL Execution Flow

```text
Application

↓

SQL Query

↓

Cloud Spanner

↓

Optimizer

↓

Execution

↓

Storage

↓

Result
```

---

# WHERE Clause

Filters data.

Example.

```sql
SELECT *

FROM Employee

WHERE Department='IT';
```

Returns only IT employees.

---

# ORDER BY

Sort data.

```sql
SELECT *

FROM Employee

ORDER BY Salary;
```

Ascending order.

Descending:

```sql
SELECT *

FROM Employee

ORDER BY Salary DESC;
```

---

# GROUP BY

Example.

```sql
SELECT Department,

COUNT(*)

FROM Employee

GROUP BY Department;
```

Returns the number of employees in each department.

---

# HAVING

Filters grouped results.

```sql
SELECT Department,

COUNT(*)

FROM Employee

GROUP BY Department

HAVING COUNT(*) > 10;
```

---

# Aggregate Functions

Common functions:

```sql
COUNT(*)

SUM(Salary)

AVG(Salary)

MAX(Salary)

MIN(Salary)
```

---

# JOIN

One of the most important SQL concepts.

Customer

| CustomerID | Name  |
| ---------- | ----- |
| 101        | Rahul |

Orders

| OrderID | CustomerID |
| ------- | ---------- |
| 1       | 101        |

Query:

```sql
SELECT *

FROM Customer

JOIN Orders

ON Customer.CustomerID = Orders.CustomerID;
```

This combines data from both tables.

---

# Index Usage

Query

```sql
SELECT *

FROM Employee

WHERE Email='rahul@gmail.com';
```

Without Email index:

```text
Scan entire table
```

With Email index:

```text
Go directly to matching row
```

---

# SQL in Oracle → Striim → Cloud Spanner Migration

Migration Flow

```text
Oracle

↓

Extract Data

↓

Striim

↓

Cloud Spanner

↓

Validation SQL
```

Example validation:

```sql
SELECT COUNT(*)

FROM Employee;
```

Compare Oracle and Cloud Spanner row counts.

---

# DevOps Engineer Responsibilities (Very Important)

Now let's focus on the DevOps perspective.

Many people think:

> "Developers write SQL. DevOps doesn't."

In real Cloud Spanner projects, DevOps engineers use SQL regularly.

---

# 1. Schema Deployment

Schema files are stored in Git.

Example:

```text
database/

V1__employee.sql

V2__customer.sql

V3__indexes.sql
```

CI/CD deploys them.

---

# 2. Data Validation

Before migration

```sql
SELECT COUNT(*)

FROM Employee;
```

After migration

```sql
SELECT COUNT(*)

FROM Employee;
```

If counts match,

Migration is likely successful.

---

# 3. Production Verification

Suppose deployment finished.

Verify:

```sql
SELECT *

FROM INFORMATION_SCHEMA.TABLES;
```

Check whether tables exist.

---

Check indexes.

```sql
SELECT *

FROM INFORMATION_SCHEMA.INDEXES;
```

---

# 4. Health Checks

Example.

```sql
SELECT COUNT(*)

FROM Orders;
```

Ensure data is present.

---

# 5. Troubleshooting

Scenario:

Application says:

> Customer not found.

Run:

```sql
SELECT *

FROM Customer

WHERE CustomerID=1001;
```

Determine whether:

* Data is missing.
* Application is querying the wrong key.
* Migration skipped the record.

---

# 6. CI/CD Pipeline

Typical flow:

```text
Developer

↓

Git Push

↓

Harness / Jenkins

↓

Validate SQL

↓

Deploy Schema

↓

Verify

↓

Notify Team
```

---

# 7. Monitoring

After deployment, monitor:

* Failed SQL executions
* Slow queries
* Query latency
* CPU utilization
* Storage usage
* Transaction failures

Tools:

* Google Cloud Monitoring
* Datadog
* Cloud Logging

---

# 8. Go-Live Checklist

Before production:

* Tables created.
* Indexes created.
* Row counts validated.
* Sample data checked.
* SQL validation completed.
* Permissions verified.
* Application connectivity tested.

---

# Common Mistakes

❌ Running `UPDATE` without a `WHERE` clause.

Example:

```sql
UPDATE Employee

SET Salary=50000;
```

This updates **every row**.

---

❌ Running `DELETE` without a `WHERE` clause.

```sql
DELETE FROM Employee;
```

Deletes **all data**.

---

❌ Using `SELECT *` unnecessarily in large production tables.

Select only the required columns when possible.

---

# Interview Questions & Answers

### Q1. What is SQL?

**Answer:**
SQL (Structured Query Language) is the standard language used to define database structures, insert, update, delete, and query relational data.

---

### Q2. What are the main categories of SQL?

**Answer:**

* DDL – Data Definition Language
* DML – Data Manipulation Language
* DQL – Data Query Language
* DCL – Data Control Language
* TCL – Transaction Control Language

---

### Q3. Why is SQL important for a DevOps Engineer?

**Answer:**
A DevOps engineer uses SQL to validate migrations, verify deployments, troubleshoot production issues, perform health checks, automate schema deployments, and support database releases.

---

### Q4. What is the difference between DDL and DML?

| DDL                        | DML                    |
| -------------------------- | ---------------------- |
| Defines database structure | Manipulates data       |
| CREATE, ALTER, DROP        | INSERT, UPDATE, DELETE |

---

### Q5. How do you validate an Oracle → Cloud Spanner migration?

**Answer:**

Typical validation includes:

* Comparing row counts (`COUNT(*)`)
* Checking sample records
* Validating schema objects
* Verifying indexes
* Running application queries
* Comparing results between Oracle and Cloud Spanner

---

# Real-Time Project Example

Suppose your team is migrating an **Employee Management System**.

Pipeline:

```text
Developer
     │
     ▼
Git Repository
     │
     ▼
Harness CI/CD
     │
     ▼
Deploy Schema (SQL)
     │
     ▼
Cloud Spanner
     │
     ▼
Striim Loads Data
     │
     ▼
DevOps Runs Validation SQL
     │
     ▼
Go-Live
```

As the DevOps engineer, you might execute SQL such as:

```sql
-- Validate table exists
SELECT TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES;

-- Validate row count
SELECT COUNT(*) FROM Employee;

-- Verify a migrated record
SELECT *
FROM Employee
WHERE EmployeeID = 101;
```

These simple validation queries are commonly used during production deployments and migration cutovers.

---

# Summary

```text
SQL
│
├── DDL (CREATE, ALTER, DROP)
├── DML (INSERT, UPDATE, DELETE)
├── DQL (SELECT)
├── DCL (Access control concepts, primarily handled by IAM in Cloud Spanner)
└── TCL (Transactions: BEGIN, COMMIT, ROLLBACK)
```

### Key Points to Remember

* SQL is the primary language for interacting with Cloud Spanner.
* Every DevOps engineer working on database migrations should know basic SQL.
* SQL is essential for **schema deployment**, **migration validation**, **production troubleshooting**, and **health checks**.
* In your Oracle → Striim → Cloud Spanner project, SQL will be one of your most frequently used tools during deployment and support.
