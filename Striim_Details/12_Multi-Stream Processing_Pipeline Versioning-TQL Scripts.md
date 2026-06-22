# Multi-Stream Processing in Striim:
## What is Multi-Stream Processing?

**Multi-Stream Processing** means processing and combining data from multiple streams simultaneously within a Striim application.

Instead of:

```text
One Source
    |
One Stream
    |
One Target
```

you process:

```text
Multiple Sources
       |
Multiple Streams
       |
Join / Aggregate / Transform
       |
Target
```

---
# Why Use Multi-Stream Processing?
Common use cases:

✅ Customer + Orders Join

✅ Orders + Payments Correlation

✅ Real-Time Analytics

✅ Fraud Detection

✅ Data Enrichment

✅ Multiple CDC Sources

---
# Architecture:
```text
Oracle Customer Table
          |
          v
   CustomerStream

Oracle Order Table
          |
          v
    OrderStream

          |
          v

      Join CQ
          |
          v

 AnalyticsStream
          |
          v

     BigQuery
```

---
# Example 1: Customer + Orders:
### Stream 1

```text
CustomerStream

CUSTOMER_ID
CUSTOMER_NAME
CITY
```

### Stream 2:
```text
OrderStream

ORDER_ID
CUSTOMER_ID
AMOUNT
```

---
## Join Streams
```sql
CREATE CQ CustomerOrders
INSERT INTO CustomerOrderStream
SELECT
C.CUSTOMER_ID,
C.CUSTOMER_NAME,
O.ORDER_ID,
O.AMOUNT
FROM CustomerStream C
JOIN OrderStream O
ON C.CUSTOMER_ID = O.CUSTOMER_ID;
```

---
### Output:
```text
1001 | Jagadeesh | ORD101 | 1500
1002 | Sai       | ORD102 | 2000
```

---
# Example 2: Orders + Payments:
```text
OrderStream
      +
PaymentStream
```

---
### Order Stream:
```text
ORDER_ID
AMOUNT
```

### Payment Stream:
```text
ORDER_ID
PAYMENT_STATUS
```

---
### TQL

```sql
CREATE CQ OrderPaymentCQ
INSERT INTO PaymentAnalytics
SELECT
O.ORDER_ID,
O.AMOUNT,
P.PAYMENT_STATUS
FROM OrderStream O
JOIN PaymentStream P
ON O.ORDER_ID = P.ORDER_ID;
```

---
# Example 3: Three-Stream Join

```text
CustomerStream
       +
OrderStream
       +
ProductStream
```

---

```sql
CREATE CQ AnalyticsCQ
INSERT INTO AnalyticsStream
SELECT
C.CUSTOMER_NAME,
O.ORDER_ID,
P.PRODUCT_NAME,
O.AMOUNT
FROM CustomerStream C
JOIN OrderStream O
ON C.CUSTOMER_ID = O.CUSTOMER_ID
JOIN ProductStream P
ON O.PRODUCT_ID = P.PRODUCT_ID;
```

---
# Multi-Source CDC Processing:
Real-world example:

```text
Oracle
   |
Customer CDC
   |
CustomerStream

SQL Server
     |
Order CDC
     |
OrderStream

     |
     v

Join CQ
     |
BigQuery
```

---
# Data Enrichment Example:
### Source Stream

```text
OrderStream

ORDER_ID
CUSTOMER_ID
AMOUNT
```

### Reference Stream

```text
CustomerStream

CUSTOMER_ID
CUSTOMER_NAME
```

---
### Enrichment Query:
```sql
CREATE CQ EnrichedOrders
INSERT INTO AnalyticsStream
SELECT
O.ORDER_ID,
O.AMOUNT,
C.CUSTOMER_NAME
FROM OrderStream O
JOIN CustomerStream C
ON O.CUSTOMER_ID = C.CUSTOMER_ID;
```

---
# Multi-Stream Aggregation:
Calculate sales by customer.

```sql
CREATE CQ CustomerSales
INSERT INTO SalesSummary
SELECT
CUSTOMER_ID,
SUM(AMOUNT) AS TOTAL_SALES
FROM OrderStream
GROUP BY CUSTOMER_ID;
```

---
# Multi-Stream with Windowing:
Suppose events arrive continuously.

### Last 5 Minutes

```sql
CREATE CQ SalesWindow
INSERT INTO WindowSummary
SELECT
COUNT(*) AS TOTAL_ORDERS,
SUM(AMOUNT) AS SALES
FROM OrderStream
GROUP BY TIME_WINDOW(MINUTE,5);
```

---
# Multi-Target Processing:
One stream can feed multiple targets.

```text
CustomerStream
      |
      +------> Kafka
      |
      +------> BigQuery
      |
      +------> Snowflake
```

---
### TQL:
```sql
CREATE TARGET KafkaTarget
USING KafkaWriter(...)
INPUT FROM CustomerStream;

CREATE TARGET BigQueryTarget
USING BigQueryWriter(...)
INPUT FROM CustomerStream;

CREATE TARGET SnowflakeTarget
USING SnowflakeWriter(...)
INPUT FROM CustomerStream;
```

---
# Real-Time Fraud Detection

```text
TransactionStream
        +
CustomerStream
```

---
### Query:
```sql
CREATE CQ FraudDetection
INSERT INTO FraudStream
SELECT
T.TRANSACTION_ID,
C.CUSTOMER_NAME,
T.AMOUNT
FROM TransactionStream T
JOIN CustomerStream C
ON T.CUSTOMER_ID=C.CUSTOMER_ID
WHERE T.AMOUNT > 100000;
```

---
# Enterprise Architecture:
```text
Oracle CDC
     |
CustomerStream

SQL Server CDC
      |
OrderStream

Kafka Events
     |
PaymentStream

      |
      v

 Multi-Stream CQ
      |
      v

 AnalyticsStream
      |
      +------> Kafka
      |
      +------> BigQuery
      |
      +------> Snowflake
```

---
# Performance Best Practices:
### Use Filters Before Joins

Good:

```sql
SELECT *
FROM OrderStream
WHERE AMOUNT > 10000
```

Then join.

This reduces processing load.

---
### Avoid SELECT *
Bad:

```sql
SELECT *
```

Good:

```sql
SELECT
ORDER_ID,
CUSTOMER_ID,
AMOUNT
```

---
### Use Proper Join Keys:
Good:

```sql
ON CUSTOMER_ID
```

Avoid joining on non-unique fields where possible.

---
### Monitor CQ Performance:
Track:

```text
Events/sec
Latency
CDC Lag
Memory Usage
```

through the Striim dashboard.

---
### What is Multi-Stream Processing?
Multi-stream processing is the ability to ingest, process, join, enrich, and aggregate data from multiple streams simultaneously within a Striim application.

---
### Why is Multi-Stream Processing used?
* Data enrichment
* Real-time analytics
* Event correlation
* Fraud detection
* Cross-system integration

---
### Can Striim join multiple streams?
Yes. Striim supports joining two or more streams using Continuous Queries (CQ) and TQL.

Example:
```sql
FROM CustomerStream C
JOIN OrderStream O
ON C.CUSTOMER_ID = O.CUSTOMER_ID
```
---
> Multi-stream processing in Striim allows data from multiple sources and streams to be processed simultaneously. Using Continuous Queries (CQ), streams can be joined, enriched, filtered, aggregated, and routed to one or more targets. This capability is commonly used for customer-order correlation, fraud detection, real-time analytics, and event-driven architectures involving Oracle, SQL Server, Kafka, BigQuery, and Snowflake.

---
# Pipeline Versioning & TQL Scripts in Striim:
Pipeline Versioning is the practice of storing and managing **TQL scripts** in a version control system such as GitHub, GitLab, or Bitbucket.

This helps teams:

* Track changes
* Collaborate safely
* Roll back to previous versions
* Maintain audit history
* Support CI/CD deployments

---
# Why Version Control TQL?
Without Git:

```text
Developer-1 changes TQL
       |
Developer-2 changes TQL
       |
Previous version lost
```

With Git:

```text
TQL Script
     |
Git Repository
     |
Version History
     |
Rollback Possible
```

**Benefits:**
✅ Change tracking

✅ Collaboration

✅ Rollback capability

✅ Audit compliance

✅ CI/CD integration

---
# Recommended Repository Structure:
```text
striim-pipelines/
│
├── apps/
│   ├── oracle-bigquery/
│   │   ├── source.tql
│   │   ├── cq.tql
│   │   ├── target.tql
│   │   └── deploy.tql
│   │
│   └── mysql-kafka/
│
├── environments/
│   ├── dev.properties
│   ├── qa.properties
│   └── prod.properties
│
└── README.md
```

---
# Example TQL Files:
### source.tql

```sql
CREATE SOURCE OracleSource
USING OracleReader (
 ConnectionURL:'jdbc:oracle:thin:@host:1521:ORCL',
 Username:'user',
 Password:'password'
)
OUTPUT TO EmployeeStream;
```

---
### cq.tql;
```sql
CREATE CQ EmployeeCQ
INSERT INTO FilteredStream
SELECT *
FROM EmployeeStream
WHERE SALARY > 50000;
```

---
### target.tql;
```sql
CREATE TARGET BigQueryTarget
USING BigQueryWriter (
 ProjectID:'gcp-project',
 Dataset:'employee'
)
INPUT FROM FilteredStream;
```

---
# Git Workflow;
## Clone Repository

```bash
git clone https://github.com/company/striim-pipelines.git
```

---

## Create Feature Branch

```bash
git checkout -b feature/add-new-cq
```

---
## Modify TQL:
Example:

```sql
WHERE SALARY > 60000
```

instead of:

```sql
WHERE SALARY > 50000
```

---
## Commit Changes:
```bash
git add .

git commit -m "Updated salary filter threshold"
```

---
## Push Branch

```bash
git push origin feature/add-new-cq
```

---
## Create Pull Request:
```text
feature/add-new-cq
        |
        v
Develop Branch
```

Reviewers verify:

* TQL syntax
* Performance impact
* Business logic

---
# Branch Strategy Example:
Since you're familiar with DevOps and GitHub branch protection:

```text
main
 |
release
 |
stage
 |
develop
 |
feature/*
```

### develop:
Used for:

```text
New CQ
New Source
New Target
```

---
### stage:
Used for:

```text
Testing
Validation
```

---
### release:
Used for:

```text
Pre-production deployment
```

---
### main:
Used for:

```text
Production TQL scripts
```

---
# Versioning Example:
### Version 1

```sql
WHERE SALARY > 50000
```

---
### Version 2:
```sql
WHERE SALARY > 60000
```

Git history:

```bash
git log
```

Output:

```text
v1.0 Initial CQ

v1.1 Updated salary filter

v1.2 Added aggregation
```

---
# Environment-Specific Configuration:
Avoid hardcoding values.

Bad:

```sql
ProjectID:'prod-project'
```

Good:

```sql
ProjectID:'${PROJECT_ID}'
```

Dev:

```text
PROJECT_ID=dev-project
```

Prod:

```text
PROJECT_ID=prod-project
```

---
# CI/CD for Striim Pipelines:
Typical DevOps Flow:

```text
Developer
    |
Git Push
    |
GitHub
    |
Jenkins/Harness
    |
Validate TQL
    |
Deploy to Striim
```

Example:

```text
GitHub
   |
Jenkins
   |
Striim API
   |
Deploy Application
```

---
# Backup Strategy:
Store:

```text
TQL Scripts
Application Definitions
Environment Files
Custom UDF JARs
```

inside Git repositories.

---
# Best Practices:
### One Application Per Folder

```text
oracle-bigquery/
mysql-kafka/
oracle-snowflake/
```

---
### Use Meaningful Commit Messages:
Good:

```text
Added Oracle CDC source

Added Customer aggregation CQ

Updated Kafka target configuration
```

Bad:

```text
changes

fix
```

---
### Use Pull Requests:
Before merging:

✔ Code Review

✔ TQL Validation

✔ Performance Review

---
### Tag Releases:
```bash
git tag v1.0

git tag v2.0
```

Useful for production rollbacks.

---

### Separate Environments

```text
dev
qa
stage
prod
```

Never deploy development scripts directly to production.

---
### Why store TQL scripts in Git?
To:
* Track changes
* Enable collaboration
* Support rollback
* Maintain version history
* Integrate with CI/CD

---
### How do you version-control Striim pipelines?
Store all TQL scripts, application definitions, configuration files, and UDF JARs in Git repositories using feature branches, pull requests, release tags, and environment-specific configurations.

---
### What are the benefits of Git-based pipeline management?
* Auditability
* Change tracking
* Team collaboration
* Rollback support
* Automated deployments

---
> Pipeline versioning in Striim is typically achieved by storing TQL scripts, configuration files, and UDF artifacts in Git repositories. Teams use branching strategies, pull requests, tags, and CI/CD pipelines to manage changes, review updates, track history, and automate deployments across development, testing, and production environments.**
