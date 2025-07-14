# AWS Scaling and Load Balancing

## Scaling Concepts

### Vertical Scalability (Scaling Up)

* Vertical Scalability means adding more power (CPU, RAM) to your existing server.
* Ex: `t2.micro` to `m5.large`
* More common for non-distributed systems, such as databases(RDS), ElastiCahe or applications that require more 
* resources to handle increased load.


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

### Horizontal Scalability (Scaling Out or elastic)

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

# Elastic Load Balancer (ELB)

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

### Use of load balancer
* Spread load across multiple downstream instances
* Expose a single point of access (DNS) to your application
* Seamlessly handle failures of downstream instances
* Do regular health checks to your instances
* Provide SSL termination (HTTPS) for your websites
* Enforce stickiness with cookies
* High availability across zones
* Separate public traffic from private traffic

### Why use an Elastic Load Balancer?

*   An Elastic Load Balancer is a **managed load balancer**
*   AWS guarantees that it will be working
*   AWS takes care of upgrades, maintenance, high availability
*   AWS provides only a few configuration knobs
*   It costs less to setup your own load balancer but it will be a lot more effort on your end
*   It is integrated with many AWS offerings / services
*   EC2, EC2 Auto Scaling Groups, Amazon ECS
*   AWS Certificate Manager (ACM), CloudWatch
*   Route 53, AWS WAF, AWS Global Accelerator

### Sticky Sessions (Session Affinity)

*   It is possible to implement stickiness so that the same client is always redirected to the same instance behind a 
    load balancer
*   This works for Classic Load Balancer, Application Load Balancer, and Network Load Balancer
*   The “cookie” used for stickiness has an expiration date you control
*   Use case: make sure the user doesn’t lose his session data
*   Enabling stickiness may bring imbalance to the load over the backend EC2 instances

To turn on sticky sessions, from EC2 > Target groups, select the target group, click on "Actions" > select "Edit target
group attributes", then "Edit target group attributes" page will be opened, then from "Target selection configuration"
check "Turn on stickiness". If this target group is associated with an Application Load Balancer, then you can see 
option "Stickiness type". Select load balancer generated cookie.

* **Load balancer generated cookie**: This is the default option. The load balancer generates a cookie to track the 
  session. With inputbox to enter "Stickiness duration", which is the duration for which the cookie is valid. "Unit of
  time" is seconds, minutes, hours, or days. Those cookies are used to route the request to the same target and managed
  by AWS itself.
* **Application generated cookie**: This option allows you to specify a custom cookie name that your application 
  generates. The load balancer will use this cookie to route the request to the same target. You can specify the cookie 
  name in the "Cookie name" inputbox. The load balancer will not generate any cookies in this case, it will only use the 
  cookies generated by your application. For this also there will be input box to enter "Stickiness duration", and "Unit 
  of time".

Now after visiting using load balancer DNS name from inspect > Application > Cookies, we can see the cookie `AWSALB`
or `AWSALBAPP` is created, which is used for sticky sessions. If we stop one of the instances, then the load balancer
will not redirect traffic to that instance, it will redirect traffic to the other healthy instance. If we refresh the
page, we will see the same instance is serving the request, which means the sticky session is working.

#### Sticky Sessions – Cookie Names

*   **Application-based Cookies**
    *   **Custom cookie**
        *   Generated by the target
        *   Can include any custom attributes required by the application
        *   Cookie name must be specified individually for each target group
        *   Don't use AWSALB, AWSALBAPP, or AWSALBTG (reserved for use by the ELB)
    *   **Application cookie**
        *   Generated by the load balancer
        *   Cookie name is AWSALBAPP
*   **Duration-based Cookies**
    *   Cookie generated by the load balancer
    *   Cookie name is AWSALB for ALB, AWSELB for CLB

### Health Checks

*   Health Checks are crucial for Load Balancers
*   They enable the load balancer to know if instances it forwards traffic to are available to reply to requests
*   The health check is done on a port and a route (/health is common)

An **Elastic Load Balancer** sends **Health Checks** to an **EC2 Instance**.

*   **Protocol:** HTTP
*   **Port:** 4567
*   **Endpoint:** /health

### Types of load balancer on AWS

*   AWS has **4 kinds of managed Load Balancers**
*   **Classic Load Balancer** (v1 - old generation) – 2009 – CLB **SHOULD NOT USE AND AWS WILL REMOVE THIS SOON**
*   HTTP, HTTPS, TCP, SSL (secure TCP)
*   **Application Load Balancer** (v2 - new generation) – 2016 – ALB
*   HTTP, HTTPS, WebSocket
*   **Network Load Balancer** (v2 - new generation) – 2017 – NLB
*   TCP, TLS (secure TCP), UDP
*   **Gateway Load Balancer** – 2020 – GWLB
*   Operates at layer 3 (Network layer) – IP Protocol
*   Overall, it is recommended to use the newer generation load balancers as they provide more features
*   Some load balancers can be setup as **internal** (private) or **external** (public) ELBs

### Load Balancer Security Groups

This setup ensures that users can access the load balancer, but only the load balancer can send traffic to the EC2 instances, enhancing security.

**Traffic Flow:**
**Users** -> (HTTPS/HTTP from anywhere) -> **Load Balancer** -> (HTTP Restricted to Load Balancer) -> **EC2**



### Load Balancer Security Group:

| Type   | Protocol  | Port Range  | Source       | Description               |
|:-------|:----------|:------------|:-------------|:--------------------------|
| HTTP   | TCP       | 80          | 0.0.0.0/0    | Allow HTTP from anywhere  |
| HTTPS  | TCP       | 443         | 0.0.0.0/0    | Allow HTTPS from anywhere |

### Application Security Group: Allow traffic only from Load Balancer
At EC2 only allow traffic from the Load Balancer security group to the EC2 instances using security group rules.

| Type  | Protocol  | Port Range  | Source                           | Description                                 |
|:------|:----------|:------------|:---------------------------------|:--------------------------------------------|
| HTTP  | TCP       | 80          | sg-054b5ff5ea02f2b6e (load-b...) | Allow Traffic only from Load Balancer SG    |


## Target Groups
Target groups are used to route requests to one or more registered targets, such as EC2 instances, containers, or IP
addresses. They define how requests are routed and monitored.

Target group can be
* EC2 instances(can be managed by Auto Scaling Group) - HTTP
* ECS task(manage by ECS itself) - HTTP
* Lambda functions - HTTP request is transferred into a JSON event
* IP address - must be private IPs
  * Can be used to route traffic to resources outside of AWS, such as on-premises servers or other cloud providers.
  * Can be used to route traffic to resources within the same VPC, such as RDS databases or ElastiCache clusters.
  * Can be used to route traffic to resources in different VPCs, such as VPC peering or Transit Gateway connections.
* Application Load Balancer - HTTP request is transferred into a JSON event  
* Health checks are performed on the target group to ensure that the targets are healthy and able to handle requests.

Health checks are at the target group level.

## Cross-Zone Load Balancing
Cross-Zone Load Balancing allows the load balancer to distribute traffic evenly across all registered targets in all
availability zones, regardless of the number of targets in each zone. This ensures that traffic is balanced across all
availability zones, improving fault tolerance and resource utilization.

Like think we have two load balancers, one has two instances in one AZ and another load balancer has eight instances in
another AZ. If cross-zone load balancing is enabled, the load balancer will distribute traffic evenly across both
load balancers, regardless of the number of instances in each AZ means if 100 requests come, 50 requests will go to
the first load balancer and 50 requests will go to the second load balancer. But as first one has only two instances, 
it will handle 20 requests(10 on each instance) other (50-20)= 30 requests will hand over to the second load balancer,
so second load balancer will handle 80 requests(50 from its own load balancer and 30 from the first load balancer). So
the traffic is evenly distributed across both load balancers and instances, which leads to better resource utilization
and fault tolerance.

If cross-zone load balancing is not enabled, then first load balancer will handle 50 requests, but as it has only two
instances, it will handle 25 requests per instance, and the second load balancer will handle 50 requests (50/8=6.25) 
per instance, which will lead to uneven traffic distribution. It can lead to overloading of the instances in the first
load balancer and underutilization of the instances in the second load balancer.

* **Enabled by default for ALB**: Cross-Zone load balancing is enabled by default(can be disabled at the target group
  level).
* **Disabled by default for NLB, GLB**: Cross-Zone load balancing is disabled by default(can be enabled at the
  target group level) for network load balancer and gateway load balancer.
* **Benefits**:
  - Improved fault tolerance: If one AZ goes down, traffic is still distributed across the remaining AZs.
  - Better resource utilization: All targets in all AZs receive traffic, preventing overloading of targets in a single AZ.
* **Configuration**: Can be enabled or disabled in the load balancer settings.
* **Cost**: 
  * For Application Load Balancer, cross-zone load balancing is enabled by default and incurs no additional cost.
  * For Network Load Balancer, cross-zone load balancing is disabled by default and incurs additional cost when enabled.

If we click on NLB or GLB from EC2 > Load Balancers, then from "Attributes" tab, we will see "Cross-zone load balancing"
is "Off". Click on "Edit" button on right, we will redirect to "Edit load balancer attributes" page, at the bottom of
that page we will see "Target selection configurateion", where we can see "Cross-zone load balancing" is toggled off.
We can toggle the button to turn it on, if we turn this on for NLB or GLB, we will see a warning message saying "Regional
data transfer charges may apply when cross-zone load balancing is turned on".

But for ALB, if we click on ALB from EC2 > Load Balancers, then from "Attributes" tab, we will see "Cross-zone load 
balancing" is "On". We can not turn it off, as it is enabled by default for ALB. And it will not incur any additional
cost. If we want to turn it off, we need to go our target group associated with the ALB, click on "Attributes" tab,
then click on "Edit" button, then we will redirect to "Edit target group attributes" page, where we can see "Cross-zone
load balancing" under "Target selection configuration". There by default will be selected "Inherit settings from load
balancer", we can select "Off" to turn off cross-zone load balancing for that target group and can select "On" to turn
it on. But it is recommended to keep it "Inherit settings from load balancer".

## Load Balancer - SSL Certificates

*   The load balancer uses an X.509 certificate (SSL/TLS server certificate)
*   You can manage certificates using ACM (AWS Certificate Manager)
*   You can create upload your own certificates alternatively
*   HTTPS listener:
    *   You must specify a default certificate
    *   You can add an optional list of certs to support multiple domains
    *   Clients can use SNI (Server Name Indication) to specify the hostname they reach
    *   Ability to specify a security policy to support older versions of SSL /TLS (legacy clients)

Users connect to the Load Balancer via an encrypted HTTPS connection. The Load Balancer decrypts the traffic and communicates with the backend EC2 Instance over an unencrypted HTTP connection inside a private VPC.

### SSL – Server Name Indication (SNI)

*   SNI solves the problem of loading multiple SSL certificates onto one web server (to serve multiple websites)
*   It’s a “newer” protocol, and requires the client to indicate the hostname of the target server in the initial SSL handshake
*   The server will then find the correct certificate, or return the default one

**Note:**
*   Only works for ALB & NLB (newer generation), CloudFront
*   Does not work for CLB (older gen)

A client requesting `www.mycorp.com`. The request goes to an ALB which holds multiple SSL certificates. The ALB uses SNI
to identify the correct SSL certificate for `www.mycorp.com`, presents it to the client, and then forwards the request 
to the appropriate target group.

### Elastic Load Balancers – SSL Certificates

*   **Classic Load Balancer (v1)**
    *   Support only one SSL certificate
    *   Must use multiple CLB for multiple hostname with multiple SSL certificates

*   **Application Load Balancer (v2)**
    *   Supports multiple listeners with multiple SSL certificates
    *   Uses Server Name Indication (SNI) to make it work

*   **Network Load Balancer (v2)**
    *   Supports multiple listeners with multiple SSL certificates
    *   Uses Server Name Indication (SNI) to make it work

#### Adding load balancer SSL certificate
Select the load balancer from EC2 > Load Balancers, then from "Listeners and rules" tab, click on "Add listener" button, then select "HTTPS" from the protocol dropdown, then select the port as 443, and fillup routing action. At "Security 
listener settings" > "Default SSL/TLS certificate" section we have three options:
* **From ACM**: Select a certificate from AWS Certificate Manager (ACM) that you have already created or imported.
* **From IAM**: Select a certificate from AWS Identity and Access Management (IAM) that you have already created or 
  imported.
* **Import certificate**: Upload a new certificate by uploading certificate private key, certificate body, and 
  certificate chain. This is useful if you have a custom SSL certificate that is not managed by ACM or IAM.

Also, we can change the security policy at same section "Security listener settings".

## Connection Draining / Deregistration Delay
Connection Draining is a feature that allows the load balancer to stop sending new requests to an instance that is
being deregistered or terminated, while still allowing existing connections to complete. This ensures that users do not
experience abrupt disconnections or errors when an instance is being removed from the load balancer.

*   **Feature naming**
    *   Connection Draining – for CLB
    *   Deregistration Delay – for ALB & NLB
*   Time to complete “in-flight requests” while the instance is de-registering or unhealthy
*   Stops sending new requests to the EC2 instance which is de-registering
*   Between 1 to 3600 seconds (default: 300 seconds)
*   Can be disabled (set value to 0)
*   Set to a low value if your requests are short

## AWS Load Balancer Types

AWS offers different types of elastic load balancers depending on your needs.

* **Application Load Balancer (ALB)** is perfect for web applications, handling complex HTTP and HTTPS requests (Layer
  7).
* **Network Load Balancer (NLB)** is designed for high-performance and low latency, perfect for TCP/UDP traffic (ex:
  gaming, financial apps) (Layer 4).
* **Gateway Load Balancer (GWLB)** helps deploy, scale, and manage third-party virtual appliances, such as firewalls and
  monitoring solutions.

## Application Load Balancer (ALB)
#### Features
- **Layer 7 (Application Layer)**: HTTP/HTTPS traffic
- **Content-Based Routing**: Route based on URL, headers, query strings(like example.com/users?id=123&order=false)
- **Host-Based Routing**: Route based on hostname(one.example.com, two.example.com)
- **Path-Based Routing**: Route based on URL path(example.com/api/*, example.com/images/*)
- **WebSocket Support**: Real-time communication
- **HTTP/2 Support**: Modern HTTP protocol
- **SSL/TLS Termination**: Certificate management
- Load balancing to multiple HTTP application across machines(target groups)
- Load balancing to multiple application on the same machine(ex: containers, ECS)
- Supports redirects (for example, HTTP to HTTPS) and fixed responses (for example, 404 Not Found)
- ALB are great fit for microservices and container-based applications (ex docker and AWS ECS)
- Has a port mapping feature to redirect traffic to a dynamic port in ECS.
- The application servers don't see the IP of the client, they see the IP of the load balancer.
  - The true IP of the client is inserted in the header `X-Forwarded-For`, which can be used by the application server 
    to log the true client IP.
  - We can also get port `X-Forwarded-Port` and protocol `X-Forwarded-Proto` in the header.


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

## Network Load Balancer (NLB)
If our application can have only one, two or three IPs then Network Load Balancer is the best choice. It is designed for extreme performance and low latency, handling millions of requests per second.

**Not include in AWS Free Tier.**

#### Features
- **Layer 4 (Transport Layer)**: Forward TCP/UDP/TLS traffic to targets
- **High Performance**: Millions of requests per second
- **Low Latency**: Ultra-low latency routing
- **Static IP**: Fixed one static IP addresses per AZ, and support assigning Elastic IPs(helpful for whitelisting 
  specific IPs)
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

## Gateway Load Balancer (GWLB)
#### Features
- **Layer 3 (Network Layer)**: IP packet level
- **Transparent Proxy**: Invisible to source and destination
- **Third-Party Integration**: Virtual appliances deployment
- **High Availability**: Distribute across multiple appliances
- **Centralized Security**: Single point for security controls
- Deploy, scale and manage third-party virtual appliances, such as firewalls, intrusion detection and prevention 
  systems, and deep packet inspection systems, payload mapulation, and more.
- Combine the features of 
  - Transparent network gateway - single entry/exit point for all traffic
  - Load balancer - distributes traffic across multiple appliances
- Uses the GENEVE protocol to encapsulate traffic between the load balancer and the appliances at port 6081.  

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




# Auto Scaling Group (ASG)

AWS ASG (Auto Scaling Group) is a service that automatically adds or removes EC2 instances based on demand to ensure
your application is always available.

It helps scale up when more capacity is needed and scale down during low usage to save costs, keeping the right number
of servers running at all times. It's managed by AWS.

An Auto Scaling Group is a component of the EC2 service. **ASG is free to use, but you pay for the EC2 instances
launched by the ASG.**

### Auto Scaling Concepts
- **Desired Capacity**: Target number of instances
- **Minimum Capacity**: Minimum instances to maintain
- **Maximum Capacity**: Maximum instances allowed
- **Health Checks**: Monitor instance health
- **Scaling Policies**: Rules for scaling actions
- **Launch Configuration/Template**: Instance configuration blueprint

### Functions

* **Automatic Scaling:** 
 * Scale out (add instances) during high demand and scale in (remove instances) during low demand.
 * Scale down/in (remove instances) when demand decreases to save costs.
 * Ensure we have a minimum number of instances and maximum number of instances to handle traffic.
 * Autometically register new instances with the load balancer when they are launched. 
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
Launch templates are used to define the configuration for instances in the ASG, including 
* AMI + Instance type
* EBS volumes
* Security groups
* EC2 User data scripts
* SSH key pair
* IAM roles for the instances
* Network + subnet configuration
* Load balancer settings

and more. Launch templates are more flexible and powerful than launch configurations, allowing you to create multiple versions of the same template.

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
EC2 > Auto Scaling Groups, click on a specific ASG, then click on "Automatic scaling" tab,  there are 

* Dynamic scaling policies
  * Policy type has those three options:
    - Target tracking scaling
    - Step scaling
    - Simple scaling
  * Scaling policy name -> Type the name of the scaling policy
  * Metric type -> Select the metric type from the dropdown,
    * Avarage CPU utilization
    * Avarage network in (bytes)
    * Avarage network out (bytes)
    * Application Load Balancer request count per target 
    * Custom CloudWatch metric
  * and other options like target value, scaling adjustment, cooldown period, based on the selected metric type.
* Predictive scaling policies
  * Scaling policy name -> Type the name of the scaling policy
  * Turn on scaling -> section
    * Scale based on forecast -> toggle button
      * If switched off, predictive scaling will only forecast capacity and not take any scaling action. Only one predictive scaling policy can have scaling turned on at a given time.
  * Metrics and target utilization
    * CPU utilization
    * Network in (bytes)
    * Network out (bytes)
    * Application Load Balancer request count per target
    * Custom CloudWatch metric
    * and other options based on the selected metric type.
  * Additional scaling settings
    * Pre-launch instances -> enter the number of instances to launch before the scaling action occurs. This helps to 
      ensure that the new instances are ready to handle traffic when the scaling action occurs.
* Scheduled scaling policies
  * Scaling policy name -> Type the name of the scaling policy
  * Desired capacity
  * Min
  * Max
  * Recurrence -> Enter the cron expression for the scheduled scaling action. For example, once, every 5 min or 1 hour
    or day, week.
  * Time zone -> Select the time zone for the scheduled scaling action. By default, it is set to UTC.
  * Specific start time -> Enter the start time for the scheduled scaling action. This is optional and can be used to specify a 
    specific date and time for the scaling action to start.  


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




# Practical Example: Complete Setup

## Example 1
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

## Example 2
Will lunch two EC2 instance(from EC2 > Instances) with AMI Amazon Linux, select key, at network select an security group
with SSH port 22, and HTTP port 80 from anywhere, with public ip, at advanced details paste the following at "User data" 
section.
```bash
#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```
Now if we visit those two EC2 instance public IP addresses using HTTP, we will see the message "Hello World from
<hostname>!" for both instances.

From EC2 > Load Balancers, click on "Create Load Balancer", select "Application Load Balancer", give a name like
`mywebserver-lb`

At "Scheme" we have two option > select "Internet-facing"
* Internet Facing
  * Serves internet-facing traffic.
  * Has public IP addresses.
  * DNS name resolves to public IPs.
  * Requires a public subnet.
* Internal
  * Serves internal traffic.
  * Has private IP addresses.
  * DNS name resolves to private IPs.
  * Compatible with the IPv4 and Dualstack IP address types.

At "Load balancer IP address type" select "IPv4"
* IPv4
  * Uses IPv4 addresses.
  * Compatible with most clients.
  * Default option for ALB and NLB.
* Dualstack
  * Includes IPv4 and IPv6 addresses.
* Dualstack without public IPv4
  * Includes a public IPv6 address, and private IPv4 and IPv6 addresses. Compatible with internet-facing load balancers
    only.
  * **This will not show if select Internal**. And will show this message if select Internal "Your selection has been 
    reset to IPv4. The previously selected Dualstack without public IPv4 IP address type is not available for Internal
    load balancers."

At "Network mapping" at vpc keep default selected one, and select all AZs at mapping and keep default subnets for those
az.

And for security selected mine already created sg with HTTP at port 80 from anywhere. And remove the default SG.

At Listeners and routing, for port 80 we need to create a target group for routing request. So click on "Create target
group" text there. It wil redirect us to EC2 > Target Groups > Create target group section. From that page Basic 
configuration > Choose a target type > select instances
* Instances
  * Supports load balancing to instances within a specific VPC.
  * Facilitates the use of Amazon EC2 Auto Scaling  to manage and scale your EC2 capacity.
* IP addresses
  * Supports load balancing to VPC and on-premises resources.
  * Facilitates routing to multiple IP addresses and network interfaces on the same instance.
  * Offers flexibility with microservice based architectures, simplifying inter-application communication.
  * Supports IPv6 targets, enabling end-to-end IPv6 communication, and IPv4-to-IPv6 NAT.
* Lambda function
  * Facilitates routing to a single Lambda function.
  * Accessible to Application Load Balancers only.
* Application Load Balancer
  * Offers the flexibility for a Network Load Balancer to accept and route TCP requests within a specific VPC.
  * Facilitates using static IP addresses and PrivateLink with an Application Load Balancer.

At "Target group name" write "demo-tg-alb", protocol keep "HTTP", Port 80, IP address type keep IPv4, VPC keep
autoselected one and protocol version keep HTTP1.

At "Health checks" keep health check protocol as HTTP, Health check path as default '/'.

Now click on "Next" button. Now at second step select our previously created EC2 instances keep 80 for "Ports for 
selected instances" and click on "Include as pending below" then click on "Create target group" button.


Now go back to our "Create Application Load Balancer" page and at "Network and routing" default action section select 
our newly create target group "demo-tg-alb" and click on "Create load balancer" button.

Now after ALB's status goes Active from Provisioning we can visit the DNS provided by load balancer and will see our 
site, and it will change its hostname as it alternatively sending traffic at those two EC2. If we stop any one EC2 then
will see only one hostname after 30 sec.

But till now have a security problem if we paste public ip's of our EC2 in browser then we will be able to see our 
application, but we should stop that. So we will create a security group only for load balancer and at EC2's sg will 
edit HTTP, port 80 inbound role will select source type custom and at source will select the security group of load
balancer's so only from load balancer our EC2 instance will be available for HTTP at port 80.

At the load balancer we can add custom rules for inbound like for certain path or hostname, like if we want to
redirect traffic to a different target group for a specific path or hostname, or send fixed response for a specific path.

EC2 > Load Balancers > select our 'mywebserver-lb' > "Listeners and rules" tab > click on "HTTP:80" > from Rules tab
click on "Add rule". Click on "Add condition" form "Conditions" card and select path and enter `/error` as path.
* **Host header**: Route based on hostname (e.g., example.com)
* **Path**: Route based on URL path (e.g., /api/*)
* **Query string**: Route based on query parameters (e.g., ?id=123)
* **HTTP request method**: Route based on HTTP method (e.g., GET, POST)
* **HTTP header**: Route based on specific HTTP headers (e.g., User-Agent)
* **Source IP**: 

Now from Actions card select > Return fixed response
* **Forward to target group**: Route to a specific target group
* **Redirect to URL**: Redirect to a different URL
* **Return fixed response**: Send a static response (e.g., 404 Not Found)

And response code 404, response body as "Not Found", content type as "text/plain" and click on "Next" button. And give
priority as 1(lower is most powerful), and click on "Next" button. Now will show us preview so click on "Create" button.
Now if we visit the load balancer DNS with path `/error`, we will see "Not Found" message, but if we visit the

## Example 3: Network Load Balancer (NLB)
Will lunch two EC2 instance(from EC2 > Instances) with AMI Amazon Linux, select key, at network select an security group
with SSH port 22, and HTTP port 80 from anywhere, with public ip, at advanced details paste the following at "User data" 
section.
```bash
#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```
Now if we visit those two EC2 instance public IP addresses using HTTP, we will see the message "Hello World from
<hostname>!" for both instances.

From EC2 > Load Balancers, click on "Create Load Balancer", select "Network Load Balancer", give a name like
`mywebserver-nlb`. At Scheme select "Internet-facing" and at "Load balancer IP address type" select "IPv4".
Dualstack without public IPv4 is not available for internal load balancers, so it will not show that option here.

At "Network mapping" at vpc keep default selected one, and select all AZs at mapping and keep default subnets for those
az.

At "Security groups" select the security group we used in EC2 instances creation. 

At "Listeners and routing", for port 80 we need to create a target group for routing request. Click on "Create target
group" text there and create a target group like we did in Application Load Balancer, but this time select "Instances"
as target type, give a name like `demo-tg-nlb`, keep protocol as "TCP", port 80, IP address type IPv4, VPC keep
autoselected one and protocol version TCP. At "Health checks" keep health check protocol as TCP, Health check path as 
default '/'.

Select protocol as TCP, and all available options are
* TCP
* TLS
* UDP
* TCP_UDP
and select our newly created target group `demo-tg-nlb` and click on "Create load balancer" button.

After NLB's status goes Active from Provisioning we can visit the DNS provided by load balancer and will see our site,
and it will change its hostname as it alternatively sending traffic at those two EC2. If we stop any one EC2 then
will see only one hostname after 30 sec.

## Example 4: Auto Scaling Group with ALB
From EC2 > Auto Scaling > Auto Scaling Groups, click on "Create Auto Scaling Group", give a name like `mywebserver-asg`, 
click on "Create a lunch templatate", it will open a new page "Create launch template". Give a name like 
`MyLaunchTemplate`, from AMI select Amazon Linux, select t2.micro as instance type, select the key pair, subnet as 
"Don't include in launch template", select security group with ssh port 22, and http port 80 from anywhere, at
"Advanced details" paste the following at "User data" section. 
```bash
#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```
Then click on "Create launch template" button. Now we will see "View launch template" button, click on that it will 
redirect us to the list of templates. 

Now from previous page click on refresh button and select my newly created `MyLaunchTemplate`, and click on "Next" 
button. On "Instance type requirements" we can click on "Override launch template" button to override vCPUs, memory and
more. From "Network" keep default VPC and select all the subnets. At "Availability Zone distribution" select Balanced best effort.
* Balanced best effort
  * If launches fail in one Availability Zone, Auto Scaling will attempt to launch in another healthy Availability Zone.
* Balanced only
  * If launches fail in one Availability Zone, Auto Scaling will continue to attempt to launch in the unhealthy Availability Zone to preserve balanced distribution.

And click on "Next" button. At "Integrate with other services" page, "Load balancing" section select "Attach to an 
existing load balancer", 
* No load balancer
  * Do not attach to a load balancer.
* Attach to an existing load balancer
  * Attach to an existing load balancer.
* Attach to a new load balancer
  * Create a new load balancer and attach to it.

then from "Attach to an existing load balancer" section select "Choose from your load balancer target groups" and at
existing load balancer target group groups select our previously created target group `tg1`,
* Choose from your load balancer target groups
  * Select an existing target group to attach to the Auto Scaling group.
* Create a new target group
  * Create a new target group to attach to the Auto Scaling group.

At "VPC Lattice integration" section select "No VPC Lattice integration",
* No VPC Lattice integration
  * Do not integrate with VPC Lattice.
* Integrate with VPC Lattice
  * Integrate with VPC Lattice to enable service discovery and traffic management.

Skip "Application Recovery Controller (ARC) zonal shift - new".  

At "Health checks" will see EC2 health checks is marked always enabled, and check "Turn on Elastic Load Balancing health 
checks", "Turn on VPC Lattice health checks" is disabled, and keep "Turn on Amazon EBS health checks" unchecked as 
default. Keep "Health check grace period" as 300 seconds, and click on "Next" button.


At "Configure group size and scaling - optional" page keep all default, like "Desired capacity" as 1(default), "Min 
desired capacity" as 1(default), "Max desired capacity" as 1(default), and "Automatic scaling" as "No scaling policies"
as default.
* No scaling policies
  * Do not create any scaling policies.
* Target tracking scaling policy
  * Create a target tracking scaling policy to scale based on a specific metric.
  * Have to enter
    * Scalling policy name -> enter a name for the scaling policy.
    * Metric type -> select a metric type from the dropdown.  
      * Average CPU utilization
      * Average network in (bytes)
      * Average network out (bytes)
      * Application Load Balancer request count per target
      * Custom CloudWatch metric

Keep "Instance maintenance policy" as "No policy" as deafault,
* No policy
  * For rebalancing events, new instances will launch before terminating others. For all other events, instances 
  terminate and launch at the same time.
* Launch before terminate
  * Launch new instances and wait for them to be ready before terminating others. This allows you to go above your 
  desired capacity by a given percentage and may temporarily increase costs.
    * Set healthy percentage -> Set the maximum percentage of your desired capacity that can be in service during instance replacement events.
      * Min -> 100 prefilled and disabled
      * Max -> enter a value between for capacity percentage, like 150, 200, 300, etc.
* Terminate and launch
  * Terminate and launch instances at the same time. This allows you to go below your desired capacity by a given 
  percentage and may temporarily reduce availability.
  * Set the minimum percentage of the desired capacity that must be healthy and ready for use for EC2 Auto Scaling to proceed with replacing instances.
    * Set healthy percentage
      * min
      * max -> 100 prefilled and disabled
* Custom behavior
  * Set custom values for the minimum and maximum amount of available capacity. This gives you greater flexibility in 
    setting how far below and over your desired capacity EC2 Auto Scaling goes when replacing instances.
  * Set the minimum and maximum percentage of your desired capacity that must be healthy and ready for use for EC2 Auto 
    Scaling to proceed with replacing instances.
    * Set healthy percentage
      * min
      * max -> 100 prefilled and disabled

At "Additional capacity setttings" keep default <br/>
Additional capacity setttings > Capacity Reservation preference: Select whether you want Auto Scaling to launch instances into an existing Capacity 
Reservation or Capacity Reservation resource group.
* Default
  * Auto Scaling uses the Capacity Reservation preference from your launch template.
* None
  * Instances will not be launched into a Capacity Reservation.
* Capacity Reservations only
  * Instances will only be launched into a Capacity Reservation. If capacity isn’t available, the instances fail to 
  launch.
* Capacity Reservations first
  *  Instances will attempt to launch into a Capacity Reservation first. If capacity isn't available, instances will run 
  in On-Demand capacity.

At "Additional settings" all are default as unchecked. <br/>
Additional settings > Instance scale-in protection: If protect from scale in is enabled, newly launched instances will 
be protected from scale in by default.
* Enable instance scale-in protection -> checkbox
Monitoring
* Enable group metrics collection within CloudWatch -> checkbox
* Default instance warmup: The amount of time that CloudWatch metrics for new instances do not contribute to the group's 
aggregated instance metrics, as their usage data is not reliable yet.
* Enable default instance warmup -> checkbox

Click on "Next" button. At "Add notifications - optional" page we will skip this, and click on "Next" button. At tags
will skip this and click on "Next" button. At "Review" page we will see all the details we selected, so click on "Create
Auto Scaling group" button.

Now from EC2 > Auto Scaling Groups, we will see our newly created Auto Scaling Group `mywebserver-asg`, and if we click 
on that and click on "Activity" tab, we will see the activity history of the Auto Scaling Group. First we will see it 
created a EC2 instance as we do not had any. And also at "Instance management" tab we will see the instance. Also will
see this instane into our target group also. Now click on ASG at <ASG-name> Capacity overview tab, click on "Edit" and 
give desired capacity as 2, and click on "Update" button. Now we will see one more instance is created by the Auto
Scaling Group to maintain the desired capacity of 2. And if we make desired capacity as 1, then one of the instance
will be terminated by the Auto Scaling Group to maintain the desired capacity of 1.

Now EC2 > Auto Scaling Groups, select our `mywebserver-asg`, click on "Automatic scaling" tab, and click on "Create 
Dynamic scaling policy" button. At "Create dynamic scaling policy" page, give a name like `mywebserver-asg-cpu`, 
policy type select "Target tracking scaling", and at "Metric type" select "Average CPU utilization", and at "Target
value" enter 40, and click on "Create policy" button. Now we will see the scaling policy is created. Now connect to
one instance(as we give 1 as desired capacity) and run the following command to stress the CPU `sudo yum install stress -y`
then `stress -c 4` and after some time it will goes cpu above 10% and saw a new instance is created by asg. After killing the stress command after some time it autometically terminates the newly created instance to maintain the desired capacity of 1. (for my case need to reboot those)Also in cloudwatch we can see two alarms are created, one for scale out and one for scale in. Those are created by the scaling policy we created. 

# Advanced Configurations

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
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)