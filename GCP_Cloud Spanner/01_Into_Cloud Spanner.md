# Cloud Spanner:

## Module 1: Introduction to Cloud Spanner

### 1.1 Introduction

Before learning Cloud Spanner, let's first understand **why databases are needed**.

Imagine you are developing an online shopping application like Amazon.

Customers can:

* Register
* Login
* Search products
* Place orders
* Make payments

All this information must be stored somewhere.

For example:

```text
Customer
---------------------
ID    Name    City
---------------------
1     John    London
2     Ravi    Chennai
3     David   New York
```

Similarly,

```text
Orders
------------------------------
OrderID  CustomerID  Amount
------------------------------
1001     1           500
1002     2           1200
1003     3           850
```

This information is stored in a **database**.

Without a database, the application cannot remember:

* Customers
* Orders
* Payments
* Products

So, every application needs a database.

---
### 1.2 What is a Database?

A database is an organized collection of data that allows applications to store, retrieve, update, and delete information efficiently.

**For example:**

A banking application stores:

* Customer details
* Account details
* Transaction history
* Loan information

An e-commerce application stores:

* Products
* Customers
* Orders
* Payments

The database acts as the application's memory.

---
### 1.3 What is a Relational Database?

There are different types of databases.

One of the most common is a **Relational Database**.

A relational database stores data in **tables**.

Example:

#### Employee Table

| Employee ID | Name  | Department |
| ----------- | ----- | ---------- |
| 101         | Rahul | IT         |
| 102         | Priya | HR         |
| 103         | David | Finance    |

#### Department Table

| Department ID | Department Name |
| ------------- | --------------- |
| 1             | IT              |
| 2             | HR              |
| 3             | Finance         |

These tables can be related to each other using keys.

Examples of relational databases include:

* Oracle Database
* MySQL
* PostgreSQL
* Microsoft SQL Server
* Cloud Spanner

---
### 1.4 What is Cloud Spanner?

Cloud Spanner is a fully managed relational database service provided by Google Cloud.

It stores data in tables, just like Oracle or MySQL, but it is designed to run in the cloud and automatically handle very large workloads.

Unlike traditional databases, Cloud Spanner can scale across many servers while still behaving like a single database.

---
### 1.5 Why Did Google Create Cloud Spanner?

Many companies started facing problems with traditional databases.

Imagine a bank has **50 million customers**.

Initially, one database server is enough.

```text
Application
     |
     |
Database Server
```

As the company grows:

* More customers join.
* More transactions occur.
* More applications connect.

Eventually, the server becomes overloaded.

Performance decreases.

Queries become slower.

Users experience delays.

Google wanted a database that could:

* Handle millions of users.
* Scale automatically.
* Remain available even if hardware fails.
* Work across multiple regions.
* Continue processing transactions without interruption.

To solve these challenges, Google developed Cloud Spanner.

---
### 1.6 Why Is Cloud Spanner Different?

Traditional databases usually run on one primary server.

Cloud Spanner distributes data across multiple servers.

```text
          Application
               |
               |
        Cloud Spanner
      /      |      \
  Server1  Server2  Server3
```

Instead of relying on one machine, Cloud Spanner uses many machines working together.

This provides:

* Better performance
* Better scalability
* Better availability
* Better reliability

---
### 1.7 Where Is Cloud Spanner Used?

Cloud Spanner is commonly used in applications that require:

* Millions of users
* Large amounts of data
* Continuous availability
* High-performance transactions

**Examples include:**

* Banking systems
* E-commerce platforms
* Telecommunications
* Healthcare systems
* Financial applications
* Airline reservation systems

---
### 1.8 Why Is Your Company Migrating to Cloud Spanner?

Based on your project, the migration flow is:

```text
Oracle Database
       |
       |  (Change Data Capture)
       |
     Striim
       |
       |
Cloud Spanner
```

The company wants to move from Oracle to Cloud Spanner because Cloud Spanner provides:

* Better scalability
* High availability
* Reduced infrastructure management
* Automatic replication
* Seamless integration with Google Cloud services

> As a DevOps Engineer, you'll help manage the infrastructure, deployment, monitoring, and migration support around this database.

--- 
### Cloud Spanner Components (High Level):

```text
GCP Project
     |
     v
Spanner Instance
     |
     v
Database
     |
     v
Tables
```

Example:

```text
Project
 |
 +-- Spanner Instance
        |
        +-- EmployeeDB
        |
        +-- CustomerDB
```

---
### Cloud Spanner vs Oracle:

| Feature           | Oracle       | Cloud Spanner |
| ----------------- | ------------ | ------------- |
| Managed Service   | No           | Yes           |
| Auto Scaling      | Limited      | Yes           |
| Global Database   | Complex      | Native        |
| Replication       | Manual       | Automatic     |
| Backup            | Manual       | Managed       |
| Patching          | DBA          | Google        |
| High Availability | Manual Setup | Built-In      |

---
### 8. Cloud Spanner vs MySQL: 

| Feature            | MySQL    | Cloud Spanner |
| ------------------ | -------- | ------------- |
| Open Source        | Yes      | No            |
| Horizontal Scaling | Limited  | Excellent     |
| Global Replication | Complex  | Native        |
| Availability       | Moderate | Very High     |
| Managed by Google  | No       | Yes           |

---
### 9. Where is Cloud Spanner Used?

#### Banking:
```text
Accounts
Payments
Transactions
```

#### Telecom:

```text
Customer Data
Billing
Usage Records
```

#### E-Commerce:

```text
Orders
Products
Customers
```

#### Healthcare:

```text
Patient Records
Appointments
Medical Data
```

---
### Cloud Spanner in Your Project:

Your migration flow:

```text
Oracle Database
      |
      |
      v
    Striim
      |
      |
      v
Cloud Spanner
```

Process:

#### Step 1

Oracle contains existing data.

```text
Customer
Orders
Payments
```

#### Step 2

Striim captures changes using CDC.

```text
Insert
Update
Delete
```

#### Step 3

Striim sends data to Cloud Spanner.

#### Step 4

Applications start reading from Cloud Spanner.

---
### As a DevOps Engineer, Why Should You Learn Cloud Spanner?

In your project, you may work on:

### Infrastructure

* GCP Projects
* IAM
* Networking

### Cloud Spanner

* Instance creation
* Scaling
* Monitoring
* Backup validation

### Migration Support

* Striim connectivity
* Data validation
* Troubleshooting

### Monitoring

* Datadog
* Cloud Monitoring
* Alerting

### Automation

* Terraform
* CI/CD
* Harness

---
#### Important Terms (Remember):

| Term     | Meaning                     |
| -------- | --------------------------- |
| Project  | GCP container for resources |
| Instance | Holds databases             |
| Database | Holds tables                |
| Table    | Stores records              |
| Row      | Single record               |
| Column   | Field                       |
| Node     | Compute capacity            |
| Replica  | Copy of data                |
| Region   | Geographic location         |

---
## What is Cloud Spanner?

> Cloud Spanner is a fully managed, horizontally scalable, globally distributed relational database service from Google Cloud. It provides SQL support, strong consistency, automatic replication, high availability, and seamless scaling while minimizing operational overhead.

---

