# AWS S3 (Simple Storage Service)

**AWS S3 (Simple Storage Service)** is a cloud-based storage service that allows you to store, manage, and retrieve
large amounts of data like files, images, videos, and backups securely and at scale.

It provides highly reliable, scalable object storage, making your data accessible from anywhere, anytime, via the
internet.

## Key S3 Features

* S3 Versioning
* S3 Replication
* Data Encryption
* S3 Bucket Policies
* S3 Storage Classes
* Logging Monitoring
* Hosting Static website
* Snow Family
* Storage gateway (hybrid solution)

## S3 Core Concepts

### Object Storage Architecture
* **Store data as objects**: Files stored as objects within buckets
* **Globally unique name**: Bucket names must be unique across all AWS accounts
* **Region specific**: Buckets exist in specific AWS regions
* **Key-value pairs**: Each object within a bucket is stored as a key-value pair
    * **Key**: The object's name (which can contain slashes /, mimicking directory structure)
    * **Value**: The content of the object (the file/data itself)

### S3 Object Structure
```
Bucket: my-company-bucket
├── documents/
│   ├── report-2024.pdf
│   └── presentation.pptx
├── images/
│   ├── logo.png
│   └── banner.jpg
└── index.html
```

### Object Limitations and Best Practices
#### Maximum Object Size:
* **5 TB (Terabytes)** is the maximum size for a single object in Amazon S3
* **Multipart upload** is recommended for objects larger than **5 GB** (split the file into smaller parts and upload them separately)
* **Maximum PUT size**: 5 GB in a single PUT operation

#### Multipart Upload Benefits
- **Improved throughput**: Upload parts in parallel
- **Quick recovery**: Restart failed uploads from where they stopped
- **Better network utilization**: Efficient use of bandwidth
- **Required for objects > 5GB**: Mandatory for large files

### S3 Naming Conventions
#### Bucket Naming Rules
- **Length**: 3-63 characters
- **Characters**: Lowercase letters, numbers, hyphens, periods
- **Start/End**: Must start and end with letter or number
- **No IP addresses**: Cannot be formatted as IP address
- **Global uniqueness**: Must be unique across all AWS accounts worldwide

#### Object Key Best Practices
- **Avoid sequential prefixes**: Distribute load across partitions
- **Use meaningful names**: Descriptive object keys
- **Consider access patterns**: Structure keys for efficient retrieval
- **Case sensitivity**: Object keys are case-sensitive

### S3 Storage Architecture
```
AWS Account
├── Region: us-east-1
│   ├── Bucket: company-data-2024
│   │   ├── Object: documents/report.pdf
│   │   ├── Object: images/logo.png
│   │   └── Object: backup/db-dump.sql
│   └── Bucket: website-assets
├── Region: eu-west-1
│   └── Bucket: eu-compliance-data
```

## S3 Bucket Policies

JSON-based access control policies that you attach directly to an S3 bucket to manage permissions for accessing the
bucket and its objects.

They allow you to define who can access the data and what actions they can perform, such as read, write, or delete,
enabling fine-grained control over the security of your data stored in S3.

### Key S3 Actions
* **GetObject**: Used to retrieve or download files from an S3 bucket
* **PutObject**: Used to upload or add files into an S3 bucket
* **DeleteObject**: Remove objects from the bucket
* **ListBucket**: List objects in the bucket
* **GetBucketLocation**: Get bucket region information

### Bucket Policy Components
- **Version**: Policy language version (typically "2012-10-17")
- **Statement**: Array of permission statements
- **Effect**: Allow or Deny
- **Principal**: Who the policy applies to
- **Action**: What actions are permitted/denied
- **Resource**: Which resources the policy applies to
- **Condition**: Optional conditions for when the policy applies

### Common Bucket Policy Examples

#### Public Read Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

#### Restrict Access by IP Address
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "IPRestriction",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::your-bucket-name",
        "arn:aws:s3:::your-bucket-name/*"
      ],
      "Condition": {
        "NotIpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        }
      }
    }
  ]
}
```

#### Time-Based Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "TimeBasedAccess",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*",
      "Condition": {
        "DateGreaterThan": {
          "aws:CurrentTime": "2024-01-01T00:00:00Z"
        },
        "DateLessThan": {
          "aws:CurrentTime": "2024-12-31T23:59:59Z"
        }
      }
    }
  ]
}
```

### Policy Generator Workflow
* **Step 1**: Access AWS Policy Generator
* **Step 2**: Select "S3 Bucket Policy" as policy type
* **Step 3**: Configure Effect, Principal, Actions, and ARN
* **Step 4**: Add statement and generate policy
* **Step 5**: Copy and paste into bucket policy editor
* **Step 6**: Modify as needed (e.g., add /* for object access)

## S3 Replication

It allows you to automatically copy objects from one S3 bucket to another S3 bucket,
* within the same region (Same-Region Replication - SRR) or
* in different regions (Cross-Region Replication - CRR).

It's commonly used for compliance, redundancy, and to improve data access performance by maintaining copies closer to
your users.

### Types of S3 Replication

#### Same-Region Replication (SRR)
- **Use Cases**: Compliance, log aggregation, live replication between accounts
- **Benefits**: Lower latency, lower cost
- **Scenarios**: Backup within region, account separation, test/dev environments

#### Cross-Region Replication (CRR)
- **Use Cases**: Disaster recovery, global content distribution, data residency
- **Benefits**: Geographic redundancy, reduced latency for global users
- **Scenarios**: Multi-region backup, compliance requirements, global applications

### Replication Requirements
- **Source bucket versioning**: Must be enabled
- **Destination bucket versioning**: Must be enabled
- **IAM permissions**: Proper permissions for replication service
- **Different buckets**: Cannot replicate to the same bucket

### Replication Configuration Options

#### Replication Scope
- **Entire bucket**: Replicate all objects
- **Prefix-based**: Replicate objects with specific prefixes
- **Tag-based**: Replicate objects with specific tags
- **Combination**: Use multiple filters together

#### Replication Features
- **Storage class**: Change storage class during replication
- **Encryption**: Maintain or change encryption settings
- **Access control**: Maintain or modify object ACLs
- **Metadata**: Preserve object metadata
- **Delete markers**: Replicate delete markers (optional)

### Replication Monitoring
```bash
# CloudWatch metrics for replication
- ReplicationLatency: Time to replicate objects
- BytesPendingReplication: Data waiting to be replicated
- OperationsPendingReplication: Number of operations pending

# S3 Replication metrics
aws s3api get-bucket-replication --bucket source-bucket-name
aws s3api get-bucket-replication-configuration --bucket source-bucket-name
```

### Replication Costs
- **Cross-region data transfer**: Charges apply for CRR
- **Storage costs**: Pay for storage in destination region
- **Request costs**: PUT/COPY requests for replication
- **Same-region replication**: No data transfer charges for SRR

## S3 Storage Classes

| S3 Storage Class            | Use Case                                            | Features                                                               | Cost                                                               |
|-----------------------------|-----------------------------------------------------|------------------------------------------------------------------------|--------------------------------------------------------------------|
| **S3 Standard**             | Frequently accessed data                            | High durability, high availability, low latency                        | Most expensive, designed for frequent access                       |
| **S3 Intelligent-Tiering**  | Data with unknown or unpredictable access patterns  | Moves data automatically between frequent and infrequent tiers         | Slightly higher than Standard but cost-saving based on usage       |
| **S3 Standard-IA**          | Infrequently accessed but quickly retrievable       | Lower cost for storage, higher cost for data retrieval                 | Lower than Standard, ideal for less frequent access                |
| **S3 One Zone-IA**          | Non-critical, infrequently accessed data            | Stored in a single Availability Zone, lower resilience                 | Cheaper than Standard-IA, good for non-critical data               |
| **S3 Glacier**              | Archival data, rarely accessed                      | Very low storage cost, retrieval time from minutes to hours            | Ideal for long-term archives with low cost                         |
| **S3 Glacier Deep Archive** | Deep archival data, almost never accessed           | Lowest cost, retrieval time up to 12 hours                             | Cheapest storage, ideal for compliance or long-term retention      |
| **S3 Outposts**             | Local storage using AWS Outposts                    | Provides S3 API locally, meets on-premises requirements                | Dependent on Outposts infrastructure usage                         |

### Detailed Storage Class Analysis

#### S3 Standard
- **Availability**: 99.99%
- **Durability**: 99.999999999% (11 9's)
- **Minimum Storage Duration**: None
- **Retrieval Fee**: None
- **First Byte Latency**: Milliseconds
- **Use Cases**: Websites, content distribution, mobile applications

#### S3 Intelligent-Tiering
- **Automatic Cost Optimization**: Moves objects between access tiers
- **No Retrieval Fees**: No fees for accessing objects
- **Monitoring Fee**: Small monthly fee per object
- **Access Tiers**:
    - Frequent Access (0-30 days)
    - Infrequent Access (30+ days)
    - Archive Instant Access (90+ days)
    - Archive Access (90+ days, optional)
    - Deep Archive Access (180+ days, optional)

#### S3 Standard-IA (Infrequent Access)
- **Availability**: 99.9%
- **Minimum Storage Duration**: 30 days
- **Retrieval Fee**: Per GB retrieved
- **Use Cases**: Backups, older data, disaster recovery

#### S3 One Zone-IA
- **Single AZ Storage**: Data stored in one Availability Zone
- **20% Less Expensive**: Than Standard-IA
- **Lower Availability**: 99.5%
- **Use Cases**: Secondary backups, recreatable data

#### S3 Glacier Instant Retrieval
- **Archive Storage**: Long-term archival with instant access
- **Minimum Storage Duration**: 90 days
- **Retrieval Time**: Milliseconds
- **Use Cases**: Medical images, news media archives

#### S3 Glacier Flexible Retrieval
- **Retrieval Options**:
    - Expedited: 1-5 minutes
    - Standard: 3-5 hours
    - Bulk: 5-12 hours
- **Minimum Storage Duration**: 90 days
- **Use Cases**: Backup and disaster recovery

#### S3 Glacier Deep Archive
- **Lowest Cost**: Cheapest storage option
- **Retrieval Time**: 12-48 hours
- **Minimum Storage Duration**: 180 days
- **Use Cases**: Compliance archives, digital preservation

### Storage Class Comparison Matrix

| Feature                | Standard  | Intelligent-Tiering | Standard-IA  | One Zone-IA  | Glacier IR  | Glacier FR  | Deep Archive  |
|------------------------|-----------|---------------------|--------------|--------------|-------------|-------------|---------------|
| **Availability**       | 99.99%    | 99.9%               | 99.9%        | 99.5%        | 99.9%       | 99.99%      | 99.99%        |
| **AZs**                | ≥3        | ≥3                  | ≥3           | 1            | ≥3          | ≥3          | ≥3            |
| **Min Duration**       | None      | None                | 30 days      | 30 days      | 90 days     | 90 days     | 180 days      |
| **Retrieval Fee**      | None      | None                | Per GB       | Per GB       | Per GB      | Per GB      | Per GB        |
| **First Byte Latency** | ms        | ms                  | ms           | ms           | ms          | min-hrs     | hrs           |

## S3 Bucket Lifecycle

You can use lifecycle policies to control the movement of objects between different storage classes or delete them
entirely, based on specific conditions like age or inactivity.

### Lifecycle Policy Components
- **Rules**: Define actions and filters
- **Actions**: Transition or expiration actions
- **Filters**: Object prefixes, tags, or object size
- **Timeline**: When actions should occur

### Lifecycle Actions

#### Transition Actions
- **Storage Class Transitions**: Move objects between storage classes
- **Minimum Days**: Respect minimum storage duration requirements
- **Cost Optimization**: Automatic cost reduction over time

#### Expiration Actions
- **Delete Objects**: Automatically delete objects after specified time
- **Delete Incomplete Multipart Uploads**: Clean up failed uploads
- **Delete Previous Versions**: Manage versioned object cleanup

### Lifecycle Transition Rules
```
S3 Standard → S3 Standard-IA (minimum 30 days)
S3 Standard-IA → S3 One Zone-IA (no minimum wait)
S3 Standard-IA → S3 Glacier IR (minimum 30 days total)
S3 Glacier IR → S3 Glacier FR (minimum 90 days total)
S3 Glacier FR → S3 Glacier Deep Archive (minimum 90 days total)
```

### Example Lifecycle Policy
```json
{
  "Rules": [
    {
      "ID": "ArchivePolicy",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "documents/"
      },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "Expiration": {
        "Days": 2555
      }
    }
  ]
}
```

### Lifecycle Best Practices
- **Analyze Access Patterns**: Use S3 Storage Class Analysis
- **Test Policies**: Start with non-critical data
- **Monitor Costs**: Track cost savings from lifecycle policies
- **Consider Retrieval Costs**: Factor in future access needs
- **Version Management**: Include versioning in lifecycle policies

## S3 Snow Family

The S3 Snow Family is a group of physical devices offered by AWS to help move large amounts of data to the cloud when
using the internet isn't practical.

These devices are used when there's too much data to upload over a regular connection or when dealing with remote areas 
without good internet.

### Snow Family Devices

#### AWS Snowcone
- **Capacity**: 8 TB HDD or 14 TB SSD
- **Form Factor**: Small, portable (4.5 lbs)
- **Use Cases**: Edge computing, data collection at remote locations
- **Network**: Wi-Fi, wired ethernet
- **Security**: 256-bit encryption, tamper-resistant
- **Power**: Battery powered or AC adapter

#### AWS Snowball Edge
- **Storage Optimized**: 80 TB usable capacity
- **Compute Optimized**: 42 TB + GPU capabilities
- **Network**: 10/25/40/100 Gbps
- **Edge Computing**: Run EC2 instances and Lambda functions
- **Use Cases**: Large data migrations, edge computing workloads

#### AWS Snowmobile
- **Capacity**: Up to 100 PB per device
- **Form Factor**: 45-foot shipping container
- **Security**: Dedicated security vehicle, GPS tracking, 24/7 monitoring
- **Use Cases**: Datacenter migrations, exabyte-scale transfers
- **Timeline**: Typically 6 months for full transfer

### Snow Family Use Cases

#### Data Migration Scenarios
- **Network Limitations**: Limited bandwidth or high costs
- **Time Constraints**: Faster than internet transfer
- **Security Requirements**: Encrypted physical transport
- **Remote Locations**: Areas with poor connectivity

#### Transfer Time Comparison
```
1 TB over different connections:
- T1 (1.5 Mbps): 82 days
- T3 (45 Mbps): 2.8 days
- 100 Mbps: 1.2 days
- 1000 Mbps: 2.9 hours
- Snowball Edge: 1 day (including shipping)
```

### Snow Family Workflow
1. **Request Device**: Order through AWS console
2. **Receive Device**: AWS ships encrypted device
3. **Load Data**: Transfer data using provided tools
4. **Ship Back**: Return device to AWS facility
5. **Data Import**: AWS imports data to S3
6. **Verification**: Confirm successful transfer

## Amazon S3 Storage Gateway

It is a hybrid cloud storage service that connects on-premises environments to cloud storage in Amazon S3. It helps
extend your local storage to the cloud by acting as a bridge.

### Storage Gateway Types

#### File Gateway
- **Protocol**: NFS/SMB
- **Use Case**: File shares in the cloud
- **Caching**: Local cache for frequently accessed data
- **Integration**: Direct access to S3 objects
- **Benefits**: Seamless cloud file storage

#### Volume Gateway
##### Stored Volumes
- **Primary Storage**: On-premises
- **Backup**: Asynchronous backup to S3
- **Capacity**: 1 GB to 16 TB per volume
- **Use Case**: Low-latency access to entire dataset

##### Cached Volumes
- **Primary Storage**: S3
- **Cache**: Frequently accessed data on-premises
- **Capacity**: 1 GB to 32 TB per volume
- **Use Case**: Large datasets with frequent access to subset

#### Tape Gateway (VTL)
- **Virtual Tape Library**: Replace physical tape infrastructure
- **Integration**: Works with existing backup software
- **Storage**: Virtual tapes stored in S3 and Glacier
- **Use Case**: Replace tape backup systems

### Storage Gateway Benefits
- **Hybrid Integration**: Seamless on-premises to cloud
- **Cost Effective**: Reduce on-premises storage costs
- **Scalability**: Virtually unlimited cloud storage
- **Security**: Encryption in transit and at rest
- **Compliance**: Meet data residency requirements

## Practical Example: Static Website Hosting

### Step-by-Step Website Hosting

From homepage of S3, click on Create bucket, keep bucket type as general purpose, and the region will be auto selected
according to your location for our case with is `ap-southeast-1`, giving bucket name `jahid-demobucket-abcd12345`, keep
object ownership as ACLs disabled, block public access as enabled, versioning as disabled, encryption as Server-side
encryption with Amazon S3 managed keys (SSE-S3), bucket key enabled, object lock disabled, tags as empty and click on
Create bucket.

Now at Amazon S3 > General purpose buckets, we will see our bucket `jahid-demobucket-abcd12345` created. Click on the
bucket name and click on upload, click add files and add our `index.html`, `default.css`, `Dennis.jpg` from the static
site folder and click on upload.

Clicking on the individual files in the bucket we can see the properties of the files, and can get preview of the files
without downloading them.

#### Host static website

We uploaded all of our site files to the bucket, but while we're visiting index.html we are not getting the css, image
as it does not get accessed by default. To fix this we need to enable static website hosting.

Select the bucket from Amazon S3 > General purpose buckets, click on Properties tab, scroll down to Static website
hosting and click on Edit, select Enable the static website hosting, hosting type as Host a static website, keep the
index document as `index.html` and skip the error document, click on Save changes.

Now at the properties tab, scroll down to Static website hosting there will be Bucket website endpoint, copy the
endpoint and paste it in the browser, then we will see

```shell
403 Forbidden
Code: AccessDenied
Message: Access Denied
RequestId: <RequestId>
HostId: <HostId>
```

This is because our website is open but the bucket is not public. So select Permissions tab, scroll down to Block public
access (bucket settings) and click on Edit, uncheck Block all public access, click on Save Changes, then type `confirm`
in the confirmation box and click on Confirm.

Despite those if we try to access the website we will still get the 403 Forbidden error, this is because we need to add
a bucket policy to allow public access to the bucket.

Select Permissions tab, scroll down to Bucket policy and click on Edit, then click on Policy generator, select Select
Type of Policy to S3 bucket policy, select Effect as Allow, select Principal as * (all users), Actions as GetObject,
and copy the arn from previous page and paste it here. Click on Add statement, then click on Generate policy, it gives
us

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Statement1",
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "<arn>"
    }
  ]
}
```

We need a small change here, we need to just add a forward slash and star at the end of the Resource, so it will be

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Statement1",
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "<arn>/*"
    }
  ]
}
```

Now click on Save changes, then we will be able to access the website from the bucket website endpoint.

### Static Website Hosting Configuration

#### Required Settings
1. **Bucket Configuration**:
    - Enable static website hosting
    - Set index document (e.g., index.html)
    - Optional: Set error document (e.g., error.html)

2. **Public Access**:
    - Disable "Block all public access"
    - Add bucket policy for public read access

3. **DNS Configuration** (Optional):
    - Use Route 53 for custom domain
    - Configure CNAME or alias records

#### Website Hosting Features
- **Custom Domain**: Use your own domain name
- **HTTPS**: Use CloudFront for HTTPS support
- **Error Pages**: Custom error document handling
- **Redirects**: Redirect requests to other URLs
- **Index Documents**: Default document for directory requests

#### Static Website Limitations
- **No Server-Side Processing**: Only static content
- **No HTTPS**: Direct S3 website endpoints don't support HTTPS
- **Limited Authentication**: Basic bucket policy authentication only
- **No CGI/PHP**: Cannot run server-side scripts

## S3 Versioning

### Enable Versioning Example

If we want to add bucket versions, select the bucket from Amazon S3 > General purpose buckets, click on Properties tab,
scroll down to Bucket Versioning and click on Edit, select Enable, click on Save changes. Now we can see the versioning
enabled in the bucket properties. But we will not be able to see the versions of the files we uploaded before enabling
versioning, we will only be able to see the versions of the files uploaded after enabling versioning.

Now like if we make any change on `index.html` file and upload it again, we will see the new version of the file in the
bucket. We can also see the versions of the files in the bucket by clicking on the "Show versions" button at the top
of the bucket page. This will show us the versions of the files in the bucket, and we can also delete a specific version
of a file by clicking on the version and then clicking on Delete. If we delete the latest version of a file, the
previous version will become the latest version.

### Versioning Concepts
- **Version ID**: Unique identifier for each object version
- **Current Version**: Latest version of the object
- **Previous Versions**: Older versions maintained in bucket
- **Delete Markers**: Special markers when objects are "deleted"

### Versioning States
- **Unversioned**: Default state, no versioning
- **Versioning-enabled**: New versions created for modifications
- **Versioning-suspended**: No new versions, but existing versions remain

### Versioning Benefits
- **Data Protection**: Protect against accidental deletion/modification
- **Change History**: Maintain complete change history
- **Easy Recovery**: Restore previous versions easily
- **Compliance**: Meet regulatory requirements for data retention

### Versioning Costs
- **Storage**: Pay for all versions stored
- **Requests**: Each version counts as separate object
- **Data Transfer**: Transfer costs apply to all versions
- **Lifecycle**: Use lifecycle policies to manage version costs

## S3 Replication Practical Example

Create another bucket with name `jahid-demobucket-abcd12345-replica` in the same region, with versioning enabled. On the
original bucket, select the Management tab, scroll down to Replication rules and click on Create replication rule, give
name `demo-bucket-replication-rule`, skipping status as Enabled(Choose whether the rule will be enabled or disabled when
created.), source bucket and source region will be auto selected, skipping "Choose a rule scope" to the default value
"Apply to all objects in the bucket". At Destination section, select "Choose a bucket in this account" and enter the
bucket name `jahid-demobucket-abcd12345-replica` and Destination Region will be auto selected. For IAM role, select
"Create a new role". And not selecting anything from Encryption, Destination storage class, Additional replication
options.

"Replicate existing objects?" popup will appear, select "Yes, replicate existing objects" and click on Submit.

Now we will redirect to Create Batch Operators job page, skipping "Job run options" to its default "Automatically run
the job when it's ready". Unchecking Generate completion report. For "Permission to access the specified resources"
select "Create new role" then click on Save.

We paste one file at the original bucket, then it automatically get replicated at the replica bucket.

### Replication Configuration Details

#### Replication Prerequisites
- **Source versioning**: Must be enabled
- **Destination versioning**: Must be enabled
- **IAM permissions**: Replication service needs proper permissions
- **Different buckets**: Cannot replicate to same bucket

#### Replication Options
- **Existing objects**: Choose whether to replicate existing objects
- **Delete marker replication**: Replicate delete markers
- **Replica modification sync**: Keep replicas in sync
- **Storage class**: Change storage class during replication

## S3 Lifecycle Practical Example

For lifecycle select the previous `jahid-demobucket-abcd12345` bucket, select the Management tab, scroll down to
Lifecycle rules and click on Create lifecycle rule, give name `jahid-demobucket-lifecycle-rule`, choose
"Apply to all objects in the bucket" for "Choose a rule scope". At "Lifecycle rule actions" choose "Transition current
versions of objects between storage classes". At new section "Transition current versions of objects between storage
classes" select "Standard-IA" for class and 30 for days after object creation. Click on "Add transition" then select
"One Zone-IA" class with time 60. Click on "Add transition" then select "Glacier Instant Retrieval" class with time 90.

### Lifecycle Rule Configuration

#### Rule Scope Options
- **Apply to all objects**: Entire bucket
- **Limit scope with filters**: Specific prefixes or tags
- **Object size filters**: Minimum/maximum object size

#### Transition Timeline Example
```
Day 0: Object created in S3 Standard
Day 30: Transition to S3 Standard-IA
Day 60: Transition to S3 One Zone-IA
Day 90: Transition to S3 Glacier Instant Retrieval
Day 365: Transition to S3 Glacier Deep Archive
Day 2555: Delete object (7 years retention)
```

#### Cost Impact Analysis
```
Storage cost reduction over time:
- S3 Standard: $0.023/GB/month
- S3 Standard-IA (30 days): $0.0125/GB/month
- S3 One Zone-IA (60 days): $0.01/GB/month
- S3 Glacier IR (90 days): $0.004/GB/month
- S3 Glacier Deep Archive (365 days): $0.00099/GB/month
```

## S3 Security and Access Control

### Access Control Methods
- **IAM Policies**: User/role-based permissions
- **Bucket Policies**: Resource-based permissions
- **ACLs**: Object/bucket level permissions (legacy)
- **Access Points**: Simplified access management for applications

### S3 Encryption Options

#### Server-Side Encryption (SSE)
- **SSE-S3**: Amazon S3 managed keys
- **SSE-KMS**: AWS Key Management Service
- **SSE-C**: Customer-provided keys

#### Client-Side Encryption
- **CSE-KMS**: AWS KMS managed keys
- **CSE-C**: Customer managed keys

### S3 Access Logging
- **Server Access Logs**: Detailed request logs
- **CloudTrail**: API call logging
- **VPC Flow Logs**: Network traffic logs
- **CloudWatch**: Monitoring and alerting

## S3 Performance Optimization

### Request Performance
- **Request Rate**: 3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD requests per second per prefix
- **Prefix Distribution**: Distribute requests across multiple prefixes
- **Multipart Upload**: For objects > 100 MB

### Transfer Acceleration
- **CloudFront Edge Locations**: Faster uploads via edge locations
- **Global Users**: Improved performance for global user base
- **Additional Cost**: Extra charges apply

### Performance Best Practices
- **Retry Logic**: Implement exponential backoff
- **Connection Pooling**: Reuse HTTP connections
- **Range GETs**: Download partial objects
- **Parallel Uploads**: Use multipart upload for large files

## S3 Cost Optimization

### Cost Components
- **Storage**: Per GB-month charges
- **Requests**: PUT, GET, DELETE charges
- **Data Transfer**: Out to internet charges
- **Management**: Inventory, analytics, tagging

### Cost Optimization Strategies
- **Storage Classes**: Use appropriate storage class
- **Lifecycle Policies**: Automatic transitions
- **Intelligent Tiering**: Automatic cost optimization
- **Request Optimization**: Minimize expensive operations
- **Data Transfer**: Use CloudFront for distribution

### Cost Monitoring Tools
- **AWS Cost Explorer**: Analyze S3 spending
- **S3 Storage Class Analysis**: Analyze access patterns
- **S3 Inventory**: Track object metadata and costs
- **Budgets and Alerts**: Monitor spending thresholds

## S3 Compliance and Governance

### Compliance Features
- **Object Lock**: WORM (Write Once Read Many) compliance
- **Legal Hold**: Prevent object deletion for legal reasons
- **Retention Policies**: Automatic retention management
- **Audit Logs**: Complete audit trail

### Data Governance
- **Data Classification**: Tag and categorize data
- **Access Reviews**: Regular access permission reviews
- **Data Lifecycle**: Automated data management
- **Cross-Region Compliance**: Meet data residency requirements

## S3 Integration with Other AWS Services

### Compute Integration
- **Lambda**: Event-driven processing
- **EC2**: Direct access via IAM roles
- **ECS/EKS**: Container storage
- **Batch**: Large-scale batch processing

### Analytics Integration
- **Athena**: Query S3 data with SQL
- **Redshift**: Data warehouse integration
- **EMR**: Big data processing
- **Glue**: ETL and data catalog

### Database Integration
- **RDS**: Database backup storage
- **DynamoDB**: Export/import capabilities
- **DocumentDB**: Backup and restore
- **Neptune**: Graph database backups

### Content Delivery
- **CloudFront**: Global content distribution
- **API Gateway**: API response caching
- **MediaConvert**: Video processing workflows
- **Elastic Transcoder**: Media transcoding

## S3 Event Notifications

### Event Types
- **Object Created**: Put, Post, Copy, CompleteMultipartUpload
- **Object Removed**: Delete, DeleteMarkerCreated
- **Object Restore**: RestoreInitiated, RestoreCompleted
- **Replication**: OperationFailedReplication, OperationMissedThreshold

### Notification Destinations
- **Lambda Functions**: Trigger serverless processing
- **SQS Queues**: Queue-based processing
- **SNS Topics**: Fan-out notifications
- **EventBridge**: Event-driven architectures

### Event Configuration Example
```json
{
  "Rules": [
    {
      "Id": "ProcessNewImages",
      "Filter": {
        "Key": {
          "FilterRules": [
            {
              "Name": "prefix",
              "Value": "images/"
            },
            {
              "Name": "suffix",
              "Value": ".jpg"
            }
          ]
        }
      },
      "Status": "Enabled",
      "Targets": [
        {
          "Id": "1",
          "Arn": "arn:aws:lambda:us-east-1:123456789012:function:ProcessImage"
        }
      ]
    }
  ]
}
```

## S3 Advanced Features

### S3 Select
- **SQL Queries**: Query objects using SQL expressions
- **Supported Formats**: CSV, JSON, Parquet
- **Cost Efficiency**: Retrieve only needed data
- **Performance**: Faster than downloading entire objects

#### S3 Select Example
```sql
SELECT s.name, s.age FROM S3Object[*] s WHERE s.age > 25
```

### S3 Inventory
- **Metadata Reports**: Generate inventory of objects and metadata
- **Scheduling**: Daily or weekly reports
- **Output Formats**: CSV, ORC, Parquet
- **Use Cases**: Audit, compliance, analytics

### S3 Analytics
- **Storage Class Analysis**: Analyze access patterns
- **Optimization Recommendations**: Suggest optimal storage classes
- **Lifecycle Insights**: Data for lifecycle policy creation
- **Export Options**: Daily reports to S3 bucket

### S3 Metrics and Monitoring
- **CloudWatch Metrics**: Standard and detailed metrics
- **Request Metrics**: Monitor specific prefixes or tags
- **Data Events**: Track object-level operations
- **Replication Metrics**: Monitor replication status

## S3 Troubleshooting

### Common Issues and Solutions

#### Access Denied Errors
```bash
# Check bucket policy
aws s3api get-bucket-policy --bucket bucket-name

# Check IAM permissions
aws iam get-user-policy --user-name username --policy-name policy-name

# Check public access block
aws s3api get-public-access-block --bucket bucket-name
```

#### Slow Upload/Download Performance
- **Check Network**: Verify bandwidth and latency
- **Use Multipart**: For objects > 100 MB
- **Transfer Acceleration**: Enable for global transfers
- **Request Distribution**: Avoid sequential prefixes

#### Replication Issues
```bash
# Check replication status
aws s3api get-bucket-replication --bucket source-bucket

# Monitor replication metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/S3 \
  --metric-name ReplicationLatency \
  --dimensions Name=SourceBucket,Value=source-bucket \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-01-02T00:00:00Z \
  --period 3600 \
  --statistics Average
```

#### Lifecycle Policy Troubleshooting
- **Check Transition Rules**: Verify minimum storage duration requirements
- **Monitor Transitions**: Use CloudWatch events for transitions
- **Test Policies**: Start with test objects before full deployment
- **Cost Analysis**: Monitor unexpected costs from transitions

### Performance Troubleshooting

#### Request Rate Limits
```bash
# Monitor request patterns
AWS CloudWatch Metrics:
- 4xxErrors: Client-side errors
- 5xxErrors: Server-side errors
- AllRequests: Total request count

# Optimize request patterns
- Distribute across prefixes
- Implement exponential backoff
- Use connection pooling
```

#### Large Object Handling
```python
# Python multipart upload example
import boto3
from boto3.s3.transfer import TransferConfig

s3 = boto3.client('s3')

# Configure multipart thresholds
config = TransferConfig(
    multipart_threshold=1024 * 25,  # 25MB
    max_concurrency=10,
    multipart_chunksize=1024 * 25,
    use_threads=True
)

# Upload large file
s3.upload_file(
    'large-file.zip',
    'my-bucket',
    'uploads/large-file.zip',
    Config=config
)
```

## S3 Best Practices

### Security Best Practices
- **Principle of Least Privilege**: Grant minimum required permissions
- **Enable Versioning**: Protect against accidental deletion
- **Use Encryption**: Encrypt data at rest and in transit
- **Monitor Access**: Enable logging and monitoring
- **Regular Audits**: Review permissions and access patterns

### Performance Best Practices
- **Optimize Request Patterns**: Distribute load across prefixes
- **Use Appropriate Storage Classes**: Match access patterns to storage class
- **Implement Caching**: Use CloudFront for frequently accessed content
- **Monitor Metrics**: Track performance and optimize accordingly

### Cost Optimization Best Practices
- **Use Lifecycle Policies**: Automatically transition to cheaper storage
- **Delete Unused Data**: Regular cleanup of unnecessary objects
- **Monitor Usage**: Track costs and usage patterns
- **Use Intelligent Tiering**: For unpredictable access patterns

### Operational Best Practices
- **Naming Conventions**: Use consistent, descriptive naming
- **Tag Resources**: Implement comprehensive tagging strategy
- **Backup Strategy**: Plan for disaster recovery
- **Documentation**: Maintain documentation for configurations

## S3 Use Case Examples

### Data Lake Architecture
```
Raw Data Layer (S3 Standard)
├── logs/
├── events/
└── transactions/

Processed Data Layer (S3 Standard-IA)
├── daily-aggregates/
├── monthly-reports/
└── cleaned-data/

Archive Layer (S3 Glacier)
├── historical-data/
└── compliance-archives/
```

### Content Distribution
```
Website Assets (S3 + CloudFront)
├── images/
│   ├── thumbnails/ (S3 Standard)
│   └── full-size/ (S3 Standard-IA)
├── css/ (S3 Standard)
├── js/ (S3 Standard)
└── videos/ (S3 Intelligent-Tiering)
```

### Backup and Disaster Recovery
```
Production Backups
├── daily-backups/ (S3 Standard-IA)
├── weekly-backups/ (S3 Glacier IR)
├── monthly-backups/ (S3 Glacier FR)
└── yearly-archives/ (S3 Glacier Deep Archive)
```

### Big Data Analytics
```
Data Pipeline
Raw Data (S3) → ETL (Glue) → Analytics (Athena/Redshift)
                ↓
           Processed Data (S3 Standard-IA)
                ↓
           Archive (S3 Glacier)
```

## S3 CLI and SDK Examples

### AWS CLI Common Commands
```bash
# Create bucket
aws s3 mb s3://my-bucket --region us-east-1

# Upload file
aws s3 cp file.txt s3://my-bucket/

# Upload directory
aws s3 sync ./local-folder s3://my-bucket/remote-folder/

# Download file
aws s3 cp s3://my-bucket/file.txt ./

# List objects
aws s3 ls s3://my-bucket/ --recursive

# Delete object
aws s3 rm s3://my-bucket/file.txt

# Set storage class
aws s3 cp file.txt s3://my-bucket/ --storage-class GLACIER

# Enable versioning
aws s3api put-bucket-versioning \
  --bucket my-bucket \
  --versioning-configuration Status=Enabled

# Create lifecycle configuration
aws s3api put-bucket-lifecycle-configuration \
  --bucket my-bucket \
  --lifecycle-configuration file://lifecycle.json
```

### Python SDK Examples
```python
import boto3

# Create S3 client
s3 = boto3.client('s3')

# Create bucket
s3.create_bucket(
    Bucket='my-bucket',
    CreateBucketConfiguration={'LocationConstraint': 'us-west-2'}
)

# Upload file with metadata
s3.upload_file(
    'local-file.txt',
    'my-bucket',
    'remote-file.txt',
    ExtraArgs={
        'Metadata': {'author': 'john', 'version': '1.0'},
        'StorageClass': 'STANDARD_IA'
    }
)

# Generate presigned URL
url = s3.generate_presigned_url(
    'get_object',
    Params={'Bucket': 'my-bucket', 'Key': 'file.txt'},
    ExpiresIn=3600  # 1 hour
)

# List objects with pagination
paginator = s3.get_paginator('list_objects_v2')
for page in paginator.paginate(Bucket='my-bucket'):
    for obj in page.get('Contents', []):
        print(f"{obj['Key']} - {obj['Size']} bytes")
```

## S3 Monitoring and Alerting

### CloudWatch Metrics
```bash
# Key S3 CloudWatch Metrics
- NumberOfObjects: Total number of objects
- BucketSizeBytes: Total size of bucket
- AllRequests: Total requests to bucket
- GetRequests: Number of GET requests
- PutRequests: Number of PUT requests
- DeleteRequests: Number of DELETE requests
- 4xxErrors: Client errors
- 5xxErrors: Server errors
- FirstByteLatency: Time to first byte
- TotalRequestLatency: Total request time
```

### Custom Monitoring Setup
```python
import boto3

cloudwatch = boto3.client('cloudwatch')

# Create custom metric for application
cloudwatch.put_metric_data(
    Namespace='MyApp/S3',
    MetricData=[
        {
            'MetricName': 'ProcessedFiles',
            'Value': 123,
            'Unit': 'Count',
            'Dimensions': [
                {
                    'Name': 'BucketName',
                    'Value': 'my-data-bucket'
                }
            ]
        }
    ]
)

# Create alarm
cloudwatch.put_metric_alarm(
    AlarmName='S3-High-Error-Rate',
    ComparisonOperator='GreaterThanThreshold',
    EvaluationPeriods=2,
    MetricName='4xxErrors',
    Namespace='AWS/S3',
    Period=300,
    Statistic='Sum',
    Threshold=10.0,
    ActionsEnabled=True,
    AlarmActions=['arn:aws:sns:us-east-1:123456789012:s3-alerts'],
    AlarmDescription='Alert when S3 4xx errors exceed threshold'
)
```

## S3 Automation and Infrastructure as Code

### CloudFormation Template Example
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'S3 Bucket with lifecycle and replication'

Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub '${AWS::StackName}-data-bucket'
      VersioningConfiguration:
        Status: Enabled
      LifecycleConfiguration:
        Rules:
          - Id: TransitionToIA
            Status: Enabled
            Transitions:
              - StorageClass: STANDARD_IA
                TransitionInDays: 30
              - StorageClass: GLACIER
                TransitionInDays: 90
      ReplicationConfiguration:
        Role: !GetAtt ReplicationRole.Arn
        Rules:
          - Id: ReplicateToSecondaryRegion
            Status: Enabled
            Prefix: important/
            Destination:
              Bucket: !Sub 'arn:aws:s3:::${AWS::StackName}-replica-bucket'
              StorageClass: STANDARD_IA

  ReplicationRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Statement:
          - Effect: Allow
            Principal:
              Service: s3.amazonaws.com
            Action: sts:AssumeRole
      Policies:
        - PolicyName: ReplicationPolicy
          PolicyDocument:
            Statement:
              - Effect: Allow
                Action:
                  - s3:GetObjectVersionForReplication
                  - s3:GetObjectVersionAcl
                Resource: !Sub '${S3Bucket}/*'
              - Effect: Allow
                Action:
                  - s3:ReplicateObject
                  - s3:ReplicateDelete
                Resource: !Sub 'arn:aws:s3:::${AWS::StackName}-replica-bucket/*'
```

### Terraform Configuration Example
```hcl
resource "aws_s3_bucket" "data_bucket" {
  bucket = "my-company-data-bucket"
}

resource "aws_s3_bucket_versioning" "data_bucket_versioning" {
  bucket = aws_s3_bucket.data_bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_lifecycle_configuration" "data_bucket_lifecycle" {
  bucket = aws_s3_bucket.data_bucket.id

  rule {
    id     = "transition_to_ia"
    status = "Enabled"

    transition {
      days          = 30
      storage_class = "STANDARD_IA"
    }

    transition {
      days          = 90
      storage_class = "GLACIER"
    }

    transition {
      days          = 365
      storage_class = "DEEP_ARCHIVE"
    }
  }
}

resource "aws_s3_bucket_public_access_block" "data_bucket_pab" {
  bucket = aws_s3_bucket.data_bucket.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

## S3 Migration Strategies

### Migration Planning
- **Assessment**: Inventory existing data and access patterns
- **Cost Analysis**: Compare storage and transfer costs
- **Timeline**: Plan migration phases and schedules
- **Testing**: Validate migration process with subset of data
- **Rollback Plan**: Prepare for potential issues

### Migration Tools
- **AWS DataSync**: Online data transfer service
- **AWS Storage Gateway**: Hybrid cloud storage
- **AWS Snow Family**: Offline data transfer
- **Third-Party Tools**: Rclone, CloudBerry, etc.

### Migration Best Practices
- **Parallel Transfers**: Use multiple threads/connections
- **Incremental Sync**: Transfer only changed data
- **Verification**: Validate data integrity after transfer
- **Monitoring**: Track progress and performance
- **Documentation**: Record migration process and lessons learned

## Conclusion

Amazon S3 is a foundational service in AWS that provides virtually unlimited, durable, and highly available object storage. Its extensive feature set, including multiple storage classes, lifecycle management, replication, and integration with other AWS services, makes it suitable for a wide range of use cases from simple file storage to complex data lake architectures.

Key takeaways for S3 implementation:
- **Choose the right storage class** based on access patterns and cost requirements
- **Implement lifecycle policies** to automatically optimize costs over time
- **Use versioning and replication** for data protection and compliance
- **Apply security best practices** including encryption, IAM policies, and access monitoring
- **Monitor performance and costs** to optimize usage and spending
- **Plan for scale** by understanding S3 limits and performance characteristics

By following the practices and examples outlined in these notes, you can effectively leverage S3 for your storage needs while maintaining security, performance, and cost efficiency.

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)