# AWS ElastiCache
ElastiCache is a fully managed in-memory data store service provided by AWS, which supports two open-source in-memory 
caching engines: Redis and Memcached. It is designed to improve the performance of web applications by allowing you to 
retrieve information from fast, managed, in-memory caches instead of relying entirely on slower disk-based databases.

*   The same way RDS is to get managed Relational Databases...
*   ElastiCache is to get managed Redis or Memcached
*   Caches are in-memory databases with really high performance, low latency
*   Helps reduce load off of databases for read intensive workloads
*   Helps make your application stateless
*   AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and 
    backups
*   Using ElastiCache involves heavy application code changes

## ElastiCache Solution Architecture - DB Cache

*   Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache.
*   Helps relieve load in RDS
*   Cache must have an invalidation strategy to make sure only the most current data is used in there.

## ElastiCache Solution Architecture – User Session Store

*   User logs into any of the application
*   The application writes the session data into ElastiCache
*   The user hits another instance of our application
*   The instance retrieves the data and the user is already logged in

## ElastiCache – Redis vs Memcached

| REDIS | MEMCACHED |
| :--- | :--- |
| • Multi AZ with Auto-Failover | • Multi-node for partitioning of data (sharding) |
| • Read Replicas to scale reads and have high availability | • No high availability (replication) |
| • Data Durability using AOF persistence | • Non persistent |
| • Backup and restore features | • Backup and restore (Serverless) |
| • Supports Sets and Sorted Sets | • Multi-threaded architecture |



# Practical
# Creating Redis Cluster
From ElastiCache dashboard, click on "Get Started" > select "Redis OSS". Now we will be on "Create Redis OSS cache" page.

## Configuration
#### Engine
Select "Redis OSS" as the engine. All options are
* Valkey
* Memcached
* Redis OSS

#### Deployment option
Select "Design your own cache" to create a cluster with your own specifications. All options are
* Serverless
  * Use to quickly create a cache that automatically scales to meet application traffic demands, with no servers to
   manage.
* Design your own cache
  * Use to create a cache with your own specifications, such as the number of nodes, node type, and replication factor.

#### Creation method
Select "Custom create" to create a cache with your own specifications. All options are
* Easy create
  * Use to quickly create a cache with the default settings.
* Custom create
  * Use to create a cache with your own specifications, such as the number of nodes, node type, and replication factor.
* Restore from backup
  * Use to restore a cache from a backup.      

## Cluster mode
Select "Disabled" to create a single-node cluster. All options are
* Enabled
  * Cluster mode enables replication across multiple shards for enhanced scalability and availability.
* Disabled
  * The Redis OSS cluster will have a single shard (node group) with one primary node and up to 5 read replica.

## Cluster info
Type name `TestRedisCluster` in the "Name" field. This is the name of the cluster that will be created.

## Location
#### Loacation
Select "AWS Cloud" as the location. All options are
* AWS Cloud
  * Use the AWS Cloud for your ElastiCache instances.
* On premises
 * Create your ElastiCache instances on an Outpost (through AWS Outposts). You need to create a subnet ID on an Outpost 
   first.

As we select there will be Multi-AZ heading with checkbox "Enable" to enable Multi-AZ with automatic failover. This is
recommended for production workloads to ensure high availability.

And auto failover will be automatically enabled when you select Multi-AZ. 

## Cache settings
keep Engine version as default, which is `7.0.5`, port as `6379`, parameter groups as `default.redis7.0` as default.
Select `cache.t2.micro` as the node type. This is the smallest node type available for testing purposes. And "Number of nodes" as `0` to create a single-node cluster.

## Subnet group settings
Give name as `TestRedisSubnetGroup` in the "Name" field. This is the name of the subnet group that will be created, 
select default VPC, and it will automatically select the default subnets in the VPC.

## Available Zone placement
Select "No preference" for the availability zone placement. This is recommended for testing purposes. In production,
you should select a specific availability zone to ensure high availability.

Click on "Next" to go to the next step.

## Security
#### Encryption at rest
If we checked "Enable" for encryption at rest, it will automatically select the default KMS key for encryption. This is recommended for production workloads to ensure data security. Or we can select a custom KMS key if we have one.
We will uncheck this.

#### Encryption in transit
If we checked "Enable" for encryption in transit. At "Access control" section we will have
* No access control
  * Use this option to allow all clients to connect to the cache without authentication.
* AUTH default user access
  * Use this option to allow clients to connect to the cache using the default user credentials. And a text box will appear to enter the authentication token.
* User group access control list
  * Use this option to allow clients to connect to the cache using user groups. And a text box will appear to enter the user group name.

we will uncheck this.


## Backup 
If we checked "Enable automatic backup" for backup, will ask for "Backup retention period" with options 1 to 35 days. 
This is recommended for production workloads to ensure data durability. Backup window with "No preference" and "Specific 
time" options. 

We will uncheck "Enable automatic backup" for testing purposes.


## Maintenance
#### Maintenance window
* No preference
* Specify maintenance window
  * Use this option to specify a maintenance window for the cache. This is recommended for production workloads to ensure that maintenance tasks do not interfere with application performance.

We will select "No preference" for testing purposes.

#### Auto upgrade minor versions
Check "Enable" to automatically upgrade minor versions of the cache engine. This is recommended for production workloads to ensure that the cache is always up to date with the latest features and security patches.

## Logs
#### Slow log
Check "Enable" to enable slow log for the cache. This is recommended for production workloads to help identify performance issues.
#### Engine log
Check "Enable" to enable engine log for the cache. This is recommended for production workloads to help identify issues with the cache engine.

Click on "Next" to go to the next step.

After reviewing the configuration, click on "Create" to create the Redis cluster.

Now from ElastiCache dashboard, we can see the newly created Redis cluster with the name `TestRedisCluster`. If we click
on that at "Cluster details" card will have the details of the cluster, and endpoint URL to connect to the cluster.


## ElastiCache – Cache Security

*   ElastiCache supports IAM Authentication for Redis
*   IAM policies on ElastiCache are only used for AWS API-level security
*   Redis AUTH
    *   You can set a “password/token” when you create a Redis cluster
    *   This is an extra level of security for your cache (on top of security groups)
    *   Support SSL in flight encryption
*   Memcached
    *   Supports SASL-based authentication (advanced)

## Patterns for ElastiCache

*   Lazy Loading: all the read data is cached, data can become stale in cache
*   Write Through: Adds or update data in the cache when written to a DB (no stale data)
*   Session Store: store temporary session data in a cache (using TTL features)
*   Quote: There are only two hard things in Computer Science: cache invalidation and naming things

## ElastiCache – Redis Use Case

*   Gaming Leaderboards are computationally complex
*   Redis Sorted sets guarantee both uniqueness and element ordering
*   Each time a new element added, it’s ranked in real time, then added in correct order


# Resources
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)
