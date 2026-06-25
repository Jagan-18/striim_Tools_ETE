Excellent choice. Before learning Cloud Spanner architecture, you must understand its **Core Terminology**. These are the words you'll hear every day in a Cloud Spanner project.

Since you're a beginner, I'll explain each term with simple examples and relate them to your **Oracle → Striim → Cloud Spanner** migration project.

# Topic 3: Core Terminology of Cloud Spanner

## Why Learn Core Terminology?

Imagine someone on your team says:

> "Create a new Cloud Spanner instance, add a database, create the schema, and grant IAM permissions."

If you don't know what an **instance**, **database**, **schema**, or **IAM** is, it will be difficult to follow the task.

So first, let's build a strong foundation.

---

# High-Level Structure

Think of Cloud Spanner like a company.

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
Rows & Columns
```

Everything starts with a **Google Cloud Project**.

---

# 1. Google Cloud Project

## What is it?

A **Google Cloud Project** is the top-level container where Google Cloud resources are created.

Think of it as a folder that contains all your cloud resources.

Example:

```text
Project Name
DataMigrationProject

Resources:
- Cloud Spanner
- Compute Engine
- Cloud Storage
- IAM
- Monitoring
```

Without a project, you cannot create a Cloud Spanner instance.

---

# 2. Cloud Spanner Instance

## What is an Instance?

An **Instance** is a container that holds one or more Cloud Spanner databases.

It provides the compute capacity (processing resources) for those databases.

Example:

```text
Cloud Spanner Instance
│
├── EmployeeDB
├── CustomerDB
└── FinanceDB
```

### Real-World Example

Think of an apartment building.

* Apartment Building = Instance
* Apartments = Databases

The building contains many apartments.

Similarly, one Cloud Spanner instance can contain multiple databases.

---

# 3. Database

## What is a Database?

A database stores your application's data.

Example:

```text
Customer Database

Customers
Orders
Payments
Products
```

In your migration project:

```text
Oracle Database
        │
        ▼
Striim
        │
        ▼
Cloud Spanner Database
```

The data moves from Oracle into a Cloud Spanner database.

---

# 4. Schema

## What is a Schema?

A schema defines the structure of your database.

It describes:

* Tables
* Columns
* Data types
* Primary keys
* Relationships

Example:

```text
Employee Table

EmployeeID
Name
Department
Salary
```

Before storing data, you first create the schema.

---

# 5. Table

## What is a Table?

A table stores related data in rows and columns.

Example:

| EmployeeID | Name  | Department |
| ---------- | ----- | ---------- |
| 101        | Rahul | HR         |
| 102        | Priya | IT         |

A database can contain many tables.

Example:

```text
Employee
Customer
Orders
Payments
Products
```

---

# 6. Row

## What is a Row?

A row represents one record.

Example:

| EmployeeID | Name  | Department |
| ---------- | ----- | ---------- |
| 101        | Rahul | HR         |

This entire line is one row.

---

# 7. Column

## What is a Column?

A column represents one attribute of the data.

Example:

| EmployeeID | Name | Department |
| ---------- | ---- | ---------- |

Columns:

* EmployeeID
* Name
* Department

---

# 8. Primary Key

## What is a Primary Key?

A primary key uniquely identifies each row in a table.

Example:

| EmployeeID | Name  |
| ---------- | ----- |
| 101        | Rahul |
| 102        | Priya |

Here, `EmployeeID` is the primary key because each value is unique.

Cloud Spanner requires every table to have a primary key.

Why?

* Prevent duplicate records
* Find data quickly
* Organize data efficiently

---

# 9. Node

## What is a Node?

A node provides the compute power for Cloud Spanner.

It handles:

* SQL query execution
* Transaction processing
* Data reads
* Data writes

Example:

```text
Application
      │
      ▼
Cloud Spanner
│
├── Node 1
├── Node 2
└── Node 3
```

More nodes generally mean more capacity.

---

# 10. Processing Unit (PU)

A **Processing Unit (PU)** is a smaller unit of compute capacity.

Instead of adding a full node, you can allocate a smaller amount of compute using processing units.

Think of it like renting part of a server instead of a whole server.

---

# 11. Region

## What is a Region?

A region is the geographic location where your Cloud Spanner resources run.

Examples:

* asia-south1
* us-central1
* europe-west1

If your users are mostly in India, you might choose a region in Asia to reduce latency.

---

# 12. Multi-Region

A multi-region deployment stores and replicates data across multiple regions.

Example:

```text
Region A
    │
    ├────────────┐
    ▼            ▼
Region B     Region C
```

Benefits:

* High availability
* Better disaster recovery
* Improved reliability

---

# 13. Replica

A replica is a copy of your data stored in another location.

Example:

```text
Original Data
      │
      ├── Replica 1
      └── Replica 2
```

If one location has a problem, another replica can continue serving data.

---

# 14. Instance Configuration

An instance configuration defines where and how your instance operates.

It specifies things such as:

* Regional or multi-regional deployment
* The locations where data is stored

---

# 15. Transaction

A transaction is a group of operations that succeed or fail together.

Example:

A bank transfer:

```text
Withdraw ₹500 from Account A
Deposit ₹500 into Account B
```

If either operation fails, the entire transaction is rolled back to keep the data consistent.

---

# 16. SQL

Cloud Spanner uses SQL to interact with data.

Examples:

```sql
SELECT * FROM Employee;
```

```sql
INSERT INTO Employee VALUES (...);
```

```sql
UPDATE Employee SET Salary = 60000;
```

```sql
DELETE FROM Employee WHERE EmployeeID = 101;
```

---

# 17. IAM (Identity and Access Management)

IAM controls who can access Cloud Spanner and what they are allowed to do.

Examples:

| Role                   | Permission          |
| ---------------------- | ------------------- |
| Database Administrator | Full access         |
| Developer              | Read and write data |
| Viewer                 | Read-only access    |

As a DevOps Engineer, you'll often configure IAM roles for applications and team members.

---

# 18. Backup

A backup is a saved copy of your database that can be restored if needed.

Example:

```text
Database
    │
    ▼
Backup
```

If data is accidentally deleted or corrupted, you can restore it from the backup.

---

# 19. Restore

Restore means creating a database from a previously taken backup.

Example:

```text
Backup
    │
    ▼
Restore
    │
    ▼
New Database
```

---

# 20. Change Data Capture (CDC)

CDC captures changes (inserts, updates, and deletes) from the source database and sends them to the target database.

In your project:

```text
Oracle
   │
   │  CDC
   ▼
Striim
   │
   ▼
Cloud Spanner
```

This allows Cloud Spanner to stay synchronized with Oracle during migration.

---

# Summary

| Term            | Meaning                                           |
| --------------- | ------------------------------------------------- |
| Project         | Top-level container for Google Cloud resources    |
| Instance        | Container that holds one or more databases        |
| Database        | Stores application data                           |
| Schema          | Structure of the database (tables, columns, keys) |
| Table           | Collection of related data                        |
| Row             | A single record                                   |
| Column          | An attribute of the data                          |
| Primary Key     | Unique identifier for each row                    |
| Node            | Compute capacity for Cloud Spanner                |
| Processing Unit | Smaller unit of compute capacity                  |
| Region          | Geographic location of resources                  |
| Multi-Region    | Data stored across multiple regions               |
| Replica         | Copy of data for availability                     |
| Transaction     | Group of operations that succeed or fail together |
| SQL             | Language used to query and modify data            |
| IAM             | Controls access and permissions                   |
| Backup          | Saved copy of the database                        |
| Restore         | Recover a database from a backup                  |
| CDC             | Captures and replicates data changes              |

These terms form the vocabulary you'll use throughout your Cloud Spanner learning journey. Understanding them well will make the upcoming topics—such as **Cloud Spanner Architecture**, **Instances**, **Nodes**, **Schema Design**, and **Oracle-to-Cloud Spanner migration**—much easier to understand.
