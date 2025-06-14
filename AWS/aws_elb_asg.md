# AWS Scaling and Load Balancing

## Scaling Concepts

### Vertical Scalability (Scaling Up)

* Vertical Scalability means adding more power (CPU, RAM) to your existing server.
* Ex: `t2.micro` to `m5.large`

#### Vertical Scaling Characteristics
- **Single Instance**: Increase capacity of existing instance
- **Downtime**: Usually requires instance restart
- **Limits**: Hardware limitations apply
- **Cost**: Can be expensive for high-end instances
- **Use Cases**: Databases, legacy applications that can't be distributed
- **AWS Implementation**: Change instance type via console or API

#### Vertical Scaling Examples
```bash
# Stop instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Modify instance type
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --instance-type m5.large

# Start instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0
```

#### Vertical Scaling Limitations
- **Single Point of Failure**: One instance handles all load
- **Hardware Limits**: Cannot scale beyond largest instance type
- **Cost Inefficiency**: May pay for unused capacity
- **Downtime Required**: Instance must be stopped for scaling

### Horizontal Scalability (Scaling Out)

* Horizontal Scalability means adding more instances (servers) to distribute the load.
* You can add more EC2 instances behind a load balancer.

#### Horizontal Scaling Characteristics
- **Multiple Instances**: Add more servers to handle load
- **No Downtime**: New instances added without stopping existing ones
- **Unlimited**: Theoretically unlimited scaling capacity
- **Cost Effective**: Pay only for what you need
- **Fault Tolerant**: If one instance fails, others continue
- **AWS Implementation**: Auto Scaling Groups, Load Balancers

#### Horizontal Scaling Benefits
- **High Availability**: Multiple instances across AZs
- **Better Performance**: Load distributed across instances
- **Cost Optimization**: Scale up/down based on demand
- **Fault Tolerance**: Redundancy prevents single points of failure
- **Geographic Distribution**: Instances in multiple regions

#### Scaling Comparison

| Aspect              | Vertical Scaling         | Horizontal Scaling           |
|---------------------|--------------------------|------------------------------|
| **Implementation**  | Upgrade hardware         | Add more servers             |
| **Downtime**        | Required                 | Not required                 |
| **Cost**            | High for large instances | Flexible, pay-as-you-go      |
| **Complexity**      | Simple                   | More complex                 |
| **Limits**          | Hardware constraints     | Nearly unlimited             |
| **Fault Tolerance** | Single point of failure  | High availability            |
| **AWS Services**    | EC2 instance types       | ASG, ELB, multiple instances |

### High Availability (HA)

* High Availability (HA) means keeping your service up and running with minimal downtime, so it's always accessible to 
  users.
* Ex: running resources in multiple AZs

#### High Availability Principles
- **Redundancy**: Multiple copies of critical components
- **Geographic Distribution**: Resources across multiple AZs/regions
- **Automated Failover**: Automatic switching to backup systems
- **Health Monitoring**: Continuous monitoring and alerting
- **Disaster Recovery**: Plans for catastrophic failures

#### AWS HA Design Patterns
- **Multi-AZ Deployments**: Resources in multiple Availability Zones
- **Cross-Region Replication**: Data replication across regions
- **Auto Scaling**: Automatic replacement of failed instances
- **Load Balancing**: Traffic distribution and health checks
- **Database HA**: RDS Multi-AZ, read replicas

#### HA Metrics
- **Uptime**: Percentage of time service is available
- **RTO (Recovery Time Objective)**: Maximum acceptable downtime
- **RPO (Recovery Point Objective)**: Maximum acceptable data loss
- **MTTR (Mean Time To Recovery)**: Average time to restore service
- **MTBF (Mean Time Between Failures)**: Average time between failures

### Elasticity

* Elasticity means the ability to automatically adjust resources as the demand changes—adding more when needed and
  removing when it's no longer necessary.
* Ex: ASG(Auto Scaling Group).

#### Elasticity Characteristics
- **Automatic**: No manual intervention required
- **Responsive**: Quick response to demand changes
- **Cost Effective**: Resources scaled based on actual need
- **Proactive**: Can scale before demand hits
- **Bidirectional**: Scale up and scale down

#### Elasticity vs Scalability
- **Scalability**: Ability to handle increased load
- **Elasticity**: Automatic scaling based on demand
- **Example**: Scalability is having the ability to add servers; Elasticity is automatically adding servers when CPU >
  80%

## Elastic Load Balancer (ELB)

* **Distributes Traffic:** It splits incoming traffic across multiple servers so no single server gets overloaded.
* **Improves Availability:** If one server goes down, the load balancer automatically sends traffic to the working
  servers, ensuring your application stays available.
* **Scales Resources:** It helps manage high demand by adding more servers during peak times and distributing the load.
* **Single point of Access need to be expose.**
* **HA across AZs**

### Load Balancer Benefits
- **High Availability**: Distributes traffic across healthy instances
- **Fault Tolerance**: Automatically routes around failed instances
- **Scalability**: Handles increasing traffic loads
- **SSL Termination**: Offloads SSL/TLS processing
- **Health Monitoring**: Continuous health checks
- **Security**: DDoS protection and security integration

### Load Balancing Algorithms
- **Round Robin**: Requests distributed evenly in order
- **Least Connections**: Route to instance with fewest connections
- **Weighted Round Robin**: Different weights for different instances
- **IP Hash**: Route based on client IP hash
- **Sticky Sessions**: Route user to same instance

## AWS Load Balancer Types

AWS offers different types of load balancers depending on your needs.

* **Application Load Balancer (ALB)** is perfect for web applications, handling complex HTTP and HTTPS requests (Layer
  7).
* **Network Load Balancer (NLB)** is designed for high-performance and low latency, perfect for TCP/UDP traffic (ex:
  gaming, financial apps) (Layer 4).
* **Gateway Load Balancer (GWLB)** helps deploy, scale, and manage third-party virtual appliances, such as firewalls and
  monitoring solutions.

### Application Load Balancer (ALB)
#### Features
- **Layer 7 (Application Layer)**: HTTP/HTTPS traffic
- **Content-Based Routing**: Route based on URL, headers, query strings
- **Host-Based Routing**: Route based on hostname
- **Path-Based Routing**: Route based on URL path
- **WebSocket Support**: Real-time communication
- **HTTP/2 Support**: Modern HTTP protocol
- **SSL/TLS Termination**: Certificate management

#### ALB Components
- **Load Balancer**: The main load balancer resource
- **Listeners**: Check for connection requests using protocol/port
- **Target Groups**: Route requests to registered targets
- **Rules**: Determine how requests are routed
- **Health Checks**: Monitor target health

#### ALB Routing Examples
```yaml
# Path-based routing
/api/* → API Target Group
/images/* → Image Server Target Group
/* → Default Web Server Target Group

# Host-based routing
api.example.com → API Target Group
www.example.com → Web Target Group
admin.example.com → Admin Target Group
```

#### ALB Use Cases
- **Microservices**: Route to different services based on path
- **A/B Testing**: Route percentage of traffic to different versions
- **Blue/Green Deployments**: Switch traffic between environments
- **Multi-Tenant Applications**: Route based on subdomain

### Network Load Balancer (NLB)
#### Features
- **Layer 4 (Transport Layer)**: TCP/UDP/TLS traffic
- **High Performance**: Millions of requests per second
- **Low Latency**: Ultra-low latency routing
- **Static IP**: Fixed IP addresses per AZ
- **Source IP Preservation**: Client IP preserved
- **Connection Multiplexing**: Single connection to multiple targets

#### NLB Characteristics
- **Protocol Support**: TCP, UDP, TLS
- **Performance**: Handle extreme loads with low latency
- **IP Addresses**: Static IP addresses for whitelisting
- **Health Checks**: TCP-based health checks
- **Zone Isolation**: Isolate failures to single AZ

#### NLB Use Cases
- **Gaming Applications**: Low latency requirements
- **IoT Applications**: High connection volume
- **Financial Applications**: Ultra-low latency trading
- **Legacy Applications**: Non-HTTP protocols
- **Extreme Performance**: Millions of connections

### Gateway Load Balancer (GWLB)
#### Features
- **Layer 3 (Network Layer)**: IP packet level
- **Transparent Proxy**: Invisible to source and destination
- **Third-Party Integration**: Virtual appliances deployment
- **High Availability**: Distribute across multiple appliances
- **Centralized Security**: Single point for security controls

#### GWLB Use Cases
- **Firewalls**: Deploy third-party firewall appliances
- **IDS/IPS**: Intrusion detection and prevention systems
- **Deep Packet Inspection**: Advanced traffic analysis
- **Compliance**: Meet regulatory requirements
- **Security Appliances**: Any third-party virtual appliance

### Load Balancer Comparison

| Feature       | ALB              | NLB              | GWLB                    |
|---------------|------------------|------------------|-------------------------|
| **OSI Layer** | Layer 7          | Layer 4          | Layer 3                 |
| **Protocols** | HTTP/HTTPS       | TCP/UDP/TLS      | IP                      |
| **Use Case**  | Web applications | High performance | Security appliances     |
| **Routing**   | Content-based    | Connection-based | Transparent             |
| **Latency**   | Medium           | Ultra-low        | Medium                  |
| **Features**  | Advanced routing | Static IP        | Third-party integration |

### Steps to Create an Application Load Balancer

* **Set Up EC2 Instances:** Create two or more EC2 instances, install a web server, and tag them for easy identification.
* **Configure Security Groups:** Set up a security group allowing HTTP and SSH access.
* **Create the Load Balancer:** Use the EC2 dashboard to create an Application Load Balancer and set it as 
  internet-facing.
* **Register Targets:** Add EC2 instances to the target group and configure health checks.
* **Test the Load Balancer:** Access the DNS name of the load balancer and observe load balancing in action.
* **Explain to Students:** Highlight key concepts like traffic distribution, high availability, and scalability.

### Detailed ALB Creation Steps

#### 1. Prepare EC2 Instances
```bash
# User data script for web server
#!/bin/bash
sudo apt update -y
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
echo "<html><h1>Web Server - $(hostname) - $(date)</h1></html>" | sudo tee /var/www/html/index.html > /dev/null
```

#### 2. Security Group Configuration
- **Inbound Rules**:
  - HTTP (80) from 0.0.0.0/0
  - HTTPS (443) from 0.0.0.0/0
  - SSH (22) from your IP
- **Outbound Rules**: Allow all (default)

#### 3. Target Group Configuration
- **Target Type**: Instances
- **Protocol**: HTTP
- **Port**: 80
- **Health Check**:
  - Protocol: HTTP
  - Path: /
  - Healthy Threshold: 2
  - Unhealthy Threshold: 3
  - Timeout: 5 seconds
  - Interval: 30 seconds

#### 4. Load Balancer Configuration
- **Scheme**: Internet-facing
- **IP Address Type**: IPv4
- **VPC**: Default or custom VPC
- **Availability Zones**: Select all available AZs
- **Security Groups**: HTTP/HTTPS security group

### ALB Advanced Features

#### SSL/TLS Termination
- **Certificate Sources**: ACM, IAM, or upload
- **Security Policies**: Predefined SSL security policies
- **SNI Support**: Multiple certificates per load balancer
- **Perfect Forward Secrecy**: Enhanced security

#### Advanced Routing
```yaml
# Weighted routing for A/B testing
Rule 1: Forward 90% to Production Target Group
Rule 2: Forward 10% to Beta Target Group

# Header-based routing
IF header "Version" = "v2" THEN forward to V2 Target Group
ELSE forward to V1 Target Group
```

## AWS ASG (Auto Scaling Group)

AWS ASG (Auto Scaling Group) is a service that automatically adds or removes EC2 instances based on demand to ensure
your application is always available.

It helps scale up when more capacity is needed and scale down during low usage to save costs, keeping the right number
of servers running at all times.

### Auto Scaling Concepts
- **Desired Capacity**: Target number of instances
- **Minimum Capacity**: Minimum instances to maintain
- **Maximum Capacity**: Maximum instances allowed
- **Health Checks**: Monitor instance health
- **Scaling Policies**: Rules for scaling actions
- **Launch Configuration/Template**: Instance configuration blueprint

### Functions

* **Automatic Scaling:** Scale the number of EC2 instances up or down based on demand.
* **Maintain Instance Health:** Replace unhealthy instances automatically to ensure reliability.
* **Use Scaling Policies:** Set rules for scaling based on metrics like CPU usage or request count.
* **Ensure Availability:** Always keep a defined number of instances running to meet application needs.
* **Schedule Scaling:** Pre-configure scaling activities for specific times (e.g., traffic peaks).
* **Distribute Instances:** Deploy instances across multiple Availability Zones for high availability.
* **Integrate with ELB:** Attach instances to an Elastic Load Balancer to automatically balance traffic.
* **Optimize Costs:** Scale down during low demand to save on infrastructure costs.

### ASG Scaling Policies

#### Target Tracking Scaling
- **CPU Utilization**: Scale based on average CPU usage
- **Request Count**: Scale based on requests per target
- **Network In/Out**: Scale based on network metrics
- **Custom Metrics**: Scale based on custom CloudWatch metrics

```json
{
  "TargetValue": 50.0,
  "PredefinedMetricSpecification": {
    "PredefinedMetricType": "ASGAverageCPUUtilization"
  }
}
```

#### Step Scaling
- **Multiple Steps**: Different scaling amounts for different thresholds
- **Flexible**: More control over scaling behavior
- **CloudWatch Alarms**: Based on CloudWatch alarm states

```yaml
Step 1: CPU > 50% → Add 1 instance
Step 2: CPU > 70% → Add 2 instances
Step 3: CPU > 90% → Add 3 instances
```

#### Simple Scaling
- **Single Action**: One scaling action per alarm
- **Cooldown Period**: Prevents rapid scaling
- **Basic**: Simple threshold-based scaling

#### Scheduled Scaling
- **Predictable Patterns**: Scale based on known patterns
- **Time-Based**: Scale at specific times
- **Recurring**: Daily, weekly, monthly schedules

```bash
# Scale up before business hours
Cron: 0 8 * * MON-FRI → Desired: 10

# Scale down after business hours
Cron: 0 18 * * MON-FRI → Desired: 2
```

### Health Checks

#### EC2 Health Checks
- **Instance Status**: EC2 instance health
- **System Status**: Underlying hardware health
- **Default**: Basic EC2 health checks

#### ELB Health Checks
- **Application Health**: HTTP response codes
- **Custom Health Checks**: Application-specific checks
- **More Accurate**: Detects application-level issues

#### Custom Health Checks
- **External Systems**: Third-party monitoring
- **API Integration**: Custom health check APIs
- **Complex Logic**: Business-specific health criteria

### Steps to Create ASG

* Launch Template or Configuration
* Create Auto Scaling Group
* Select VPC and Subnets
* Attach Load Balancer (Optional)
* Configure Scaling Policies
* Health Checks
* Add Notifications (Optional)
* Review and Create

#### Detailed ASG Creation Steps

##### 1. Create Launch Template
```json
{
  "LaunchTemplateName": "WebServer-Template",
  "LaunchTemplateData": {
    "ImageId": "ami-12345678",
    "InstanceType": "t3.micro",
    "SecurityGroupIds": ["sg-12345678"],
    "UserData": "base64-encoded-script",
    "IamInstanceProfile": {
      "Name": "EC2-Role"
    },
    "TagSpecifications": [{
      "ResourceType": "instance",
      "Tags": [{"Key": "Name", "Value": "ASG-Instance"}]
    }]
  }
}
```

##### 2. Configure ASG Settings
- **Group Size**:
  - Minimum: 1
  - Desired: 2
  - Maximum: 10
- **Network**: VPC and subnets across multiple AZs
- **Load Balancing**: Attach to target group
- **Health Checks**: ELB health checks enabled

##### 3. Scaling Policies
```yaml
Scale Out Policy:
  Metric: CPU Utilization
  Threshold: > 75%
  Action: Add 2 instances
  Cooldown: 300 seconds

Scale In Policy:
  Metric: CPU Utilization
  Threshold: < 25%
  Action: Remove 1 instance
  Cooldown: 300 seconds
```

### ASG Best Practices

#### Launch Template vs Launch Configuration
- **Launch Template** (Recommended):
  - Versioning support
  - Multiple instance types
  - Spot instances support
  - Latest features
- **Launch Configuration** (Legacy):
  - Single instance type
  - No versioning
  - Limited features

#### Instance Distribution
- **Multiple AZs**: Distribute across availability zones
- **Instance Types**: Use multiple instance types
- **Spot Instances**: Mix spot and on-demand instances
- **Placement Groups**: Consider placement strategies

#### Monitoring and Alerting
- **CloudWatch Metrics**: Monitor ASG metrics
- **SNS Notifications**: Get notified of scaling events
- **CloudTrail Logs**: Track API calls and changes
- **Cost Monitoring**: Monitor scaling costs

## Practical Example: Complete Setup

### User Data Scripts

#### Amazon Linux Script
```bash
#!/bin/bash
sudo yum update -y

# Install Apache web server (httpd)
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd

# Create a simple HTML file to verify the web server is running, including dynamic hostname
echo "<html><h1>Welcome to Apache Web Server on Amazon Linux - $(hostname)!</h1></html>" > /var/www/html/index.html
```

#### Ubuntu Script
```bash
#!/bin/bash
sudo apt update -y
# Install Apache web server (apache2)
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
# Create a simple HTML file to verify the web server is running, including dynamic hostname
echo "<html><h1>Welcome to Apache Web Server on Ubuntu - $(hostname)!</h1></html>" | sudo tee /var/www/html/index.html > /dev/null
```

### Complete Walkthrough

EC2 > Instances > Launch Instance it will redirect to the launch wizard. Give a name like `mywebserver`, select ubuntu
free AMI, select t2.micro instance type, select old key, at security group selecting previously created security group
with ssh port 22 from anywhere, http port 80 from anywhere, https port 443 from anywhere, at advanced details paste the
following user data as script, also enable public IPv4 address,
```bash
#!/bin/bash
sudo apt update -y
# Install Apache web server (apache2)
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
# Create a simple HTML file to verify the web server is running, including dynamic hostname
echo "<html><h1>Welcome to Apache Web Server on Ubuntu - $(hostname)!</h1></html>" | sudo tee /var/www/html/index.html > /dev/null
```

type number of instances as 2 and click on launch instance. Then from EC2 > Instances, rename those as `mywebserver-1`
and `mywebserver-2`. Now if we visit those public IP addresses using HTTP we will see the message
"Welcome to Apache Web Server on Ubuntu - <hostname>!".

Now goes to EC2 > Load Balancers. Click on Create Load Balancer, select Application Load Balancer, choose application
load balancer, give a name like `mywebserver-lb`, select internet facing, IPv4, keeping default VPC, selecting all the
availability zones `ap-southeast-1a`, `ap-southeast-1b`, `ap-southeast-1c`, for better availability, clicking on create
new security group, give a name like `http__anywhere` at inbound rules HTTP port 80 from anywhere, click on create
security group. Now at create application load balancer, select `http__anywhere` security group.

At listener and routing, click on create target group, select `Instances`, give a name like `mywebserver-tg`, keep
protocol port HTTP and port 80, ip address type IPv4, keep default VPC, protocol version HTTP1, keeping health check
default settings, click on next. At register targets, select both `mywebserver-1` and `mywebserver-2`, click on
include as pending below, click on create target group.

Now at the Create Application Load Balancer, select the `mywebserver-tg` target group, click on create load balancer.
Now if we take the DNS name of the load balancer and visit it using HTTP, we will see the message we put inside those   
web servers, like "Welcome to Apache Web Server on Ubuntu - <hostname>!" for both instances.

If we stop one of our web servers, then we will see load balancer only redirecting traffic to the other running web
server means it checks the health of the instances and if one is unhealthy, it will not redirect traffic to that
instance.

Now go to EC2 > Instances, select `mywebserver-1`, click on Actions > Image and templates > Create image, give a name
like `mywebserver-image`, click on create image keeping all default.

Now EC2 > Auto Scaling Groups, click on Create Auto Scaling Group, give a name like `mywebserver-asg`, select the
creation a launch template, give name like `mywebserver-template`, select the image we created `mywebserver-image`,
instance type t2.micro, select the security group we created `http__anywhere`, click on create launch template.

Now at previous page, select the launch template `mywebserver-template`, select version 1 and click on next. Keeping
default VPC, select all the availability zones `ap-southeast-1a`, `ap-southeast-1b`, `ap-southeast-1c`, click on
next. Then select Attach to an existing load balancer, select the Choose from your load balancer target groups, select
`mywebserver-tg`, skip vpc lattice integration option, check elastic load balancing health checks, keep all things
default and click on next.

Now at Configure group size and scaling write Desired capacity as 2, minimum capacity as 1, maximum capacity as 3, can
add target tracking policies like CPU utilization, select the target value as 50%, or like Application Load Balancer
request count per target and select target group `mywebserver-tg`, select target value as 1000, for this example we will
skip this and select no scaling policies, click on next.

Skipping Notification and click next. Skipping Tags and click next. At Review, check all the details and click on
Create Auto Scaling Group.

Now if we goes to load balancer and see resource map there will be four instances, two of them are the we created
manually and two of them are created by the Auto Scaling Group. Now we can delete the two instances we created
manually. Also if we goes to auto scaling group and select `mywebserver-asg`, we can see the instance management
there will be two instances running. And if we visit the load balancer DNS name, we will see hostname get fixed not
changing, because the Auto Scaling Group used same image we created for the web server, so it will have the same
hostname.

Now we have two instance(after deleting the manually created instances) running in the Auto Scaling Group, if we terminate
one of them, then the Auto Scaling Group will automatically launch a new instance to maintain the desired capacity of 2.

### Advanced Configurations

#### Multi-Instance Type ASG
```json
{
  "MixedInstancesPolicy": {
    "LaunchTemplate": {
      "LaunchTemplateSpecification": {
        "LaunchTemplateName": "WebServer-Template",
        "Version": "1"
      },
      "Overrides": [
        {"InstanceType": "t3.micro"},
        {"InstanceType": "t3.small"},
        {"InstanceType": "t2.micro"}
      ]
    },
    "InstancesDistribution": {
      "OnDemandPercentage": 70,
      "SpotInstancePools": 3
    }
  }
}
```

#### Warm Pool Configuration
- **Pre-initialized Instances**: Keep instances ready
- **Faster Scaling**: Reduce launch time
- **Cost Effective**: Pay reduced rates for stopped instances
- **Use Cases**: Applications with long startup times

## Monitoring and Troubleshooting

### CloudWatch Metrics

#### Load Balancer Metrics
- **RequestCount**: Number of requests processed
- **TargetResponseTime**: Response time from targets
- **HTTPCode_Target_2XX**: Successful responses
- **HTTPCode_ELB_5XX**: Load balancer errors
- **HealthyHostCount**: Number of healthy targets
- **UnHealthyHostCount**: Number of unhealthy targets

#### Auto Scaling Metrics
- **GroupDesiredCapacity**: Target number of instances
- **GroupInServiceInstances**: Running instances
- **GroupMinSize/MaxSize**: Capacity limits
- **GroupTotalInstances**: Total instances (all states)

### Common Issues and Solutions

#### Load Balancer Issues
- **502 Bad Gateway**: Target application errors
- **503 Service Unavailable**: No healthy targets
- **504 Gateway Timeout**: Target response timeout
- **Connection Timeouts**: Security group or NACL issues

#### Auto Scaling Issues
- **Instances Not Launching**: Check launch template, quotas, availability
- **Health Check Failures**: Review health check configuration
- **Scaling Not Working**: Verify scaling policies and CloudWatch alarms
- **Cost Issues**: Monitor instance usage and scaling patterns

### Debugging Commands
```bash
# Check load balancer status
aws elbv2 describe-load-balancers

# Check target group health
aws elbv2 describe-target-health --target-group-arn arn:aws:elasticloadbalancing:region:account:targetgroup/name

# Check ASG activity
aws autoscaling describe-scaling-activities --auto-scaling-group-name mywebserver-asg

# Check ASG instances
aws autoscaling describe-auto-scaling-instances
```

## Cost Optimization

### Load Balancer Cost Optimization
- **Right-sizing**: Use appropriate load balancer type
- **Idle Resources**: Remove unused load balancers
- **Data Transfer**: Optimize cross-AZ traffic
- **Reserved Capacity**: Consider Savings Plans for consistent workloads

### Auto Scaling Cost Optimization
- **Spot Instances**: Use spot instances for cost savings
- **Scheduled Scaling**: Scale down during off-hours
- **Right-sizing**: Use appropriate instance types
- **Monitoring**: Track scaling patterns and optimize
- **Warm Pools**: Reduce launch time and costs

### Cost Monitoring
- **CloudWatch**: Monitor scaling metrics
- **Cost Explorer**: Analyze scaling costs
- **Budgets**: Set alerts for cost thresholds
- **Trusted Advisor**: Get cost optimization recommendations

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)