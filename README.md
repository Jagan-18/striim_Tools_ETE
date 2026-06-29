Since your project is:

* **Source:** Oracle
* **Target:** Cloud Spanner
* **Migration Tool:** Striim
* **Cloud:** GCP
* **CI/CD:** Harness
* **Monitoring:** Datadog
* **Infrastructure:** Terraform
* **Role:** DevOps Engineer

Your work is **not writing application code**. Your job is to **build, deploy, monitor, automate, troubleshoot, and support the migration platform**.

Below is the complete end-to-end work you can expect in a real project.

---

# Complete DevOps Responsibilities

```text
Project Started
      │
      ▼
Requirement Discussion
      │
      ▼
POC
      │
      ▼
Infrastructure Setup
      │
      ▼
Terraform Deployment
      │
      ▼
GCP Resource Creation
      │
      ▼
Install Striim
      │
      ▼
Configure Oracle CDC
      │
      ▼
Create Cloud Spanner
      │
      ▼
Deploy Migration Pipeline
      │
      ▼
Testing
      │
      ▼
Performance Testing
      │
      ▼
Go-Live
      │
      ▼
Production Monitoring
      │
      ▼
Production Support
      │
      ▼
Enhancements
```

---

# Phase 1 - Requirement Gathering

### Your Lead may ask:

> A new customer wants to migrate Oracle data to Cloud Spanner.

### Your tasks

* Join project meetings.
* Understand the migration architecture.
* Understand the source and target databases.
* Understand the number of databases and tables.
* Understand data volume (GB/TB).
* Identify environments (DEV, QA, UAT, PROD).
* Review the migration plan.

---

# Phase 2 - POC (Proof of Concept)

### Example

The client asks:

> "Can Striim migrate 50 million Oracle records to Cloud Spanner?"

### Your work

* Create a test environment.
* Deploy Striim.
* Configure Oracle connectivity.
* Configure Cloud Spanner connectivity.
* Run a small migration.
* Validate data.
* Measure migration time.
* Prepare a POC report.

---

# Phase 3 - Infrastructure Setup

Usually handled using Terraform.

Example tasks:

* Create VPC
* Create subnet
* Create firewall rules
* Create VM instances
* Create Cloud Storage bucket
* Create Cloud Spanner instance
* Create IAM roles
* Create Service Accounts
* Configure Secrets
* Configure VPN/Interconnect if needed

Example command:

```bash
terraform init

terraform plan

terraform apply
```

---

# Phase 4 - GCP Resource Creation

Your team lead may ask:

> "Create a new Cloud Spanner instance."

Tasks:

* Create instance
* Create database
* Configure IAM
* Enable APIs
* Configure backups
* Configure monitoring

---

# Phase 5 - Install Striim

Typical tasks:

* Install Striim
* Configure cluster
* Configure JVM
* Configure storage
* Configure HA
* Configure security
* Configure SSL

Example:

```text
Server-1

Install Striim

↓

Configure Cluster

↓

Join Cluster

↓

Verify Services
```

---

# Phase 6 - Configure Oracle CDC

Example task:

> Enable CDC.

Your work:

* Verify Oracle archive logs
* Verify supplemental logging
* Verify Oracle user permissions
* Configure Oracle Reader

Example issue:

```
Redo Log Missing
```

Your responsibility:

* Check archive logs
* Contact DBA if required
* Restart pipeline

---

# Phase 7 - Configure Cloud Spanner

Tasks:

* Create tables
* Create schema
* Configure IAM
* Configure users
* Validate connectivity

---

# Phase 8 - Create Striim Pipeline

Example

```
Oracle

↓

Oracle Reader

↓

Transformation

↓

Cloud Spanner Writer
```

Tasks

* Create Source
* Create Target
* Create Application
* Deploy Pipeline
* Validate Pipeline

---

# Phase 9 - CI/CD (Harness)

Typical task:

```
Developer

↓

Git

↓

Harness

↓

Deploy

↓

Striim

↓

Production
```

Tasks

* Create pipeline
* Configure deployment stages
* Configure approval gates
* Configure rollback
* Configure notifications

---

# Phase 10 - Testing

Types of testing:

* Connectivity testing
* CDC testing
* Full load testing
* Incremental testing
* Performance testing
* Failover testing
* Security testing

---

# Phase 11 - Go-Live

Typical activities:

Morning

* Verify Oracle
* Verify Cloud Spanner
* Verify Striim
* Verify Harness
* Verify Datadog

Deployment

* Deploy latest pipeline
* Enable CDC
* Monitor migration
* Validate data

After deployment

* Compare record counts
* Validate sample data
* Obtain customer sign-off

---

# Phase 12 - Production Support

This is where you will spend most of your time.

---

## Example Task 1

Alert:

```
Pipeline Failed
```

Your work:

* Login to Striim
* Check logs
* Restart application
* Validate data flow

---

## Example Task 2

Alert

```
Oracle Connection Failed
```

Check:

* Oracle status
* Network
* Firewall
* Credentials
* Password expiry

---

## Example Task 3

Alert

```
Cloud Spanner Timeout
```

Check:

* CPU
* Sessions
* Query latency
* Locking
* Quotas

---

## Example Task 4

Alert

```
CDC Lag = 2 Hours
```

Possible reasons:

* Network issue
* Oracle slow
* Large transactions
* Cloud Spanner slow writes
* JVM memory issue

---

## Example Task 5

Alert

```
Datadog CPU 95%
```

Tasks:

* Login to server
* Run:

```bash
top

free -h

df -h

iostat

vmstat
```

Find the root cause.

---

## Example Task 6

Disk Full

Alert

```
Disk Usage 100%
```

Tasks

```bash
df -h

du -sh *

find / -size +1G
```

Delete old logs if appropriate.

---

## Example Task 7

Certificate Expired

Tasks

* Renew certificate
* Restart services
* Validate SSL

---

## Example Task 8

Password Expired

Tasks

* Update Secret Manager
* Restart application
* Validate connectivity

---

## Example Task 9

Migration Stopped

Tasks

* Check Striim logs
* Check Oracle logs
* Check Cloud Spanner logs
* Resume application

---

# Daily Activities

Every morning

* Check Datadog dashboard
* Check failed pipelines
* Check overnight alerts
* Check CDC lag
* Check server health
* Attend stand-up meeting

Afternoon

* Deploy changes
* Fix incidents
* Review tickets
* Create automation
* Update documentation

Evening

* Production deployment (if scheduled)
* Post-deployment validation
* Monitor production
* Close change request

---

# Common Client Requests

Your client may ask you to:

* Create a new Cloud Spanner database.
* Deploy a new Striim application.
* Add new Oracle tables to the migration.
* Migrate a new schema.
* Increase VM size.
* Increase Cloud Spanner processing units.
* Investigate missing records.
* Troubleshoot slow migration.
* Perform production deployment.
* Roll back a failed deployment.
* Create Datadog dashboards.
* Configure new alerts.
* Create IAM users.
* Rotate passwords or certificates.
* Prepare SOPs and runbooks.
* Support UAT and Go-Live.

---

# Documentation You May Create

* Infrastructure SOP
* Deployment SOP
* Rollback SOP
* Go-Live Checklist
* Incident Runbook
* Disaster Recovery (DR) Document
* Access Request Document
* POC Report
* Root Cause Analysis (RCA)
* Change Request (CR) Document
* Production Validation Checklist

---

# Skills You Will Use Daily

| Area                | Typical Work                                               |
| ------------------- | ---------------------------------------------------------- |
| GCP                 | Create and manage cloud resources                          |
| Terraform           | Provision and update infrastructure                        |
| Striim              | Configure, deploy, and troubleshoot CDC pipelines          |
| Oracle              | Validate connectivity, logs, and CDC prerequisites         |
| Cloud Spanner       | Create databases, monitor performance, troubleshoot issues |
| Harness             | Build CI/CD pipelines, deployments, and rollbacks          |
| Datadog             | Monitor infrastructure, applications, and alerts           |
| Linux               | Server administration, log analysis, troubleshooting       |
| Git                 | Source control and configuration management                |
| Shell Scripting     | Automation and operational tasks                           |
| Incident Management | Investigate, resolve, and document production issues       |

## What your Team Lead expects from you after a few months

If you become comfortable with the project, your lead should be able to assign tasks like:

* "Deploy the latest Striim pipeline to UAT."
* "Investigate why CDC lag increased overnight."
* "Provision a new Cloud Spanner environment using Terraform."
* "Set up Datadog alerts for the new migration servers."
* "Perform tonight's production deployment using Harness."
* "Troubleshoot the Oracle connectivity issue."
* "Prepare the Go-Live checklist and support the migration window."
* "Analyze logs, identify the root cause, and provide an RCA."

---

Based on what you've told me over the last few conversations, I recommend learning these topics in this exact order:

1. **Data Migration Fundamentals**
2. **Oracle Basics for DevOps**
3. **Cloud Spanner**
4. **CDC (Change Data Capture)**
5. **Striim**
6. **Terraform**
7. **Harness CI/CD**
8. **Datadog Monitoring**
9. **Production Support**
10. **Go-Live and Troubleshooting**

By following this sequence, you'll build the knowledge needed to perform the real tasks listed above rather than just understanding the theory.
