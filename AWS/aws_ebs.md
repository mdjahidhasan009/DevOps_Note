# AWS EBS

AWS EBS (Elastic Block Store) is a block storage service designed for use with Amazon EC2 instances. It provides
persistent storage that can be attached to EC2 instances, allowing users to store data that needs to persist beyond the
lifecycle of the instance.

For example, if you're hosting a MySQL or PostgreSQL database, you need reliable, high-performance storage to handle
frequent read/write operations.

EBS provides persistent, fast storage that ensures your data is saved even if the EC2 instance is stopped or restarted,
making it ideal for database workloads.

## What is AWS EBS?
Amazon Elastic Block Store (EBS) is a high-performance block storage service designed for Amazon EC2. Think of EBS as a
virtual hard drive that you can attach to your EC2 instances. Unlike the temporary storage that comes with some EC2
instances, EBS volumes persist independently of the EC2 instance lifecycle, making them perfect for storing important
data, operating systems, and applications.

## Key Features of EBS
- **Persistent Storage**: Data persists independently of EC2 instance lifecycle
- **High Performance**: Provides consistent, low-latency performance
- **Highly Available**: Built-in redundancy within Availability Zones
- **Scalable**: Can be resized without downtime
- **Secure**: Supports encryption at rest and in transit
- **Backup and Recovery**: Point-in-time snapshots stored in S3

## Important points about EBS
* **Region & AZ specific**
* **Build-in Redundancy**
  * EBS volumes are automatically replicated within the same Availability Zone to prevent data loss due to hardware
    failures.
* **Different Volume Types**
  * gp2/3, io1/2, st1, sc1
* **Allow Encryption & Snapshot for backup**
* **Scalable (Volume can be resizeable)**
  * No data loss will occur during resizing.
  * No need to restart the EC2 instance during the process.

## EBS Volume Types

### General Purpose SSD (gp3/gp2)
- **gp3 (Latest Generation)**:
  - Base performance: 3,000 IOPS and 125 MiB/s throughput
  - Can provision up to 16,000 IOPS and 1,000 MiB/s throughput independently
  - More cost-effective than gp2
  - Size range: 1 GiB to 16 TiB
- **gp2 (Previous Generation)**:
  - Performance scales with volume size (3 IOPS per GiB)
  - Baseline performance: 100-16,000 IOPS
  - Burst performance up to 3,000 IOPS for smaller volumes
  - Size range: 1 GiB to 16 TiB

### Provisioned IOPS SSD (io2/io1)
- **io2 (Latest Generation)**:
  - Up to 64,000 IOPS and 1,000 MiB/s throughput
  - 99.999% durability (higher than io1)
  - Sub-millisecond latency
  - Size range: 4 GiB to 16 TiB
- **io1 (Previous Generation)**:
  - Up to 64,000 IOPS and 1,000 MiB/s throughput
  - 99.999% durability
  - Size range: 4 GiB to 16 TiB

### Throughput Optimized HDD (st1)
- **Use Cases**: Big data, data warehouses, log processing
- **Performance**: Up to 500 MiB/s throughput
- **Size Range**: 125 GiB to 16 TiB
- **Cost**: Lower cost per GB than SSD
- **Cannot be used as boot volume**

### Cold HDD (sc1)
- **Use Cases**: Less frequently accessed workloads
- **Performance**: Up to 250 MiB/s throughput
- **Size Range**: 125 GiB to 16 TiB
- **Cost**: Lowest cost per GB
- **Cannot be used as boot volume**

### Volume Type Comparison

| Volume Type  | Use Case             | IOPS         | Throughput      | Size Range     | Boot Volume  |
|--------------|----------------------|--------------|-----------------|----------------|--------------|
| gp3          | General purpose      | 3,000-16,000 | 125-1,000 MiB/s | 1 GiB-16 TiB   | Yes          |
| gp2          | General purpose      | 100-16,000   | 128-250 MiB/s   | 1 GiB-16 TiB   | Yes          |
| io2          | High IOPS            | 100-64,000   | 125-1,000 MiB/s | 4 GiB-16 TiB   | Yes          |
| io1          | High IOPS            | 100-64,000   | 125-1,000 MiB/s | 4 GiB-16 TiB   | Yes          |
| st1          | Throughput optimized | 500          | 40-500 MiB/s    | 125 GiB-16 TiB | No           |
| sc1          | Cold storage         | 250          | 12-250 MiB/s    | 125 GiB-16 TiB | No           |

## EBS Volume Management

### Delete on Termination
By default, EBS volumes are set to delete on termination of the EC2 instance. You can change this setting to retain the
volume after the instance is terminated, allowing you to keep the data for future use while creating a ec2 instance.

### Volume States
- **Creating**: Volume is being created
- **Available**: Volume is ready to be attached
- **In-use**: Volume is attached to an instance
- **Deleting**: Volume is being deleted
- **Deleted**: Volume has been deleted
- **Error**: Volume creation failed

### Volume Operations
- **Attach**: Connect volume to an EC2 instance
- **Detach**: Disconnect volume from an EC2 instance
- **Modify**: Change volume size, type, or IOPS
- **Create Snapshot**: Create point-in-time backup
- **Delete**: Permanently remove volume

## Hands-on: EBS Volume Management

___________________________________________
Now create a new EC2 instance with volume delete on termination set to false.

We can verify this from EC2 > Instances > Click on Instance Id > Click on Storage tab from bottom, now at Block Devices
we can see No at Delete on Termination column and for my case Device name is `/dev/sda1`.

Now connect with EC2 instance using EC2 Instance Connect and run the following command to check the EBS volume
```bash
ubuntu@ip-172-31-22-136:~$ lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 27.2M  1 loop /snap/amazon-ssm-agent/11320
loop1      7:1    0 73.9M  1 loop /snap/core22/1963
loop2      7:2    0 50.9M  1 loop /snap/snapd/24505
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot
ubuntu@ip-172-31-22-136:~$ 
```
Now goes to EC2 > Elastic Block Store > Volumes, here we can see the EBS volume created with the instance.

Now create an EBS volume at same availability zone as the EC2 instance(ap-south-1b) with 5GB size and gp3 type.
Select this new EBS volume and click on Actions > Attach Volume, now select the instance id of our previously created
EC2 instance, give device name as `/dev/sdd` and click on Attach Volume.

Now if we go to EC2 > Elastic Block Store > Volumes, here will be new attached `/dev/sdd` 5GB EBS volume along with our
`/dev/sda1` 8GB EBS volume which is created by default with the EC2 instance.

```shell
ubuntu@ip-172-31-22-136:~$ lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 27.2M  1 loop /snap/amazon-ssm-agent/11320
loop1      7:1    0 73.9M  1 loop /snap/core22/1963
loop2      7:2    0 50.9M  1 loop /snap/snapd/24505
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot
xvdd     202:48   0    5G  0 disk # This is the new EBS volume we created
```

Now goes to EC2 > Elastic Block Store > Volumes, select the newly created EBS volume `/dev/sdd` and click on Actions >
Modify Volume, here we can change the size of the EBS volume to 6GB and click on Modify button. So now from the EC2>
Elastic Block Store > Volumes, we can see the EBS volume size is changed to 6GB, also from the EC2 instances dashboard
we can see the EBS volume size is changed to 6GB.

```shell
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 27.2M  1 loop /snap/amazon-ssm-agent/11320
loop1      7:1    0 73.9M  1 loop /snap/core22/1963
loop2      7:2    0 50.9M  1 loop /snap/snapd/24505
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot
xvdd     202:48   0    6G  0 disk # Changed size to 6GB 
```

We're done this without stoping or restarting the EC2 instance.

**NOTE**: We only can increase the size of the EBS volume, we cannot decrease it. If you want to decrease the size of the
EBS volume, you need to create a new EBS volume with the desired size, copy the data from the old volume to the new one,
and then delete the old volume.

Now, goes to EC2 > Elastic Block Store > Volumes, select the EBS volume `/dev/sdd` and click on Actions > Detach Volume.
This will detach the EBS volume from the EC2 instance. After detaching the EBS volume is available to attach to any
other EC2 instance in the same availability zone but it will not be available to attach to any other EC2 instance in a
different availability zone.

```shell
ubuntu@ip-172-31-22-136:~$ lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 27.2M  1 loop /snap/amazon-ssm-agent/11320
loop1      7:1    0 73.9M  1 loop /snap/core22/1963
loop2      7:2    0 50.9M  1 loop /snap/snapd/24505
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot
```
After that we again select the EBS volume `/dev/sdd` and click on Actions > Delete Volume, this will delete the EBS
volume and all the data stored in it will be lost. So, make sure to take a snapshot of the EBS volume before deleting it
if you want to keep the data for future use.

### Additional EBS Operations Commands

```bash
# Check file system type
sudo file -s /dev/xvdf

# Format new volume (WARNING: This will erase all data)
sudo mkfs -t ext4 /dev/xvdf

# Create mount point
sudo mkdir /mnt/my-volume

# Mount volume
sudo mount /dev/xvdf /mnt/my-volume

# Check mounted volumes
df -h

# Unmount volume
sudo umount /mnt/my-volume

# Make mount persistent (add to /etc/fstab)
echo '/dev/xvdf /mnt/my-volume ext4 defaults,nofail 0 2' | sudo tee -a /etc/fstab

# Extend file system after volume resize
sudo resize2fs /dev/xvdf  # For ext4
sudo xfs_growfs /mnt/my-volume  # For XFS
```

## EBS Snapshot

* What if we want to copy our data to
  * New Availability Zone
  * New Region
* For future use
  * Backup
  * Disaster Recovery
  * Migration
* EBS Snapshots are incremental backups of EBS volumes that are stored in Amazon S3.
* Snapshots are region-specific and can be used to create new EBS volumes in the same or different Availability Zones.
* Snapshots can be created manually or automatically using Amazon Data Lifecycle Manager (DLM).

### EBS Snapshot Features
- **Incremental Backups**: Only changed blocks are stored after the first snapshot
- **Point-in-Time Recovery**: Restore to exact state when snapshot was taken
- **Cross-Region Copy**: Copy snapshots to different AWS regions
- **Cross-Account Sharing**: Share snapshots with other AWS accounts
- **Encryption**: Snapshots of encrypted volumes are automatically encrypted
- **Cost-Effective**: Pay only for changed data stored

### Snapshot States
- **Pending**: Snapshot is being created
- **Completed**: Snapshot is ready for use
- **Error**: Snapshot creation failed

### Take a snapshot of EBS volume and copy it to another availability zone and attach it to another EC2 instance
In our current EC2 create a folder `myfolder` and create a file `test.txt` add `hello` in it.

```shell
ubuntu@ip-172-31-22-136:~$ ls
ubuntu@ip-172-31-22-136:~$ pwd
/home/ubuntu
ubuntu@ip-172-31-22-136:~$ mkdir myfolder
ubuntu@ip-172-31-22-136:~$ cd ./myfolder/
ubuntu@ip-172-31-22-136:~/myfolder$ echo "hello">test.txt
ubuntu@ip-172-31-22-136:~/myfolder$ cat test.txt
hello
ubuntu@ip-172-31-22-136:~/myfolder$ 
```

As while creating this we select `Delete on Termination` as `false`, so the EBS volume will not be deleted when the
EC2 instance is terminated.

EC2 > Elastic Block Store > Volumes, select the EBS volume `/dev/sda1` and click on Actions > Create Snapshot. It will
be available at EC2 > Elastic Block Store > Snapshots, here we can see the snapshot created with the EBS volume id. Wait
for the snapshot to be in `completed` state, it will take some time depending on the size of the EBS volume. Select the
snapshot click on Action > Create volume from snapshot, here we can select the availability zone as `ap-south-1c` and
volume type as `gp3`, then click on Create Volume button. Now at EC2 > Elastic Block Store > Volumes, we can see the
newly created EBS volume with the same size as the original EBS volume at `ap-south-1c` availability zone. Goes to EC2
instance dashboard, click on Launch Instance button, give name `new-test-ec2-at-1c` select ubuntu as before, select t2.
micro as instance type, select the same key pair as before, at Network settings select the same VPC, same security group
as before but subnet from `ap-south-1c` availability zone. And keep the storage as default, which is 8GB EBS volume
created by default. Now click on Launch Instance button.

Now `new-test-ec2-at-1c` will be available at EC2 > Instances. Now connect with it using EC2 Instance Connect.

```shell
ubuntu@ip-172-31-0-122:~$ lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 27.2M  1 loop /snap/amazon-ssm-agent/11320
loop1      7:1    0 73.9M  1 loop /snap/core22/1963
loop2      7:2    0 50.9M  1 loop /snap/snapd/24505
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot
ubuntu@ip-172-31-0-122:~$ 
```

Now if we go to EC2 > Elastic Block Store > Volumes, we can see the newly created EBS volume at `ap-south-1c` AZ is in
`available` state. Now select the EBS volume and click on Actions > Attach Volume, select the instance
`new-test-ec2-at-1c` and give device name as `/dev/sdd` and click on Attach Volume button.

```shell
ubuntu@ip-172-31-0-122:/$ lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 27.2M  1 loop /snap/amazon-ssm-agent/11320
loop1      7:1    0 73.9M  1 loop /snap/core22/1963
loop2      7:2    0 50.9M  1 loop /snap/snapd/24505
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot
xvdd     202:48   0    8G  0 disk  # This is the new EBS volume we created from snapshot
├─xvdd1  202:49   0    7G  0 part  # This is the partition of the new EBS volume
├─xvdd14 202:62   0    4M  0 part  # This is the partition of the new EBS volume
├─xvdd15 202:63   0  106M  0 part  # This is the partition of the new EBS volume
└─xvdd16 259:1    0  913M  0 part  # This is the partition of the new EBS volume
ubuntu@ip-172-31-0-122:/$ 

```

Also, the volume at EC2 > Elastic Block Store > Volumes is in `in-use` state. Now we can see the new EBS volume is
attached to the EC2 instance `new-test-ec2-at-1c`. Now we can mount the new EBS volume to the EC2 instance and
access the data stored in it.

To mount the new EBS volume, we need to create a directory to mount it to, then format the volume and mount it.

```shell
ubuntu@ip-172-31-0-122:~$ lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 27.2M  1 loop /snap/amazon-ssm-agent/11320
loop1      7:1    0 73.9M  1 loop /snap/core22/1963
loop2      7:2    0 50.9M  1 loop /snap/snapd/24505
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot
xvdd     202:48   0    8G  0 disk 
├─xvdd1  202:49   0    7G  0 part 
├─xvdd14 202:62   0    4M  0 part 
├─xvdd15 202:63   0  106M  0 part 
└─xvdd16 259:1    0  913M  0 part 
ubuntu@ip-172-31-0-122:~$ sudo file -s /dev/xvdd1
/dev/xvdd1: Linux rev 1.0 ext4 filesystem data, UUID=7d3f70ca-c681-4e70-bd46-03a369436317, volume name "cloudimg-rootfs" (needs journal recovery) (extents) (64bit) (large files) (huge files)
ubuntu@ip-172-31-0-122:~$ sudo mount -o nouuid /dev/xvdd1 /mnt/myfoldertomountnewvolumeatnewec2 # But in tutorial it was working
mount: /mnt/myfoldertomountnewvolumeatnewec2: wrong fs type, bad option, bad superblock on /dev/xvdd1, missing codepage or helper program, or other error.
       dmesg(1) may have more information after failed mount system call.
ubuntu@ip-172-31-0-122:~$ sudo mkdir /mnt/myfoldertomountnewvolumeatnewec2       
ubuntu@ip-172-31-0-122:~$ sudo mount /dev/xvdd1 /mnt/myfoldertomountnewvolumeatnewec2 
ubuntu@ip-172-31-0-122:~$ cd ./mnt/myfoldertomountnewvolumeatnewec2
mkdir: cannot create directory '/mnt/myfoldertomountnewvolumeatnewec2': File exists
ubuntu@ip-172-31-0-122:~$ cd /mnt/myfoldertomountnewvolumeatnewec2
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2$ ls
bin                boot  etc   lib                lib64       media  opt   root  sbin                snap  sys  usr
bin.usr-is-merged  dev   home  lib.usr-is-merged  lost+found  mnt    proc  run   sbin.usr-is-merged  srv   tmp  var
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       6.8G  1.8G  5.0G  26% /
tmpfs           479M     0  479M   0% /dev/shm
tmpfs           192M  896K  191M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/xvda16     881M   86M  734M  11% /boot
/dev/xvda15     105M  6.2M   99M   6% /boot/efi
tmpfs            96M   12K   96M   1% /run/user/1000
/dev/xvdd1      6.8G  1.8G  5.0G  26% /mnt/myfoldertomountnewvolumeatnewec2
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2$ cd ./home
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home$ ls
ubuntu
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home$ cd ./ubuntu/
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu$ ls
myfolder
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu$ cd ./myfolder/
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu/myfolder$ ls
test.txt
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu/myfolder$ cat test.txt
hello
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu/myfolder$
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu/myfolder$ sudo umount /mnt/myfoldertomountnewvolumeatnewec2
umount: /mnt/myfoldertomountnewvolumeatnewec2: target is busy.
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu/myfolder$ sudo -l umount /mnt/myfoldertomountnewvolumeatnewec2
/usr/bin/umount /mnt/myfoldertomountnewvolumeatnewec2
ubuntu@ip-172-31-0-122:/mnt/myfoldertomountnewvolumeatnewec2/home/ubuntu/myfolder$ 
```

This `test.txt` file was created in the previous EC2 instance and we can see it in the new EC2 instance after mounting
the EBS volume created from the snapshot of the original EBS volume. This shows that we can copy our data to a new
Availability Zone or Region by taking a snapshot of the EBS volume and creating a new EBS volume from the snapshot in
the desired Availability Zone or Region.

### Additional Snapshot Management Commands

```bash
# Create snapshot using AWS CLI
aws ec2 create-snapshot --volume-id vol-12345678 --description "My backup snapshot"

# List snapshots
aws ec2 describe-snapshots --owner-ids self

# Copy snapshot to another region
aws ec2 copy-snapshot --source-region us-east-1 --source-snapshot-id snap-12345678 --destination-region us-west-2

# Create volume from snapshot
aws ec2 create-volume --snapshot-id snap-12345678 --availability-zone us-east-1a

# Delete snapshot
aws ec2 delete-snapshot --snapshot-id snap-12345678

# Share snapshot with another account
aws ec2 modify-snapshot-attribute --snapshot-id snap-12345678 --attribute createVolumePermission --operation-type add --user-ids 123456789012
```

### Copy snapshot to another region
Select the snapshot Action > Copy Snapshot and select the required region.
**NOTE**: It will cost money to copy the snapshot to another region, so make sure to check the pricing before copying
the snapshot. IT IS NOT FREE UNDER FREE TIER.

### Cross-Region Snapshot Copy Considerations
- **Data Transfer Costs**: Charges apply for data transfer between regions
- **Time**: Copy time depends on snapshot size and network conditions
- **Encryption**: Can change encryption settings during copy
- **Permissions**: Copied snapshots don't inherit source permissions

## EBS Encryption

### Encryption Features
- **At Rest**: Data stored on volumes is encrypted
- **In Transit**: Data moving between instance and volume is encrypted
- **Snapshots**: Encrypted volumes create encrypted snapshots
- **Key Management**: Uses AWS KMS (Key Management Service)
- **Performance**: Minimal impact on performance
- **Transparent**: No changes needed to applications

### Encryption Process
1. **Create Encrypted Volume**: Enable encryption when creating volume
2. **Choose KMS Key**: Use AWS managed key or customer managed key
3. **Attach to Instance**: Mount and use like any other volume
4. **Snapshots**: Automatically encrypted with same key

### Encryption Commands
```bash
# Create encrypted volume
aws ec2 create-volume --size 10 --volume-type gp3 --availability-zone us-east-1a --encrypted

# Create encrypted snapshot
aws ec2 create-snapshot --volume-id vol-12345678 --description "Encrypted snapshot" --encrypted

# Copy snapshot with encryption
aws ec2 copy-snapshot --source-region us-east-1 --source-snapshot-id snap-12345678 --encrypted --kms-key-id alias/my-key
```

## EC2 > Elastic Block Store > Lifecycle Manager
Lifecycle Manager is a service that automates the creation, retention, and deletion of EBS snapshots. It allows you to
create policies to manage your EBS snapshots based on specific criteria, such as age, tags, or volume IDs. This helps
you to automate the backup and retention of your EBS volumes, ensuring that you have the necessary snapshots for
disaster recovery and compliance purposes.

### Data Lifecycle Manager (DLM) Features
- **Automated Snapshots**: Schedule automatic snapshot creation
- **Retention Policies**: Automatically delete old snapshots
- **Tag-Based Targeting**: Target volumes based on tags
- **Cross-Region Copy**: Automatically copy snapshots to other regions
- **Fast Snapshot Restore**: Enable FSR for faster volume restoration
- **Cost Optimization**: Reduce storage costs through automated cleanup

### DLM Policy Components
- **Resource Type**: EBS volumes or instances
- **Target Tags**: Tags that identify resources to backup
- **Schedule**: When to create snapshots (hourly, daily, weekly, monthly)
- **Retention**: How many snapshots to keep
- **Copy Tags**: Copy tags from volume to snapshot
- **Advanced Settings**: Cross-region copy, FSR, etc.

### Creating a DLM Policy
1. **Go to**: EC2 > Elastic Block Store > Lifecycle Manager
2. **Create Policy**: Choose resource type and target tags
3. **Configure Schedule**: Set frequency and retention
4. **Set Permissions**: Choose or create IAM role
5. **Review and Create**: Verify settings and create policy

### DLM Policy Example
```json
{
  "ResourceType": "VOLUME",
  "TargetTags": [
    {
      "Key": "Environment",
      "Value": "Production"
    }
  ],
  "Schedules": [
    {
      "Name": "Daily Backup",
      "CreateRule": {
        "Interval": 24,
        "IntervalUnit": "HOURS",
        "Times": ["03:00"]
      },
      "RetainRule": {
        "Count": 7
      },
      "TagsToAdd": [
        {
          "Key": "AutoSnapshot",
          "Value": "true"
        }
      ]
    }
  ]
}
```

## EBS Performance Optimization

### Performance Factors
- **Volume Type**: Choose appropriate type for workload
- **IOPS**: Provision adequate IOPS for your needs
- **Throughput**: Ensure sufficient throughput for data transfer
- **Instance Type**: Use EBS-optimized instances
- **Multi-Attach**: Share volumes between instances (io1/io2 only)

### Best Practices
- **Use gp3**: Generally better price/performance than gp2
- **EBS-Optimized Instances**: Use instances with dedicated EBS bandwidth
- **Pre-warming**: Initialize volumes from snapshots before production use
- **RAID Configuration**: Use RAID 0 for increased performance (with risk)
- **Monitoring**: Use CloudWatch metrics to monitor performance
- **Placement Groups**: Use cluster placement groups for low latency

### Performance Monitoring Metrics
- **VolumeReadOps/VolumeWriteOps**: Number of read/write operations
- **VolumeReadBytes/VolumeWriteBytes**: Amount of data transferred
- **VolumeTotalReadTime/VolumeTotalWriteTime**: Total time for operations
- **VolumeQueueLength**: Number of pending I/O requests
- **BurstBalance**: Available burst credits for gp2 volumes

## EBS Multi-Attach

### Multi-Attach Overview
- **Supported Volume Types**: io1 and io2 only
- **Maximum Instances**: Up to 16 instances per volume
- **Use Cases**: Shared storage for clustered applications
- **Requirements**: Cluster-aware file systems
- **Availability**: Same Availability Zone only

### Multi-Attach Considerations
- **File System**: Must use cluster-aware file system (not ext4/XFS)
- **Application Logic**: Applications must handle concurrent access
- **Data Integrity**: Risk of data corruption without proper coordination
- **Backup Strategy**: More complex backup and recovery procedures

## EBS vs Instance Store

### Comparison Table

| Feature | EBS | Instance Store |
|---------|-----|----------------|
| Persistence | Persistent | Temporary |
| Performance | Consistent | Very High |
| Durability | High (replicated) | Lost on stop/terminate |
| Backup | Snapshots | Manual copy required |
| Cost | Storage + IOPS charges | Included with instance |
| Resize | Yes | No |
| Encryption | Yes | No |
| Multi-Attach | Yes (io1/io2) | No |

### When to Use Instance Store
- **High Performance**: Need maximum I/O performance
- **Temporary Data**: Cache, temporary processing, logs
- **Cost Sensitive**: Want to avoid additional storage costs
- **Distributed Systems**: Applications with built-in replication

### When to Use EBS
- **Persistent Data**: Databases, file systems, boot volumes
- **Backup Requirements**: Need point-in-time recovery
- **Flexibility**: Need to resize or change performance
- **Durability**: Cannot afford data loss

## EBS Cost Optimization

### Cost Components
- **Storage**: Charged per GB-month provisioned
- **IOPS**: Additional charges for provisioned IOPS (io1/io2)
- **Throughput**: Additional charges for provisioned throughput (gp3)
- **Snapshots**: Charged for actual data stored in S3
- **Data Transfer**: Cross-region snapshot copies

### Cost Optimization Strategies
- **Right-sizing**: Monitor usage and adjust volume sizes
- **Volume Type**: Use gp3 instead of gp2 for better price/performance
- **Snapshot Management**: Use DLM to manage snapshot lifecycle
- **Delete Unused Volumes**: Remove unattached, unused volumes
- **Monitor Utilization**: Use CloudWatch to identify underutilized volumes

### Cost Monitoring Tools
- **AWS Cost Explorer**: Analyze EBS spending patterns
- **AWS Budgets**: Set alerts for EBS spending
- **CloudWatch**: Monitor volume utilization metrics
- **Trusted Advisor**: Identify cost optimization opportunities

## Troubleshooting EBS Issues

### Common Issues
- **Volume Not Visible**: Check attachment and device mapping
- **Mount Failures**: Verify file system and mount points
- **Performance Issues**: Check IOPS limits and instance types
- **Snapshot Failures**: Verify permissions and volume state
- **Encryption Errors**: Check KMS key permissions

### Debugging Commands
```bash
# Check volume attachment
lsblk
df -h
mount | grep /dev/xvd

# Check file system
sudo file -s /dev/xvdf
sudo fsck /dev/xvdf

# Check mount options
cat /proc/mounts | grep /dev/xvdf

# Check system logs
dmesg | grep -i error
journalctl -u systemd-logind

# Check CloudWatch logs
aws logs describe-log-groups
aws logs get-log-events --log-group-name /aws/ec2/ebs
```

### Performance Troubleshooting
```bash
# Check I/O statistics
iostat -x 1
iotop

# Check disk usage patterns
sar -d 1

# Monitor real-time performance
sudo iotop -o
sudo nload

# Check for I/O wait
top (look for %wa column)
```

## EBS Security Best Practices

### Access Control
- **IAM Policies**: Restrict EBS actions to authorized users
- **Resource Tags**: Use tags for resource-based access control
- **Cross-Account Sharing**: Carefully control snapshot sharing
- **Encryption Keys**: Manage KMS keys properly

### Data Protection
- **Encryption**: Enable encryption for sensitive data
- **Regular Backups**: Implement automated snapshot policies
- **Access Logging**: Use CloudTrail to log EBS API calls
- **Network Security**: Use VPC security groups appropriately

### Compliance Considerations
- **Data Residency**: Understand data location requirements
- **Encryption Standards**: Meet regulatory encryption requirements
- **Audit Trails**: Maintain logs for compliance reporting
- **Retention Policies**: Implement appropriate data retention

## EBS Integration with Other AWS Services

### EC2 Integration
- **Auto Scaling**: Volumes can be created from snapshots automatically
- **Load Balancers**: EBS provides persistent storage for application data
- **Elastic IPs**: Combined with EBS for persistent, accessible services

### Database Services
- **RDS**: Uses EBS for database storage
- **DocumentDB**: Built on EBS infrastructure
- **ElastiCache**: Can use EBS for backup storage

### Backup and Disaster Recovery
- **AWS Backup**: Centralized backup management for EBS
- **Cross-Region Replication**: Snapshot copying for DR
- **AWS Storage Gateway**: Hybrid cloud backup solutions

### Monitoring and Management
- **CloudWatch**: Performance and utilization monitoring
- **Systems Manager**: Automated patching and management
- **Config**: Track EBS configuration changes

## Advanced EBS Features

### Fast Snapshot Restore (FSR)
- **Purpose**: Eliminate snapshot restoration penalty
- **Cost**: Additional charges apply
- **Performance**: Volumes perform at full capacity immediately
- **Use Cases**: Critical applications requiring fast recovery

### EBS Direct APIs
- **EBS Direct**: Read snapshot data directly without creating volumes
- **Use Cases**: Backup verification, data analysis
- **Performance**: Faster than creating volumes for data access
- **Integration**: Works with third-party backup solutions

### Volume Modifications
- **Dynamic Resizing**: Increase size without downtime
- **Type Changes**: Change volume type (with limitations)
- **IOPS Adjustments**: Modify provisioned IOPS
- **Monitoring**: Track modification progress

## EBS Limits and Quotas

### Service Limits
- **Volumes per Region**: 5,000 (default, can be increased)
- **Snapshots per Region**: 100,000
- **Volume Size**: Up to 64 TiB (depending on type)
- **IOPS per Volume**: Up to 64,000 (io1/io2)
- **Throughput per Volume**: Up to 1,000 MiB/s

### Request Limits
- **API Rate Limits**: Throttling may occur with high API usage
- **Concurrent Snapshots**: Limited concurrent snapshot creation
- **Attachment Limits**: One volume per instance per device

### Increasing Limits
- **Service Quotas Console**: Request limit increases
- **Support Cases**: Contact AWS Support for assistance
- **Business Justification**: Provide use case details

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)