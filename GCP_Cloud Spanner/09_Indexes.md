# Topic 10: Cloud Spanner Indexes (Complete End-to-End Guide)

---

# Chapter 1: Introduction

## What is an Index?

An **Index** is a special database object that helps Cloud Spanner find data faster without scanning the entire table.

Think of an index as the **index page of a book**.

Suppose you have a 1000-page book.

If someone asks:

> "Find the topic 'Cloud Spanner'."

There are two ways.

### Without Index

You start reading page 1.

```text
Page 1

↓

Page 2

↓

Page 3

↓

...

↓

Page 1000
```

Very slow.

---

### With Index

Open the book's index.

```text
Cloud Spanner → Page 456
```

Go directly to page 456.

Very fast.

**Database indexes work exactly the same way.**

---

# Why Do We Need Indexes?

Suppose your Employee table has

```text
50 Million Records
```

Application Query

```sql
SELECT *
FROM Employee
WHERE Email='rahul@gmail.com';
```

Without an index,

Cloud Spanner checks

```text
Row 1

↓

Row 2

↓

Row 3

↓

...

↓

Row 50,000,000
```

This is called a **Table Scan**.

Very expensive.

---

With an index

```text
Email Index

↓

rahul@gmail.com

↓

Employee Found
```

Only a tiny amount of data is read.

---

# Real-Life Example

Imagine a library.

Without catalog:

```text
Books

↓

Search every shelf
```

With catalog:

```text
Book Name

↓

Shelf Number

↓

Take Book
```

The catalog is the index.

---

# Cloud Spanner Storage

Suppose Employee table

| EmployeeID | Name  | Email                                     |
| ---------- | ----- | ----------------------------------------- |
| 101        | Rahul | [rahul@gmail.com](mailto:rahul@gmail.com) |
| 102        | Priya | [priya@gmail.com](mailto:priya@gmail.com) |

Primary Key

```text
EmployeeID
```

Cloud Spanner stores rows ordered by EmployeeID.

Now application searches by Email.

Problem.

Email is **not** the primary key.

Without an index,

Cloud Spanner scans every row.

---

# What Happens Internally?

Application

↓

SQL Query

↓

Cloud Spanner

↓

Check Index

↓

Index Exists?

↓

YES

↓

Go Directly

↓

Return Result

---

If No Index

```text
Application

↓

SQL Query

↓

Cloud Spanner

↓

Read Every Row

↓

Find Match

↓

Return Data
```

This takes longer.

---

# Index Structure

Imagine this table.

Employee

| EmployeeID | Name  | Email                                     |
| ---------- | ----- | ----------------------------------------- |
| 101        | Rahul | [rahul@gmail.com](mailto:rahul@gmail.com) |
| 102        | Priya | [priya@gmail.com](mailto:priya@gmail.com) |
| 103        | Amit  | [amit@gmail.com](mailto:amit@gmail.com)   |

Index on Email

```text
Email

↓

amit@gmail.com

↓

EmployeeID 103

----------------

priya@gmail.com

↓

EmployeeID 102

----------------

rahul@gmail.com

↓

EmployeeID 101
```

Cloud Spanner uses the index to quickly locate the row.

---

# Types of Indexes

## 1. Primary Index

Every table automatically has one.

Primary Key

```text
EmployeeID
```

This is the table's main storage order.

No need to create it.

---

## 2. Secondary Index

Created manually.

Example

Application searches by

```text
Email
```

Create an index.

```sql
CREATE INDEX IDX_EMAIL
ON Employee(Email);
```

Now Cloud Spanner can search by Email efficiently.

---

## 3. Composite Index

Sometimes

Application searches

```sql
WHERE Department='IT'

AND Salary > 50000
```

Create

```sql
CREATE INDEX IDX_DEPT_SALARY
ON Employee(Department, Salary);
```

One index supports both columns.

---

# Why Not Create Indexes on Every Column?

Many beginners think:

> "More indexes = better performance."

That's incorrect.

Indexes improve **read** performance.

But every INSERT, UPDATE, or DELETE must also update the indexes.

Too many indexes increase:

* Storage
* Write latency
* Maintenance cost

---

# Example

Table

Employee

Columns

```text
EmployeeID

Name

Email

Department

Salary

Phone

Address

Manager

JoiningDate

Gender
```

Wrong

```text
Index on every column
```

Every insert updates many indexes.

Performance suffers.

---

Correct

Create indexes only for columns frequently used in searches.

---

# Covering Index

Cloud Spanner supports **stored columns** in indexes.

Example:

```sql
CREATE INDEX IDX_EMAIL
ON Employee(Email)
STORING(Name, Department);
```

Why?

Suppose query

```sql
SELECT Name, Department

FROM Employee

WHERE Email='rahul@gmail.com';
```

Cloud Spanner can answer using only the index.

No need to read the base table.

Benefits:

✔ Faster reads

✔ Lower latency

---

# Oracle → Striim → Cloud Spanner Migration

Suppose Oracle

Employee Table

Indexes

```text
Email Index

Department Index
```

Migration Flow

```text
Oracle

↓

Analyze Existing Indexes

↓

Do We Really Need Them?

↓

Create Required Indexes

↓

Deploy Schema

↓

Start Striim
```

Important:

Don't blindly migrate every Oracle index.

Cloud Spanner has a different storage architecture.

Some Oracle indexes may no longer be necessary.

---

# DevOps Engineer Responsibilities

Many developers think indexes are only a DBA responsibility.

In real projects, DevOps engineers work closely with DBAs and developers.

---

## 1. Deploy Indexes

Pipeline

```text
Developer

↓

Git

↓

Pull Request

↓

Harness/Jenkins

↓

Deploy SQL

↓

Cloud Spanner
```

Example SQL

```sql
CREATE INDEX IDX_EMAIL

ON Employee(Email);
```

---

## 2. Store Schema as Code

Repository

```text
database/

V1__employee.sql

V2__customer.sql

V3__indexes.sql
```

Version-controlled.

Peer-reviewed.

Automatically deployed.

---

## 3. CI/CD Pipeline

Typical flow

```text
Developer

↓

Git

↓

Harness

↓

Validate SQL

↓

Deploy

↓

Verify Index

↓

Notify
```

---

## 4. Monitoring

After deployment

Watch

* Query latency
* Read latency
* Write latency
* CPU utilization
* Storage usage
* Index creation progress
* Slow queries

Tools

* Google Cloud Monitoring
* Datadog
* Cloud Logging

---

## 5. Troubleshooting

### Scenario 1: Slow Search by Email

Query

```sql
SELECT *

FROM Employee

WHERE Email='rahul@gmail.com';
```

Possible cause

No Email index.

Solution

Create a secondary index on Email.

---

### Scenario 2: Slow Inserts

Possible causes

* Too many indexes
* Heavy write traffic

Action

Review whether all indexes are necessary.

---

### Scenario 3: Migration Is Slow

Possible causes

Indexes being maintained while loading large amounts of data.

Some teams:

1. Load data first.
2. Create indexes afterward.

Whether that's appropriate depends on the migration strategy and application requirements.

---

# Best Practices

✔ Create indexes only for frequently searched columns.

✔ Review actual query patterns before creating indexes.

✔ Monitor performance after deployment.

✔ Remove unused indexes.

✔ Keep index definitions under version control.

---

# Common Mistakes

❌ Creating indexes on every column.

❌ Migrating all Oracle indexes without analysis.

❌ Ignoring write performance.

❌ Forgetting to monitor index usage.

---

# Production Example

Application

```sql
SELECT *

FROM Customer

WHERE Mobile='9876543210';
```

Performance issue.

Investigation shows

No Mobile index.

Developer creates

```sql
CREATE INDEX IDX_MOBILE

ON Customer(Mobile);
```

Pipeline

Git

↓

Harness

↓

Deploy

↓

Production

↓

Query time drops significantly.

---

# Interview Questions & Answers

### Q1. What is an Index?

**Answer:**

An index is a database object that helps Cloud Spanner locate rows more efficiently, reducing the amount of data scanned for queries.

---

### Q2. Why are indexes needed?

**Answer:**

Indexes improve query performance by allowing Cloud Spanner to quickly locate matching rows instead of scanning the entire table.

---

### Q3. What is the difference between a Primary Index and a Secondary Index?

| Primary Index                              | Secondary Index                               |
| ------------------------------------------ | --------------------------------------------- |
| Automatically created from the primary key | Created manually                              |
| Determines table storage order             | Optimizes searches on non-primary-key columns |
| Required                                   | Optional                                      |

---

### Q4. Can too many indexes cause problems?

**Answer:**

Yes. While indexes improve reads, every INSERT, UPDATE, and DELETE must also update the indexes, which can increase write latency, storage usage, and maintenance overhead.

---

### Q5. What is the DevOps role in index management?

**Answer:**

A DevOps engineer automates index deployment through CI/CD, manages schema changes in Git, validates deployments, monitors performance after releases, supports migrations, and collaborates with database teams to troubleshoot performance issues.

---

# Real-Time Project Example (Oracle → Striim → Cloud Spanner)

Suppose your project migrates a banking application.

Oracle table:

```text
ACCOUNT
---------
ACCOUNT_ID
CUSTOMER_ID
ACCOUNT_NUMBER
EMAIL
PHONE
BALANCE
```

The application frequently searches by:

* `ACCOUNT_NUMBER`
* `EMAIL`

During migration:

1. Analyze Oracle indexes.
2. Determine which indexes are still needed in Cloud Spanner.
3. Create the required secondary indexes.
4. Deploy the schema through CI/CD.
5. Start Striim CDC.
6. Monitor query latency and write performance after go-live.

---

# Summary

```text
Cloud Spanner Table
        │
        ├── Primary Key (Primary Index)
        │
        ├── Secondary Index (Email)
        │
        ├── Secondary Index (AccountNumber)
        │
        └── Composite Index (Department, Salary)
```

## Key Points to Remember

* **Every table automatically has a primary index** based on its primary key.
* **Secondary indexes** improve searches on non-primary-key columns.
* **More indexes are not always better**; they speed up reads but add overhead to writes.
* In Oracle → Cloud Spanner migrations, review and redesign indexes rather than copying them blindly.
* As a **DevOps Engineer**, you'll automate index deployments, include them in version-controlled schema changes, monitor production performance, and work with developers and database teams to optimize query performance.
