# Striim Installation & Setup (On-Premise / Cloud)

As a DevOps Engineer, you can install Striim either:

1. **On-Premise** (Linux/Windows Servers)
2. **Cloud VMs** (AWS EC2, GCP VM, Azure VM)
3. **Kubernetes/OpenShift** (Enterprise deployments)
4. **Striim Cloud** (Managed SaaS – no installation required)

---
# 1. Prerequisites:

### Hardware Requirements

| Environment | Minimum                           |
| ----------- | --------------------------------- |
| CPU         | 4 vCPU                            |
| RAM         | 8 GB (16 GB+ recommended)         |
| Disk        | 50 GB+ SSD                        |
| OS          | RHEL, CentOS, Ubuntu, Rocky Linux |
| Java        | Bundled with recent versions      |

---
# 2. Striim Components Installed:

```text
+------------------------+
| Striim Server          |
|------------------------|
| Web UI                |
| TQL Engine            |
| Stream Processing     |
| CDC Engine            |
| Monitoring Services   |
+------------------------+
```

---
# 3. On-Premise Installation:

## Step 1: Download Striim

Download from:

[Striim Official Website](https://www.striim.com/?utm_source=chatgpt.com)

Example package:

```bash
Striim-5.x.x-Linux64.bin
```

---

## Step 2: Make Installer Executable

```bash
chmod +x Striim-5.x.x-Linux64.bin
```

---
## Step 3: Run Installation:

```bash
./Striim-5.x.x-Linux64.bin
```

Installation wizard opens.

Choose:

* Installation Directory
* Administrator Password
* Port Configuration

Example:

```bash
/opt/striim
```

---
## Step 4: Verify Installation:

```bash
cd /opt/striim
ls
```

Expected folders:

```text
bin
conf
logs
samples
data
```

---
# 4. Start Striim Services:

### Start Server

```bash
cd /opt/striim/bin

./server.sh start
```

Check status:

```bash
./server.sh status
```

---

### Stop Server

```bash
./server.sh stop
```

---
# 5. Access Striim UI:

Open browser:

```text
http://<server-ip>:9080
```

Example:

```text
http://10.10.10.20:9080
```

Login with:

* Admin Username
* Password created during installation

---
# 6. Cloud VM Installation:

Example: GCP VM

```text
GCP VM
 |
 |-- Ubuntu 22.04
 |-- Install Striim
 |
 +--> Connect Sources
```

### Steps:
1. Create VM
2. Open firewall ports
3. Install Striim
4. Configure sources/targets

Firewall:

```bash
9080
9081
9092 (Kafka)
```

---
# 7. Multi-Node Cluster Setup:

Production deployments usually use multiple nodes.

## Node 1

```bash
./server.sh start
```

Creates cluster coordinator.

---
## Node 2

Edit cluster configuration.

```properties
cluster.name=prod-cluster
seed.node=node1.company.com
```

Start service.

```bash
./server.sh start
```

---
## Node 3

Join same cluster.

```properties
cluster.name=prod-cluster
seed.node=node1.company.com
```

Start service.

---
### Cluster Architecture

```text
               +----------------+
               | Coordinator    |
               | Node-1         |
               +-------+--------+
                       |
          --------------------------
          |            |           |
          v            v           v
       Node-2       Node-3      Node-4
```

Benefits:

* High Availability
* Failover
* Load Distribution

---
# 8. Metadata Database Setup

Striim stores:

* Applications
* User accounts
* Checkpoints
* Cluster information

Common databases:

* PostgreSQL
* Oracle
* SQL Server

Example:

```text
Striim
   |
   +---- Metadata DB
```

---
# 9. Configure Source Connections

Example Oracle CDC:

```sql
CREATE SOURCE OracleSource
USING OracleReader (
 ConnectionURL:'jdbc:oracle:thin:@host:1521/orcl',
 Username:'striim',
 Password:'password'
)
OUTPUT TO OracleStream;
```

---

# 10. Configure Target Connections

Example Snowflake:

```sql
CREATE TARGET SnowflakeTarget
USING SnowflakeWriter (
 ConnectionURL:'jdbc:snowflake://account',
 Username:'user',
 Password:'password'
)
INPUT FROM OracleStream;
```

---

# 11. Monitoring

Useful directories:

```bash
/opt/striim/logs
```

View logs:

```bash
tail -f server.log
```

Check processes:

```bash
ps -ef | grep striim
```

Check ports:

```bash
netstat -tulpn | grep 9080
```

---

# 12. Backup and Recovery

Backup:

* Configuration files
* Metadata database
* TQL applications

Example:

```bash
tar -cvzf striim_backup.tar.gz \
/opt/striim/conf \
/opt/striim/data
```

---
# DevOps Deployment Flow

```text
Install Striim
      |
      v
Create Cluster
      |
      v
Configure Metadata DB
      |
      v
Create Source
      |
      v
Create Stream
      |
      v
Create CQ
      |
      v
Create Target
      |
      v
Start Application
      |
      v
Monitor Dashboard
```

# Interview Answer

**How do you install and set up Striim?**

Striim can be installed on Linux servers, cloud VMs, or Kubernetes environments. The setup includes installing the Striim server, configuring cluster nodes, setting up a metadata database, defining source and target connections, creating TQL applications, and monitoring through the Striim Web UI. Production deployments typically use a multi-node cluster for high availability and scalability.

---
# Striim Installation & Setup on GCP (Google Cloud Platform):
Since you're working with **GCP and DevOps**, this is the common enterprise setup.

## GCP Architecture:
```text
Cloud SQL / Oracle / MySQL
           |
           | CDC
           v
     Striim Server
      (Compute Engine)
           |
     -----------------
     |               |
     v               v
  BigQuery       Kafka
```

---
# Option 1: Install Striim on Compute Engine VM:
## Step 1: Create VM

1. Go to GCP Console
2. Navigate to **Compute Engine → VM Instances**
3. Click **Create Instance**

Example Configuration:

```text
Name: striim-server
Region: us-central1
Machine Type: e2-standard-4
CPU: 4 vCPU
Memory: 16 GB
OS: RHEL 8 / Ubuntu 22.04
Disk: 100 GB SSD
```

---
## Step 2: Configure Firewall:
Allow ports:

```text
22    SSH
9080  Striim UI
9081  Striim REST API
443   HTTPS
```

Example:

```bash
gcloud compute firewall-rules create striim-ui \
--allow tcp:9080
```

---
## Step 3: Connect to VM:
```bash
gcloud compute ssh striim-server
```

or use SSH from the GCP Console.

---
## Step 4: Install Java:
Check Java:

```bash
java -version
```

Install OpenJDK:

```bash
sudo yum install java-11-openjdk -y
```

or Ubuntu:

```bash
sudo apt install openjdk-11-jdk -y
```

Verify:

```bash
java -version
```

---
## Step 5: Upload Striim Package:
```bash
scp Striim_<version>.tar.gz user@server:/opt/
```

Extract:

```bash
cd /opt

tar -xvf Striim*.tar.gz
```

---
## Step 6: Start Striim:

```bash
cd /opt/striim

./bin/striim-node.sh start
```

Check:

```bash
./bin/striim-node.sh status
```

---
## Step 7: Access UI:
Open browser:

```text
http://<VM_EXTERNAL_IP>:9080
```

Login using admin credentials.

---
# GCP Production Architecture:

For production:

```text
                    +----------------+
                    | Load Balancer  |
                    +--------+-------+
                             |
          ---------------------------------
          |               |               |
          v               v               v
      Striim-1       Striim-2        Striim-3
      Compute VM     Compute VM      Compute VM

                 |
          -----------------
          |               |
          v               v
      BigQuery         Kafka
```

Benefits:

* High Availability
* Scalability
* Failover

---
# Connect Striim to Cloud SQL:

Example:

```text
Cloud SQL (MySQL)
        |
        v
   MySQLReader
        |
        v
      Stream
        |
        v
 BigQueryWriter
```

TQL Example:

```sql
CREATE SOURCE MySQLSource
USING MySQLReader (
 ConnectionURL:'jdbc:mysql://10.x.x.x:3306/testdb',
 Username:'admin',
 Password:'password'
)
OUTPUT TO MySQLStream;
```

---
# Connect Striim to BigQuery:

Example:

```sql
CREATE TARGET BigQueryTarget
USING BigQueryWriter (
 ProjectId:'my-project',
 Dataset:'sales'
)
INPUT FROM MySQLStream;
```

---
# GCP Services Commonly Used with Striim:

| GCP Service      | Purpose                  |
| ---------------- | ------------------------ |
| Compute Engine   | Host Striim servers      |
| Cloud SQL        | Source database          |
| BigQuery         | Target data warehouse    |
| Cloud Storage    | Backup and staging       |
| Pub/Sub          | Streaming integration    |
| GKE              | Run Striim on Kubernetes |
| Secret Manager   | Store credentials        |
| Cloud Monitoring | Monitoring and alerts    |

---
# DevOps Responsibilities:

As a DevOps Engineer, you may handle:

### Infrastructure

```bash
gcloud compute instances create striim-server
```

### Monitoring:
Monitor:

* CPU
* Memory
* Disk
* Network
* Striim service status

Using:

* Google Cloud Cloud Monitoring
* Striim Dashboard

### Backup:
Backup:

* VM snapshots
* Configuration files
* Metadata database

Example:

```bash
gcloud compute disks snapshot striim-disk
```

### Security:
* Use Service Accounts
* Store passwords in Secret Manager
* Restrict firewall access
* Enable HTTPS

---
**How would you deploy Striim on GCP?**

Striim is typically deployed on a Compute Engine VM or GKE cluster. First, create the infrastructure, install Java, deploy the Striim package, configure networking and firewall rules, and start the Striim services. Striim can then connect to source systems such as Cloud SQL, Oracle, or MySQL and stream data to targets like BigQuery, Pub/Sub, Kafka, or Cloud Storage in real time. For production environments, multiple Striim nodes are deployed across Compute Engine instances for high availability and scalability.
