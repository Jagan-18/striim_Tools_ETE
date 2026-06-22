# Security & Access Control in Striim

Security is a critical aspect of Striim deployments, especially in production environments handling sensitive business and customer data.

Striim security focuses on:

✅ Authentication

✅ Authorization (RBAC)

✅ Encryption

✅ Secure Communication

✅ Audit Logging

✅ Credential Management

---

# Security Architecture

```text id="5owzho"
Users
   |
Authentication
   |
Authorization (RBAC)
   |
Striim Platform
   |
Encrypted Data
   |
Source / Target Systems
```

---

# Authentication

Authentication verifies user identity.

Example:

```text id="y2vvyv"
Username
Password
```

Supported methods include:

* Local Striim Users
* LDAP
* Active Directory (AD)
* Single Sign-On (SSO)

Example:

```text id="qql8ef"
Corporate AD
      |
      v
Striim Login
```

---

# Role-Based Access Control (RBAC)

RBAC controls what users can do within Striim.

Instead of:

```text id="9qcfwa"
Everyone = Full Access
```

Use:

```text id="ynb37g"
Admin
Developer
Operator
Viewer
```

---

# Common Roles

## Administrator

Full access.

Can:

```text id="h9nd4o"
Create Applications
Delete Applications
Manage Users
Manage Cluster
```

---

## Developer

Can:

```text id="11khmf"
Create Pipelines
Modify TQL
Deploy Applications
```

Cannot:

```text id="0r5rl7"
Manage Users
```

---

## Operator

Can:

```text id="g4bzpf"
Start Applications
Stop Applications
Monitor Pipelines
```

Cannot:

```text id="t4mqyc"
Modify TQL
```

---

## Viewer

Read-only access.

Can:

```text id="56lgdd"
View Dashboards
View Metrics
```

Cannot make changes.

---

# Example RBAC Matrix

| Action          | Admin | Developer | Operator | Viewer |
| --------------- | ----- | --------- | -------- | ------ |
| Create App      | Yes   | Yes       | No       | No     |
| Deploy App      | Yes   | Yes       | No       | No     |
| Start/Stop App  | Yes   | Yes       | Yes      | No     |
| Manage Users    | Yes   | No        | No       | No     |
| View Monitoring | Yes   | Yes       | Yes      | Yes    |

---

# Principle of Least Privilege

Best practice:

```text id="7fuw6j"
Give Only Required Access
```

Example:

```text id="p8bjiz"
Developer
```

should not receive:

```text id="mdm3u9"
Cluster Administration
```

permissions.

---

# Encryption

Striim supports encryption for:

### Data in Transit

```text id="c5eh9d"
Client
   |
TLS/SSL
   |
Striim
```

---

### Data at Rest

Protect:

```text id="wt5zq2"
Metadata
Checkpoints
Configurations
Logs
```

using disk/database encryption.

---

# TLS/SSL Configuration

Without SSL:

```text id="smvslr"
User
  |
HTTP
  |
Striim
```

Risk:

```text id="m8tklv"
Plain Text Traffic
```

---

With SSL:

```text id="7mjlwm"
User
  |
HTTPS
  |
Striim
```

Benefits:

✅ Encrypted communication

✅ Secure login

✅ Secure API access

---

# Securing Source Connections

Example:

### Oracle

```text id="ok5b1s"
Oracle
   |
SSL/TLS
   |
OracleReader
```

---

### Kafka

```text id="0bfp3g"
Kafka Cluster
     |
SSL/SASL
     |
KafkaReader
```

---

### BigQuery

```text id="sqj6hl"
Striim
   |
HTTPS
   |
BigQuery
```

---

# Credential Management

Avoid:

```text id="d4u1su"
Hardcoded Passwords
```

Bad:

```sql id="t7mhfi"
Password:'admin123'
```

---

Better:

```text id="54hsyg"
Secrets Store
Environment Variables
Encrypted Credentials
```

---

# Audit Logging

Track:

```text id="zj39xt"
User Login
Application Changes
Deployments
Configuration Updates
```

Example:

```text id="ld9x3x"
10:00 AM

User: jagadeesh

Action:
Started Oracle_To_BigQuery Application
```

Useful for:

* Compliance
* Security reviews
* Troubleshooting

---

# API Security

If using Striim REST APIs:

Use:

```text id="ww1ukw"
HTTPS
Authentication Tokens
```

Avoid:

```text id="mujlwm"
Open Anonymous Access
```

---

# Network Security

Recommended architecture:

```text id="w9xg9l"
Users
   |
Load Balancer
   |
Firewall
   |
Striim Cluster
```

Restrict access to:

```text id="wqk0t8"
Admin Ports
Management Ports
Database Ports
```

---

# Cluster Security

Protect:

```text id="epcw77"
Node-to-Node Communication
```

Use:

```text id="wphydw"
TLS
Firewall Rules
Private Networks
```

---

# Security Best Practices

## Use RBAC

Assign:

```text id="owd76h"
Admin
Developer
Operator
Viewer
```

appropriately.

---

## Enable SSL/TLS

Protect:

```text id="6xx4zv"
UI Access
API Access
Source Connections
Target Connections
```

---

## Rotate Passwords

Regularly update:

```text id="wkvl8d"
Database Credentials
Kafka Credentials
Service Accounts
```

---

## Enable Audit Logging

Monitor:

```text id="mqel56"
Who
What
When
```

for every critical action.

---

## Restrict Network Access

Allow only:

```text id="gtvj2m"
Application Teams
Admins
Automation Tools
```

to access Striim.

---

## Secure Backups

Encrypt:

```text id="c1k2gf"
Metadata Backups
TQL Scripts
Configuration Files
```

---

# Security Monitoring

Monitor:

```text id="hz6k1j"
Failed Logins
Permission Changes
Application Changes
Node Access
```

Alert on:

```text id="0yn9ea"
Repeated Login Failures
Unauthorized Access Attempts
```

---
### What is RBAC?

Role-Based Access Control (RBAC) restricts user actions based on assigned roles such as Admin, Developer, Operator, and Viewer.

---
### How do you secure Striim communications?

By enabling TLS/SSL for UI access, APIs, cluster communication, and source/target connections.

---
### Why is audit logging important?

Audit logs provide a record of user actions, deployments, configuration changes, and access attempts for compliance and troubleshooting.

---
### What is the prin+ciple of least privilege?

Users should be granted only the permissions required to perform their job functions and no more.

---
> Striim security is implemented using authentication, role-based access control (RBAC), encryption, audit logging, and secure credential management. RBAC ensures users receive only the permissions required for their role. TLS/SSL is used to secure communication between users, Striim nodes, and external systems such as Oracle, Kafka, and BigQuery. Audit logging tracks user activity and deployments, while network controls and encrypted credential storage help protect production environments from unauthorized access.
