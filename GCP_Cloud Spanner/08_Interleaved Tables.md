# Topic 9: Cloud Spanner Interleaved Tables (Complete End-to-End Guide):
---

# Chapter 1: Introduction

## What are Interleaved Tables?

An **Interleaved Table** is a child table whose rows are **physically stored together with the rows of its parent table** based on the parent's primary key.

Unlike a normal parent-child relationship, Cloud Spanner can physically organize related data together to improve performance.

### Simple Definition

> **Interleaved Tables store parent and child data close together on storage so related data can be read more efficiently.**

---

# Why Did Google Introduce Interleaved Tables?

In traditional databases:

Suppose we have two tables:

```text
Customer
```

and

```text
Orders
```

Relationship:

```text
Customer

CustomerID

↓

Orders

CustomerID
```

Customer data is stored in one place.

Order data may be stored somewhere completely different.

When a query needs both:

```sql
SELECT *
FROM Customer
JOIN Orders
```

The database has to search two different storage locations.

That takes time.

Google asked:

> **What if parent and child data were stored together?**

That idea became **Interleaved Tables**.

---

# Real-Life Analogy

Imagine a school.

Traditional storage:

```text
Building A

Students

------------------

Building B

Student Books
```

To get information:

Student

↓

Walk to Building B

↓

Find Books

↓

Return

Lots of movement.

---

Interleaved storage:

```text
Student

↓

Books

↓

Student

↓

Books

↓

Student

↓

Books
```

Each student's books are kept with that student.

Faster.

---

# Normal Tables vs Interleaved Tables

## Normal Design

```text
Customer Table

1001

1002

1003



Orders Table

5001

5002

5003

5004
```

Different storage areas.

---

## Interleaved Design

```text
Customer 1001

   Order 1

   Order 2

Customer 1002

   Order 3

Customer 1003

   Order 4
```

Customer and Orders are stored together.

---

# Internal Working

Application

↓

Get Customer

↓

Need Orders

↓

Cloud Spanner

↓

Customer and Orders already together

↓

Return Results

No large scan required.

---

# Parent Table

Parent table stores master data.

Example

```text
Customer
```

| CustomerID | Name  |
| ---------- | ----- |
| 1001       | Rahul |

Primary Key

```text
CustomerID
```

---

# Child Table

Orders

| CustomerID | OrderID | Amount |
| ---------- | ------- | ------ |
| 1001       | 1       | 500    |
| 1001       | 2       | 700    |

Notice

CustomerID exists in both tables.

---

# How Cloud Spanner Creates an Interleaved Table

Example SQL:

```sql
CREATE TABLE Customers (
  CustomerID INT64 NOT NULL,
  Name STRING(100)
) PRIMARY KEY (CustomerID);

CREATE TABLE Orders (
  CustomerID INT64 NOT NULL,
  OrderID INT64 NOT NULL,
  Amount FLOAT64
) PRIMARY KEY (CustomerID, OrderID),
INTERLEAVE IN PARENT Customers ON DELETE CASCADE;
```

Key points:

* `CustomerID` is the parent key.
* The child table's primary key begins with the parent's primary key.
* `INTERLEAVE IN PARENT` tells Cloud Spanner to store the child rows with the parent rows.
* `ON DELETE CASCADE` means deleting a customer automatically deletes that customer's orders.

---

# Storage Visualization

Without interleaving:

```text
Storage

Customer

1001

1002

1003



Orders

5001

5002

5003
```

---

With interleaving:

```text
Customer 1001

   Order 1

   Order 2

Customer 1002

   Order 3

Customer 1003

   Order 4
```

Cloud Spanner keeps related records together.

---

# Why is Interleaving Useful?

Suppose an application runs this query thousands of times:

```sql
SELECT *
FROM Orders
WHERE CustomerID = 1001;
```

Because the orders are stored with the customer,

Cloud Spanner reads less data.

Benefits:

✔ Faster reads

✔ Lower latency

✔ Better locality

✔ Reduced storage scanning

---

# Requirements for Interleaving

Not every table should be interleaved.

The child table:

* Must have a parent table.
* Must begin its primary key with the parent's primary key.
* Should have a strong parent-child relationship.
* Should usually be accessed together with the parent.

---

# Good Example

```text
Customer

↓

Orders
```

One customer owns many orders.

Application usually loads:

Customer

*

Orders

Together.

Good candidate.

---

# Another Good Example

```text
Department

↓

Employees
```

Most employee queries begin with the department.

---

# Bad Example

```text
Products

↓

Payments
```

These don't have a direct ownership relationship.

Don't interleave.

---

# Interleaving vs Foreign Key

Many people confuse these.

## Foreign Key

A foreign key defines a logical relationship.

Example:

```text
CustomerID
```

inside Orders references Customer.

It enforces integrity.

---

## Interleaved Table

Interleaving changes the **physical storage layout**.

It improves performance.

It is about **where the data is stored**, not just how it is related.

---

# Oracle → Striim → Cloud Spanner Migration

Suppose Oracle has:

```text
Customer

↓

Orders
```

During migration:

1. Analyze the schema.
2. Determine whether Orders should be interleaved under Customer.
3. Create the Cloud Spanner schema.
4. Start Striim.

Flow:

```text
Oracle

↓

Schema Analysis

↓

Interleaved Design Decision

↓

Cloud Spanner Schema

↓

Striim Migration

↓

Application
```

Not every Oracle parent-child relationship should automatically become interleaved.

---

# Advantages

✔ Faster parent-child reads.

✔ Better query performance.

✔ Reduced storage scanning.

✔ Better data locality.

✔ Lower read latency.

---

# Disadvantages

⚠ Child rows depend on the parent.

⚠ Poor design can reduce flexibility.

⚠ Not suitable for every relationship.

⚠ Changing the design later can require schema migration.

---

# DevOps Engineer Responsibilities

Many developers think interleaving is only a DBA concern.

In real projects, DevOps engineers also have important responsibilities.

---

## 1. Deploy Schema

Typical pipeline:

```text
Developer

↓

Git

↓

Pull Request

↓

Harness / Jenkins

↓

Deploy SQL

↓

Cloud Spanner
```

The deployment includes:

* Parent table
* Child table
* Interleaving definition

---

## 2. Validate Schema

After deployment, verify:

* Parent table exists.
* Child table exists.
* Primary keys are correct.
* Interleaving was applied successfully.

---

## 3. Support Migration

Before enabling Striim:

```text
Oracle

↓

Review Parent-Child Relationships

↓

Create Interleaved Tables (if appropriate)

↓

Deploy Schema

↓

Start CDC
```

If the schema is incorrect, migration can fail or performance may suffer.

---

## 4. CI/CD Integration

Store schema in Git.

Example:

```text
database/
├── V1__customers.sql
├── V2__orders.sql
├── V3__interleave_orders.sql
```

Pipeline:

1. Validate SQL.
2. Deploy to Development.
3. Test.
4. Promote to UAT.
5. Promote to Production.

---

## 5. Monitoring After Deployment

Watch for:

* Query latency
* Read performance
* CPU usage
* Transaction latency
* Application errors

Compare performance before and after interleaving.

---

## 6. Troubleshooting

### Scenario 1: Application Is Slow

Possible causes:

* Tables that should be interleaved are not.
* Missing indexes.
* Inefficient queries.

Action:

Review access patterns and the schema design.

---

### Scenario 2: Migration Errors

Possible causes:

* Parent table missing.
* Incorrect primary key order.
* Child table created before parent.

Action:

Verify the schema deployment order and definitions.

---

# Best Practices

✔ Use interleaving only for strong parent-child relationships.

✔ Ensure the child primary key starts with the parent's primary key.

✔ Base the decision on application query patterns.

✔ Test performance before and after implementation.

✔ Document the schema clearly.

---

# Common Mistakes

❌ Interleaving unrelated tables.

❌ Forgetting that the child key must begin with the parent key.

❌ Assuming every Oracle relationship should become interleaved.

❌ Ignoring application access patterns.

---

# Interview Questions & Answers

### Q1. What is an Interleaved Table?

**Answer:**
An interleaved table is a child table whose rows are physically stored with the rows of its parent table based on the parent's primary key, improving locality and read performance.

---

### Q2. Why are Interleaved Tables used?

**Answer:**
They improve the performance of parent-child queries by storing related data together, reducing the amount of data that Cloud Spanner needs to scan.

---

### Q3. What is the difference between a Foreign Key and an Interleaved Table?

| Foreign Key                    | Interleaved Table                               |
| ------------------------------ | ----------------------------------------------- |
| Logical relationship           | Physical storage organization                   |
| Enforces referential integrity | Improves data locality and query performance    |
| Does not determine storage     | Determines how related rows are stored together |

---

### Q4. Can every child table be interleaved?

**Answer:**
No. Interleaving is appropriate only when there is a strong ownership relationship and the application frequently accesses the parent and child together.

---

### Q5. What is the DevOps role in Interleaved Tables?

**Answer:**
A DevOps engineer automates schema deployments, validates interleaving definitions, supports Oracle-to-Spanner migrations, manages CI/CD pipelines for schema changes, monitors performance after deployment, and helps troubleshoot production issues related to schema design.

---

# Summary

```text
Project
   │
   ▼
Instance
   │
   ▼
Database
   │
   ▼
Schema
   │
   ▼
Customers (Parent)
      │
      └── Orders (Interleaved Child)
```

## Key Points to Remember

* **Interleaved Tables are unique to Cloud Spanner's storage model.**
* They physically store parent and child rows together.
* They are designed to improve performance for common parent-child access patterns.
* They are **not a replacement for good schema design**—they should be used only when the application's data access patterns justify them.
* As a DevOps Engineer, you'll help automate, validate, deploy, monitor, and troubleshoot schema changes that include interleaved tables, especially during Oracle → Striim → Cloud Spanner migration projects.
