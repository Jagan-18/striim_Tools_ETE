# CDC (Change Data Capture) – DevOps Engineer's Perspective

## First Understand One Thing

Suppose your company has:

```text
Oracle Database (Production)

↓

Millions of Transactions Every Day
```

The business decides to migrate to Cloud Spanner.

Question:

**Can we stop Oracle for 3 days to copy all data?**

Answer:

**No.**

Because:

* Customers are making payments.
* New orders are coming.
* Employees are updating records.
* ATM transactions are happening.
* New users are registering.

The source database is continuously changing.

This is where CDC comes in.

---

# What is CDC?

CDC captures only the changes made in the source database and sends those changes to the target database.

Instead of copying the whole database every time:

```text
Oracle

↓

INSERT

UPDATE

DELETE

↓

Striim

↓

Cloud Spanner
```

Only the changes are replicated.

---

# Why is CDC Important for DevOps?

Because after deployment, your job is to ensure:

* CDC pipeline is running.
* No replication lag.
* No data loss.
* No failed events.
* Pipeline recovers after failures.
* Alerts are working.

---

# Your Project Architecture

```text
                  Oracle Database
                         │
                  Redo Logs Generated
                         │
                         ▼
                     Striim
             (Reads Oracle Changes)
                         │
                 Transform (if needed)
                         │
                         ▼
                  Cloud Spanner
                         │
                         ▼
                  Application Reads Data
```

As a DevOps Engineer, you don't usually write the Oracle queries that generate data—you make sure this entire pipeline stays healthy.

---

# DevOps Responsibilities Before Migration

Before the migration starts, your work may include:

### 1. Infrastructure Preparation

Create:

* Cloud Spanner Instance
* Cloud Spanner Database
* Service Accounts
* IAM Roles
* Networking
* Monitoring
* Alerting

---

### 2. Verify Connectivity

Confirm:

```text
Oracle

↓

Striim

↓

Cloud Spanner
```

Questions you'll verify:

* Can Striim connect to Oracle?
* Can Striim connect to Cloud Spanner?
* Are firewall rules correct?
* Are credentials valid?
* Are IAM permissions correct?

---

### 3. Configure Monitoring

Set up dashboards for:

* CDC Lag
* Pipeline Status
* CPU
* Memory (for Striim hosts)
* Cloud Spanner CPU
* Read/Write Latency
* Error Count

---

# During Initial Data Load

Your responsibilities include monitoring:

```text
Oracle

↓

Initial Load

↓

Striim

↓

Cloud Spanner
```

Watch for:

* Is the pipeline running?
* Is throughput normal?
* Any failed records?
* Any connection failures?
* Any CPU spikes?
* Any storage issues?

---

# During CDC Replication

This is where you'll spend most of your time.

Pipeline:

```text
Oracle

↓

New Transaction

↓

Redo Log

↓

Striim Reads

↓

Cloud Spanner Updated
```

You continuously monitor whether this process is healthy.

---

# Daily DevOps Checklist

Every morning:

```text
Login

↓

Check Datadog

↓

Check Cloud Monitoring

↓

Check Striim Dashboard

↓

Check CDC Lag

↓

Check Errors

↓

Check Cloud Spanner

↓

Health OK?
```

Typical questions:

* Is the pipeline UP?
* Is CDC running?
* Any replication failures?
* Any disconnected sources?
* Any disconnected targets?
* Any alert overnight?

---

# Important Metrics to Monitor

## 1. CDC Lag ⭐⭐⭐⭐⭐

Most important metric.

Suppose:

```text
Oracle

10:00 AM

↓

Cloud Spanner

10:00 AM
```

Lag = 0 seconds.

Healthy.

Now:

```text
Oracle

10:00 AM

↓

Cloud Spanner

10:08 AM
```

Lag = 8 minutes.

Problem.

---

## 2. Pipeline Status

Always verify:

```text
Running ✅

Stopped ❌

Paused ⚠

Failed ❌
```

---

## 3. Replication Throughput

Example:

```text
1000 Events/sec
```

If it suddenly drops to:

```text
10 Events/sec
```

Investigate.

Possible causes:

* Network issue
* Oracle issue
* Striim issue
* Cloud Spanner issue

---

## 4. Error Count

Example:

```text
Today's Errors

0
```

Healthy.

```text
Today's Errors

2000
```

Investigate immediately.

---

## 5. CPU Usage

Monitor:

* Striim Server
* Cloud Spanner
* Supporting infrastructure

If CPU remains high:

* Review workload
* Review pipeline health
* Check for slow operations

---

## 6. Cloud Spanner Metrics

Monitor:

* Read Latency
* Write Latency
* Commit Latency
* CPU
* Storage
* Transaction Aborts

---

# Common Production Issues

## Scenario 1

Alert:

```text
CDC Lag = 20 Minutes
```

What should you do?

Step 1

Check:

```text
Is Striim Running?
```

If No

Restart after understanding why it stopped.

---

Step 2

If Striim is running,

Check:

```text
Oracle Connection
```

Is Oracle reachable?

---

Step 3

Check:

```text
Cloud Spanner Connection
```

Can Striim still write?

---

Step 4

Check:

```text
Network

Firewall

VPN

Latency
```

---

Step 5

Check logs.

Identify:

* Authentication failures
* Connection timeouts
* Permission issues
* Pipeline exceptions

---

# Scenario 2

Pipeline Stopped

Possible reasons:

* Oracle maintenance
* Invalid credentials
* Expired password
* Service account issue
* Network failure

DevOps actions:

* Verify the root cause.
* Restore connectivity.
* Restart the pipeline if appropriate.
* Confirm replication resumes.

---

# Scenario 3

Cloud Spanner Write Failure

Possible causes:

* IAM changes
* Permission removed
* Database unavailable
* Schema mismatch

Check:

* Service account
* IAM roles
* Database status
* Cloud Logging

---

# CI/CD Responsibilities

Typical deployment:

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

↓

Start CDC Validation
```

Your tasks:

* Verify deployment success.
* Confirm schema matches expectations.
* Ensure CDC continues after deployment.
* Validate monitoring dashboards.

---

# Go-Live Responsibilities

Migration night checklist:

```text
Backup Completed          ✅

Cloud Spanner Healthy     ✅

Striim Running            ✅

CDC Lag = 0               ✅

Datadog Alerts            ✅

Application Connected     ✅

IAM Verified              ✅

Smoke Test Passed         ✅
```

---

# Tools You'll Use

| Tool                          | Why You'll Use It                                           |
| ----------------------------- | ----------------------------------------------------------- |
| **Striim**                    | Check pipeline health, lag, errors, throughput              |
| **Google Cloud Console**      | Check Cloud Spanner instance, CPU, storage, database status |
| **Datadog**                   | Dashboards, alerts, incident investigation                  |
| **Cloud Monitoring**          | Metrics and alert policies                                  |
| **Cloud Logging**             | Troubleshoot errors and failures                            |
| **Harness**                   | Deploy schema or application changes                        |
| **Terraform**                 | Provision infrastructure and IAM                            |
| **Oracle (read-only access)** | Validate connectivity and assist with troubleshooting       |

---

# Skills You Should Build as a DevOps Engineer

You do **not** need to become an Oracle DBA.

Focus on:

* Understanding the end-to-end CDC flow.
* Monitoring Striim pipelines.
* Understanding Cloud Spanner health.
* Reading logs.
* Investigating alerts.
* IAM and Service Accounts.
* Network connectivity.
* Backup and recovery basics.
* CI/CD integration.
* Production support.

---

# What You Usually DON'T Do

Typically, you won't be responsible for:

* Designing Oracle schemas.
* Writing business SQL queries.
* Developing application code.
* Creating business logic.
* Designing data models.

You'll collaborate with DBAs, developers, and data engineers on those tasks.

---

