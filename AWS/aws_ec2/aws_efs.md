
# Amazon EFS – Elastic File System

- Managed NFS (Network File System) that can be mounted on many EC2 instances.
- EFS works with EC2 instances in a multi-AZ setup.
- Highly available, scalable, and pay-per-use (can be expensive, ~3x gp2).
- **Use cases:** content management, web serving, data sharing, WordPress.
- Uses **NFSv4.1** protocol.
- Uses **security groups** to control access to the EFS file system.
- Compatible with **Linux based AMIs** (not compatible with Windows).
- Supports **Encryption at rest** using AWS KMS.
- It's a **POSIX file system** (~Linux) that has a standard file API.
- The file system **scales automatically**, is pay-per-use, and requires no capacity planning.

### EFS – Performance

#### EFS Scale
- Supports 1000s of concurrent NFS clients and provides 10 GB+/s throughput.
- Can grow to a Petabyte-scale network file system automatically.

#### Performance Mode (set at EFS creation time)
- **General Purpose (default):** Ideal for latency-sensitive use cases like web servers, CMS, etc.
- **Max I/O:** Higher latency and throughput, designed for highly parallel workloads like big data and media processing.

#### Throughput Mode
- **Bursting:** Throughput scales with file system size (e.g., 50MiB/s + burst of up to 100MiB/s for 1 TB).
- **Provisioned:** Set your desired throughput regardless of storage size (e.g., 1 GiB/s for 1 TB of storage).
- **Elastic:** Automatically scales throughput up or down based on your workloads.
    - Up to 3 GiB/s for reads and 1 GiB/s for writes.
    - Used for unpredictable workloads.

### EFS – Storage Classes & Availability

#### Storage Tiers (Lifecycle Management)
- This feature allows you to move files between tiers after N days.
- **Standard:** For frequently accessed files.
- **Infrequent Access (EFS-IA):** Cost-effective for files not accessed every day. There is a cost to retrieve files, 
  but a lower price to store.
- **Archive:** For rarely accessed data (a few times each year), offering significant cost savings (e.g., 50% cheaper).
- Implement **lifecycle policies** to automatically move files between these storage tiers.
  - Like if a file is not accessed for 30 days, it can be moved to EFS-IA.

#### Availability and Durability
- **Standard:** Multi-AZ, providing high availability and durability, great for production workloads.
- **One Zone:** Stores data in a single AZ. It's great for development/testing and enables backups by default. It's 
  compatible with IA (EFS One Zone-IA).

- Can lead to over **90% in cost savings** by using appropriate storage tiers and lifecycle policies.



# Practical
## Creating EFS and attach into two EC2 instances
### Create a EFS 
From EFS > click on "Create file system" button, give name "demo-efs" and keep auto selected VPS and click on 
"Customize" button.


Now a page will open with three steps 

#### File system settings -> First step

**File System type** > selected regional for this option
* **Regional** will be available in all az in the region and can be mounted on multiple EC2 instances in different az.
  * Give the highest level of availability and durability
* **One Zone** will be available in a single az and can be mounted on multiple EC2 instances in that az.
  * Cost-effective for development and testing workloads
  * Not recommended for production workloads

**Automatic backups** > keep it enabled, this will create a backup of the EFS file system every 12 hours and keep it for
35 days.

**Lifecycle management** automatically moves files to different storage classes based on access patterns.
* Keep default 30 days for EFS-IA, this means files that are not accessed for 30 days will be moved to EFS-IA storage 
  class.
* Keep default 90 days for EFS-Archive, this means files that are not accessed for 90 days will be moved to EFS-Archive 
  storage class.
* Transition into standard select "On first access" to move files back to standard storage class when they are accessed.

**Encryption** > keep it enabled, this will encrypt the data at rest using AWS KMS.

#### Performance settings
**Throughput mode** 
* **Enhanced**: Provides more flexibility and higher throughput levels for workloads with a range of performance 
  requirements.
  * **Elastic:** Automatically scales throughput up or down based on workloads. Use this mode for workloads with 
    unpredictable I/O. With Elastic Throughput, performance automatically scales with your workload activity, and you 
    only pay for the throughput you use (data transferred for your file systems per month). 
  * **Provisioned:** Allows you to set a specific throughput level for your file system, regardless of the amount of 
    data stored. Use this mode for workloads with predictable I/O patterns that require consistent throughput. If we
    use about our throughput MiB/s need then we can use this mode.
* **Bursting**: Scales throughput with file system size, ideal for workloads with variable I/O patterns. This is the 
  default mode and is suitable for most workloads.

Now click on "Next" button.

#### Network access -> Second Step
**VPC** > select default one or can create a new one.
**Mount targets** > There will be options for all az by default with some options
  * Availability Zone > select all the az in the region, this will create mount targets in all selected az.
  * Subnet Id > select the subnet in which you want to create the mount target. This subnet should be in the same az as 
    selected above.
  * IP address Type > keep it as default "IPv4 only" for this option.
  * IP Address > keep it empty as default.
  * IPv4 address > keep it empty as default.
  * Security groups > can create new one for this EFS or can select existing one. This security group will be used to 
    control access to the EFS file system.
    * Make sure to allow inbound traffic on port 2049 (NFS) from the security group of the EC2 instances that will mount 
      this EFS.

Now click on "Next" button.

#### File system policy - optional 
For now keeping this as default and click on "Next" button.

#### Review and create
Now review the settings and click on "Create file system" button. This will create the EFS file system with the
specified settings. It may take a few minutes to create the file system and mount targets.

### Create two EC2 and add the newly created EFS
From EC2 > click on "Launch instance" button, select the AMI as "Amazon Linux 2" and instance type as "t2.micro" and
at first one select a subnet from 1a and at second one select a subnet from 1b from network settings. From "Configure 
storage", click on "Edit" text on the right of "0 x File systems". From there keep select "EFS" from file system type
and select the EFS file system we create above. And keep checked as default those checkboxes and keep mount point name
as it is.

* Automatically create and attach security groups
  * To enable access to the file system, the required security groups will be automatically created and attached to this
    instance and the selected file system. To manually manage the security groups, clear the checkbox. Learn more.
* Automatically mount shared file system by attaching required user data script
  * Automatically mount your file system by updating your user data to install efs-utils. If you would like to manually 
    mount your file system, clear the checkbox.

and click on "Lunch instance" for both of the instances.

Now we will see two security group is get created by AWS for allowing access to the EFS file system. One for each's 
type is NFS and protocol is TCP, port range 2049, source custom and with security group of the EC2 instance that
will mount this EFS file system. Now we can see the EFS file system is mounted on both of the EC2 instances.

Now connect with those two EC2 instance using "EC2 Instance Connect"

Add a new file `hello.txt` with text "Hello world" it first EC2 instance
```shell
[ec2-user@ip-172-31-40-60 ~]$ sudo su
[root@ip-172-31-40-60 ec2-user]# echo "hello world" > /mnt/efs/fs1/hello.txt
[root@ip-172-31-40-60 ec2-user]# cat /mnt/efs/fs1/hello.txt
hello world
```
Now from second EC2 instance check the file `hello.txt` created in first EC2 instance
```shell
[ec2-user@ip-172-31-20-82 ~]$ cat /mnt/efs/fs1/hello.txt
hello world
```

So we can say `efs/fs1` is a shared file system that can be mounted on multiple EC2 instances and can be accessed by all 
of them.

# Resources
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)