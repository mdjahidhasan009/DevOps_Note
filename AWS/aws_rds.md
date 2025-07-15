# AWS RDS (Relational Database Service)

## What is AWS RDS?

AWS RDS as a managed database service that simplifies database setup, operation, and scaling. It mainly supports 
relational databases and automates administrative tasks such as backups, patching, monitoring, and scaling.

**Purpose**: handling administrative tasks like backups, patching, monitoring, and scaling.

### Key Value Propositions
- **Managed Service**: AWS handles database administration tasks
- **High Availability**: Built-in failover and redundancy options
- **Scalability**: Easy vertical and horizontal scaling
- **Security**: Encryption, VPC isolation, and IAM integration
- **Cost-Effective**: Pay-as-you-go pricing with reserved instances
- **Multiple Engine Support**: Wide variety of database engines

## RDS Database Engine Options

### Aurora (AWS-Optimized)
- **Aurora (MySQL Compatible)**: Up to 5x MySQL performance
- **Aurora (PostgreSQL Compatible)**: Up to 3x PostgreSQL performance

### Traditional RDS Engines
- **RDS for MySQL**: Popular open-source relational database
- **RDS for MariaDB**: MySQL-compatible with additional features
- **RDS for PostgreSQL**: Advanced open-source object-relational database
- **RDS for Oracle**: Enterprise-grade commercial database
- **RDS for Microsoft SQL Server**: Microsoft's relational database
- **RDS for IBM DB2**: IBM's enterprise database system

### Engine Selection Criteria
| Engine         | Best For                        | Key Features                                     |
|----------------|---------------------------------|--------------------------------------------------|
| **MySQL**      | Web applications, e-commerce    | Open source, wide compatibility                  |
| **PostgreSQL** | Complex queries, data analytics | Advanced SQL features, JSON support              |
| **Oracle**     | Enterprise applications         | Advanced features, high performance              |
| **SQL Server** | Microsoft ecosystem             | .NET integration, reporting services             |
| **MariaDB**    | MySQL replacement               | Enhanced performance, additional storage engines |
| **Aurora**     | Cloud-native applications       | AWS-optimized, auto-scaling storage              |

## Aurora Features and Benefits

Aurora offers:
* **Up to 5x the throughput** of MySQL Community Edition & **3x of PostgreSQL**
* **Up to 128 TB** of autoscaling SSD storage
* **Six-way replication** across three Availability Zones
* **Up to 15 read replicas** with replica lag under 10ms
* **Automatic monitoring** with failover

### Aurora Architecture
```
Aurora Cluster
├── Writer Instance (Primary)
│   ├── Handles all write operations
│   └── Can handle read operations
├── Reader Instance 1 (Read Replica)
├── Reader Instance 2 (Read Replica)
└── Up to 15 total readers

Shared Storage Volume (up to 128TB)
├── 6 copies across 3 AZs
├── Self-healing storage
└── Continuous backup to S3
```

### Aurora Advantages over RDS
- **Performance**: Significantly faster than standard RDS
- **Storage**: Auto-scaling shared storage architecture
- **Availability**: 99.99% availability SLA
- **Durability**: Six-way replication across three AZs
- **Backtrack**: Point-in-time recovery without restoring from backup
- **Global Database**: Cross-region replication with low latency

## Advantage over using RDS versus deploying DB on EC2

*   RDS is a managed service:
    *   Automated provisioning, OS patching
    *   Continuous backups and restore to specific timestamp (Point in Time Restore)!
    *   Monitoring dashboards
    *   Read replicas for improved read performance
    *   Multi AZ setup for DR (Disaster Recovery)
    *   Maintenance windows for upgrades
    *   Scaling capability (vertical and horizontal)
    *   Storage backed by EBS
*   BUT you can't SSH into your instances

## RDS – Storage Auto Scaling

*   Helps you increase storage on your RDS DB instance dynamically
*   When RDS detects you are running out of free database storage, it scales automatically
*   Avoid manually scaling your database storage
*   You have to set Maximum Storage Threshold (maximum limit for DB storage)
*   Automatically modify storage if:
    *   Free storage is less than 10% of allocated storage
    *   Low-storage lasts at least 5 minutes
    *   6 hours have passed since last modification
*   Useful for applications with unpredictable workloads
*   Supports all RDS database engines

## RDS Read Replicas for read scalability

*   Up to 15 Read Replicas
*   Within AZ, Cross AZ or Cross Region
*   Replication is ASYNC, so reads are eventually consistent
*   Replicas can be promoted to their own DB
*   Applications must update the connection string to leverage read replicas

## RDS Read Replicas – Use Cases

*   You have a production database that is taking on normal load
*   You want to run a reporting application to run some analytics
*   You create a Read Replica to run the new workload there
*   The production application is unaffected
*   Read replicas are used for SELECT (=read) only kind of statements (not INSERT, UPDATE, DELETE)

## RDS Read Replicas – Network Cost

*   In AWS there's a network cost when data goes from one AZ to another
*   For RDS Read Replicas within the same region, you don't pay that fee

## RDS Multi AZ (Disaster Recovery)

*   SYNC replication
*   One DNS name – automatic app failover to standby
*   Increase availability
*   Failover in case of loss of AZ, loss of network, instance or storage failure
*   No manual intervention in apps
*   Not used for scaling
*   **Note:** The Read Replicas be setup as Multi AZ for Disaster Recovery (DR)

## RDS – From Single-AZ to Multi-AZ

*   Zero downtime operation (no need to stop the DB)
*   Just click on “modify” for the database
*   The following happens internally:
    *   A snapshot is taken
    *   A new DB is restored from the snapshot in a new AZ
    *   Synchronization is established between the two databases

## RDS Custom

*   Managed Oracle and Microsoft SQL Server Database with OS and database customization
*   RDS: Automates setup, operation, and scaling of database in AWS
*   Custom: access to the underlying database and OS so you can
    *   Configure settings
    *   Install patches
    *   Enable native features
    *   Access the underlying EC2 Instance using SSH or SSM Session Manager
*   De-activate Automation Mode to perform your customization, better to take a DB snapshot before
*   RDS vs. RDS Custom
    *   RDS: entire database and the OS to be managed by AWS
    *   RDS Custom: full admin access to the underlying OS and the database

### **Amazon Aurora**

*   Aurora is a proprietary technology from AWS (not open sourced)
*   Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or MySQL database)
*   Aurora is “AWS cloud optimized” and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS
*   Aurora storage automatically grows in increments of 10GB, up to 128 TB.
*   Aurora can have up to 15 replicas and the replication process is faster than MySQL (sub 10 ms replica lag)
*   Failover in Aurora is instantaneous. It’s HA native.
*   Aurora costs more than RDS (20% more) – but is more efficient

### **Aurora High Availability and Read Scaling**


*   6 copies of your data across 3 AZ:
    *   4 copies out of 6 needed for writes
    *   3 copies out of 6 need for reads
    *   Self healing with peer-to-peer replication
    *   Storage is striped across 100s of volumes
*   One Aurora Instance takes writes (master)
*   Automated failover for master in less than 30 seconds
*   Master + up to 15 Aurora Read Replicas serve reads
*   Support for Cross Region Replication


### **Aurora DB Cluster**

*(Diagram text)*
*   client
*   Writer Endpoint
    *   Pointing to the master, if master fails, it will point to the new master so that applications don't need to 
        change the connection string
*   Reader Endpoint
    *   Connection Load Balancing, it will point to the read replicas automatically if scaling happens user doesn't need
        to change the connection string, also help with load balancing
*   Auto Scaling
*   Shared storage Volume
*   Auto Expanding from 10G to 128 TB


### **Features of Aurora**

*   Automatic fail-over
*   Backup and Recovery
*   Isolation and security
*   Industry compliance
*   Push-button scaling
*   Automated Patching with Zero Downtime
*   Advanced Monitoring
*   Routine Maintenance
*   Backtrack: restore data at any point of time without using backups

## Benefits of Using RDS

* **High availability and fault tolerance**
* **Vertical and Horizontal Scaling**
* **Automated backups and recovery**
* **Read replicas for improved read performance**
* **Multi AZ setup for DR (Disaster Recovery)**
* **Cost-effectiveness**

### Detailed Benefits Analysis

#### High Availability and Fault Tolerance
- **Multi-AZ Deployments**: Automatic failover to standby instance
- **Automated Backups**: Point-in-time recovery capability
- **Maintenance Windows**: Scheduled maintenance with minimal downtime
- **Health Monitoring**: Continuous monitoring with automatic recovery

#### Scalability Options
- **Vertical Scaling**: Upgrade instance types with minimal downtime
- **Horizontal Scaling**: Add read replicas for read-heavy workloads
- **Storage Scaling**: Automatic or manual storage expansion
- **Aurora Serverless**: Automatic scaling based on demand

#### Cost Management
- **Reserved Instances**: Significant cost savings for predictable workloads
- **On-Demand Pricing**: Pay only for what you use
- **Storage Optimization**: Different storage types for different needs
- **Cost Monitoring**: CloudWatch metrics and Cost Explorer integration

## RDS Features Comparison Table

| Feature                                                   | How It Works                                                                        | Purpose                                       |
|-----------------------------------------------------------|-------------------------------------------------------------------------------------|-----------------------------------------------|
| **Multi-AZ Deployment**                                   | Synchronous replication to standby in a different AZ with automatic failover.       | High Availability and fault tolerance.        |
| **Read Replicas**                                         | Asynchronous replication to read-only instances, in the same or different regions.  | Horizontal scaling for read-heavy workloads.  |
| **Automated Backups**                                     | Daily backups and transaction log storage for point-in-time recovery.               | Data durability and recovery.                 |
| **Manual Snapshots**                                      | User-initiated snapshots stored indefinitely.                                       | Long-term storage and recovery options.       |
| **VPC and Security Groups**                               | Network isolation and traffic control within a VPC.                                 | Network security and restricted access.       |
| **CloudWatch, Enhanced Monitoring, Performance Insights** | Real-time monitoring, OS-level metrics, and query analysis.                         | Performance optimization and troubleshooting. |
| **Encryption and IAM**                                    | KMS-based encryption for storage, SSL/TLS for transit, IAM access control.          | Data security and access management.          |

## RDS Deployment Architectures

### RDS Read Replica - Multi Region
* **Primary DB Instance**: One primary DB instance in one region handles read and write operations
* **Read Replica**: Another instance in asynchronous replication with the primary DB instance in another region, used for read operations only
* **Use Cases**: Disaster recovery, geographic distribution, read scaling

```
Primary Region (us-east-1)
├── Primary RDS Instance (Read/Write)
└── Local Read Replica (Read Only)

Secondary Region (us-west-2)
├── Cross-Region Read Replica (Read Only)
└── Can be promoted to primary for DR
```

#### Cross-Region Read Replica Benefits
- **Disaster Recovery**: Can be promoted to primary in case of region failure
- **Geographic Distribution**: Serve users from multiple regions with low latency
- **Read Scaling**: Distribute read traffic across multiple regions
- **Compliance**: Meet data residency requirements

### RDS Multi-AZ Deployments

#### Multi-AZ DB Instance (Traditional)
* **Primary DB Instance**: One primary DB instance in one availability zone handles read and write operations
* **Standby Instance**: Synchronous replication with the primary DB instance in another availability zone, used for read 
  and write operations only when the primary DB instance is down
* **Purpose**: High availability and automatic failover

```
Availability Zone A
├── Primary DB Instance
│   ├── Handles all read/write operations
│   └── Synchronously replicates to standby

Availability Zone B
├── Standby DB Instance
│   ├── Receives synchronous replication
│   ├── Not accessible for read operations
│   └── Automatic failover target
```

#### Multi-AZ DB Cluster (Aurora Style for RDS)
* **Primary DB Instance**: One primary DB instance in one availability zone handles read and write operations
* **Read Replicas**: Two or more read replicas in other availability zones (excluding primary AZ) handle read operations only
* **Replication**: Asynchronous replication with the primary DB instance

```
Multi-AZ Cluster Architecture
├── Writer Instance (AZ-1) - Read/Write
├── Reader Instance (AZ-2) - Read Only
├── Reader Instance (AZ-3) - Read Only
└── Shared Storage (Replicated across AZs)
```

#### Multi-AZ Comparison

| Feature              | Multi-AZ DB Instance        | Multi-AZ DB Cluster     |
|----------------------|-----------------------------|-------------------------|
| **Read Scaling**     | No (standby not accessible) | Yes (multiple readers)  |
| **Failover Time**    | 1-2 minutes                 | Under 1 minute          |
| **Reader Endpoints** | None                        | Multiple read endpoints |
| **Cost**             | Lower                       | Higher                  |
| **Use Case**         | High availability only      | HA + read scaling       |

## Common Use Cases for RDS

### Application Categories
* **Web Applications**: Relational databases are ideal for web apps requiring structured data
* **E-commerce Platforms**: For handling inventory, customer data, and order transactions
* **Business Applications**: ERP, CRM, and financial applications with strong data integrity needs

### Specific Use Case Examples

#### E-Commerce Platform
```sql
-- Product catalog management
CREATE TABLE products (
                          id INT PRIMARY KEY,
                          name VARCHAR(255),
                          price DECIMAL(10,2),
                          inventory_count INT,
                          category_id INT
);

-- Order processing
CREATE TABLE orders (
                        id INT PRIMARY KEY,
                        user_id INT,
                        total_amount DECIMAL(10,2),
                        order_date TIMESTAMP,
                        status VARCHAR(50)
);
```

#### Content Management System
- **User management**: Authentication and authorization
- **Content storage**: Articles, media metadata, comments
- **Analytics**: Page views, user engagement metrics
- **Search functionality**: Full-text search capabilities

#### Financial Applications
- **Transaction processing**: ACID compliance for financial transactions
- **Audit trails**: Complete transaction history
- **Reporting**: Complex financial reports and analytics
- **Compliance**: Meet financial industry regulations

## RDS Instance Types and Performance

### Instance Classes
#### General Purpose (T3/T4g)
- **Burstable Performance**: Credit-based CPU performance
- **Use Cases**: Variable workloads, development/testing
- **Sizes**: db.t3.micro to db.t3.2xlarge
- **Best For**: Small to medium databases

#### Memory Optimized (R5/R6i)
- **High Memory**: Optimized for memory-intensive applications
- **Use Cases**: In-memory databases, real-time analytics
- **Sizes**: db.r5.large to db.r5.24xlarge
- **Best For**: High-performance databases

#### Compute Optimized (C5/C6i)
- **High CPU**: Optimized for compute-intensive applications
- **Use Cases**: CPU-intensive database workloads
- **Sizes**: db.c5.large to db.c5.24xlarge
- **Best For**: High-performance computing

### Storage Types
#### General Purpose SSD (gp2/gp3)
- **Performance**: 3 IOPS per GB (gp2), up to 16,000 IOPS
- **Size Range**: 20 GB to 64 TB
- **Use Cases**: Most database workloads
- **Cost**: Balanced cost and performance

#### Provisioned IOPS SSD (io1/io2)
- **Performance**: Up to 80,000 IOPS
- **Size Range**: 100 GB to 64 TB
- **Use Cases**: I/O intensive applications
- **Cost**: Higher cost for guaranteed performance

#### Magnetic Storage (Legacy)
- **Performance**: Lower performance
- **Use Cases**: Infrequent access, cost-sensitive workloads
- **Recommendation**: Use SSD storage types instead

## Security and Compliance

### Network Security
- **VPC Deployment**: Deploy in private subnets
- **Security Groups**: Control inbound/outbound traffic
- **NACLs**: Additional network-level security
- **VPC Endpoints**: Private connectivity to AWS services

### Data Encryption
#### Encryption at Rest
- **KMS Integration**: AWS Key Management Service
- **Automatic Encryption**: Encrypt databases, backups, snapshots
- **Transparent**: No application changes required

#### Encryption in Transit
- **SSL/TLS**: Encrypted connections to database
- **Certificate Validation**: Verify server certificates
- **Force SSL**: Require encrypted connections

### Access Control
- **IAM Integration**: Database access via IAM roles
- **Database Users**: Traditional database authentication
- **Password Policies**: Strong password requirements
- **Audit Logging**: Track database access and changes

## Monitoring and Performance

### CloudWatch Metrics
- **CPU Utilization**: Monitor instance performance
- **Database Connections**: Track connection usage
- **Read/Write IOPS**: Monitor storage performance
- **Free Storage Space**: Monitor available storage

### Enhanced Monitoring
- **OS-Level Metrics**: CPU, memory, disk, network
- **Real-Time Monitoring**: Sub-minute granularity
- **Custom Dashboards**: Visualize key metrics
- **Alerting**: Set up automated alerts

### Performance Insights
- **Query Analysis**: Identify slow-running queries
- **Wait Events**: Understand database bottlenecks
- **Top SQL**: Find most resource-intensive queries
- **Historical Analysis**: Track performance over time

## Backup and Recovery

### Automated Backups
- **Daily Backups**: Automatic daily backup snapshots
- **Transaction Logs**: Continuous backup for point-in-time recovery
- **Retention Period**: 1-35 days configurable
- **Storage Location**: Cross-AZ storage for durability

### Manual Snapshots
- **User-Initiated**: Create snapshots on-demand
- **Indefinite Retention**: Keep snapshots as long as needed
- **Cross-Region Copy**: Copy snapshots to other regions
- **Restore Options**: Create new instances from snapshots

### Point-in-Time Recovery
- **Time Range**: Restore to any point within retention period
- **Granularity**: Down to the second
- **New Instance**: Creates new RDS instance
- **Use Cases**: Data corruption recovery, testing

## RDS Instance Quick Setup

### RDS Instance
* Create a RDS MySQL instance
    * Use Free Tier
    * Username will be 'admin' and you can set password (you can't use special character)
    * Keep the Public access to True to access it from Local or remote server
    * Create a security group (and allow 3306 from everywhere)
    * After creating, you can find Endpoint (hostname) to connect to this DB.

### EC2 Instance
```shell
sudo yum install -y docker
sudo service docker start
sudo usermod -aG docker ec2-user
sudo docker pull philippaul/node-mysql-app:02
```

for ubuntu
```shell
# 1. Update package list and install Docker
sudo apt update
sudo apt install -y docker.io

# 2. Start the Docker service (systemctl is the modern standard)
sudo systemctl start docker
sudo systemctl enable docker

# 3. Add the 'ubuntu' user to the 'docker' group
# Note: On Ubuntu EC2 instances, the default user is 'ubuntu'.
# Using $USER is a more portable way to refer to the current user.
sudo usermod -aG docker $USER

# IMPORTANT: You need to log out and log back in for the group change to take effect.
# Alternatively, you can run `newgrp docker` in your current shell.

# 4. Pull the Docker image (this command is the same)
docker pull philippaul/node-mysql-app:02
```

```
docker run --rm -p 80:3000 -e DB_HOST="your-db-hostname" -e DB_USER="your-db-username" -e DB_PASSWORD="your-db-password" -d philippaul/node-mysql-app:02
```

**Configuration Details:**
- `DB_HOST`: Go to Aurora and RDS > Databases > click on your database > At Connectivity & security tab > Endpoint. Copy the endpoint without port number
- `DB_USER`: For our case it is 'admin'

```
docker run -it --rm mysql:8.0 mysql -h db.example.com -u admin -p
```

## Practical Implementation Example

### RDS Instance Setup

Goes to Aurora and RDS > Databases. Click on "Create database".

Select "Standard Create" from Choose a database creation method.

Select "MySQL" from Engine options edition MySQL Community it gets automatically selected. Keeping Engine version MySQL
8.0.41 as default.

Select "Free tier" from Templates.

In Availability and durability only have Single-AZ DB instance deployment(1 instance) for free tier.

In Settings keep DB instance identifier as "database-1", Credentials Settings give Master username as "admin" and at
Credentials management select "Self managed" and enter master password.

At instance configuration keep Burstable classes(includes t classes) and select "db.t4.micro" as instance class same
as default.

At storage keep storage type as General purpose SSD (gp2) and allocation storage 20 GiB. From Additional storage
configuration uncheck "Enable storage autoscaling".

In Connectivity keep Compute resource as Don't connect to an EC2 compute resource. Keeping Network type IPv4, keep VPC
as default VPC and DB subnet group as default and public access as Yes. For VPC security group (firewall) select "Create
New" give it name at New VPC security group name "MySQL-SG" and keeping all thing as default such as unchecked Create an
RDS Proxy, certificate authority as rds-ca-rsa2048-g1 (default), additional configuration > Database port 3306.

Database authentication keep this as default "Password authentication".

At monitoring keeping default uncheck "Enable Enhanced monitoring". Now click on "Create database" button.

### RDS Configuration Best Practices

#### Production Configuration
```yaml
Engine: MySQL 8.0
Instance Class: db.r5.xlarge
Storage: 
  Type: Provisioned IOPS SSD (io1)
  Size: 500 GB
  IOPS: 3000
Multi-AZ: Enabled
Backup Retention: 7 days
Monitoring: Enhanced Monitoring enabled
Encryption: Enabled with KMS
VPC: Private subnets only
Security Groups: Restrict to application servers only
```

#### Development Configuration
```yaml
Engine: MySQL 8.0
Instance Class: db.t3.micro
Storage:
  Type: General Purpose SSD (gp2)
  Size: 20 GB
Multi-AZ: Disabled
Backup Retention: 1 day
Monitoring: Basic CloudWatch
Encryption: Optional
VPC: Can use public subnets for testing
Security Groups: Restricted access
```

## EC2 Integration Example

### EC2 Instance Setup

Now lunch a EC2 instance, EC2 > Instances > Launch instance.

Name and tags enter "node-app" as EC2 instance name.

Application and OS Images (Amazon Machine Image) select Ubuntu, keep Amazon Machine Image (AMI) as Ubuntu Server 22.04
LTS (HVM), SSD Volume Type and architecture as x86_64.

Instance type keep is as default t2.micro.

At key pair select my old key.

At Network settings keep default VPC, subnet, auto-assign public IP as Enable, for security group select my old security
group with SSH and HTTP access from anywhere.

Configure storage keep default 8 GiB General purpose SSD (gp23), 3000 IOPS.

Now click on "Launch instance".


## Practical Walkthrough Results

### Application Setup Results
```bash
ubuntu@ip-172-31-22-50:~$ sudo apt update
ubuntu@ip-172-31-22-50:~$ sudo apt install -y docker.io
ubuntu@ip-172-31-22-50:~$ sudo systemctl start docker
ubuntu@ip-172-31-22-50:~$ sudo systemctl enable docker
ubuntu@ip-172-31-22-50:~$ sudo usermod -aG docker $USER
ubuntu@ip-172-31-22-50:~$ sudo docker pull philippaul/node-mysql-app:02
# ... image pulling output ...
ubuntu@ip-172-31-22-50:~$ sudo docker run --rm -p 80:3000 \
  -e DB_HOST="database-1.ctsksu2mqlap.ap-southeast-1.rds.amazonaws.com" \
  -e DB_USER="admin" \
  -e DB_PASSWORD="<password>" \
  -d philippaul/node-mysql-app:02
0ea154b2d7a1b0828c0e82012380c2d233b4d473a8da7fb0b3f89853c95d2ef2

ubuntu@ip-172-31-22-50:~$ sudo docker ps
CONTAINER ID   IMAGE                          COMMAND                  CREATED         STATUS         PORTS                                     NAMES
0ea154b2d7a1   philippaul/node-mysql-app:02   "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds   0.0.0.0:80->3000/tcp, [::]:80->3000/tcp   adoring_chaplygin

ubuntu@ip-172-31-22-50:~$ sudo docker logs -f adoring_chaplygin
Server is running on http://localhost:3000
Database "my_app_db" is ready.
Using database "my_app_db"
Table "contacts" is ready.
```

Now if we visit the public ip of EC2 instance in browser, we can see the app running and we can add contacts to the
database.

Inside the EC2 terminal we can run the following command to connect to the RDS MySQL database:

```shell
docker run -it --rm mysql:8.0 mysql -h database-1.ctsksu2mqlap.ap-southeast-1.rds.amazonaws.com -u admin -p
```

Then enter the password you set during RDS creation.

### Database Connection Results
```bash
ubuntu@ip-172-31-22-50:~$ sudo docker run -it --rm mysql:8.0 mysql \
  -h database-1.ctsksu2mqlap.ap-southeast-1.rds.amazonaws.com -u admin -p
# ... mysql client pulling and connection ...
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 56
Server version: 8.0.41 Source distribution

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| my_app_db          |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> use my_app_db;
Database changed

mysql> show tables;
+---------------------+
| Tables_in_my_app_db |
+---------------------+
| contacts            |
+---------------------+
1 row in set (0.01 sec)

mysql> select * from contacts;
+----+-----------------------------+
| id | username                    |
+----+-----------------------------+
|  1 | asdf                        |
|  2 | adsf                        |
|  3 | lol                         |
|  4 | new user added from website |
+----+-----------------------------+
4 rows in set (0.00 sec)
```

Now at the database dashboard we will see two connections, one from the EC2 instance and one from the Docker container
running the Node.js app.

## RDS Advanced Features

### RDS Proxy
- **Connection Pooling**: Manage database connections efficiently
- **High Availability**: Automatic failover for database connections
- **Security**: IAM authentication and secret management
- **Performance**: Reduce connection overhead

### ElastiCache Integration
- **Caching Layer**: Redis or Memcached for application caching
- **Performance**: Reduce database load for read-heavy applications
- **Session Storage**: Store user sessions outside the application
- **Real-time Analytics**: Fast data access for analytics

### Database Migration
#### AWS Database Migration Service (DMS)
- **Homogeneous Migrations**: Same database engine
- **Heterogeneous Migrations**: Different database engines
- **Minimal Downtime**: Continuous replication during migration
- **Schema Conversion**: Convert database schemas

#### Migration Strategies
```
Migration Approaches:
1. Snapshot and Restore
   - Take snapshot of source database
   - Restore to RDS instance
   - Suitable for small databases with acceptable downtime

2. Database Migration Service (DMS)
   - Continuous replication
   - Minimal downtime
   - Suitable for production databases

3. Native Tools
   - MySQL: mysqldump, binary logs
   - PostgreSQL: pg_dump, logical replication
   - Manual process with more control
```

## Cost Optimization

### Pricing Models
#### On-Demand Instances
- **Pay-as-you-go**: Hourly pricing with no long-term commitment
- **Flexibility**: Start, stop, modify instances anytime
- **Use Cases**: Development, testing, unpredictable workloads

#### Reserved Instances
- **Cost Savings**: Up to 75% savings compared to On-Demand
- **Terms**: 1-year or 3-year commitments
- **Payment Options**: All upfront, partial upfront, no upfront
- **Use Cases**: Steady-state production workloads

### Cost Optimization Strategies
- **Right-sizing**: Match instance size to actual workload
- **Reserved Instances**: Use for predictable workloads
- **Storage Optimization**: Choose appropriate storage type
- **Backup Management**: Optimize backup retention periods
- **Multi-AZ Planning**: Balance availability with cost

### Cost Monitoring
```bash
# CloudWatch metrics for cost monitoring
- DatabaseConnections: Monitor connection usage
- CPUUtilization: Right-size instances
- FreeStorageSpace: Plan storage expansion
- ReadIOPS/WriteIOPS: Monitor storage performance

# AWS Cost Explorer
- Track RDS spending over time
- Compare costs across instances
- Identify cost optimization opportunities
```

## Troubleshooting Common Issues

### Connection Issues
#### Common Symptoms
- Application cannot connect to database
- Connection timeouts
- Authentication failures

#### Troubleshooting Steps
```bash
# 1. Check security groups
aws rds describe-db-instances --db-instance-identifier database-1

# 2. Verify network connectivity
telnet database-endpoint 3306

# 3. Check VPC configuration
aws ec2 describe-vpc-endpoints

# 4. Test DNS resolution
nslookup database-endpoint
```

### Performance Issues
#### Monitoring Key Metrics
- **CPU Utilization**: Should be < 80% consistently
- **Database Connections**: Monitor connection pool usage
- **IOPS**: Check if you're hitting storage limits
- **Query Performance**: Use Performance Insights

#### Optimization Strategies
- **Index Optimization**: Create appropriate indexes
- **Query Tuning**: Optimize slow-running queries
- **Connection Pooling**: Use RDS Proxy or application pooling
- **Scaling**: Vertical or horizontal scaling as needed

### Backup and Recovery Issues
#### Common Problems
- Backup failures
- Long restore times
- Point-in-time recovery confusion

#### Best Practices
- **Monitor Backup Status**: Check backup completion
- **Test Restores**: Regularly test backup restoration
- **Document Procedures**: Maintain recovery procedures
- **Retention Planning**: Balance cost with recovery needs

## Security Best Practices

### Network Security
#### VPC Configuration
```yaml
Production RDS Security:
VPC: Custom VPC (not default)
Subnets: Private subnets only
Security Groups:
  - Allow 3306 from application servers only
  - No public internet access
Route Tables: Private routes only
NACLs: Additional network restrictions
```

#### Security Group Rules
```bash
# Application server security group
Inbound:
- Port 3306: From application security group only
- No public access (0.0.0.0/0)

Outbound:
- Allow necessary outbound traffic only
```

### Data Protection
#### Encryption Best Practices
- **Enable Encryption at Rest**: Use KMS for all production databases
- **Encrypt Backups**: Ensure backups are encrypted
- **SSL/TLS**: Force encrypted connections
- **Key Management**: Regular key rotation

#### Access Control
- **IAM Integration**: Use IAM roles where possible
- **Database Users**: Minimal privilege principle
- **Regular Audits**: Review access permissions
- **Password Policies**: Strong password requirements

## Integration with Other AWS Services

### Compute Services
- **EC2**: Host application servers
- **Lambda**: Serverless database operations
- **ECS/EKS**: Container-based applications
- **Elastic Beanstalk**: Quick application deployment

### Storage Services
- **S3**: Store database backups and logs
- **EFS**: Shared file systems for applications
- **FSx**: High-performance file systems

### Analytics Services
- **Redshift**: Data warehousing for analytics
- **Athena**: Query data stored in S3
- **EMR**: Big data processing
- **Glue**: ETL operations

### Monitoring Services
- **CloudWatch**: Database monitoring and alerting
- **CloudTrail**: API call logging
- **Config**: Configuration compliance
- **Systems Manager**: Parameter management

## Migration and Modernization

### Legacy Database Migration
#### Assessment Phase
- **Inventory**: Catalog existing databases
- **Dependencies**: Map application dependencies
- **Performance**: Baseline current performance
- **Cost Analysis**: Compare current vs. cloud costs

#### Migration Planning
- **Migration Strategy**: Big bang vs. phased approach
- **Downtime Windows**: Plan for acceptable downtime
- **Rollback Plan**: Prepare for potential issues
- **Testing Strategy**: Comprehensive testing plan

### Modernization Opportunities
#### Database Engine Upgrades
- **Latest Versions**: Upgrade to latest engine versions
- **Performance Improvements**: Benefit from engine optimizations
- **Security Updates**: Latest security patches
- **New Features**: Access to latest database features

#### Architecture Improvements
- **Microservices**: Database per service pattern
- **Read Replicas**: Separate read and write workloads
- **Caching**: Implement ElastiCache for performance
- **Serverless**: Consider Aurora Serverless for variable workloads

# Practical
## Practical walkthrough Example 1
From RDS > Dashboard, click on "Create database". It will take you to the "Create database" page.

### Choose a database creation method
"Choose a database creation method" select "Standard Create".
* **Standard create**: You set all of the configuration options, including ones for availability, security, backups, and
  maintenance.
* **Easy create**: You set only the basic configuration options, and Amazon RDS sets the rest of the options for you. 
  * Use recommended best-practice configurations. Some configuration options can be changed after the database is created.

### Configuration
#### Engine options
"Engine options" select "MySQL" from the "Engine options" section.
* **Aurora (MySQL compatible)**: Up to 5x the performance of MySQL Community Edition.
* **Aurora (PostgreSQL compatible)**: Up to 3x the performance of PostgreSQL.
* **MySQL**: The most popular open-source relational database.
* **MariaDB**: A MySQL-compatible database with additional features.
* **PostgreSQL**: An advanced open-source object-relational database.
* **Oracle**: An enterprise-grade commercial database.
* **Microsoft SQL Server**: A relational database from Microsoft.

"Edition" is automatically selected as "MySQL Community" when you select "MySQL" from the "Engine options". And cannot 
be changed.

At "Engine version" will be filter options 
* Show only versions that support the Multi-AZ DB cluster
  * Create a A Multi-AZ DB cluster with one primary DB instance and two readable standby DB instances. Multi-AZ DB 
  clusters provide up to 2x faster transaction commit latency and automatic failover in typically under 35 seconds.
* Show only versions that support the Amazon RDS Optimized Writes
  * Amazon RDS Optimized Writes improves write throughput by up to 2x at no additional cost.

And version "MySQL 8.0.41" is selected by default and we will keep it as default. And will keep "Enable RDS Extended
Support" unchecked as default.

### Templates
"Templates" select "Production" from the "Templates" section.
* **Free tier**: Use the free tier for 12 months. The free tier includes 750 hours of db.t4g.micro instances each month,
  20 GB of storage, and 20 GB of backup storage.
  * Available EC2 instance types: db.t4g.micro, db.t3.micro.
* **Production**: Use production-ready configurations for high availability, performance, and security.
* **Dev/Test**: Use configurations for development and testing environments with lower availability and performance 
  requirements.

### Availability and durability
#### Deployment options
"Deployment options" select "Single-AZ DB instance" from the "Deployment options" section.
* **Multi-AZ DB cluster deployment (3 instances)** 
  * Create a Multi-AZ DB cluster with one primary DB instance and two readable standby DB instances. Multi-AZ DB clusters 
  provide up to 2x faster transaction commit latency and automatic failover in typically under 35 seconds.
    * 99.95% uptime
    * Redundancy across Availability Zones
    * Increased read capacity
    * Reduced write latency
* **Multi-AZ DB instance deployment (2 instances)**
  * Create a Multi-AZ DB instance with one primary DB instance and one standby DB instance. Multi-AZ DB instances provide 
  automatic failover in typically under 60 seconds.
    * 99.95% uptime
    * Redundancy across Availability Zones
    * Increased read capacity
    * Reduced write latency
* **Single-AZ DB instance deployment (1 instance)**
  * Create a Single-AZ DB instance with one primary DB instance. Single-AZ DB instances provide the lowest cost and are 
  suitable for development and testing environments.

### Settings
"Settings" enter "database-1" as DB instance identifier, "admin" as Master username and enter a password for the
Master password. And keep "Self managed" as Credentials management   
* **Managed in AWS Secrets Manager - most secure**: Use AWS Secrets Manager to manage your database credentials. 
  This option provides the highest level of security and is recommended for production environments.
* **Self managed - least secure**: Manage your database credentials yourself. This option is suitable for development 
  and testing environments.

### Instance configuration
#### DB instance class
"DB instance class" select "db.t3.micro" from the "DB instance class" section.

If **Multi-AZ DB cluster deployment (3 instances)** is selected then options are:
* Standard classes (includes m classes)
* Memory optimized classes (includes r classes)
* Compute optimized classes (includes c classes)

If **Multi-AZ DB instance deployment (2 instances)** is selected then options are:
* Standard classes (includes m classes)
* Memory optimized classes (includes r and x classes)
* Burstable classes (includes t classes)

If **Single-AZ DB instance deployment (1 instance)** is selected then options are:
* Standard classes (includes m classes)
* Memory optimized classes (includes r and x classes)
* Burstable classes (includes t classes)

### Storage
#### Storage type
"Storage type" select "General purpose SSD (gp2)" from the "Storage type" section to be in free tier. At production
use io1 or io2 storage types.
* **General purpose SSD (gp2)**: Balanced price and performance for most workloads.
* **Provisioned IOPS SSD (io1)**: High performance for I/O-intensive workloads.
* **Magnetic (standard)**: Low-cost storage for infrequently accessed data.

#### Allocated storage
Allocated storage giving 10 GiB.

From "Additional storage configuration" check "Enable storage autoscaling" to automatically increase the storage size
when needed. And keep "Maximum storage threshold" as default 1,000 IOPS.

### Connectivity 
#### Compute resource
"Compute resource" select "Don't connect to an EC2 compute resource" from the "Compute resource" section.
* **Connect to an existing EC2 compute resource**: Connect the RDS instance to an existing EC2 instance. This option 
  is suitable for applications that require low-latency access to the database. **Not recommended for production use**.
* **Don't connect to an EC2 compute resource**: Do not connect the RDS instance to an EC2 instance. This option is 
  suitable for applications that do not require low-latency access to the database.

#### Network type
"Network type" select "IPv4" from the "Network type" section.

#### Virtual private cloud (VPC)
"Virtual private cloud (VPC)" select "default VPC" from the "Virtual private cloud (VPC)" section.

#### Public access
"Public access" select "Yes" from the "Public access" section to access the RDS instance from the internet.
* **Yes**: Allow public access to the RDS instance. This option is suitable for development and testing environments.
* **No**: Do not allow public access to the RDS instance. This option is suitable for production environments.

#### VPC security group (firewall)
"VPC security group (firewall)" select "Create new" from the "VPC security group (firewall)" section and enter
"SG_created_by_RDS" as New VPC security group name, "Availability Zone" as "No preference".

#### RDS Proxy
RDS Proxy is a fully managed, highly available database proxy that improves application scalability, resiliency, and
security. RDS automatically creates an IAM role and a Secrets Manager secret for the proxy. RDS Proxy has additional 
costs.

Keep "Create an RDS Proxy" as unchecked as default.

#### Certificate authority - optional
Using a server certificate provides an extra layer of security by validating that the connection is being made to an
Amazon database. It does so by checking the server certificate that is automatically installed on all databases that you
provision.

If you don't select a certificate authority, RDS chooses one for you.

### Database authentication
"Database authentication" select "Password authentication" from the "Database authentication" section.
* **Password authentication**: Use a username and password to authenticate to the database. This option is suitable for
  most applications.
* **IAM database authentication**: Use AWS Identity and Access Management (IAM) to authenticate to the database. This 
  option is suitable for applications that require fine-grained access control and integration with other AWS services.
* **Password and IAM database authentication**: Use both password and IAM authentication. This option is suitable for 
  applications that require both types of authentication.

### Monitoring
"Monitoring" keep "Database Insights" as selected from the "Monitoring" section.
* **Database Insights - Advanced": Provides advanced monitoring and performance insights for the database. 
  This option is suitable for production environments.
* **Database Insights - Standard**: Provides basic monitoring and performance insights for the database. 
  This option is suitable for development and testing environments.

#### Enhanced Monitoring
Keep "Enable Enhanced monitoring" unchecked. If it is checked then there will be additional costs. Enhanced monitoring
provides real-time metrics for the database instance, including CPU, memory, disk, and network usage. It also provides
detailed information about the database engine, such as query performance and wait events.
If checked then will have "OS metrics granularity", "Monitoring role for OS metrics".

#### Log exports
There will be many options to export logs to CloudWatch Logs. We will keep all of them unchecked as default.
* **Audit log**: Export the database audit log to CloudWatch Logs.
* **Error log**: Export the database error log to CloudWatch Logs.
* **General log**: Export the database general log to CloudWatch Logs.
* **iam-db-auth-error log**: Export the IAM database authentication error log to CloudWatch Logs.
* **Slow query log**: Export the database slow query log to CloudWatch Logs.

### Additional configuration
#### Database options
#### Initial database name
TestDB
#### DB parameter group
Keep default "default.mysql8.0"
#### Option group
Keep default "default:mysql-8-0"

#### Backup
We will keep "Enable automated backups" unchecked. If we checked it then there will be "Backup retention period"
* **Backup retention period**: The number of days to retain automated backups. The default is 1 day, and the maximum is 
  35 days.
* **Backup window**: The time period during which automated backups are created. The default is a 30-minute window that
  starts at a random time within the specified time zone.
  * **Choose a window**: Select a time period during which automated backups are created. The default is a 30-minute window 
    that starts at a random time within the specified time zone.
  * **No preference**: RDS chooses a time period for automated backups. This option is suitable for most applications.

#### Backup replication
If "Enable automated backups" is checked then will have "Backup replication" options. If we checked "Enable replication
in another AWS Region" then will have
* **Destination Region**: The AWS Region to which the backups are replicated. This option is suitable for disaster 
  recovery and cross-region replication.
* **Replicated backup retention period**: The number of days to retain replicated backups. The default is 7 days, and 
  the maximum is 35 days.

#### Enable encryption
If we checked "Enable encryption" then will have "KMS key" options.

#### Maintenance
We can check "Enable automatic minor version upgrade" to automatically upgrade the database engine to the latest minor
version. This option is suitable for most applications.

#### Maintenance window
Select the period you want pending modifications or maintenance applied to the database by Amazon RDS.
* **Choose a window**: Select a start day like monday, tuesday, etc. and a start time like 00:00 UTC and duration
  like 30 minutes. This option is suitable for most applications.
* **No preference**: RDS chooses a time period for maintenance. This option is suitable for most applications.

#### Deletion protection
We can check "Enable deletion protection" to prevent accidental deletion of the database. This option is suitable for
production environments. If checked then will have "Deletion protection" options.

Now click on "Create database" button to create the RDS instance.

If we go to RDS > Databases, we can see the newly created RDS instance. It will take some time to create the RDS
instance. Once the status is "Available", we can connect to the database. Click on the RDS instance to see the
and will see new security group created by RDS but in that group we can see that port 3306 is not open to
everywhere. So we will give it access to everywhere by putting 0.0.0.0 as the source in the security group.

RDS > Databases, click on new RDS instance click on "Connectivity & security" tab, Enpoint and port will be there. Copy
those put those into Sqelectron using server address, port 3306, username admin and password you set during RDS
instance creation. After conneting to the db we can see all the databases and tables in the RDS instance.

While inside of the rds instance, we can click on "Actions" button and select "Create read replica" to create a read
replica of the RDS instance. (Althoungh for my case it is not available as I selected Single-AZ DB instance deployment
(1 instance) for free tier.) From "Actions" button we can also take snapshot of the RDS instance by selecting "Take
snapshot". Also can migrate the snapshot to another region by selecting "Migrate snapshot". 

For deleting first remove deleting protection by clicking on "Modify" button and unchecking "Enable deletion protection"
and then click on "Continue" button and then click on "Modify DB instance" button. After that click on "Actions" button 
andselect "Delete". In the delete confirmation dialog, check the "I acknowledge that I want to delete this DB instance"
checkbox also uncheck "Create final snapshot" and click on "Delete" button.



## Practical walkthrough Example 2
RDS > Databases, click on "Create database" button. It will take you to the "Create database" page.

Choose a database creation method > Standard Create.
Engine options > Aurora (MySQL compatible).
Engine version > Aurora MySQL 3.08.02 (compatible with MySQL 8.0.39) - default for major version 8.0 -> this was default
Template > Production.
DB cluster identifier > DB2
Master username -> Enter a username like "admin".
Credentials management > Self managed -> Enter a password for the master user.

### Cluster storage configuration
#### Configuration options
Choose "Aurora Standard" from the "Configuration options".

* **Aurora I/O-Optimized**: Use Aurora I/O-Optimized storage for improved performance and cost savings.
  * Predictable pricing for all applications. Improved price performance for I/O-intensive applications (I/O costs >25% 
    of total database costs).
  * No additional charges for read/write I/O operations. DB instance and storage prices include I/O usage.
* **Aurora Standard**: Use Aurora Standard storage for general-purpose workloads.
  * Cost-effective pricing for many applications with moderate I/O usage (I/O costs <25% of total database costs).
  * Pay-per-request I/O charges apply. DB instance and storage prices don’t include I/O usage.

### Instance configuration
Select "Bustable classes (includes t classes)" from the "DB instance class" section with db.t3.medium as default.
* **Serverless v2**: Automatically scales compute capacity based on workload demand. Ideal for variable workloads.
  * Capacity range
    * Minimum capacity (ACUs) 
    * Maximum capacity (ACUs)
    * Pause after inactivity
* **Memory optimized classes (includes r classes)**: High memory-to-CPU ratio for memory-intensive applications.
* **Burstable classes (includes t classes)**: Cost-effective option for workloads with variable CPU

### Availability & durability 
#### Multi-AZ deployment
Select "Don't create an Aurora Replica" 

* Create an Aurora Replica or Reader node in a different AZ (recommended for scaled availability)
* Don't create an Aurora Replica

### Connectivity 
Compute resource select "Don't connect to an EC2 compute resource" from the "Compute resource" section.
Network type select "IPv4" from the "Network type" section.
Virtual private cloud (VPC) select "default VPC" from the "Virtual private cloud (VPC)" section.
Public access select "Yes" from the "Public access" section to access the RDS instance from the internet.
VPC security group (firewall) select "Create new" from the "VPC security group (firewall)" section and enter
"SG_created_by_RDS_For_Aurora" as "New VPC security group name", "Availability Zone" as "No preference".

## Read replica write forwarding
Keep unchecked "Turn on local write forwarding" as default.

## Database authentication 
Keep all those unchecked as default.
* IAM database authentication
  * Authenticates using IAM database authentication.
* Kerberos authentication
  * Authenticates using Kerberos authentication through an AWS Directory Service for Microsoft Active Directory.


## Monitoring
select Database Insights - Standard




# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)
