# Topic 5: Cloud Spanner Databases (Complete End-to-End Guide)

---

# 1. Introduction

## What is a Database?

A **database** is a structured collection of related data stored electronically.

In Cloud Spanner, a database is created **inside a Cloud Spanner Instance**. It contains:

* Tables
* Columns
* Rows
* Primary Keys
* Indexes
* Views (where supported)
* Data

Simply put:

> **A database is where your application's data is stored and managed.**

---

# 2. Why Do We Need a Database?

Imagine you're developing an online shopping application.

The application needs to store:

* Customers
* Products
* Orders
* Payments
* Shipping Details

Where will all this information be stored?

Answer: **In a database.**

Without a database:

* ❌ No customer information
* ❌ No order history
* ❌ No product catalog
* ❌ No payment records

---

# 3. Real-Time Example

Suppose you're working for an e-commerce company.

The application has:

```text
Amazon Application
       │
       ▼
Cloud Spanner Database
```

Inside the database:

```text
Database
│
├── Customers
├── Orders
├── Products
├── Payments
└── Delivery
```

Each table stores a different type of information.

---

# 4. Database Hierarchy

Let's understand where a database fits.

```text
Google Cloud Project
        │
        ▼
Cloud Spanner Instance
        │
        ▼
Database
        │
        ▼
Schema
        │
        ▼
Tables
        │
        ▼
Rows
```

Remember:

* One **Project** can have multiple Instances.
* One **Instance** can have multiple Databases.
* One **Database** can have multiple Tables.

---

# 5. Real-Time Migration Example

Your project:

```text
Oracle
   │
   ▼
Striim
   │
   ▼
Cloud Spanner
```

Suppose Oracle contains:

```text
Oracle
│
├── HR_DB
├── SALES_DB
└── FINANCE_DB
```

After migration:

```text
Cloud Spanner Instance
│
├── HR_DB
├── SALES_DB
└── FINANCE_DB
```

Striim reads changes from Oracle and writes them into the corresponding Cloud Spanner databases.

---

# 6. What Does a Database Contain?

A Cloud Spanner database contains several objects.

```text
Database
│
├── Tables
├── Indexes
├── Primary Keys
├── Foreign Keys
├── Data
├── Constraints
└── Schema
```

Each object has a specific purpose.

---

# 7. Database Components

## Tables

A table stores related data.

Example:

Employee Table

| EmployeeID | Name  | Department |
| ---------- | ----- | ---------- |
| 101        | Rahul | HR         |
| 102        | Priya | IT         |

---

## Rows

Each record is called a row.

Example:

| EmployeeID | Name  | Department |
| ---------- | ----- | ---------- |
| 101        | Rahul | HR         |

This single record is one row.

---

## Columns

Columns describe each attribute.

Example:

| EmployeeID | Name | Department |

Columns are:

* EmployeeID
* Name
* Department

---

## Primary Key

Every Cloud Spanner table must have a primary key.

Example:

```sql
CREATE TABLE Employee (
EmployeeID INT64 NOT NULL,
Name STRING(100)
) PRIMARY KEY(EmployeeID);
```

The primary key uniquely identifies every row.

---

## Index

Indexes make queries faster.

Example:

Instead of scanning millions of rows:

```text
Without Index

1
2
3
4
5
6
7
8
...
1000000
```

With an index:

```text
Jump directly to EmployeeID = 1001
```

---

# 8. Database Creation Flow

Typical flow:

```text
Create Project
      │
      ▼
Create Instance
      │
      ▼
Create Database
      │
      ▼
Create Tables
      │
      ▼
Insert Data
```

---

# 9. Database Lifecycle

A database goes through several stages.

```text
Create

↓

Configure

↓

Create Tables

↓

Insert Data

↓

Run Application

↓

Backup

↓

Scale

↓

Maintain

↓

Delete (if no longer needed)
```

---

# 10. Database Naming Best Practices

Good names:

```text
customer-db

finance-db

sales-db

hr-db
```

Avoid:

```text
database1

test

abc

newdb
```

Choose names that clearly describe their purpose.

---

# 11. Database Security

Security is very important.

Typical controls include:

* IAM permissions
* Service Accounts
* Encryption at rest
* Encryption in transit
* Audit logging

As a DevOps Engineer, you may help ensure applications have the correct permissions to access the database.

---

# 12. Database Backup

Regular backups protect against accidental data loss.

Example:

```text
Database

↓

Backup

↓

Cloud Storage (managed by Cloud Spanner)
```

If data is accidentally deleted, it can be restored from a backup.

---

# 13. Database Restore

Restore creates a new database from an existing backup.

Example:

```text
Production Database

↓

Backup

↓

Restore

↓

Recovered Database
```

---

# 14. Database Scaling

As more users access the application:

```text
100 Users

↓

1,000 Users

↓

10,000 Users

↓

100,000 Users
```

The database itself remains the same, but the **instance's compute capacity (nodes or processing units)** can be increased to handle the additional workload.

---

# 15. DevOps Engineer Responsibilities

In a Cloud Spanner project, a DevOps Engineer may:

* Create databases
* Manage IAM permissions
* Automate infrastructure using Terraform
* Support migration from Oracle
* Monitor performance and latency
* Configure backups
* Integrate monitoring tools like Datadog
* Help troubleshoot production issues
* Coordinate with application and database teams during deployments

---

# 16. Common Mistakes

❌ Creating tables without planning the schema.

❌ Choosing poor primary keys that cause performance issues.

❌ Not planning backup and recovery.

❌ Giving excessive IAM permissions.

❌ Ignoring monitoring and capacity planning.

---

# 17. Troubleshooting Examples

### Scenario 1: Application Cannot Connect

Possible causes:

* Incorrect database name
* Wrong instance name
* Missing IAM permissions
* Invalid service account

---

### Scenario 2: Slow Queries

Possible causes:

* Missing indexes
* Inefficient SQL
* Poor schema design

---

### Scenario 3: Data Not Appearing After Migration

In your project:

```text
Oracle

↓

Striim

↓

Cloud Spanner
```

Possible causes:

* Striim pipeline failure
* CDC interruption
* Target mapping issues
* Network connectivity problems

---

# 18. Interview Questions & Answers

### Q1. What is a Cloud Spanner database?

**Answer:**
A Cloud Spanner database is a logical container inside a Cloud Spanner instance that stores application data, schemas, tables, indexes, and other database objects.

---

### Q2. Can an instance contain multiple databases?

**Answer:**
Yes. A single Cloud Spanner instance can host multiple databases that share the instance's compute resources.

---

### Q3. What objects are stored inside a Cloud Spanner database?

**Answer:**
A database stores:

* Tables
* Rows
* Columns
* Primary keys
* Indexes
* Constraints
* Data

---

### Q4. Can we create a database without an instance?

**Answer:**
No. Every Cloud Spanner database must be created inside an existing Cloud Spanner instance.

---

### Q5. What is the difference between an instance and a database?

**Answer:**

| Instance                                         | Database                         |
| ------------------------------------------------ | -------------------------------- |
| Provides compute resources                       | Stores application data          |
| Contains one or more databases                   | Contains tables and data         |
| Defines regional or multi-regional configuration | Defines schema and business data |

---

### Q6. What is the role of a DevOps Engineer with Cloud Spanner databases?

**Answer:**
A DevOps Engineer helps provision databases, automate infrastructure, manage IAM permissions, support Oracle-to-Spanner migrations, monitor performance, configure backups, and assist with production operations.

---

# Summary

```text
Google Cloud Project
        │
        ▼
Cloud Spanner Instance
        │
        ▼
Database
        │
        ▼
Tables
        │
        ▼
Rows & Columns
```

### Key Points to Remember

* A **database stores your business data**.
* A **database always belongs to an instance**.
* A **database contains tables, indexes, and schema objects**.
* A **single instance can host multiple databases**.
* **Data migration tools like Striim** move data from Oracle into Cloud Spanner databases in migration projects.
* As a **DevOps Engineer**, you'll mainly work with provisioning, IAM, monitoring, backups, automation, and supporting migration pipelines rather than designing business schemas.
