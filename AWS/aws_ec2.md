# AWS EC2 (Amazon Elastic Compute Cloud)

AWS EC2 (Amazon Elastic Compute Cloud) is a cloud service that provides resizable virtual servers, called instances,
which you can use to run applications.




## What is AWS EC2?
Amazon EC2 is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make 
web-scale cloud computing easier for developers by providing complete control over computing resources and letting you 
run on Amazon's proven computing environment.




## Key Components of EC2 Instance
When launching an EC2 instance, you need to configure several components:

- **OS** (Operating System) Linux, Windows or Mac OS
- **RAM** (Memory)
- **CPU** (Processing Power)
- **Disk Space** (Storage) 
  - **Network Attached** EBS(Elastic Block Store), EFS(Elastic File System), 
  - nstance Store (Ephemeral Storage)
- **Network/Firewall** (Security Groups)
- **How to access the machine?** (Key Pairs, SSH/RDP)
- **Scaling** Auto Scaling Group (ASG), Load Balancing (ELB)
- **Network Card**: Speed of the card, public ip address




## EC2 Instance Configuration Options

* **Instance Type**: Select the hardware capacity (e.g., CPU, memory).
* **AMI (Amazon Machine Image)**: Choose the operating system and software (linux, mac, windows).
* **Storage**: Configure the type and size of storage (e.g., EBS volume).
* **Security Groups**: Set up firewall rules to control inbound/outbound traffic.
* **Key Pair**: Create or use an existing key pair for SSH access.
* **Network Settings**: Configure VPC, subnet, and assign public or private IP addresses.
* **IAM Role**: Attach an IAM role for permissions to access other AWS resources.
* **User Data(Boostrap Script)**: Add scripts to be executed when the instance starts.
* **Elastic IP**: Optionally associate a static IP address for consistent public access.

### Amazon Machine Image (AMI)
An AMI is a template that contains the software configuration (operating system, application server, and applications)
required to launch your instance. AWS provides several types of AMIs:

- **AWS-provided AMIs**: Pre-configured by AWS (Amazon Linux, Ubuntu, Windows Server)
- **AWS Marketplace AMIs**: Third-party software solutions
- **Community AMIs**: Shared by AWS community members
- **Custom AMIs**: Created by users from existing instances

### Storage Options
- **EBS (Elastic Block Store)**: Network-attached storage that persists independently
- **Instance Store**: Temporary storage directly attached to the host computer
- **EFS (Elastic File System)**: Scalable file storage for use with EC2 instances
- **S3**: Object storage accessible via API




## Types of EC2 Instances
EC2 instances come in various types, each optimized for different use cases. The main categories include:
* **General Purpose**: Great for diversity of workloads such as web servers or code repositories. **t2, t3, m5, etc.**
  * Balanced between
    * **CPU**, 
    * **memory**, 
    * **network** 
* **Compute Optimized**: Greate for compute-intensive tasks requires high performance processing power. **c5, c6i, etc.**
  * **Batch Processing**: Suitable for tasks that can be parallelized, such as data analysis and rendering
  * **Gaming**: Ideal for game servers that require high CPU performance
  * **Media Transcoding**: Efficient for converting media formats and resolutions
  * **High Performance web sever**: Optimized for high-performance web servers that require low latency and high
    throughput
  * **High Performance Computing (HPC)**: Suitable for scientific modeling, simulations, and complex calculations
  * **Scientific modeling, simulations, and machine learning**: Ideal for tasks that require high computational power,
    such as scientific simulations and machine learning training
* **Memory Optimized**: Fast performance for workloads that process large data sets in memory (e.g., r5).
  * High performance, relational/non-relational databases.
  * Distributed web scale cache stores.
  * In-memory databases for real-time analytics or in-memory databases for BI(Business Intelligence) applications.
  * **Real-time big data analytics**: Suitable for processing large volumes of data in real-time, such as log analysis 
    and stream processing(can be used with Apache Kafka or Apache Spark).
* **Storage Optimized**: Storage intensive tasks that require high, sequential read and write(IOPS) access to large
  data sets on local storage (e.g., i3).
  * High frequency online transaction processing (OLTP) systems.
  * Relational and non-relational databases.
  * Cache for in-memory databases(for example, Redis or Memcached).
  * Data warehousing applications that require high throughput and low latency.
  * Distributed file systems and big data workloads that require high storage throughput and low latency.
* **Accelerated Computing**: Instances with hardware accelerators like GPUs (e.g., p3, g4).
* **High Performance Computing (HPC)**: Optimized for high-performance computing tasks (e.g., hpc6id).

https://aws.amazon.com/ec2/instance-types/

### Instance Type Families
- **T-series**: Burstable performance instances (t3, t4g)
- **M-series**: General purpose instances (m5, m6i)
- **C-series**: Compute optimized (c5, c6i)
- **R-series**: Memory optimized (r5, r6i)
- **X-series**: High memory instances (x1e, x2gd)
- **I-series**: Storage optimized (i3, i4i)
- **P-series**: GPU instances for ML/AI (p3, p4)
- **G-series**: GPU instances for graphics workloads (g4, g5)

### Instance Size Naming Convention
Instance sizes follow a pattern: `instance_family+genation.size`
- **instance_family**: The type of instance (e.g., t3, m5, c5)
- **generation**: The generation of the instance 3, 4, 5, etc.
- **size**: The size of the instance, which indicates its resources like `micro`, `small`, `medium`, `large`, etc.
- **Example**: `t3.micro`, `m5.large`, `c5.4xlarge`

### Use Case Examples
* **Case 1: Small Website or Blog, web server or code repository**
  * Suitable Type: t3.micro or t3.small (General Purpose)
* **Case 2: E-Commerce Application**
  * Suitable Type: m5.large or m5.xlarge (General Purpose)
* **Case 3: Real-Time Video Rendering and Streaming (Accelerated Computing)**
  * Instance Type: g5.12xlarge or g5.24xlarge
* **Case 4: In-Memory Database for Real-Time Analytics (Memory Optimized)**
  * r6g.16xlarge or x2idn.32xlarge (Memory Optimized)

### Additional Use Cases
* **Machine Learning Training**: p3.8xlarge, p4d.24xlarge
* **High-Performance Databases**: r5.24xlarge, x1e.32xlarge
* **Distributed Analytics**: i3.16xlarge, i4i.32xlarge
* **Web Applications**: t3.medium, m5.large
* **Development/Testing**: t3.micro, t3.small


| Instance         | vCPU  | Mem (GiB)  | Storage              | Network Performance  | EBS Bandwidth (Mbps)  |
|:-----------------|:------|:-----------|:---------------------|:---------------------|:----------------------|
| `t2.micro`       | 1     | 1          | EBS-Only             | Low to Moderate      | -                     |
| `t2.xlarge`      | 4     | 16         | EBS-Only             | Moderate             | -                     |
| `c5d.4xlarge`    | 16    | 32         | 1 x 400 NVMe SSD     | Up to 10 Gbps        | 4,750                 |
| `r5.16xlarge`    | 64    | 512        | EBS Only             | 20 Gbps              | 13,600                |
| `m5.8xlarge`     | 32    | 128        | EBS Only             | 10 Gbps              | 6,800                 |

https://instances.vantage.sh/


## Purchasing Options

| Purchasing Option       | Best for                                                                                                                                   | Cost                                        | Commitment                    | Flexibility                   |
|-------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------|-------------------------------|-------------------------------|
| **On-Demand**           | Short-term workload, unpredictable workloads                                                                                               | High, predictable pricing -> pay by second  | None                          | High                          |
| **Reserved Instances**  | Reserved instances, Long-term workload, steady workloads, convertible reserved instance -> long workloads with fixable instances           | Medium to Low (Up to 75% off)               | 1 or 3 years                  | Low                           |
| **Spot Instances**      | Fault-tolerant, flexible, batch jobs, big data, CI/CD, distributed computing, short workloads, can lose instances(less reliable)           | Very Low (Up to 90% off)                    | None (subject to termination) | Medium (can be interrupted)   |
| **Savings Plans**       | Consistent workloads but needing more flexibility than Reserved Instances, commitment to an amount of uses, long workload                  | Medium to Low (Up to 72% off)               | 1 or 3 years                  | High (more flexible than RI)  |
| **Dedicated Hosts**     | Workloads requiring complete hardware control (compliance, software licensing), book an entire physical server, control instance placement | High                                        | 1 or 3 years                  | Medium                        |
| **Dedicated Instances** | Workloads needing physical isolation without full control over the hardware, no other customers will share your hardware                   | High                                        | None                          | Medium                        |

# Cloud Purchasing Options

## On-Demand Instances
*   **Best for:** Short-term, spiky, or unpredictable workloads where you can't forecast compute needs. Perfect for 
    development, testing, and applications being launched for the first time.
*   **Cost:** Highest, but with no upfront payment. You pay for what you use, typically by the second or hour, with 
    predictable pricing.
    * Linux or windows - billing per second, after first minute.
    * All other os - billing per hour.
*   **Commitment:** None. You can start, stop, or terminate instances at any time.
*   **Flexibility:** High. Easily scale your resources up or down as your needs change.

## Reserved Instances (RI)
*   **Best for:** Steady-state(like database), predictable, or long-term workloads. Convertible RIs are available for 
    long-term workloads where the instance type or family might need to change over time.
*   **Cost:** Medium to Low. Offers a significant discount (up to 75% off) compared to On-Demand pricing in exchange for
    a long-term commitment.
*   **Commitment:** 1 or 3 years.
*   **Flexibility:** Low. You are committed to specific instance families, OS, tenancy, regions, and terms, although 
    Convertible RIs offer some flexibility to change instance attributes.
*   **Scope**: Regional or Zonal(reserve capacity in an AZ).
*   Can buy or sell(if not needed) Reserved Instances in the AWS Marketplace.

### Convertable Reserved Instances
* Can change instance attributes like instance type, OS, tenancy or OS.
* Up to 66% discount.

## Spot Instances
*   **Best for:** Fault-tolerant, stateless, or flexible applications. Ideal for batch processing, data analysis, image
    processing, CI/CD pipelines, distributed computing/workload, workload with a flexible start and end time and any 
    workload that can withstand interruptions.
    * Not suitable for critical applications that require high availability or low latency or databases.
*   **Cost:** Very Low. The cheapest option, offering massive discounts (up to 90% off) by using a cloud provider's 
    spare compute capacity.
*   **Commitment:** None, but your instances can be terminated with a short (e.g., 2-minute) warning if the capacity is
    needed back.
*   **Flexibility:** Medium. You have flexibility in what you run, but you must be prepared for interruptions.

## Savings Plans
*   **Best for:** Consistent, long-term workloads where you need more flexibility than Reserved Instances. Instead of 
    reserving a specific instance, you commit to a certain amount of compute usage (e.g., $10/hour).
*   **Cost:** Medium to Low. Offers significant discounts (up to 72% off) similar to RIs.
*   **Commitment:** 1 or 3 years.
*   **Flexibility:** High. Much more flexible than RIs, as the discount automatically applies to any instance usage 
    across different families and regions (depending on the plan type).
    * Locked to a specific instance family and AWS region.
    * Flexible across instance sizes(e.g. m5.xlarge, m5.2xlarge), operating systems(e.g. Linux, Windows), and tenancy(
      host, dedicated, or shared).
*   Usages beyond EC2 saving plans is billed at the on demand price. 

## Dedicated Hosts
*   **Best for:** Workloads with strict compliance requirements or complex software licensing models (Bring Your Own 
    License - BYOL) that are tied to physical hardware, use your existing server-bound software licenses (per-socket, 
    per-core, pe—VM software licenses). You get an entire physical server and have control over instance 
    placement.
*   **Cost:** High. Typically, the most expensive option as you are leasing an entire physical machine.
*   **Commitment:** Can be purchased On-Demand, or with a 1 or 3-year reservation for savings.
*   **Flexibility:** Medium. You control the hardware, but you are tied to that specific server.

## Dedicated Instances
*   **Best for:** Workloads that require physical isolation for security or compliance but do not require the full 
    control (or cost) of a Dedicated Host. Your instances run on hardware dedicated to you, but you don't control the 
    physical server itself. May share hardware with other instances in same account. No control over placement(can move
    hardware after stop or start).
*   **Cost:** High. More expensive than standard On-Demand instances because of the hardware isolation.
*   **Commitment:** None. Billed on a standard, per-hour basis.
*   **Flexibility:** Medium. You get instance isolation without being locked into a specific physical server.

| Characteristic                                        | Dedicated Instances | Dedicated Hosts |
|:------------------------------------------------------|:-------------------:|:---------------:|
| Enables the use of dedicated physical servers         |          X          |        X        |
| Per instance billing (subject to a $2 per region fee) |          X          |                 |
| Per host billing                                      |                     |        X        |
| Visibility of sockets, cores, host ID                 |                     |        X        |
| Affinity between a host and instance                  |                     |        X        |
| Targeted instance placement                           |                     |        X        |
| Automatic instance placement                          |          X          |        X        |
| Add capacity using an allocation request              |                     |        X        |

## Capacity Reservations
*   **Best for:** Mission-critical workloads where you must guarantee the ability to launch instances in a specific 
    Availability Zone (AZ) for any duration. Ideal for disaster recovery or ensuring capacity for essential 
    applications. Have always access to EC2 capacity when you need it.
*   **Cost:** Billed at the standard On-Demand rate for the capacity you reserve, whether you use it or not. *Note: 
    Discounts from existing Savings Plans or RIs can be applied to cover the cost.* no billing discount.
*   **Commitment:** None. Can be created for any duration and cancelled at any time.
*   **Flexibility:** Medium. While flexible, the reservation is very specific (locked to an instance type and AZ), 
    ensuring you get exactly what you need.
*   Combine with Regional Reserved Instances and Savings Plans to benefit from billing discounts
*   You're charged at On-Demand rate whether you run instances or not
*   Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ


## Price Comparison Example – m4.large – us-east-1

| Price Type                             | Price (per hour)                           |
|:---------------------------------------|:-------------------------------------------|
| On-Demand                              | $0.10                                      |
| Spot Instance (Spot Price)             | $0.038 - $0.039 (up to 61% off)            |
| Reserved Instance (1 year)             | $0.062 (No Upfront) - $0.058 (All Upfront) |
| Reserved Instance (3 years)            | $0.043 (No Upfront) - $0.037 (All Upfront) |
| EC2 Savings Plan (1 year)              | $0.062 (No Upfront) - $0.058 (All Upfront) |
| Reserved Convertible Instance (1 year) | $0.071 (No Upfront) - $0.066 (All Upfront) |
| Dedicated Host                         | On-Demand Price                            |
| Dedicated Host Reservation             | Up to 70% off                              |
| Capacity Reservations                  | On-Demand Price                            |


# Spot Request
For seeing pricing history from EC2 > Instances > Spot Requests, click on "Pricing history" button. It will open popup
"Spot Instance pricing history" with a graph showing the price history with some filters.

Of course. Here is the information from your slide and note, refactored into a Markdown format suitable for study notes.

***

# EC2 Spot Instances

EC2 Spot Instances offer a way to leverage spare Amazon EC2 computing capacity at discounts of up to 90% compared to On-Demand pricing. They are an excellent choice for workloads that can tolerate interruptions.

## How Spot Instances Work

*   **Pricing Model:** The price for Spot Instances (the "spot price") fluctuates based on real-time supply and demand 
    for spare EC2 capacity. You pay the current spot price for the time your instance is running.
*   **Bidding Strategy:** You define a maximum price you are willing to pay per hour. Your instance will run whenever
    the current spot price is below your specified maximum.
    *   **Best Practice:** In most cases, it's recommended not to specify a maximum price. The default behavior caps 
    your price at the standard On-Demand rate, which prevents you from overpaying and simplifies management.
*   **Interruption Model:** The defining characteristic of Spot Instances is that AWS can reclaim them at any time if 
    the capacity is needed elsewhere.
    *   You receive a **two-minute interruption notice** before the instance is stopped or terminated, giving your 
        application a short grace period to save its state or complete current tasks.

## Use Cases

The interruptible nature of Spot Instances makes them ideal for specific types of workloads.

### ✅ Ideal Use Cases
*   **Fault-tolerant and stateless applications.**
*   **Batch processing jobs:** Large-scale rendering, media transcoding, or scientific simulations.
*   **Big data and analytics:** Running jobs on frameworks like Hadoop or Spark.
*   **CI/CD and testing environments:** Running builds and tests that can be easily restarted.

### ❌ Unsuitable Use Cases
*   **Critical production workloads** that require high availability (e.g., e-commerce websites).
*   **Stateful applications** like single-node databases that cannot easily handle sudden termination.

## Deprecated Feature: Spot Blocks

Spot Blocks were a variation of Spot Instances that allowed you to request an instance for a predefined, continuous 
duration (from 1 to 6 hours) with a lower probability of being interrupted.

*   A Spot Fleet is a collection of Spot Instances, and can optionally include On-Demand Instances to meet a target
    capacity.

*   The fleet attempts to meet the target capacity based on your defined constraints.
    *   You define multiple **launch pools** (e.g., instance type, OS, Availability Zone).
    *   Having multiple pools gives the fleet flexibility to find capacity.
    *   The fleet stops launching new instances once the target capacity or maximum cost is reached.
*   **Allocation Strategies for Spot Instances:**
    *   `lowestPrice`: Launches from the pool with the lowest price. Best for cost optimization and short workloads.
    *   `diversified`: Distributes instances across all defined pools. Best for availability and long-running workloads.
    *   `capacityOptimized`: Launches from the pool with the optimal capacity, reducing the chance of interruption.
    *   `priceCapacityOptimized` **(Recommended)**: Chooses from pools with the highest capacity available, and then 
         picks the one with the lowest price. This is the best choice for most workloads.

*   Spot Fleets provide a way to automatically request Spot Instances that meet your cost and capacity needs.

> #### ⚠️ Important Note on Deprecation
>
> Spot Blocks are a legacy feature and are **no longer supported**.
>
> *   They became unavailable to new AWS customers on **July 1, 2021**.
> *   Support for all customers ended on **December 31, 2022**.
>
> **Exam Note:** While this feature is obsolete, older exam questions *might* still reference the concept. It is useful 
> to know what they were, but your primary focus should be on the standard Spot Instance model.


# Managing and Terminating Spot Instances

Understanding the lifecycle of a Spot Request is crucial for managing your instances effectively. The process isn't as
simple as terminating a standard On-Demand instance because the request itself can persist and launch new instances.

## Spot Request Lifecycle

When you create a Spot Instance request, you define its behavior, which falls into two main types:

*   **One-Time Request:** You request one or more instances for a single fulfillment. Once the instances are launched 
    and later interrupted (either by you or by AWS), the request is `closed` and will not attempt to launch new 
    instances.
*   **Persistent Request:** The goal of this request is to maintain a target capacity. If an instance is interrupted or 
    stopped, the Spot service will automatically try to launch a replacement instance to fulfill the request again once 
    capacity is available and the spot price is below your maximum.

## Spot Request State Machine

A Spot Request moves through several states during its lifecycle:

*   `open`: The request has been created and is waiting for the Spot service to find available capacity at a suitable 
    price.
*   `active`: The request has been fulfilled, and the associated Spot Instances have been launched and are running.
*   `failed`: The request could not be fulfilled due to invalid parameters or other issues.
*   `cancelled`: The user has manually cancelled the request.
*   `closed`: A **one-time** request has finished its lifecycle (e.g., after its instance was terminated).
*   `disabled`: A **persistent** request is temporarily inactive. This happens if you stop its associated instance or 
    if it's interrupted by AWS. The request will attempt to become `open` again if re-enabled or if capacity returns.

## How to Properly Terminate Spot Instances

This is a critical two-step process. Simply terminating the instance is not enough for persistent requests.

> **Key Rule:** Cancelling a Spot Request **does not** automatically terminate the running instances associated with it.

To permanently stop using Spot Instances and avoid unexpected charges, you must follow this order:

1.  **Cancel the Spot Instance Request First:** This is the most important step. It tells AWS to stop trying to fulfill 
    the request. If you skip this, a persistent request will simply launch a new instance after you terminate the 
    current one.
    *   You can only cancel requests that are in the `open`, `active`, or `disabled` states.
2.  **Terminate the Associated Spot Instances:** After the request is cancelled, you can safely terminate any running 
    Spot Instances. They will not be replaced.


### Diagram 1: Spot Request Flow

Here is a text-based representation of the Spot Request and Instance flow diagram.

*   **Interrupt (persistent):** An **involuntary** action initiated by **AWS**. It happens because AWS needs the 
    capacity back. The persistent request will automatically try to get a new instance later.
*   **Stop (persistent):** A **voluntary** action initiated by **You (the user)**. You are choosing to pause the 
    instance. The persistent request will wait for you to start it again.


```
                                                          
                                                           start(persistent)  <------------- Stop (persistent)
                                                                |                                     ↑
                                                                |     -- Interrupt (persistent) <-----|                            
                                                                |     |                               |
                                                                ↓     ↓                               |                      
                               +---------------------------------------+                              |
Create request --------------> |            Spot request               |                              |
                               | - Maximum price                       |                              |
                               | - Desired number of instances         |-- Launch instances --> +-----------+
                               |                                       |                        |  [EC2] xN |  
                               | - Launch specification                |                        +-----------+
                               | - Request type: one-time | persistent |                               |  |
                               | - Valid from, Valid until             |                                  |
                               +---------------------------------------+                                  |
                                       |                                                                  ↓
                                       ↓                                       Interrupt (one-time) --> [TERMINATED]  
                                Request failed
```

                                        

### Diagram 2: Spot Request State Machine

Here is a text-based representation of the state transition diagram.

```
                     +---------+
Create request ----> |  (open) | -----------------> (failed)
                     +---------+
                         | |
                         | +---------------------> (cancelled)
                         |
                         V
      +----------------+
      |    (active)    |
      +----------------+
      |       |        |
      |       |        +-----------------------> (cancelled)
      |       |
 [one-time] [persistent]
      |       |
      V       V
  (closed)  +------------+
            | (disabled) |
            +------------+
              |      |
              |      +-------------------------> (cancelled)
              |
              +----[persistent]---> (Back to open)
```

# Creating Spot Instances
From EC2 > Instances > Spot Request, click on "Create Spot Fleet request".

## Launch Configuration Method
- **Manually configure launch parameters:** Select an AMI and configure optional parameters individually.
- **Use a launch template:** Use a pre-configured template for faster instance launches.

_After selection, you specify the **AMI** and **Key Pair**._

### Additional Launch Parameters (Optional)

#### EBS-optimized
Enables dedicated, high-throughput connection between the EC2 instance and its EBS volumes for improved performance. We 
can enable this by simply checking the box at left of **Launch EBS-optimized instances**

#### Instance Store
Provides temporary, block-level storage. Data on an instance store persists only for the lifetime of the instance (data 
is lost on stop/termination). We can enable this by simply checking the box at lef of **Attach at launch**

### EBS Volumes
You can add, remove, and configure EBS volumes for persistent storage. There already will be one root volume attached
to the instance, but you can add additional volumes as needed.

| Device           | Snapshot      | Size (GiB) | Volume Type               | IOPS  | Throughput | Delete on Term. | Encrypted |
|:-----------------|:--------------|:-----------|:--------------------------|:------|:-----------|:----------------|:----------|
| `Root:/dev/xvda` | `snap-083...` | 8          | General Purpose SSD (gp2) | N/A   | N/A        | ✅               | No        |

### Monitoring
Monitor, collect, and analyze instance metrics through Amazon CloudWatch. The default is free, basic monitoring, where
data is available in 5-minute periods. You can enable detailed monitoring, where data is available in 1-minute periods 
(additional charges apply). We can enable this by simply checking the box at left of **Enable CloudWatch detailed 
monitoring**.

### Tenancy
Your instance runs on hardware shared with other AWS customers. You can also choose dedicated hardware. Options are:
* **Default - run a shared hardware instance** The instance runs on hardware shared with other AWS customers. This is 
  the most cost-effective option.
* **Dedicated - run on dedicated hardware** The instance runs on a physical server dedicated to your use. This is useful
  for compliance or licensing requirements.

### Security Groups
A stateful firewall that controls inbound and outbound traffic for your instance. You can select one or more existing
security groups or create a new one.

### Auto-assign IPv4 public IP
Assigns a public IPv4 address to your instance at launch, making it reachable from the Internet. Can be set to use the 
subnet's default setting or enabled or disabled explicitly. If enabled, the instance will receive a public IP
address, allowing it to communicate with the Internet. If disabled, the instance will not receive a public IP address,
and it will only be reachable within the VPC or through a VPN or Direct Connect connection.

### IAM instance profile
A container for an IAM role that grants permissions to the EC2 instance to access other AWS services. Can select an 
existing IAM role or create a new one. This is useful for granting the instance permissions to access other AWS
services, such as S3 or DynamoDB, without needing to manage access keys. If you don't select an IAM role, the instance
will not have any permissions to access other AWS services.

### User data
- A script provided during launch to perform automated configuration tasks or run software.
- **Input Methods:** Can be provided as plain text or from a file.
- **Encoding:** A checkbox is available if the input is already Base64 encoded.


## Additional Request Details

If we check "Apply defaults" then IAM fleet role will be set and the request will be valid for 1 year. 

If we check this then all the options will be available for configuration at below of this line.

### IAM fleet role
None is default. And can select any existing IAM role or create a new one. This role grants the Spot Fleet permission to
launch and manage instances on your behalf. It is required for the Spot Fleet to function properly.

### Request Validity Period
- **Request valid from:** The start date and time for the Spot request to become active.
    - *Example:* `2025/07/09 22:59`
- **Request valid until:** The expiration date and time for the Spot request.
    - *Example:* `2026/07/09 22:59`
- **Terminate instances when request expires:** An option to automatically terminate all instances in the fleet when the
  request's validity period ends if it is checked. If not checked, the instances will continue to run until they are
  manually terminated or interrupted by AWS.

### Load Balancing
Automatically register instances launched by the Spot Fleet with one or more load balancers to distribute traffic. If
we checked this option then the blow options will be available for configuration.
- **Classic Load Balancers:**
    - Select an existing Classic Load Balancer.
    - Option to create a new one.
- **Target Groups:**
    - Used with Application Load Balancers (ALB) and Network Load Balancers (NLB).
    - Select a target group where the Spot instances will be registered as targets.

## Target Capacity

### Total Target Capacity
- **Total target capacity:** Defines the desired size of the fleet, specified in **instances** or **vCPUs** or **memory**.
    - _Example: `1` instances._
- **Include On-Demand base capacity:** Option to allocate a fixed number of instances as On-Demand, which are guaranteed
  and not subject to Spot interruptions. The remaining capacity is filled by Spot Instances.

### Capacity Maintenance & Interruption
**Maintain target capacity:** If we checked this then "Interruption behavior" and "Capacity rebalance" will be available.
the fleet will automatically launch replacements for any Spot Instances that are interrupted, ensuring the fleet size 
remains at its target.
- **Interruption behavior:** Defines what happens when a Spot Instance is reclaimed by AWS.
    - **Terminate:** The instance is shut down and terminated.
    - **Stop:** The instance is stopped and can be restarted later, but it will not count towards the target capacity.
    - **Hibernate:** The instance is hibernated, preserving its state, and can be resumed later. This option is only 
      available for certain instance types and requires EBS-backed instances.
- **Capacity rebalance:** A proactive feature where Spot Fleet attempts to replace an instance that has received a
  rebalance notification (a warning of an upcoming interruption). If we checked this then the following options will be
  available.
    - **Instance replacement strategy:**
        - **Launch only:** The fleet launches a new replacement instance but **does not** terminate the original 
          instance that received the rebalance warning.
        - **Launch before terminate:** Replacement instances will be launched and instances marked for rebalance will be
          automatically terminated after the termination delay.

_**Note:** You are charged for both the old and new instances until the original one is either manually terminated or 
interrupted by EC2._

### Cost Management
**Set maximum cost for Spot Instances:** Sets a ceiling on the total price you are willing to pay **per hour** for all
the Spot Instances in your fleet. If we checked this then the input box "Set your max cost(per hour)" will appear.

_Example: Set your max cost to `$` [amount] per hour._

## Network 
Here we will select vpc and az.

## Instance type requirements
- **Specify instance attributes:** Let AWS automatically select instance types for you based on your defined 
  minimum/maximum requirements for:
    - **vCPUs**
    - **Memory (GiB)**
    - Other optional attributes

- **Manually select instance types:** Directly choose the specific instance types (e.g., `t3.micro`, `m5.large`) you 
want to use.


### Additional Instance Attributes (Optional)
- **Purpose:** To further refine the automatic instance selection beyond just vCPU and memory.
- **Action:** Add specific requirements like network performance, CPU architecture, storage type, etc., to narrow down 
  the pool of suitable instance types.

### Preview matching instance types
Will show the list of instance types that match your requirements. This is useful to verify that the selected instance 
types meet your needs before launching the Spot Fleet.

## Spot Allocation Strategy

Determines how Spot Fleet selects from available instance pools to fulfill your request.

-   **Price capacity optimized (recommended):** Balances the lowest price with the most available pools. Best for most 
    workloads.
-   **Capacity optimized:** Prioritizes pools with the most available capacity to minimize the risk of interruption.
-   **Diversified:** Spreads instances evenly across all available pools. *(Cannot be used with attribute-based instance
    selection.)*

## Summary of fleet
There will be card saying "Your fleet request at a glance" where will a glance of my current fleet. With info like
Total target capacity, Instance requirements, Fleet strength, Estimated hourly price

### Detailed Purchasing Options

#### On-Demand Instances
- Pay by the hour or second (Linux/Windows)
- No long-term commitments
- Good for applications with unpredictable workloads
- Ideal for development and testing

#### Reserved Instances (RIs)
- **Standard RIs**: Up to 75% discount, least flexibility
- **Convertible RIs**: Up to 54% discount, can change instance attributes
- **Scheduled RIs**: Available for specific time windows
- Payment options: All Upfront, Partial Upfront, No Upfront

#### Spot Instances
- Bid for unused EC2 capacity
- Can be terminated with 2-minute notice
- Best for fault-tolerant, flexible applications
- Examples: Batch jobs, data analysis, image processing

#### Savings Plans
- **Compute Savings Plans**: Flexible across instance families, sizes, OS, tenancy
- **EC2 Instance Savings Plans**: Lower prices for specific instance families in specific regions
- Automatic application to usage




## Security Groups
Security groups act as virtual firewalls for your EC2 instances, controlling inbound and outbound traffic. They allow
you to specify rules based on protocols, ports, and source/destination IP addresses. Security groups are stateful,
meaning if you allow incoming traffic on a specific port, the response traffic is automatically allowed. They control
how traffic is allowed into or out of our EC2 instances. Security groups rules can be reference by IP or by security 
group.

### Important points about security groups
* **Region specific**
* **Only 'Allow' rule** (but no deny rule)
* **All inbound traffic blocked and all outbound allowed by default**.
* You define rules for specific:
  * Allowed protocols (like HTTP, HTTPS, SSH, etc.).
  * Allowed port numbers (e.g., port 80 for HTTP, port 22 for SSH).
  * Allowed IP(IPv4 and IPv6) addresses or ranges (e.g., allow traffic only from a specific IP or range of IPs).
* If you allow incoming traffic on a specific port (e.g., port 80 for HTTP), the outgoing response traffic is
  automatically allowed without an explicit outbound rule.
* Control of inbound network(from other to the instance) 
* Control outbound network(from the instance to others)
* Can be attached to multiple instances
* Locked down to a region and VPC
* Does live "outside" the EC2 instance - if traffic is blocked the EC2 instance won't see it.
* It's good to maintain one separate security group for SSH access.
* If application is not accessible (time out), then it's a security group issue.
* If application gives a "connection refused" error, then it's an application issue, or it's not launched.

### Security Group Features
- **Stateful**: Return traffic is automatically allowed
- **Multiple Security Groups**: Can attach multiple security groups to one instance
- **References**: Can reference other security groups as source/destination
- **Live Changes**: Changes take effect immediately
- **Default Behavior**: Deny all inbound, allow all outbound

### Security Group Rules Structure
Each rule consists of:
- **Type**: HTTP, HTTPS, SSH, Custom TCP, etc.
- **Protocol**: TCP, UDP, ICMP
- **Port Range**: Single port or range
- **Source/Destination**: IP addresses, CIDR blocks, or security group IDs
- **Description**: Optional description for the rule

### Some ports commonly used in security groups:

* **HTTP (Port 80)** – Unencrypted web traffic.
* **HTTPS (Port 443)** – Encrypted web traffic (SSL/TLS).
* **SSH (Port 22)** – Secure remote access to servers (Linux/Unix).
* **FTP (Port 21)** – File Transfer Protocol (unsecured).
* **SFTP (Port 22)** – Secure File Transfer Protocol using SSH.
* **SMTP (Port 25)** – Simple Mail Transfer Protocol (email sending).
* **RDP (Port 3389)** – Remote Desktop Protocol (Windows remote access).
* **MySQL (Port 3306)** – MySQL database connections.
* **PostgreSQL (Port 5432)** – PostgreSQL database connections.
* **DNS (Port 53)** – Domain Name System (converts domain names to IP addresses).
* **Remote Desktop Protocol (RDP) (Port 3389)** – Remote access to Windows instances.

### Additional Common Ports
* **Telnet (Port 23)** – Unencrypted remote access (not recommended)
* **SMTP Secure (Port 587)** – Secure email submission
* **IMAP (Port 143)** – Internet Message Access Protocol
* **IMAPS (Port 993)** – IMAP over SSL/TLS
* **POP3 (Port 110)** – Post Office Protocol version 3
* **LDAP (Port 389)** – Lightweight Directory Access Protocol
* **LDAPS (Port 636)** – LDAP over SSL/TLSff
* **MongoDB (Port 27017)** – MongoDB database connections
* **Redis (Port 6379)** – Redis database connections


# SSH Summary Table

| Platform      | SSH  | Putty  | EC2 Instance Connect |
|---------------|------|--------|----------------------|
| Mac           | ✓    |        | ✓                    |
| Linux         | ✓    |        | ✓                    |
| Windows < 10  |      | ✓      | ✓                    |
| Windows >= 10 | ✓    | ✓      | ✓                    |

## User Data Script -> Startup Commands for EC2 Instance

For User Data Script (startup commands) goes to advance settings and at the bottom we can write our script or upload a
file with the script. Example script to install Apache web server with a simple HTML page when the instance starts:

### What is User Data?
User Data is a script that runs when an EC2 instance first starts. It's executed with root privileges and can be used to:
- Install software packages
- Configure services
- Download and install applications
- Set up monitoring
- Configure security settings

### User Data Limitations
- Maximum size: 16 KB
- Base64 encoded when passed to the instance
- Runs only once at first boot (by default)
- Executed as root user
- Must include shebang line (#!/bin/bash)

### Red Hat-based Linux distributions like:

* Red Hat Enterprise Linux (RHEL)
* CentOS
* Fedora
* Amazon Linux

```bash
#!/bin/bash
sudo yum update -y

# Install Apache web server (httpd)
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd

# Create a simple HTML file to verify the web server is running
echo "<html><h1>Welcome to Apache Web Server on Amazon Linux!</h1></html>" > /var/www/html/index.html
```

### For debian-based Linux distributions like:
* Ubuntu
* Debian
* Linux Mint

```bash
#!/bin/bash

# Update package lists
sudo apt update -y

# Install Apache web server (apache2)
sudo apt install -y apache2

# Start and enable Apache
sudo systemctl start apache2
sudo systemctl enable apache2

# Create a simple HTML file to verify the web server is running
# Note: On Apache2 (Debian/Ubuntu), the default web root is /var/www/html/
echo "<html><h1>Welcome to Apache Web Server on Ubuntu/Debian!</h1></html>" | sudo tee /var/www/html/index.html > /dev/null
```

### Advanced User Data Examples

#### Installing Docker
```bash
#!/bin/bash
yum update -y
yum install -y docker
systemctl start docker
systemctl enable docker
usermod -a -G docker ec2-user
```

#### Setting up a Python Web Server
```bash
#!/bin/bash
apt update -y
apt install -y python3 python3-pip
pip3 install flask
mkdir /app
cat > /app/app.py << 'EOF'
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return '<h1>Hello from EC2!</h1>'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
EOF
cd /app && python3 app.py
```

### Run User Data Script on Restart

First I add `yum` inside user data but my machine was Ubuntu, so it didn't work. Then I added `apt` but as the user data
script at the first boot cycle, so restarting or stopping and starting the instance again, it didn't run the script.

For troubleshooting, I connected to the instance using SSH and checked the user data script in the
`/var/lib/cloud/instance/scripts/` directory. There was update command in the script. Using
`cat /var/log/cloud-init-output.log` I checked the output of the user data script, and it showed that the old script is
executed only during the first boot cycle.

### Troubleshooting User Data
```shell
jahid@<pc_name>:<path>$ chmod 400 "jahid.pem"
jahid@<pc_name>:<path>$ ssh -i "jahid.pem" ubuntu@47.129.244.118
ubuntu@ip-172-31-23-204:~$ sudo rm -rf /var/lib/cloud/instance/*
ubuntu@ip-172-31-23-204:~$ sudo rm -rf /var/lib/cloud/data/*
ubuntu@ip-172-31-23-204:~$ sudo cloud-init init --local
Cloud-init v. 24.4.1-0ubuntu0~24.04.3 running 'init-local' at Tue, 10 Jun 2025 16:26:39 +0000. Up 585.90 seconds.
ubuntu@ip-172-31-23-204:~$ sudo cloud-init init
ubuntu@ip-172-31-23-204:~$ cd /var/lib/cloud/instance/scripts/
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ ls
part-001
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ cat part-001
cat: part-001: Permission denied
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ sudo cat part-001
#!/bin/bash
sudo apt update -y

sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2

echo "<html><h1>Welcome to Apache Web Server on Ubuntu/Debian!</h1></html>"  | sudo tee /var/www/html/index.html > /dev/null
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ cloud-init clean
Error:
Could not remove /var/lib/cloud/instances: [Errno 13] Permission denied: 'vendor-data2.txt'
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ sudo cloud-init clean
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ sudo rm -rf /var/lib/cloud/instance/*
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ sudo rm -rf /var/lib/cloud/data/*
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ sudo cat part-001
cat: part-001: No such file or directory
ubuntu@ip-172-31-23-204:/var/lib/cloud/instance/scripts$ sudo cat /var/log/cloud-init-output.log 
```

Despite AWS EC2 instance has the updated user data, it only runs during the first boot cycle. If we want to run the
user data script every time the instance is restarted, we can use a mime multi-part file format for the user data
script. This format allows us to specify multiple parts, including cloud-config and shell scripts, which can be
executed in a specific order. The cloud-config part can specify that the user script should always run, even on
subsequent boots, by using the `cloud_final_modules` directive with the `scripts-user` module set to `always`.

A mime multi-part file allows us command to override how frequently user data is run in the cloud-init package. Then,
the file runs the user command

### MIME Multi-part User Data (Runs on Every Restart)
This one works (on restart it runs the script we give it):

```shell
Content-Type: multipart/mixed; boundary="//"
MIME-Version: 1.0
 
--//
Content-Type: text/cloud-config; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment;
 filename="cloud-config.txt"
 
#cloud-config
cloud_final_modules:
- [scripts-user, always]
--//
Content-Type: text/x-shellscript; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="userdata.txt"
 
#!/bin/bash
sudo apt update -y
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
echo "<html><h1>Welcome to Apache Web Server on Ubuntu/Debian!11</h1></html>" | sudo tee /var/www/html/index.html > /dev/null
echo "all done"
--//--
```

After that using `cat /var/log/cloud-init-output.log` I checked the output of the user data script, and it showed that
the new script is executed every time the also get `all done` message in the output.

### Useful Cloud-Init Commands for Debugging
```bash
# Check cloud-init logs
sudo cat /var/log/cloud-init-output.log
sudo cat /var/log/cloud-init.log

# Check user data script
sudo cat /var/lib/cloud/instance/user-data.txt

# Re-run user data (for testing)
sudo cloud-init clean
sudo cloud-init init

# Check cloud-init status
sudo cloud-init status

# View cloud-init configuration
sudo cloud-init query --all
```



## Attach IAMReadOnlyAccess policy with EC2

```shell
sudo apt-get update
sudo apt-get install python3-pip
sudo pip install awscli


sudo apt install -y unzip curl
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

Now before adding the `IAMReadOnlyAccess` to the EC2 instance we are getting error
```shell
ubuntu@ip-172-31-27-115:~$ aws iam list-users

Unable to locate credentials. You can configure credentials by running "aws configure".
```

As the cli does not have access to the IAM. As We are using EC2 intance connect if we add our aws secrect key to this
then any one has access to this EC2 can have our credential. So we can add `IAMReadOnlyAccess` with this EC2 so it will
able to get our IAM. 

Select the instance click on "Action" > Security > Modify IAM Role. Now at modify IAM role page at IAM role input select
our created role with has `IAMReadOnlyAccess` and click on "Update IAM role".

Now from the shell we will see the list user
```shell
ubuntu@ip-172-31-27-115:~$ aws iam list-users
{
    "Users": [
        {
            "Path": "/",
            "UserName": "jahid",
            "UserId": <userId>,
            "Arn": <arn>,
            "CreateDate": <date>,
            "PasswordLastUsed": <date>
        }
    ]
}
```


## EC2 Instance Lifecycle

### Instance States
- **Pending**: Instance is being launched
- **Running**: Instance is running and ready to use
- **Stopping**: Instance is being stopped
- **Stopped**: Instance is shut down
- **Shutting-down**: Instance is being terminated
- **Terminated**: Instance is terminated and cannot be restarted

### Instance Actions
- **Start**: Boot a stopped instance
- **Stop**: Shut down instance (EBS-backed only)
- **Reboot**: Restart the instance
- **Terminate**: Permanently delete the instance
- **Hibernate**: Save RAM contents to EBS root volume

## Monitoring and Management

### CloudWatch Metrics
EC2 automatically provides several metrics:
- **CPU Utilization**: Percentage of allocated CPU time
- **Network In/Out**: Network traffic metrics
- **Disk Read/Write**: Disk I/O operations
- **Status Check Failed**: Instance and system reachability

### Enhanced Monitoring
- Detailed monitoring provides metrics every 1 minute (additional cost)
- Custom metrics can be sent to CloudWatch
- CloudWatch Logs for log file monitoring

### Systems Manager (SSM)
- **Session Manager**: Browser-based shell access
- **Run Command**: Execute commands on multiple instances
- **Patch Manager**: Automated patching
- **Parameter Store**: Secure storage for configuration data

### Some other commands
```shell
cat /etc/os-release # Check OS version
free # Check memory usage
df -h # Check disk space usage
lsblk # List block devices (disks and partitions)
```

### Additional Useful Commands
```bash
# System information
uname -a              # System information
whoami                # Current user
uptime                # System uptime and load
top                   # Real-time process viewer
htop                  # Enhanced process viewer (if installed)
ps aux                # List all running processes

# Network information
ifconfig              # Network interface configuration (deprecated)
ip addr show          # Modern way to show IP addresses
netstat -tuln         # Show listening ports
ss -tuln              # Modern replacement for netstat
curl ifconfig.me      # Get public IP address

# Storage information
fdisk -l              # List all disk partitions
mount                 # Show mounted filesystems
du -sh /path/to/dir   # Directory size
find / -type f -size +100M 2>/dev/null # Find large files

# Service management (systemd)
systemctl status service_name    # Check service status
systemctl start service_name     # Start a service
systemctl stop service_name      # Stop a service
systemctl enable service_name    # Enable service at boot
systemctl disable service_name   # Disable service at boot
journalctl -u service_name       # View service logs

# Package management
# For Ubuntu/Debian:
apt list --installed             # List installed packages
apt search package_name          # Search for packages
# For Amazon Linux/RHEL/CentOS:
yum list installed               # List installed packages
yum search package_name          # Search for packages
```

## Best Practices

### Security Best Practices
- **Use IAM Roles**: Attach IAM roles instead of embedding credentials
- **Secure Key Pairs**: Store private keys securely, use unique keys per environment
- **Regular Updates**: Keep OS and software updated
- **Principle of Least Privilege**: Grant minimum necessary permissions
- **Network Segmentation**: Use VPCs and subnets appropriately

### Performance Optimization
- **Right-sizing**: Choose appropriate instance types for workload
- **Monitoring**: Use CloudWatch to monitor performance metrics
- **Auto Scaling**: Implement auto scaling for variable workloads
- **Load Balancing**: Distribute traffic across multiple instances
- **Storage Optimization**: Choose appropriate storage types (EBS, Instance Store)

### Cost Optimization
- **Reserved Instances**: Use RIs for predictable workloads
- **Spot Instances**: Use spot instances for fault-tolerant workloads
- **Scheduled Scaling**: Scale resources based on usage patterns
- **Resource Cleanup**: Terminate unused instances and delete unused resources
- **Cost Monitoring**: Use AWS Cost Explorer and budgets

### Backup and Recovery
- **EBS Snapshots**: Regular backups of EBS volumes
- **AMI Creation**: Create AMIs for quick instance recovery
- **Multi-AZ Deployment**: Deploy across multiple Availability Zones
- **Disaster Recovery**: Plan and test disaster recovery procedures


# AWS Charges for Public IPv4 Addresses

## General Pricing Policy
*   **Effective Date:** As of **February 1st, 2024**, AWS charges for all Public IPv4 addresses associated with your
    account.
*   **Cost:** The standard charge is **$0.005 per hour** for each Public IPv4 address, which equates to approximately 
    **$3.6 per month**.

## AWS Free Tier for EC2
*   **Eligibility:** The Free Tier for Public IPv4 is available only to **new AWS accounts** for their first
    **12 months**.
*   **Allowance:** It includes **750 hours of Public IPv4 usage per month** specifically for **EC2 instances**. If you 
    run one EC2 instance continuously for a month, its Public IP usage will be covered by the Free Tier.

## Charges for Other Services (No Free Tier)
*   There is **no free tier** for Public IPv4 addresses used by any service other than EC2.
*   **Load Balancers:** Consume one Public IPv4 address per Availability Zone (AZ) they are active in. These are charged
    at the standard rate with no free tier.
*   **RDS Databases:** Publicly accessible RDS instances use one Public IPv4 address, which is charged at the standard 
    rate with no free tier.

Troubleshoot tips: https://repost.aws/articles/ARknH_OR0cTvqoTfJrVGaB8A/why-am-i-seeing-charges-for-public-ipv4-addresses-when-i-am-under-the-aws-free-tier

Also using IPAM we will get all our public IPv4 addresses and their usage. IPAM is a service that helps you manage IP 
addresses in your AWS environment, including tracking Public IPv4 addresses and their usage across different services 
like EC2, RDS, and Load Balancers. It provides visibility into your IP address allocations and helps you optimize your 
IP address usage.

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
* [How do I utilize user data to automatically run a script with every restart of my Amazon EC2 Linux instance?](https://repost.aws/knowledge-center/execute-user-data-ec2)
* [Amazon EC2 Instance types](https://aws.amazon.com/ec2/instance-types/)