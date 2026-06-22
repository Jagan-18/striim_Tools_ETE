# Striim Cloud vs Striim Platform (On-Premise):

Striim can be deployed in two ways:

1. **Striim Cloud** (SaaS Managed Service)
2. **Striim Platform (On-Premise/Self-Managed)**

---
# High-Level Comparison

| Feature          | Striim Cloud          | Striim Platform (On-Prem) |
| ---------------- | --------------------- | ------------------------- |
| Deployment       | Managed by Striim     | Managed by Customer       |
| Infrastructure   | Striim manages        | Customer manages          |
| Upgrades         | Automatic             | Manual                    |
| HA Setup         | Built-in              | Customer configures       |
| Scaling          | Automatic/Easy        | Manual                    |
| Maintenance      | Striim                | Customer                  |
| Monitoring       | Built-in              | Customer responsibility   |
| Cost Model       | Subscription          | License + Infrastructure  |
| Control          | Limited               | Full Control              |
| Security Control | Shared Responsibility | Full Responsibility       |

---
# Striim Cloud Architecture

```text
Source DB
    |
Striim Cloud
    |
BigQuery / Snowflake / Kafka
```

Managed by Striim:

✅ Infrastructure

✅ Patching

✅ Upgrades

✅ Monitoring

✅ High Availability

---
# On-Premise Architecture

```text
Oracle
   |
Customer Managed Striim Cluster
   |
BigQuery / Kafka / Snowflake
```

Customer manages:

✅ Servers

✅ Storage

✅ Security

✅ HA

✅ Backups

---
# Striim Cloud

## What is it?

A fully managed SaaS offering.

No server installation required.

Example:

```text
Oracle
   |
Striim Cloud
   |
BigQuery
```

---
## Advantages

### Quick Setup

Can start in minutes.

```text
Create Account
      |
Create Pipeline
      |
Start CDC
```

---
### No Infrastructure Management

No need to manage:

```text
VMs
Storage
Patches
Upgrades
```

---
### Built-in HA

Striim manages:

```text
Failover
Recovery
Scaling
```

---
### Lower Operational Overhead

Good for teams without dedicated platform administrators.

---
## Limitations

Less control over:

```text
Operating System
Network
Infrastructure
```

---
# On-Premise Striim Platform

## What is it?

Installed on customer-owned infrastructure.

Examples:

* Data Center
* VMware
* GCP VMs
* AWS EC2
* Azure VMs

---
## Advantages

### Full Control

Control:

```text
Servers
Storage
Network
Security
```

---
### Regulatory Compliance

Suitable when data cannot leave the organization.

Example:

```text
Banking
Government
Healthcare
```

---
### Custom Integrations

Easier integration with:

```text
Internal Systems
Private Networks
Legacy Applications
```

---
## Challenges

Need to manage:

```text
Patching
Upgrades
Backups
HA
Monitoring
```

---
# GCP Example

Since you're learning GCP, this is a common deployment model.

## Striim Cloud

```text
Cloud SQL
    |
Striim Cloud
    |
BigQuery
```

Benefits:

* Fast setup
* Managed service

---
## On-Prem/GCP VM Deployment

```text
Cloud SQL
      |
GCP VM (Striim)
      |
BigQuery
```

You manage:

```text
VM
Firewall
Storage
HA
```

---
# High Availability Comparison

## Striim Cloud

```text
HA Managed by Striim
```

Built-in:

```text
Failover
Checkpoint Recovery
Scaling
```

---
## On-Prem

You configure:

```text
Node-1
Node-2
Node-3
```

Including:

```text
Quorum
Checkpoints
Failover
Monitoring
```

---
# Security Comparison

## Striim Cloud

Shared responsibility:

```text
Striim -> Platform Security

Customer -> Users & Data Access
```

---

## On-Prem

Customer responsible for:

```text
OS Security
Firewall
TLS
RBAC
Backups
```

---
# Cost Comparison

## Striim Cloud

```text
Subscription Based
```

Pay for:

```text
Usage
Capacity
Subscription
```

---
## On-Prem

Pay for:

```text
License
Servers
Storage
Operations Team
```

---
# When to Choose Striim Cloud?

Choose Striim Cloud when:

✅ Fast implementation required

✅ Limited infrastructure team

✅ Cloud-first strategy

✅ Minimal maintenance desired

✅ Real-time analytics to BigQuery/Snowflake

Example:

```text
Cloud SQL
   |
Striim Cloud
   |
BigQuery
```

---
# When to Choose On-Prem?

Choose On-Prem when:

✅ Strict compliance requirements

✅ Data residency requirements

✅ Internal network integration

✅ Full infrastructure control required

Example:

```text
Oracle (Data Center)
       |
Striim Cluster
       |
Kafka
```

---
### What is the difference between Striim Cloud and Striim Platform?

Striim Cloud is a fully managed SaaS service where Striim manages infrastructure, upgrades, and HA. Striim Platform is self-managed and deployed on customer infrastructure, giving full control but requiring management of servers, security, upgrades, and failover.

---
### Which is easier to manage?
**Striim Cloud**, because infrastructure, scaling, monitoring, and upgrades are handled by Striim.

---
### Why choose On-Prem?
Organizations with compliance, security, regulatory, or data residency requirements often choose On-Prem deployments for complete control over infrastructure and data.

---
> Striim Cloud is a fully managed SaaS platform where Striim handles infrastructure, upgrades, scaling, monitoring, and high availability. Striim Platform (On-Premise) is deployed and managed by the customer, providing complete control over infrastructure, networking, security, and compliance requirements. Cloud deployments reduce operational overhead, while on-premise deployments offer greater customization and control for regulated environments.
