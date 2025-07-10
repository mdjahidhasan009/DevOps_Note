# AWS EBS (Elastic Block Store)
EBS is a block storage service designed for use with Amazon EC2 instances. It provides persistent storage that can be
attached to EC2 instances, allowing you to store data that needs to persist beyond the lifecycle of the instance, also
after the instance is stopped or terminated. EBS volumes can be used for a variety of purposes, such as storing
databases, file systems, or application data. They can be easily scaled, backed up, and encrypted for security. It's
like a hard drive for your EC2 instances.

* EBS can be munted at multiple instances at the same time.
* EBS volumes can be attached to an EC2 instance at launch or after the instance is running.
* EBS volumes can be resized, allowing you to increase or decrease their size as needed.
* EBS volumes can be backed up using snapshots, which are point-in-time copies of the volume.
* EBS supports encryption, allowing you to secure your data at rest and in transit.
* EBS volumes can be detached from one instance and attached to another, allowing for flexibility in resource management.
* EBS volumes can be used as boot volumes for EC2 instances, allowing you to launch instances with pre-configured
  operating systems and applications.
* They are bound to a single Availability Zone, meaning they cannot be shared across different AZs. But making a snapshot
  of an EBS volume allows you to copy it to another AZ or region.
* Free tier give 30 GB of EBS storage, 2 million I/O requests, and 1 GB of snapshot storage per month for the first 12
  months.
* As EBS is a network drive not a physical drive uses network to communicate with the EC2 instance, it has latency and 
 throughput limitations. The performance of EBS volumes can vary based on the type of volume and the instance type they
 are attached to.
* It has provision capacity size GBs, and IOPS and get billed for capacity.


By default, while creating EC2 it checked that on deleting the root EC2 EBS volume will be deleted if we uncheck that
while creating EC2 the root EBS volume will not be deleted while deleting the EC2. By default, other EBS volume which
is not root volume(created with while creating EC2) if attached with a EC2 instance if we delete EC2 the EBS volume(
which is crated by us) will not be deleted.



After creating a EC2 select that instance and goes to storage tab, there will be "Block devices" tab, there we can see
our root volume and at "Delete on termination" column value is "Yes". Now at EC2 > Elastic Block Store > Volumes, will
see that the root volume. Now click on "Create volume" button, there we can select the volume type, size, IOPS, 
availability zone, and other options. We will create 2GB EBS volume with same az as EC2 and keep other options as it is.
Now select newly created EBS volume and click on "Actions" button and select "Attach volume". Now select the instance
to which we want to attach the EBS volume and click on "Attach volume" button. Now go to the EC2 instance select that
instance goes to "Storage" tab and click on "Block devices" tab, there we can see the newly attached EBS volume and at
"Delete on termination" column for volume we created value is "No". After terminate the EC2 instance we can see the root
volume from EC2 > Elastic Block Store > Volumes, is vanished.





# Resources
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)
