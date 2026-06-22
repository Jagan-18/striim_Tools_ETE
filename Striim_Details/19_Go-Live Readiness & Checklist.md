# Go-Live Readiness & Checklist for Striim Pipelines

A **Go-Live Readiness Check** ensures the Striim pipeline is fully tested, stable, secure, monitored, and approved before deployment to Production.

---
# Go-Live Process Flow

```text
Development
     |
SIT Completed
     |
UAT Completed
     |
Performance Testing
     |
Go-Live Readiness Review
     |
Production Deployment
     |
Hypercare Support
```

---
# 1. Source Validation

Verify source database readiness.

### Oracle CDC Example

Check:

```sql
SELECT LOG_MODE
FROM V$DATABASE;
```

Expected:

```text
ARCHIVELOG
```

---

### Supplemental Logging

```sql
SELECT SUPPLEMENTAL_LOG_DATA_MIN
FROM V$DATABASE;
```

Expected:

```text
YES
```

---

### Verify CDC User

Check:

```text
Database User
Permissions
Connectivity
```

---

# Checklist

✅ Database reachable

✅ CDC enabled

✅ Required permissions granted

✅ Network connectivity verified

---
# 2. Target Validation

Verify target readiness.

### BigQuery

Check:

✅ Dataset exists

✅ Tables created

✅ Service account configured

✅ IAM permissions available

---

### Kafka

Check:

✅ Topics created

✅ Partitions configured

✅ Replication configured

---

### Snowflake

Check:

✅ Warehouse available

✅ Schema available

✅ User permissions configured

---
# 3. Application Validation

Verify Striim application.

Check:

```text
Application Status
```

Expected:

```text
Validated Successfully
```

---

Verify:

✅ Source configured

✅ CQ validated

✅ Target configured

✅ No validation errors

---
# 4. CDC Validation

Perform test transactions.

### Insert

```sql
INSERT INTO CUSTOMER
VALUES (1001,'Jagadeesh');
```

Expected:

```text
Record appears in target
```

---

### Update

```sql
UPDATE CUSTOMER
SET NAME='Jagadeesh Reddy'
WHERE CUSTOMER_ID=1001;
```

Expected:

```text
Target updated
```

---

### Delete

```sql
DELETE FROM CUSTOMER
WHERE CUSTOMER_ID=1001;
```

Expected:

```text
Delete reflected in target
```

---

# Checklist

✅ INSERT working

✅ UPDATE working

✅ DELETE working

---
# 5. Data Reconciliation

Compare source and target.

### Source

```sql
SELECT COUNT(*)
FROM CUSTOMER;
```

### Target

```sql
SELECT COUNT(*)
FROM CUSTOMER;
```

Expected:

```text
Counts Match
```

---

### Sample Record Validation

Compare:

```text
Source Record
Target Record
```

Fields should match.

---

# Checklist

✅ Counts match

✅ Sample data validated

✅ No missing records

---
# 6. Performance Validation

Monitor:

```text
Throughput
Latency
CDC Lag
```

Example:

```text
Throughput : 5000 events/sec

Latency : < 2 seconds

CDC Lag : < 100 events
```

---

# Checklist

✅ Throughput meets SLA

✅ Latency acceptable

✅ No excessive lag

---
# 7. HA & Failover Validation

If using multi-node cluster.

Example:

```text
Node-1
Node-2
Node-3
```

---

Test:

```bash
Stop Node-1
```

Verify:

```text
Application moves to Node-2
```

---

Verify:

```text
No Data Loss
```

---

# Checklist

✅ Failover tested

✅ Checkpoint recovery verified

✅ Cluster healthy

---
# 8. Monitoring & Alerting Validation

Verify monitoring dashboards.

Monitor:

```text
Application Status
CDC Lag
CPU
Memory
Errors
```

---

Verify alerts:

```text
Source Disconnect

Target Failure

High Lag

Node Failure
```

---

# Checklist

✅ Dashboards working

✅ Alerts configured

✅ Notification channels tested

---
# 9. Security Validation

Verify:

### RBAC

```text
Admin
Developer
Operator
Viewer
```

---

### SSL/TLS

```text
HTTPS Enabled
```

---

### Credentials

```text
Passwords Secured
Service Accounts Valid
```

---

# Checklist

✅ RBAC configured

✅ TLS enabled

✅ Credentials secured

---
# 10. Backup & Recovery Validation

Verify backups exist.

Backup:

```text
TQL Scripts

Applications

Metadata

UDF JARs
```

---

Verify recovery process.

---

# Checklist

✅ Backup completed

✅ Recovery procedure documented

---
# 11. Deployment Readiness

Verify:

```text
Deployment Plan
Rollback Plan
Support Contacts
```

---
### Rollback Example

If deployment fails:

```text
Stop New Pipeline
Restore Previous Version
Resume CDC
```

---
# Checklist

✅ Deployment plan approved

✅ Rollback plan tested

✅ Support team informed

---
# 12. Business Sign-Off

Required approvals:

### Business Team

```text
Data Validation Approved
```

### DBA Team

```text
Source Approved
```

### DevOps Team

```text
Infrastructure Approved
```

### Application Team

```text
Deployment Approved
```

---
# Go-Live Checklist Summary

| Area                | Status |
| ------------------- | ------ |
| Source Validation   | ✅      |
| Target Validation   | ✅      |
| CDC Validation      | ✅      |
| Data Reconciliation | ✅      |
| Performance Testing | ✅      |
| HA Testing          | ✅      |
| Monitoring Setup    | ✅      |
| Security Review     | ✅      |
| Backup Verification | ✅      |
| Rollback Plan       | ✅      |
| Business Sign-Off   | ✅      |

---
# Go-Live Day Activities:

### Before Deployment

✅ Check source health

✅ Check target health

✅ Verify cluster health

✅ Verify no active incidents

---
### During Deployment

✅ Deploy application

✅ Start pipeline

✅ Monitor logs

✅ Monitor lag

---
### After Deployment

✅ Validate records

✅ Check CDC flow

✅ Verify dashboards

✅ Verify alerts

---
# Hypercare Support (First 24–72 Hours)

Monitor:

```text
CDC Lag

Pipeline Errors

CPU

Memory

Target Writes
```

Provide quick response to:

```text
Data Issues

Performance Issues

Connectivity Issues
```

---
# Common Interview Question

### What checks do you perform before a Striim Go-Live?

**Answer:**

Before Go-Live, I validate source and target connectivity, CDC functionality (insert/update/delete), data reconciliation, schema mappings, performance metrics, HA failover, monitoring and alerting, security controls, backups, rollback procedures, and business sign-offs. I ensure CDC lag is minimal, counts match between source and target, and all stakeholders approve the deployment before moving to production.

---
###  Go-Live Checklist

```text
✅ Source Reachable
✅ Target Reachable
✅ CDC Working
✅ Data Counts Match
✅ Performance Tested
✅ HA Tested
✅ Monitoring Enabled
✅ Alerts Configured
✅ Backups Available
✅ Rollback Plan Ready
✅ UAT Sign-Off Complete
✅ Business Approval Received
```

This is the checklist most teams use before promoting a Striim pipeline from **UAT/Stage to Production**.
