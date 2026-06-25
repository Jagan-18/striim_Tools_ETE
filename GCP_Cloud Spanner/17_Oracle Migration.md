# Topic 17: Oracle to Cloud Spanner Migration:

This is a very large topic. In professional training, it typically spans several days because it combines database migration, cloud architecture, DevOps, and production operations.

We'll cover:

1. What is Database Migration?
2. Why migrate from Oracle to Cloud Spanner?
3. Migration challenges
4. Migration phases
5. End-to-end architecture
6. Schema migration
7. Data migration
8. CDC (Change Data Capture)
9. Striim integration
10. Validation
11. Cutover
12. Go-Live
13. Rollback
14. DevOps responsibilities
15. Production support
16. Troubleshooting
17. Best practices
18. Interview questions

---
# Chapter 1: What is Database Migration?

## Definition

Database migration is the process of moving data, database objects, and applications from one database platform to another while preserving data integrity and minimizing downtime.

In your project:

```text
Source Database
        │
        ▼
Oracle
        │
        ▼
Cloud Spanner
```

---
# Real-Life Example:

Suppose a bank has been using Oracle for 20 years.

The database contains:

* Customers
* Accounts
* Transactions
* Loans
* Credit Cards

The company decides to modernize its platform and move to Google Cloud.

They migrate from Oracle to Cloud Spanner.

---

# Chapter 2: Why Do Companies Migrate?

Common reasons include:

### 1. Infrastructure Management

Oracle often requires teams to manage:

* Servers
* Storage
* Patching
* High availability
* Disaster recovery

Cloud Spanner is a managed service, reducing operational overhead.

---

### 2. Scalability

Oracle scaling can require significant planning.

Cloud Spanner allows compute capacity to be increased more easily.

---

### 3. High Availability

Cloud Spanner provides built-in replication and high availability features.

---

### 4. Global Applications

Cloud Spanner supports applications serving users across multiple regions.

---

### 5. Reduced Operational Burden

Google manages much of the underlying infrastructure, allowing teams to focus more on applications.

---

# Chapter 3: Oracle vs Cloud Spanner

| Oracle                                  | Cloud Spanner                           |
| --------------------------------------- | --------------------------------------- |
| Self-managed or managed by organization | Fully managed service                   |
| Traditional relational database         | Distributed relational database         |
| Manual infrastructure tasks             | Managed infrastructure                  |
| Manual scaling in many deployments      | Online scaling of compute capacity      |
| Manual HA/DR design                     | Built-in high availability capabilities |

---

# Chapter 4: Migration Architecture

This is similar to what you'll see in your project.

```text
               Oracle Database
                     │
                     │
             Initial Data Read
                     │
                     ▼
                Striim Platform
                     │
          ┌──────────┴──────────┐
          │                     │
      Initial Load         CDC (Ongoing Changes)
          │                     │
          └──────────┬──────────┘
                     ▼
              Cloud Spanner
                     │
                     ▼
               Application
```

---

# Chapter 5: Migration Phases

A professional migration is performed in stages.

```text
Assessment

↓

Planning

↓

Schema Migration

↓

Initial Data Load

↓

CDC Synchronization

↓

Validation

↓

Cutover

↓

Go-Live

↓

Post-Go-Live Monitoring
```

Let's discuss each phase.

---

# Phase 1: Assessment

Before moving anything, the team analyzes the Oracle database.

Typical questions:

* How many databases?
* How many tables?
* Database size?
* Number of rows?
* Primary keys?
* Indexes?
* Constraints?
* Stored procedures?
* Triggers?
* Performance requirements?

Example:

```text
Oracle

Tables = 350

Rows = 500 Million

Size = 2 TB
```

---

# Phase 2: Planning

Migration strategy is defined.

Questions include:

* Full migration or phased migration?
* Downtime requirements?
* Rollback plan?
* Validation approach?
* Target architecture?
* Timeline?

---

# Phase 3: Schema Migration

Cloud Spanner needs the target schema before loading data.

Tasks:

* Analyze Oracle schema.
* Convert unsupported data types if necessary.
* Review primary keys.
* Review indexes.
* Review relationships.
* Create Cloud Spanner schema.

Example:

```text
Oracle Schema

↓

Convert

↓

Cloud Spanner Schema
```

---

# Phase 4: Initial Data Load

Historical data is copied.

```text
Oracle

↓

Export

↓

Striim

↓

Cloud Spanner
```

This loads existing data.

---

# Phase 5: Change Data Capture (CDC)

While the initial load runs, Oracle continues receiving new transactions.

Example:

```text
Customer Creates New Order
```

That change must also reach Cloud Spanner.

Flow:

```text
Oracle

↓

Redo Logs

↓

Striim

↓

Cloud Spanner
```

This keeps the target database synchronized until cutover.

---

# Phase 6: Validation

Before go-live, verify that Oracle and Cloud Spanner contain equivalent data.

Typical checks:

* Row counts
* Sample data
* Primary keys
* Indexes
* Constraints (where applicable)
* Application functionality

Example:

```sql
SELECT COUNT(*)
FROM Customer;
```

Run in both Oracle and Cloud Spanner.

---

# Phase 7: Cutover

Cutover is when production traffic moves to Cloud Spanner.

```text
Before

Application

↓

Oracle


After

Application

↓

Cloud Spanner
```

This is one of the highest-risk stages.

---

# Phase 8: Go-Live

After cutover:

Monitor continuously.

Watch:

* CPU
* Read latency
* Write latency
* Transaction errors
* CDC status
* Application health
* User issues

---

# Phase 9: Rollback

Suppose major issues occur.

Example:

```text
Application Errors

↓

Business Impact
```

Decision:

```text
Cloud Spanner

↓

Rollback

↓

Oracle
```

Rollback planning should exist before the migration starts.

---

# Chapter 6: DevOps Engineer Responsibilities (Most Important)

This is the part that directly relates to your job.

## Before Migration

You help prepare the environment.

Typical tasks:

* Create Cloud Spanner instance
* Create databases
* Configure IAM
* Create service accounts
* Configure networking
* Configure monitoring
* Configure Datadog dashboards
* Prepare Harness pipelines
* Validate Terraform deployments

Checklist:

```text
Cloud Spanner ✓

IAM ✓

Networking ✓

Monitoring ✓

Datadog ✓

Harness ✓
```

---

## During Schema Deployment

Typical CI/CD flow:

```text
Developer

↓

Git

↓

Harness

↓

Deploy Schema

↓

Cloud Spanner
```

You ensure:

* Schema deployed successfully
* Tables created
* Indexes created
* Primary keys correct

---

## During Initial Data Load

Monitor:

* Striim health
* Throughput
* Errors
* CPU
* Storage
* Cloud Spanner latency

---

## During CDC

Watch:

```text
Oracle

↓

Redo Logs

↓

Striim

↓

Cloud Spanner
```

Key metrics:

* CDC lag
* Replication failures
* Error logs
* Throughput

---

## During Validation

Run SQL checks.

Examples:

```sql
SELECT COUNT(*) FROM Customer;
```

```sql
SELECT COUNT(*) FROM Orders;
```

Verify row counts match expectations.

---

## During Cutover

Checklist:

* Backup available
* PITR configured
* Dashboards healthy
* Alerts enabled
* Application tested
* Stakeholders informed

---

## After Go-Live

Monitor:

* CPU
* Storage
* Transactions
* Query latency
* Read latency
* Write latency
* Application errors
* Striim status (if still in use)
* User feedback

---

# Chapter 7: Production Scenarios

### Scenario 1: CDC Lag Increases

Possible causes:

* High Oracle activity
* Striim bottleneck
* Cloud Spanner under heavy load
* Network issue

Actions:

* Check Striim dashboards.
* Review Cloud Spanner metrics.
* Check logs.

---

### Scenario 2: Row Count Mismatch

Possible causes:

* Migration interrupted
* Filter configuration error
* Failed CDC events

Actions:

* Compare logs.
* Re-run validation.
* Identify missing records.

---

### Scenario 3: Application Slow After Go-Live

Possible causes:

* Missing indexes
* Inefficient SQL
* High CPU
* Transaction contention

Actions:

* Review monitoring dashboards.
* Analyze queries.
* Scale only if needed.

---

# Chapter 8: Troubleshooting Workflow

```text
Alert

↓

Check Datadog

↓

Check Cloud Monitoring

↓

Check Striim

↓

Check Cloud Logging

↓

Identify Root Cause

↓

Fix

↓

Validate

↓

Monitor
```

---

# Chapter 9: Best Practices

✔ Perform detailed assessment before migration.

✔ Validate schema before loading data.

✔ Test migration in lower environments.

✔ Monitor every phase.

✔ Automate deployments.

✔ Have a rollback plan.

✔ Validate before cutover.

✔ Communicate with stakeholders during go-live.

---

# Chapter 10: Common Mistakes

❌ Migrating without validation.

❌ Ignoring CDC lag.

❌ Missing backups.

❌ No rollback plan.

❌ Not monitoring after go-live.

❌ Assuming Oracle indexes should all be copied directly.

---
### Q1. What are the major phases of an Oracle to Cloud Spanner migration?

**Answer:**

* Assessment
* Planning
* Schema migration
* Initial data load
* CDC synchronization
* Validation
* Cutover
* Go-Live
* Post-Go-Live monitoring

---
### Q2. Why is CDC required?

**Answer:**

CDC captures changes made to Oracle after the initial data load, allowing Cloud Spanner to stay synchronized until cutover.

---
### Q3. What should be validated before cutover?

**Answer:**

* Schema
* Row counts
* Sample data
* Application functionality
* Monitoring
* Backups
* Alerts
* IAM
* Connectivity

---
### Q4. What is the DevOps Engineer's role?

**Answer:**

A DevOps engineer provisions infrastructure, deploys schemas through CI/CD, configures IAM, monitoring, and dashboards, monitors Striim and Cloud Spanner during migration, validates deployments, supports cutover, and assists with production troubleshooting.

---
### Q5. What is the difference between Initial Load and CDC?

| Initial Load                    | CDC                                               |
| ------------------------------- | ------------------------------------------------- |
| Copies existing historical data | Replicates ongoing changes after the initial load |
| One-time process                | Continuous until cutover                          |

---
# Final Summary:

```text
Oracle Database
      │
      ▼
Assessment
      │
      ▼
Planning
      │
      ▼
Schema Migration
      │
      ▼
Initial Data Load
      │
      ▼
CDC (Striim)
      │
      ▼
Validation
      │
      ▼
Cutover
      │
      ▼
Go-Live
      │
      ▼
Monitoring
```

## Most Important DevOps Takeaways:
For **your project**, your day-to-day work is likely to include:

* Provisioning Cloud Spanner
* Managing IAM and service accounts
* Deploying schema changes via Harness
* Monitoring Striim pipelines
* Watching CDC lag and Cloud Spanner metrics
* Validating migrated data
* Supporting production cutovers
* Responding to alerts from Datadog and Google Cloud Monitoring
* Troubleshooting migration and performance issues

---

