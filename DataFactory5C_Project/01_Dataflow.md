Perfect. I understand your situation.

You are a DevOps Engineer, but for this **Data Factory 5C project**, you have **zero knowledge about Data Migration, Oracle, CDC, Striim, and Cloud Spanner**.

So let's do this properly.

**Do NOT learn GCP, Harness, Datadog, or Terraform first.**

We will start from the absolute beginning.

# Topic 1: What is Data Migration? (Start Here)

This is the first topic you must learn.

---

# Imagine a Real-Life Example

Suppose your old phone is Samsung.

You buy a new iPhone.

Now you want to move:

* Contacts
* Photos
* Videos
* WhatsApp Data

from Samsung to iPhone.

This process is called:

### Data Migration

```text
Old Phone
    |
    v
New Phone
```

---

# Database Example

In your project:

```text
Old Database = Oracle

New Database = Cloud Spanner
```

Business wants:

```text
Oracle
    |
    |
    v
Cloud Spanner
```

This movement is called:

## Data Migration

---

# Why Companies Do Data Migration

### Example

Company has:

```text
Oracle Database
```

Problems:

* Expensive
* On-premise servers
* Maintenance cost
* Scaling issues

Company decides:

```text
Move to Cloud
```

Target:

```text
Google Cloud Spanner
```

Benefits:

* Cloud managed
* High availability
* Auto scaling
* Less maintenance

---

# What Data Exists in Database?

Suppose we have a CUSTOMER table.

### Oracle

| ID | NAME  | CITY      |
| -- | ----- | --------- |
| 1  | Reddy | Hyderabad |
| 2  | Ram   | Bangalore |
| 3  | Sai   | Chennai   |

Business wants same data in Cloud Spanner.

### Cloud Spanner

| ID | NAME  | CITY      |
| -- | ----- | --------- |
| 1  | Reddy | Hyderabad |
| 2  | Ram   | Bangalore |
| 3  | Sai   | Chennai   |

This is migration.

---

# The Biggest Problem

Suppose migration takes 10 hours.

During those 10 hours:

Customer enters new record:

```sql
INSERT INTO CUSTOMER
VALUES(4,'Virat','Mumbai');
```

Now Oracle has:

| ID | NAME  |
| -- | ----- |
| 1  | Reddy |
| 2  | Ram   |
| 3  | Sai   |
| 4  | Virat |

But Cloud Spanner still has:

| ID | NAME  |
| -- | ----- |
| 1  | Reddy |
| 2  | Ram   |
| 3  | Sai   |

Missing record.

This is a huge problem.

---

# Solution = CDC

CDC means:

## Change Data Capture

CDC captures:

```text
INSERT
UPDATE
DELETE
```

changes happening in Oracle.

Whenever Oracle changes:

```sql
INSERT INTO CUSTOMER
VALUES(4,'Virat');
```

CDC immediately captures it.

---

# Without CDC

```text
Oracle
   |
Export File
   |
Import File
   |
Cloud Spanner
```

Problems:

* Slow
* Downtime
* Manual work

---

# With CDC

```text
Oracle
   |
Real-Time Changes
   |
CDC
   |
Cloud Spanner
```

Benefits:

* Near real-time
* Low downtime
* Automated

---

# Where Does Striim Come In?

Striim is the tool that performs CDC.

```text
Oracle
   |
   v
Striim
   |
   v
Cloud Spanner
```

Striim continuously watches Oracle.

Whenever Oracle changes:

```sql
INSERT
UPDATE
DELETE
```

Striim captures and sends to Spanner.

---

# Complete Project Flow

This is your entire project in one diagram:

```text
Oracle Database
       |
       |
       v
   Striim CDC
       |
       |
       v
Cloud Spanner
       |
       |
       v
Applications
```

As a DevOps Engineer, everything you do supports this flow.

---

# Your Responsibilities Later

You will eventually manage:

```text
Harness
   |
Deployment
```

```text
Datadog
   |
Monitoring
```

```text
Terraform
   |
Infrastructure
```

But right now forget these.

---

# Important Terms (Memorize)

### Data Migration

Moving data from one database to another.

### Source Database

Database where data currently exists.

Example:

```text
Oracle
```

### Target Database

Database where data will move.

Example:

```text
Cloud Spanner
```

### CDC

Change Data Capture.

Captures:

```text
INSERT
UPDATE
DELETE
```

### Striim

Tool used to perform CDC.

---

# Today's Homework

Make sure you can answer these 5 questions without looking:

1. What is Data Migration?
2. Why do companies migrate databases?
3. What is a Source Database?
4. What is a Target Database?
5. What is CDC and why is it needed?

Once you understand these concepts clearly, tell me:

**"Topic 1 completed"**

Then I will teach **Topic 2: Databases, Tables, Records, Primary Keys, Oracle Basics** from absolute beginner level with diagrams and real project examples. That topic is the foundation before learning Striim.
