# Topic 7: Cloud Spanner Primary Keys (Complete End-to-End Guide)

---

# Chapter 1: Introduction

## What is a Primary Key?

A Primary Key is one or more columns that uniquely identify every row in a table.

Every row must have a unique Primary Key value.

Example

Employee Table

| EmployeeID | Name  | Department |
| ---------- | ----- | ---------- |
| 101        | Rahul | IT         |
| 102        | Priya | HR         |
| 103        | Amit  | Finance    |

Here

```text
EmployeeID
```

is the Primary Key.

---

## Definition

A Primary Key is a unique identifier that ensures:

* No duplicate rows
* Fast data lookup
* Efficient storage
* Correct data relationships

---

# Why Do We Need a Primary Key?

Imagine a company with 10,000 employees.

Two employees have the same name.

| EmployeeID | Name  |
| ---------- | ----- |
| 101        | Rahul |
| 205        | Rahul |

If someone asks:

> "Find Rahul."

Which Rahul?

Impossible to know.

Instead

```text
Find EmployeeID = 205
```

Now the record is uniquely identified.

---

# Real-Life Example

Imagine a country.

People may have the same name.

But each citizen has a unique Aadhaar number.

| Real World     | Database    |
| -------------- | ----------- |
| Aadhaar Number | Primary Key |
| Citizen        | Row         |

Names can repeat.

Aadhaar cannot.

---

# Characteristics of a Good Primary Key

A Primary Key should be:

✅ Unique

```text
101

102

103
```

No duplicates.

---

### NOT NULL

Cannot be empty.

Wrong

```text
EmployeeID = NULL
```

Correct

```text
EmployeeID = 1001
```

---

### Stable

Bad

Phone Number

because

People change numbers.

Good

EmployeeID

because

Never changes.

---

### Short

Good

```text
INT64
```

Bad

```text
Very Long String
```

Short keys improve storage and performance.

---

# Cloud Spanner vs Oracle

This is where many beginners get confused.

## Oracle

Primary Key

↓

Uniquely identifies rows.

Done.

---

## Cloud Spanner

Primary Key

↓

Uniquely identifies rows

↓

Determines Storage

↓

Determines Distribution

↓

Determines Replication

↓

Determines Performance

↓

Determines Scalability

The Primary Key has a much larger impact.

---

# How Cloud Spanner Stores Data

Suppose we have this table:

```sql
Employee

EmployeeID

Name

Department
```

Primary Key

```text
EmployeeID
```

Cloud Spanner automatically stores rows in **Primary Key order**.

Example

```text
1001

1002

1003

1004

1005
```

Notice

The data is sorted.

Unlike some traditional databases, Cloud Spanner organizes storage based on the key.

---

# Internal Working

Application

↓

Insert EmployeeID = 1005

↓

Cloud Spanner

↓

Read Schema

↓

Check Primary Key

↓

Find Correct Storage Location

↓

Write Data

Everything starts with the Primary Key.

---

# Composite Primary Key

Sometimes

One column is not enough.

Example

Orders

| CustomerID | OrderID |
| ---------- | ------- |
| 101        | 1       |
| 101        | 2       |
| 101        | 3       |

Primary Key

```sql
PRIMARY KEY(CustomerID, OrderID)
```

Together

CustomerID + OrderID

become unique.

---

# Why Composite Keys?

Example

Customer

101

can have

many orders.

OrderID alone may not be globally unique.

Using both columns guarantees uniqueness.

---

# Primary Key Ordering

Cloud Spanner stores rows according to the order of the Primary Key columns.

Example

```sql
PRIMARY KEY(CustomerID, OrderID)
```

Data is grouped like this:

```text
Customer 101
    Order 1
    Order 2
    Order 3

Customer 102
    Order 1
    Order 2
```

Queries for all orders of Customer 101 become efficient.

---

# Choosing the Right Primary Key

## Good Example

Employee Table

```text
EmployeeID
```

Unique and stable.

---

## Bad Example

```text
EmployeeName
```

Problems:

* Names repeat.
* Names can change.

---

## Bad Example

```text
Phone Number
```

Phone numbers change.

Not stable.

---

# Hotspot Problem (Very Important)

One of the biggest Cloud Spanner concepts.

Suppose IDs are generated sequentially:

```text
1001

1002

1003

1004

1005
```

Every new row is inserted at the "end" of the key range.

Millions of users insert data simultaneously.

```text
1006

1007

1008

1009
```

All writes target the same area.

Result:

```text
High Write Traffic

↓

Single Storage Split

↓

Performance Issues

↓

Hotspot
```

A **hotspot** occurs when too many reads or writes are directed to a small portion of the key space, creating a bottleneck.

---

# Better Key Design

Instead of strictly increasing values, use key designs that distribute writes more evenly.

Examples include:

* Universally unique IDs (UUIDs)
* Hash-based prefixes
* Other application-specific strategies

Example:

```text
A91X7

K42P9

Q88LM

D17RT
```

These values are spread across the key space, allowing Cloud Spanner to distribute load more effectively.

> **Note:** The best strategy depends on your application's query patterns. Sometimes sequential keys are acceptable, while in other cases a distributed key design is better.

---

# Primary Key in Oracle → Striim → Cloud Spanner Migration

Migration Flow

```text
Oracle

↓

Read Schema

↓

Read Primary Key

↓

Convert Schema

↓

Cloud Spanner

↓

Start Striim
```

If the Oracle table has no suitable primary key, the migration team may need to redesign the schema before loading data into Cloud Spanner.

---

# DevOps Engineer Responsibilities

Many people think Primary Keys are only a Database Administrator's responsibility.

In reality, DevOps engineers often work closely with database and application teams.

Typical responsibilities include:

## 1. Deploy Schema

Example pipeline:

```text
Developer

↓

Git

↓

Harness / Jenkins

↓

Deploy SQL

↓

Cloud Spanner
```

Ensure tables and primary keys are created successfully.

---

## 2. Validate Schema

After deployment, verify:

* Tables exist
* Primary keys were created
* Schema matches the expected version
* Migration scripts completed successfully

---

## 3. Support Oracle → Striim Migration

Before enabling CDC:

```text
Oracle

↓

Schema Validation

↓

Primary Key Validation

↓

Start Striim
```

Without a correct primary key, data replication can fail or become unreliable.

---

## 4. Monitor Performance

After deployment, monitor:

* Write latency
* Read latency
* CPU utilization
* Storage growth
* Query latency

A poorly chosen primary key can contribute to higher latency and uneven workload distribution.

---

## 5. Troubleshooting

### Scenario: Migration Fails

Possible causes:

* Missing primary key
* Duplicate key values
* Data type mismatch

Action:

Review the source schema and ensure the target table has the correct primary key definition.

---

### Scenario: Slow Inserts

Possible causes:

* Hotspot due to sequential keys
* Heavy write traffic
* Insufficient compute capacity

Action:

Work with database architects to review key design, analyze workload, and scale the instance if needed.

---

# Best Practices

✔ Use unique values.

✔ Choose stable columns that rarely change.

✔ Keep keys as small as practical.

✔ Understand your application's read and write patterns before selecting a key.

✔ Design keys to distribute workload appropriately for Cloud Spanner.

---

# Common Mistakes

❌ Using names as primary keys.

❌ Using frequently changing values.

❌ Ignoring workload patterns.

❌ Migrating Oracle schemas without reviewing whether the primary key design suits Cloud Spanner.

---

# Interview Questions & Answers

### Q1. What is a Primary Key?

**Answer:**
A Primary Key uniquely identifies each row in a table. In Cloud Spanner, it also determines how data is organized and influences performance and scalability.

---

### Q2. Why is a Primary Key mandatory in Cloud Spanner?

**Answer:**
Every Cloud Spanner table must have a primary key because Cloud Spanner uses it to organize data, locate rows efficiently, distribute data across storage, and process transactions.

---

### Q3. What is the difference between Oracle and Cloud Spanner Primary Keys?

| Oracle                              | Cloud Spanner                                                                      |
| ----------------------------------- | ---------------------------------------------------------------------------------- |
| Mainly ensures uniqueness           | Ensures uniqueness and determines data organization, distribution, and performance |
| Less impact on physical data layout | Direct impact on storage layout and scalability                                    |

---

### Q4. What is a hotspot?

**Answer:**
A hotspot occurs when too many operations target a small portion of the key space, causing uneven load and reduced performance. Good primary key design helps minimize hotspots.

---

### Q5. What is a composite primary key?

**Answer:**
A composite primary key uses two or more columns together to uniquely identify a row. For example, `(CustomerID, OrderID)`.

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
Table
   │
   ▼
Primary Key
   │
   ├── Uniquely identifies rows
   ├── Organizes data
   ├── Affects performance
   ├── Influences scalability
   └── Helps distribute workload
```

## Important Points to Remember

* **Every Cloud Spanner table requires a Primary Key.**
* In Cloud Spanner, the Primary Key is much more than a uniqueness constraint—it is central to data organization and performance.
* Good Primary Key design is critical for successful Oracle → Striim → Cloud Spanner migrations.
* As a DevOps Engineer, you'll validate schema deployments, support migrations, monitor performance, troubleshoot issues, and collaborate with database teams to ensure the chosen key design works well in production.
