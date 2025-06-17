# Amazon DynamoDB
A fast and flexible NoSQL database service for any scale.

DynamoDB is a fully managed, key-value, and document database that delivers single-digit-millisecond performance at any
scale.

## What is NoSQL?
NoSQL is a type of database designed to store and manage data in flexible, **non-tabular formats**, making it ideal for
handling large volumes of unstructured data.

DynamoDB stores data as items in **tables**, with each item represented as a JSON-like **document** consisting of
**key-value** pairs.

## Free Tier
DynamoDB also offers a free tier that provides 25 GB of storage, 25 provisioned write capacity units (WCU), and 25 
provisioned read capacity units (RCU) per month for the first 12 months after you create your AWS account which is
enough to handle 200 M requests per month.

### Free Tier Offer Details
- **ALWAYS FREE**
- 25 GB of Storage per month
- 25 provisioned write capacity units per month
- 25 provisioned read capacity units per month

## Key Features

- **Serverless:** No need for server provisioning, software installation, maintenance, or patching.
- **Automatic Scaling:** Instantly scales up or down based on demand, with no manual adjustments needed.
- **Zero Downtime:** Provides continuous availability without maintenance windows.
- **On-Demand Pricing:** Pay only for the read/write requests used, ideal for fluctuating workloads.
- **Idle Cost Savings:** Scales down to zero during inactivity, so there's no cost when tables have no traffic.

## Use Cases
Its flexible data model and reliable performance make DynamoDB a great fit for **mobile, web, gaming, advertising 
technology, Internet of Things, and other applications**.

## DynamoDB Accelerator - DAX

- Fully Managed in-memory cache for DynamoDB
- DAX offers microsecond latency achieving up to a 10x performance over standard DynamoDB queries.
- High Availability and Scalability: can be deployed in multiple AZs
- DAX is only used for and is integrated with DynamoDB, while ElastiCache can be used for other databases

```
Amazon VPC

      EC2 <--------> DAX cluster <--------> DynamoDB
```

### DynamoDB Global Tables
DynamoDB Global Tables provide multi-region, multi-master replication, allowing you to have fully managed, serverless 
global databases. They automatically replicate your tables across multiple AWS regions, providing fast local read and 
write performance for globally distributed applications. Global Tables enable automatic failover and disaster recovery 
while maintaining eventual consistency across all regions.

## Partition Key
The partition key is used to distribute data across multiple partitions for scalability and performance.

---

## Hands-on Tutorial: Setting up DynamoDB with EC2 and Docker

### Step 1: Create DynamoDB Table
1. Go to DynamoDB dashboard and click on "Create table"
2. At "Table details" section, enter partition key name "id" and type "String". Skip Sort key as it is optional
3. At "Table settings" section, leave all as default, and we will see "Default settings" is selected previously
4. Click on "Create table" button at the bottom of the page to create the table
5. At DynamoDB > Tables section, we can see the table "Contacts" is created successfully

### Step 2: Add Sample Data
1. Click on the Contacts table to see the details
2. Click on "Explore table items" to view table items (empty initially)
3. Click on "Create item" button to add a new item:
    - Give "01" as the value for "id" (partition key)
    - Click on "Add new attribute" button
    - Enter "username" as the attribute name and "Raju" as the value
    - Click on "Create item" button
4. Create another item:
    - Give "02" as the value for "id" (partition key)
    - Add "username" attribute with value "Sham"
    - Add "age" attribute (number type) with value 30

### Step 3: Configure Capacity Settings
You can change the "Capacity mode" to "Provisioned" and set:
- "Read capacity units" to 5
- "Write capacity units" to 5

This allows reading and writing 5 items per second. Access this from: DynamoDB > Tables > Settings tab > Action > Edit Capacity.

### Step 4: Setup EC2 Instance
Create an EC2 instance with:
- Public IP
- Security group with SSH and HTTP access
- Connect to the instance using SSH

#### For Amazon Linux:
```shell
sudo yum install -y docker
sudo service docker start
sudo usermod -aG docker ec2-user
sudo docker pull philippaul/node-dynamodb-demo
```

#### For Ubuntu:
```shell
# 1. Update package list and install Docker
sudo apt update
sudo apt install -y docker.io

# 2. Start and enable the Docker service (so it starts on reboot)
sudo systemctl start docker
sudo systemctl enable docker

# 3. Add the 'ubuntu' user to the 'docker' group
#    Using $USER is a more portable way to get the current user's name.
sudo usermod -aG docker $USER
# IMPORTANT: You must log out and log back in for this change to take effect.
# Alternatively, you can run `newgrp docker` to apply the new group in your current shell.

# 4. Pull the Docker image
#    After logging back in, you no longer need 'sudo' for Docker commands.
docker pull philippaul/node-dynamodb-demo

ubuntu@ip-172-31-25-229:~$ docker ps 
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
ubuntu@ip-172-31-25-229:~$ docker images
REPOSITORY                      TAG       IMAGE ID       CREATED        SIZE
philippaul/node-dynamodb-demo   latest    1b7972c90460   7 months ago   1.24GB
ubuntu@ip-172-31-25-229:~$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
     Active: active (running) since Tue 2025-06-17 18:47:35 UTC; 11min ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 2800 (dockerd)
      Tasks: 11
     Memory: 378.6M (peak: 426.9M)
        CPU: 23.608s
     CGroup: /system.slice/docker.service
             └─2800 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Jun 17 18:47:34 ip-172-31-25-229 systemd[1]: Starting docker.service - Docker Application Container Engine...
Jun 17 18:47:34 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:34.477823300Z" level=info msg="Starting up"
Jun 17 18:47:34 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:34.479253019Z" level=info msg="OTEL tracing is not configured, using no-op tracer provider"
Jun 17 18:47:34 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:34.479339972Z" level=info msg="detected 127.0.0.53 nameserver, assuming systemd-resolved, so using resolv.conf: /run/systemd/resolve/resolv.conf"
Jun 17 18:47:34 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:34.661634697Z" level=info msg="Loading containers: start."
Jun 17 18:47:34 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:34.990083049Z" level=info msg="Loading containers: done."
Jun 17 18:47:35 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:35.017544418Z" level=info msg="Docker daemon" commit="27.5.1-0ubuntu3~24.04.2" containerd-snapshotter=false storage-driver=overlay2 version=27.5.1
Jun 17 18:47:35 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:35.017889742Z" level=info msg="Daemon has completed initialization"
Jun 17 18:47:35 ip-172-31-25-229 dockerd[2800]: time="2025-06-17T18:47:35.071215757Z" level=info msg="API listen on /run/docker.sock"
Jun 17 18:47:35 ip-172-31-25-229 systemd[1]: Started docker.service - Docker Application Container Engine.
ubuntu@ip-172-31-25-229:~$ 
```

### Step 5: Create IAM Access Keys
1. Go to IAM > Users
2. Select already created user
3. Select "Security credentials" tab
4. Click on "Create access key"
5. Select "CLI" as the access key type
6. Check "I understand that..." and click "Next"
7. At "Description tag value" section, enter "dynamodb"
8. Click "Create access key" button
9. Copy the access key and secret key to a secure place

### Step 6: Run Docker Container
Run the docker container with the access key and secret key as environment variables:

```shell
sudo docker run --rm -d -p 80:3000 --name node-dynamo-app \
  -e AWS_REGION=ap-southeast-1 \
  -e AWS_ACCESS_KEY_ID=<your-access-key> \
  -e AWS_SECRET_ACCESS_KEY=<your-secret-key> \
  philippaul/node-dynamodb-demo
```

### Step 7: Verify the Setup
```shell
# Check running containers
docker ps

# Check container logs
sudo docker logs -f node-dynamo-app
```

Expected output:
```
ubuntu@ip-172-31-25-229:~$ sudo docker run --rm -d -p 80:3000 --name node-dynamo-app -e AWS_REGION=ap-southeast-1 -e AWS_ACCESS_KEY_ID=<key> -e AWS_SECRET_ACCESS_KEY=<key> philippaul/no
de-dynamodb-demo
5c9280e0816f22c2fcc6a332080dbb52da04fe7090e55b8536b23808023a1d52
ubuntu@ip-172-31-25-229:~$ docker ps
CONTAINER ID   IMAGE                           COMMAND                  CREATED          STATUS          PORTS                                     NAMES
5c9280e0816f   philippaul/node-dynamodb-demo   "docker-entrypoint.s…"   11 seconds ago   Up 10 seconds   0.0.0.0:80->3000/tcp, [::]:80->3000/tcp   node-dynamo-app
ubuntu@ip-172-31-25-229:~$ sudo docker logs -f  node-dynamo-app
Server is running on http://localhost:3000
(node:1) NOTE: The AWS SDK for JavaScript (v2) is in maintenance mode.
 SDK releases are limited to address critical bug fixes and security issues only.

Please migrate your code to use AWS SDK for JavaScript (v3).
For more information, check the blog post at https://a.co/cUPnyil
(Use `node --trace-warnings ...` to show where the warning was created)
Table "Contacts" already exists.
^Ccontext canceled
ubuntu@ip-172-31-25-229:~$ 
```

### Step 8: Test the Application
1. Visit `http://<public-ip>` in the browser
2. You should see the app running and be able to view the old records created with DynamoDB GUI
3. Add new items from the website
4. Go to DynamoDB > Explore items and select the Contacts table to see the new items added

---

## Additional Notes
- The application uses AWS SDK for JavaScript v2, which is in maintenance mode. Consider migrating to v3 for new projects.
- Ensure your security groups allow HTTP traffic on port 80 for the web application to be accessible.
- Always secure your AWS access keys and never commit them to version control.
- Consider using IAM roles for EC2 instances instead of access keys for better security in production environments.


# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)