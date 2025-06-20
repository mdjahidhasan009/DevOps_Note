# AWS EC2 (Amazon Elastic Compute Cloud)

AWS EC2 (Amazon Elastic Compute Cloud) is a cloud service that provides resizable virtual servers, called instances,
which you can use to run applications.

## What is AWS EC2?
Amazon EC2 is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make 
web-scale cloud computing easier for developers by providing complete control over computing resources and letting you 
run on Amazon's proven computing environment.

## Key Components of EC2 Instance
When launching an EC2 instance, you need to configure several components:

- **OS** (Operating System)
- **RAM** (Memory)
- **CPU** (Processing Power)
- **Disk Space** (Storage)
- **Network/Firewall** (Security Groups)
- **How to access the machine?** (Key Pairs, SSH/RDP)

## EC2 Instance Configuration Options

* **Instance Type**: Select the hardware capacity (e.g., CPU, memory).
* **AMI (Amazon Machine Image)**: Choose the operating system and software (linux, mac, windows).
* **Storage**: Configure the type and size of storage (e.g., EBS volume).
* **Security Groups**: Set up firewall rules to control inbound/outbound traffic.
* **Key Pair**: Create or use an existing key pair for SSH access.
* **Network Settings**: Configure VPC, subnet, and assign public or private IP addresses.
* **IAM Role**: Attach an IAM role for permissions to access other AWS resources.
* **User Data**: Add scripts to be executed when the instance starts.
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
* **General Purpose**: Balanced CPU, memory, and network resources (e.g., t3, m5).
* **Compute Optimized**: High CPU performance for compute-intensive tasks (e.g., c5).
* **Memory Optimized**: High memory capacity for memory-intensive applications (e.g., r5).
* **Accelerated Computing**: Instances with hardware accelerators like GPUs (e.g., p3, g4).
* **Storage Optimized**: High disk throughput and IOPS for data-intensive workloads (e.g., i3).
* **High Performance Computing (HPC)**: Optimized for high-performance computing tasks (e.g., hpc6id).

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
Instance sizes follow a pattern: `family.size`
- nano, micro, small, medium, large, xlarge, 2xlarge, 4xlarge, etc.
- Example: t3.micro, m5.large, c5.4xlarge

### Use Case Examples
* **Case 1: Small Website or Blog**
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

## Purchasing Options

| Purchasing Option       | Best for                                                                               | Cost                                | Commitment                    | Flexibility                   |
|-------------------------|----------------------------------------------------------------------------------------|-------------------------------------|-------------------------------|-------------------------------|
| **On-Demand**           | Short-term, unpredictable workloads                                                    | High                                | None                          | High                          |
| **Reserved Instances**  | Long-term, steady workloads (1-3 years)                                                | Medium to Low (Up to 75% off)       | 1 or 3 years                  | Low                           |
| **Spot Instances**      | Fault-tolerant, flexible, batch jobs, big data, CI/CD, distributed computing           | Very Low (Up to 90% off)            | None (subject to termination) | Medium (can be interrupted)   |
| **Savings Plans**       | Consistent workloads but needing more flexibility than Reserved Instances              | Medium to Low (Up to 72% off)       | 1 or 3 years                  | High (more flexible than RI)  |
| **Dedicated Hosts**     | Workloads requiring complete hardware control (compliance, software licensing)         | High                                | 1 or 3 years                  | Medium                        |
| **Dedicated Instances** | Workloads needing physical isolation without full control over the hardware            | High                                | None                          | Medium                        |

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
meaning if you allow incoming traffic on a specific port, the response traffic is automatically allowed.

### Important points about security groups
* **Region specific**
* **Only 'Allow' rule** (but no deny rule)
* **All inbound traffic blocked and outbound allowed by default**.
* You define rules for specific:
  * Protocols (like HTTP, HTTPS, SSH, etc.).
  * Port numbers (e.g., port 80 for HTTP, port 22 for SSH).
  * IP addresses or ranges (e.g., allow traffic only from a specific IP or range of IPs).
* If you allow incoming traffic on a specific port (e.g., port 80 for HTTP), the outgoing response traffic is
  automatically allowed without an explicit outbound rule.

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
* **SFTP (Port 22)** – Secure File Transfer Protocol.
* **SMTP (Port 25)** – Simple Mail Transfer Protocol (email sending).
* **RDP (Port 3389)** – Remote Desktop Protocol (Windows remote access).
* **MySQL (Port 3306)** – MySQL database connections.
* **PostgreSQL (Port 5432)** – PostgreSQL database connections.
* **DNS (Port 53)** – Domain Name System (converts domain names to IP addresses).

### Additional Common Ports
* **Telnet (Port 23)** – Unencrypted remote access (not recommended)
* **SMTP Secure (Port 587)** – Secure email submission
* **IMAP (Port 143)** – Internet Message Access Protocol
* **IMAPS (Port 993)** – IMAP over SSL/TLS
* **POP3 (Port 110)** – Post Office Protocol version 3
* **LDAP (Port 389)** – Lightweight Directory Access Protocol
* **LDAPS (Port 636)** – LDAP over SSL/TLS
* **MongoDB (Port 27017)** – MongoDB database connections
* **Redis (Port 6379)** – Redis database connections

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

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
* [How do I utilize user data to automatically run a script with every restart of my Amazon EC2 Linux instance?](https://repost.aws/knowledge-center/execute-user-data-ec2)
* [Amazon EC2 Instance types](https://aws.amazon.com/ec2/instance-types/)