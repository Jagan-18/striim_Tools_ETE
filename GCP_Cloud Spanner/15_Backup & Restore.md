# Topic 15: Cloud Spanner Backup & Restore (Complete End-to-End Professional Guide)

# Chapter 1: What is Backup?

## Definition

A **Backup** is a secure copy of your database that can be used later if the original database is lost, damaged, or corrupted.

Think of it like this.

Suppose you have an important photo on your phone.

If the phone is lost,

what happens?

Everything is gone.

Instead,

you upload it to Google Photos.

Now,

even if the phone breaks,

your photo is safe.

Database backups work exactly the same way.

---

# Chapter 2: Why Do We Need Backups?

Imagine this situation.

Production database

```text
Customer Table

10 Million Customers
```

Someone accidentally runs

```sql
DELETE FROM Customer;
```

Without backup

```text
Everything Lost
```

With backup

```text
Restore Backup

↓

Customer Data Returns
```

---

# Real-Life Example

Imagine a company payroll system.

Database contains

* Employee Salary
* Bank Accounts
* Tax Records

One mistake deletes all salary records.

Without backup,

the company cannot pay employees.

With backup,

everything can be recovered.

---

# Chapter 3: What is Restore?

Restore means

Taking a backup

and creating a usable database from it.

Simple definition:

> Restore is the process of recovering data from a backup.

---

# Backup vs Restore

| Backup            | Restore              |
| ----------------- | -------------------- |
| Creates a copy    | Recovers from a copy |
| Preventive action | Recovery action      |
| Done regularly    | Done when needed     |

---

# Chapter 4: Backup Architecture

```text
Application

↓

Cloud Spanner

↓

Backup Storage

↓

Backup Available
```

If disaster happens

```text
Backup

↓

Restore

↓

New Database

↓

Application
```

---

# Chapter 5: Backup Lifecycle

Every backup goes through several stages.

```text
Create Backup

↓

Store Backup

↓

Monitor

↓

Use for Restore

↓

Expire/Delete (based on retention policy)
```

---

# Chapter 6: How Cloud Spanner Backup Works

Suppose you have

```text
Employee Database

↓

Tables

↓

Indexes

↓

Schema

↓

Data
```

When a backup is created,

Cloud Spanner stores:

* Schema
* Tables
* Indexes
* Data

It does **not** simply copy a running database file.

Cloud Spanner creates a managed backup that can later be restored.

---

# Chapter 7: Backup Components

A backup includes

```text
Database

↓

Schema

↓

Tables

↓

Indexes

↓

Data
```

---

# Chapter 8: What Does a Backup NOT Include?

Important point.

Backups generally do **not** include operational configuration such as:

* IAM policies on the project or instance
* Monitoring dashboards
* Alert policies
* CI/CD pipelines
* Terraform state
* Harness pipelines
* Datadog dashboards

Those must be managed separately.

---

# Chapter 9: Backup Types

## Manual Backup

Created by an administrator.

Example

Production deployment

↓

Take backup

↓

Deploy application

---

## Scheduled Backup

Automatically created based on company policy.

Example

```text
Every Day

2:00 AM
```

Many companies automate backup creation.

---

# Chapter 10: Backup Retention

Backups are not kept forever.

Example policy

```text
Daily Backup

↓

Keep 30 Days

↓

Automatically Delete
```

Retention depends on business and compliance requirements.

---

# Chapter 11: Restore Process

Imagine

Production database fails.

```text
Production Database

↓

Backup

↓

Restore

↓

Recovered Database
```

Important:

In Cloud Spanner, a restore creates a **new database** from the backup. It does not overwrite the existing database.

---

# Chapter 12: Restore Workflow

```text
Backup

↓

Restore Request

↓

Cloud Spanner

↓

Create New Database

↓

Validate

↓

Application Testing

↓

Production Ready
```

After restoring, teams typically validate the new database before redirecting applications.

---

# Chapter 13: Point-in-Time Recovery (PITR)

Cloud Spanner also supports **Point-in-Time Recovery (PITR)**.

Imagine this timeline.

```text
10:00

Everything OK

↓

10:30

Accidental Delete

↓

11:00

Issue Discovered
```

Instead of restoring yesterday's backup,

you can recover the database to a point **before** the accidental delete, provided it falls within the configured PITR retention window.

This can significantly reduce data loss compared to relying only on backups.

---

# Chapter 14: Encryption

Backups are encrypted.

Two stages:

## At Rest

Backup storage is encrypted.

## In Transit

Data is encrypted while being transferred during backup and restore operations.

---

# Chapter 15: IAM Security

Not everyone should restore production backups.

Typical permissions:

| User                | Permission                  |
| ------------------- | --------------------------- |
| Developer           | Usually No                  |
| DevOps              | Depending on company policy |
| DBA                 | Usually Yes                 |
| Cloud Administrator | Usually Yes                 |

Companies commonly require approvals before production restores.

---

# Chapter 16: Monitoring Backup Jobs

Monitor:

* Backup success
* Backup failure
* Backup duration
* Backup storage usage
* Restore duration
* Backup retention

Alerts should be configured for failures.

---

# Chapter 17: Oracle → Striim → Cloud Spanner Example

Project

```text
Oracle

↓

Striim

↓

Cloud Spanner
```

Before migration

```text
Take Backup

↓

Start Migration

↓

Validate

↓

Go Live
```

If migration fails

```text
Restore Backup

↓

Retry Migration
```

This provides a recovery option during major changes.

---

# Chapter 18: DevOps Engineer Responsibilities (Very Important)

This is one of your key responsibilities.

---

## 1. Backup Strategy

Help define:

* When backups are taken
* Retention period
* Naming conventions
* Monitoring
* Recovery procedures

---

## 2. Automating Backups

Using automation tools such as:

* Terraform (where applicable)
* CI/CD workflows
* Scheduled jobs
* Google Cloud services

Automate repetitive backup tasks instead of relying on manual execution.

---

## 3. Pre-Deployment Backup

Before production deployment

```text
Backup

↓

Deploy

↓

Validate
```

If deployment fails

```text
Restore

↓

Rollback
```

---

## 4. Go-Live Checklist

Before Go-Live

Confirm:

* Latest backup exists
* Backup completed successfully
* Restore process tested
* Monitoring enabled

---

## 5. Restore Validation

After restoring

Check:

```sql
SELECT COUNT(*)

FROM Employee;
```

Compare row counts.

Verify:

* Tables
* Indexes
* Data
* Application connectivity

---

## 6. Monitoring

Watch:

* Backup failures
* Backup duration
* Storage growth
* Restore jobs
* Alerts

---

## 7. Disaster Recovery (DR)

A DevOps engineer should know:

* Where backups are stored
* How long restore takes (RTO)
* How much data can be lost (RPO)
* Who approves restores
* Recovery runbooks

---

# Chapter 19: Production Scenarios

### Scenario 1

Developer accidentally deletes data.

Solution:

* Evaluate whether PITR can recover the data.
* If not, restore from the appropriate backup.
* Validate recovered data.

---

### Scenario 2

Production deployment fails.

Solution:

* Assess impact.
* Restore or roll back as appropriate.
* Validate applications.

---

### Scenario 3

Migration corrupts data.

Solution:

* Stop migration.
* Restore a clean copy.
* Correct the migration issue.
* Retry.

---

# Chapter 20: Troubleshooting

## Backup Failure

Possible causes:

* Insufficient permissions
* Quota limits
* Service issues

Action:

* Review Cloud Logging.
* Verify IAM.
* Check quotas.

---

## Restore Failure

Possible causes:

* Invalid backup
* Missing permissions
* Target database naming conflicts

Action:

* Verify backup integrity.
* Check IAM roles.
* Review restore logs.

---

## Slow Restore

Possible causes:

* Large database size
* Heavy workload
* Resource constraints

Action:

* Estimate restore time in advance.
* Communicate expected recovery duration.

---

# Chapter 21: Best Practices

✔ Automate backup creation.

✔ Test restores regularly—not just backups.

✔ Monitor backup jobs.

✔ Document recovery procedures.

✔ Keep appropriate retention periods.

✔ Use PITR where it fits business requirements.

✔ Take a backup before major production changes.

---

# Chapter 22: Common Mistakes

❌ Assuming backups have never been tested.

❌ Forgetting to monitor backup failures.

❌ Keeping backups for too short a period.

❌ Allowing too many users to perform restores.

❌ Not documenting recovery steps.

---

# Interview Questions & Answers

### Q1. What is a backup?

**Answer:**
A backup is a secure copy of a database that can be used to recover data if the original database is lost, corrupted, or accidentally modified.

---

### Q2. What is the difference between Backup and Restore?

| Backup                     | Restore                           |
| -------------------------- | --------------------------------- |
| Creates a recoverable copy | Recovers a database from a backup |
| Preventive activity        | Recovery activity                 |

---

### Q3. What is Point-in-Time Recovery (PITR)?

**Answer:**
PITR allows you to restore a database to a specific point in time within the configured recovery window, helping recover from accidental changes without relying only on scheduled backups.

---

### Q4. What are the DevOps responsibilities for Backup & Restore?

**Answer:**

* Define backup strategy
* Automate backups
* Monitor backup jobs
* Validate restores
* Support disaster recovery
* Ensure backups exist before deployments
* Document recovery procedures

---

### Q5. Why should backups be tested?

**Answer:**
A successful backup is only useful if it can be restored. Regular restore testing verifies that recovery procedures work and that the backups are usable.

---

# Real-Time Project Example (Oracle → Striim → Cloud Spanner)

Suppose your migration is scheduled for Saturday night.

```text
Friday
│
├── Verify latest Cloud Spanner backup
├── Confirm PITR configuration
├── Review IAM permissions for restore
└── Notify stakeholders

Saturday
│
├── Start Oracle → Striim migration
├── Monitor replication
├── Validate row counts and application
└── Go Live

If a major issue occurs:
│
├── Stop migration
├── Decide whether PITR or backup restore is appropriate
├── Recover the database
├── Validate data
└── Retry after fixing the root cause
```

---

# Summary

```text
Cloud Spanner Database
        │
        ├── Scheduled Backup
        ├── Manual Backup
        ├── Point-in-Time Recovery
        │
        ▼
Backup Storage
        │
        ▼
Restore (Creates New Database)
        │
        ▼
Validation
        │
        ▼
Production Use
```

## Final Takeaways

* **Backup protects your data; Restore recovers it.**
* Cloud Spanner backups and **Point-in-Time Recovery (PITR)** together provide strong recovery capabilities.
* A DevOps engineer is responsible not just for creating backups, but for **automating, monitoring, testing, documenting, and validating** the entire recovery process.
* In your Oracle → Striim → Cloud Spanner project, backup and recovery planning should be part of every production deployment and migration, not something considered only after a failure.
