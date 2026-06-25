# Module 2: Cloud Spanner Architecture:

## 2.1 Introduction

Before learning Cloud Spanner Architecture, let's understand why architecture is important.

Suppose you join a new project and someone says:

> "Cloud Spanner is slow today."

As a DevOps Engineer, you should think:

* Where is the request going?
* Which component is slow?
* Is the issue in the application?
* Is the issue in the network?
* Is the issue in Cloud Spanner?
* Is it a resource (CPU/Nodes) problem?

To answer these questions, you must understand the architecture.
> Before we start learning the architecture of Cloud Spanner, let's first understand **why learning architecture is important**.

Many beginners make one common mistake.

They start learning:

* SQL
* Tables
* Databases
* Instances
* Nodes

without understanding **how everything works together**.

Imagine someone gives you a car.

You learn:

* How to start the engine.
* How to use the steering.
* How to press the accelerator.

But you never learn:

* How the engine works.
* How power reaches the wheels.
* How the brakes work.

You can drive the car, but when something goes wrong, you won't know how to diagnose the problem.

The same idea applies to Cloud Spanner.

Learning commands is not enough.

You need to understand **how Cloud Spanner works internally**.

That is called **Cloud Spanner Architecture**.

---
# Why is Architecture Important?

Let's imagine a real company scenario.

You have joined a project as a DevOps Engineer.

The application team suddenly raises an incident.

> **"The application is very slow today."**

Immediately, people from different teams join the incident call.

* Application Team
* DevOps Team
* Database Team
* Network Team
* Cloud Team

Everyone starts investigating.

As a DevOps Engineer, your first job is **not to restart everything**.

Your first job is to understand **where the problem is**.

---
# What Questions Should a DevOps Engineer Ask?

Instead of guessing, you should investigate systematically.

### Question 1:
**Where is the request going?**

For example,

```text
User
 |
 |
Web Application
 |
 |
API Server
 |
 |
Cloud Spanner
```

Ask yourself:

Is the request reaching Cloud Spanner?

Or is it getting stuck before reaching the database?

If the request never reaches Cloud Spanner, then Cloud Spanner is probably **not** the problem.

---
### Question 2:
**Which component is slow?**

Cloud Spanner is not a single machine.

Internally, it has many components.

For example,

```text
Application
      |
      |
Compute Layer
      |
      |
Storage Layer
      |
      |
Persistent Storage
```

Maybe:

* Compute Layer is busy.
* Storage Layer is overloaded.
* CPU utilization is high.
* Network latency is high.

Architecture helps you identify **which component is responsible**.

---
### Question 3:
**Is the issue in the Application?**

Sometimes developers think:

> "Database is slow."

But the actual problem is:

```text
Application Bug
       |
       |
Slow SQL Query
       |
       |
Cloud Spanner
```

Cloud Spanner is working perfectly.

The application is sending a poorly written query.

Example:

Instead of searching one customer,

```sql
SELECT * FROM Customer
WHERE CustomerID = 100;
```

The application might execute:

```sql
SELECT * FROM Customer;
```

If the table has millions of rows, the query takes much longer.

The database isn't broken—the query is inefficient.

---
### Question 4:
**Is the issue in the Network?**

Suppose the application runs in one region and the database is in another.

```text
Application
      |
      |
Internet / Network
      |
      |
Cloud Spanner
```

If there is network latency:

* Queries become slow.
* Timeouts occur.
* Users complain.

Again, Cloud Spanner may be healthy, but the network is causing delays.

---
### Question 5:
**Is Cloud Spanner Actually the Problem?**

Sometimes it is.

Examples:

* CPU utilization is 95%.
* Too many client connections.
* Storage resources are heavily utilized.
* Poor schema design causing hotspots.
* Long-running transactions.

These issues require investigation within Cloud Spanner itself.

---
### Question 6:
**Is It a Resource Problem?**

Cloud Spanner uses compute capacity (Nodes or Processing Units).

Imagine your application has:

* 1,000 users today.
* 100,000 users tomorrow.

If compute capacity isn't increased:

```text
Users Increase
       |
       |
Same Resources
       |
       |
Performance Decreases
```

As a DevOps Engineer, you'll monitor resource usage and decide whether scaling is needed.

---
# Why Is This Knowledge Important for Your Project?

Your project's migration flow is:

```text
Oracle Database
       |
       |
    Striim (CDC)
       |
       |
Cloud Spanner
       |
       |
Business Applications
```

Imagine users report:

> "The migrated application is slow."

Without understanding the architecture, you might immediately think:

> "Cloud Spanner is slow."

But the actual problem could be:

* Oracle is slow.
* Striim is lagging.
* Network connectivity is poor.
* IAM permissions are incorrect.
* Cloud Spanner needs more processing units.
* The application is sending inefficient queries.

Architecture helps you identify **the correct component**, reducing troubleshooting time and avoiding unnecessary changes.

---
# DevOps Engineer Perspective

When working with Cloud Spanner, your responsibilities include:

* Monitoring CPU and storage utilization.
* Monitoring latency and request failures.
* Verifying connectivity between applications, Striim, and Cloud Spanner.
* Checking IAM permissions and service accounts.
* Scaling resources when demand increases.
* Supporting incident investigations.
* Working with application and database teams to identify root causes.

You don't need to know every SQL query, but you **do need to understand how all the components interact** so you can quickly determine where a problem lies.

---
# Summary:
By understanding Cloud Spanner Architecture, you will be able to:

* Understand how requests travel from the application to the database.
* Identify which component is causing an issue.
* Troubleshoot production incidents more effectively.
* Communicate confidently with developers, DBAs, and cloud engineers.
* Perform your DevOps responsibilities with a clear understanding of the entire system.

---
# 2.2 What is Cloud Spanner Architecture?

Cloud Spanner Architecture is the internal design of how Cloud Spanner stores data, processes requests, replicates data, and serves millions of users while maintaining high availability and strong consistency.

In simple words:

> Architecture explains **how Cloud Spanner works internally**.

---
# 2.3 High-Level Architecture:

Imagine your company has an application.

```text
                Customer
                    |
                    |
             Web / Mobile App
                    |
                    |
            Backend Application
                    |
                    |
          Cloud Spanner Database
```

The application never talks directly to the storage disks.

Instead, Cloud Spanner manages everything between the application and the stored data.

---
# 2.4 Complete Architecture Overview

```text
                     Users
                       |
        -------------------------------
        |             |              |
      Web App      Mobile App      API
        |             |              |
        -------- Backend Service -----
                    |
                    |
        ===============================
        |        Cloud Spanner        |
        ===============================
        |                             |
        |   Compute Layer             |
        |                             |
        |-----------------------------|
        |                             |
        |   Storage Layer             |
        |                             |
        ===============================
                    |
               Persistent Storage
```

Cloud Spanner has two major layers:

1. Compute Layer
2. Storage Layer

These two layers work together.

---
# 2.5 Main Components

Cloud Spanner architecture consists of several important components:

```text
Application
      |
      |
Cloud Spanner
      |
      +---- Compute Layer
      |
      +---- Storage Layer
      |
      +---- Replication
      |
      +---- Transactions
```

We will learn each one.

---
# 2.6 Component 1 – Client/Application

Everything starts with the application.

Example:

A banking application.

Customer performs:

* Login
* View balance
* Transfer money
* Pay bills

Example request:

```text
Customer clicks "Transfer ₹500"
```

The application sends a SQL request to Cloud Spanner.

Example:

```sql
UPDATE Accounts
SET Balance = Balance - 500
WHERE AccountID = 1001;
```

Cloud Spanner receives this request.

---
# 2.7 Component 2 – Compute Layer

This is the "brain" of Cloud Spanner.

The Compute Layer is responsible for:

* Receiving SQL requests
* Parsing SQL
* Validating SQL
* Executing queries
* Coordinating transactions
* Communicating with storage

Think of it like a restaurant waiter.

Customer:

"I want Biryani."

Waiter:

* Takes the order
* Sends it to the kitchen
* Brings the food back

The waiter doesn't cook the food.

Similarly,

The Compute Layer does **not** permanently store data.

It processes requests.

---
# 2.8 Component 3 – Storage Layer

The Storage Layer stores the actual data.

Example:

```text
Employee Table

101 Rahul
102 Priya
103 David
```

This information lives in the Storage Layer.

Responsibilities include:

* Store rows
* Read rows
* Update rows
* Delete rows
* Maintain indexes
* Replicate data

---
# 2.9 Component 4 – Replication

Replication means keeping multiple copies of the same data.

Example:

```text
Region A
    |
    |---- Copy 1

Region B
    |
    |---- Copy 2

Region C
    |
    |---- Copy 3
```

If one region becomes unavailable, the others continue serving requests.

This provides:

* High Availability
* Fault Tolerance
* Disaster Recovery

---
# 2.10 Component 5 – Transactions

A transaction is a group of operations that succeed or fail together.

Example:

Transfer ₹500.

```text
Account A

1000
```

After transfer:

```text
Account A

500
```

Account B:

```text
500
```

Both updates must happen together.

Cloud Spanner guarantees this using ACID transactions.

---
# 2.11 Request Flow

Let's see what happens when a user places an order.

```text
Step 1
Customer clicks "Buy Now"

↓

Step 2
Application receives request

↓

Step 3
Application sends SQL

↓

Step 4
Cloud Spanner Compute Layer receives SQL

↓

Step 5
Compute Layer checks transaction

↓

Step 6
Storage Layer updates data

↓

Step 7
Replication occurs

↓

Step 8
Success response returned
```

This entire process usually completes in milliseconds.

---
# 2.12 Regional vs Multi-Regional Architecture

### Regional

```text
Delhi Region

Cloud Spanner
```

Data stays in one region.

Advantages:

* Lower latency
* Lower cost

Best for:

* Applications serving one geographic region.

---

### Multi-Regional

```text
Delhi
     |
Mumbai
     |
Singapore
```

Copies of the data are maintained across multiple regions.

Advantages:

* Higher availability
* Better disaster recovery
* Global applications

---
# 2.13 How This Fits Your Project

Your migration architecture might look like this:

```text
          Oracle Database
                 |
         Change Data Capture (CDC)
                 |
               Striim
                 |
        Data Transformation
                 |
          Cloud Spanner
                 |
        Business Applications
```

### What each component does

**Oracle**

* Source database
* Existing production data

**Striim**

* Reads ongoing changes (Insert, Update, Delete)
* Transforms data if needed
* Sends data to Cloud Spanner

**Cloud Spanner**

* Stores migrated data
* Serves application requests
* Replicates data automatically

---
# 2.14 DevOps Engineer Responsibilities

As a DevOps Engineer, you usually won't design the database schema, but you'll support the platform that runs it.

Typical responsibilities include:

### Infrastructure

* Create Cloud Spanner instances.
* Choose regional or multi-regional configuration.
* Configure processing units or nodes.

### Security

* Configure IAM roles.
* Manage service accounts.
* Secure access from applications and Striim.

### Networking

* Verify connectivity between applications, Striim, and Cloud Spanner.
* Troubleshoot firewall or VPC issues if applicable.

### Monitoring

* Monitor CPU utilization.
* Monitor storage growth.
* Track query latency.
* Configure alerts using Cloud Monitoring and Datadog.

### Automation

* Provision Cloud Spanner with Terraform.
* Integrate infrastructure changes into Harness or other CI/CD pipelines.

### Migration Support

* Validate Oracle-to-Spanner connectivity.
* Assist with migration testing.
* Help troubleshoot CDC pipeline failures.

---
# 2.15 Best Practices

* Choose the correct regional configuration based on business needs.
* Use IAM with the principle of least privilege.
* Monitor resource utilization before scaling.
* Test disaster recovery procedures.
* Automate infrastructure creation using Terraform.
* Enable monitoring and alerting before production go-live.

---
# Module Summary

After this module, you should understand:

* What Cloud Spanner Architecture is.
* The difference between the Compute Layer and the Storage Layer.
* How a request flows through Cloud Spanner.
* Why replication is important.
* The difference between regional and multi-regional deployments.
* How Cloud Spanner fits into your Oracle → Striim → Cloud Spanner migration.
* What a DevOps Engineer typically manages around Cloud Spanner.

---
