# AWS EC2 Instance Store
In AWS EC2, an instance store is a type of storage that is physically attached to the host machine where the EC2 
instance runs. It provides temporary block-level storage that is ideal for applications that require high I/O 
performance and low latency. However, it is important to note that instance store volumes are ephemeral, meaning that 
they are lost when the instance is stopped or terminated.

Instance store volumes are often used for temporary data, such as caches, buffers, or scratch space, where data loss is
acceptable. They are not suitable for data that needs to persist beyond the lifecycle of the instance, such as databases
or application data that must be retained after the instance is stopped or terminated.

Of course. Here is the content from the slides formatted as a Markdown note.


### Key Characteristics
- **Better I/O performance:** Instance stores are physically attached to the host computer, providing much higher I/O 
  performance than network-attached EBS volumes.
- **Ephemeral Storage:** EC2 Instance Store volumes lose all their data if the instance is **stopped** or **terminated**.
  The data persists through a reboot.
- **Good for:** buffer, cache, scratch data, and other temporary content.
- **Risk of Data Loss:** There is a risk of data loss if the underlying hardware fails.
- **Your Responsibility:** You are responsible for backups and data replication.

## Local EC2 Instance Store Performance

Instance store volumes can deliver **very high IOPS** (Input/Output Operations Per Second). The performance varies by 
instance family and size.

| Instance Size | 100% Random Read IOPS | Write IOPS  |
|:--------------|:----------------------|:------------|
| i3.large*     | 100,125               | 35,000      |
| i3.xlarge*    | 206,250               | 70,000      |
| i3.2xlarge    | 412,500               | 180,000     |
| i3.4xlarge    | 825,000               | 360,000     |
| i3.8xlarge    | 1.65 million          | 720,000     |
| i3.16xlarge   | 3.3 million           | 1.4 million |
| i3.metal      | 3.3 million           | 1.4 million |
| i3en.large*   | 42,500                | 32,500      |
| i3en.xlarge*  | 85,000                | 65,000      |
| i3en.2xlarge* | 170,000               | 130,000     |
| i3en.3xlarge  | 250,000               | 200,000     |
| i3en.6xlarge  | 500,000               | 400,000     |
| i3en.12xlarge | 1 million             | 800,000     |
| i3en.24xlarge | 2 million             | 1.6 million |
| i3en.metal    | 2 million             | 1.6 million |


# Resources
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)