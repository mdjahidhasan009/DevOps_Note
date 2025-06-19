# Amazon ECS (Elastic Container Service) Complete Guide

**⚠️ NOTE:** Amazon ECS (Elastic Container Service) does not come with a free tier. You will be charged for the 
resources you use.

## What is ECS?

Amazon ECS is a cloud-based container management service that allows you to run and manage Docker containers on a
cluster of virtual servers.

## Why ECS?

ECS automatically handles:
- **Creation** - Automated container deployment
- **Management** - Resource allocation and monitoring
- **Updating** - Rolling updates and health checks

## ECS Key Terms

| Term        | Description                                                             |
|-------------|-------------------------------------------------------------------------|
| **Cluster** | Group of Tasks and Services, hosts all the resources and infrastructure |
| **Service** | Handles Scalability and Load balancing of containers                    |
| **Task**    | Represents the running containers of your Application                   |

---

## Step-by-Step Implementation Guide

### Step 1: Create ECS Cluster

1. Navigate to **Amazon Elastic Container Service > Clusters**
2. Click on **"Create cluster"** button
3. **Cluster Configuration:**
    - **Cluster name:** `MyCluster`
4. **Infrastructure Section:**
    - Keep default checked **"AWS Fargate (serverless)"**
5. **Skip the following sections** (use defaults):
    - Monitoring
    - Encryption
    - Tags
6. Click **"Create"** button at the bottom of the page

✅ **Result:** Your cluster is now created.

### Step 2: Create Task Definition

1. Navigate to **Amazon Elastic Container Service > Task definitions**
2. Click on **"Create new Task Definition"** button

#### Task Definition Configuration
- **Task definition name:** `MyTask`

#### Infrastructure Requirements
- **Launch type:** AWS Fargate
- **Operating system/architecture:** Linux/X86_64
- **Task size:**
    - **vCPU:** 0.25
    - **Memory:** 0.5 GB
- **Skip these sections** (use defaults):
    - Task roles
    - Task placement
    - Fault injection

#### Container Configuration (Container - 1)
- **Name:** `nginxc`
- **Image URL:** `nginx:latest`
- **Port mappings:**
    - **Container port:** 80
    - **Protocol:** TCP

#### Skip the following sections (use defaults):
- Resource allocation limits - conditional
- Environment variables
- Logging
- Restart policy
- HealthCheck
- Startup dependency ordering
- Container timeouts
- Container network settings
- Docker configuration
- Resource limits (Ulimits)
- Docker labels
- Storage
- Monitoring
- Tags

3. Click **"Create"** button at the bottom of the page

✅ **Result:** Your task definition is now created.

### Step 3: Deploy Task via Service

> **Note:** You can deploy tasks using the "Deploy" button (Create service, Update service, Run task), but we'll deploy 
> from the cluster page.

1. Navigate to **Amazon Elastic Container Service > Clusters**
2. Click on **"MyCluster"** cluster
3. Go to **Services** tab
4. Click **"Create"** button

#### Service Details
- **Task definition family:** MyTask
- **Task definition revision:** 1
- **Service name:** MyTask-service-hbrz1axf (default)

#### Environment
- **Existing cluster:** MyCluster (default)
- **Compute configuration:** Launch Type
- **Launch type:** FARGATE (default)
- **Platform version:** LATEST (default)

#### Deployment Configuration
- **Service type:** REPLICA (default)
- **Number of tasks:** 2
- **Availability Zone rebalancing:** ✓ (default checked)
- **Health check grace period:** 0 (default)
- **Skip these sections** (use defaults):
    - Deployment options
    - Deployment failure detection

#### Networking
- **VPC:** Keep default value
- **Subnets:** Keep default value
- **Security Group:** Create a new security group
    - **Security group name:** `cluster-at-ecs`
    - **Inbound rules:** Add rule for HTTP, source from anywhere

#### Skip the following sections (use defaults):
- Service Connect
- Service discovery
- Load balancer
- VPC Lattice
- Service auto scaling
- Volume
- Tags

4. Click **"Create"** button at the bottom of the page

✅ **Result:** Your service is now created.

### Step 4: Verify Deployment

1. Navigate to **Amazon Elastic Container Service > Clusters**
2. Click on **"MyCluster"** cluster
3. Click on **Tasks** tab
4. You should see **two tasks running**
5. Click on one of the tasks to view task details
6. Click on **Network** tab
7. Copy the **public IP** and paste it in your browser
8. You should see the **nginx welcome page**
9. Click on **Logs** tab to view container logs

### Step 5: Update Service (Scale Down Example)

1. Navigate to **Amazon Elastic Container Service > Clusters**
2. Click on **"MyCluster"** cluster
3. Scroll to the bottom of the page
4. Click on your service **`MyTask-service-hbrz1axf`**
5. Click **"Update service"** button in the top right corner
6. Change **Desired tasks** from 2 to **1**
7. Click **"Update"** button at the bottom of the page

✅ **Result:** After some time, you'll see only one task running in the Tasks tab.

---

## Additional Information

### ECS Launch Types
- **AWS Fargate:** Serverless compute engine (used in this guide)
- **EC2:** Run containers on your own EC2 instances

### Key Benefits of ECS
- **Serverless option** with AWS Fargate
- **Integrated with AWS services** (ALB, CloudWatch, IAM)
- **Auto-scaling capabilities**
- **High availability** across multiple AZs
- **Security** with VPC and security groups

### Cost Considerations
- **Fargate pricing:** Pay for vCPU and memory resources
- **Data transfer costs** may apply
- **Load balancer costs** if using ALB/NLB

---

## Resources

- [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
- [AWS ECS Official Documentation](https://docs.aws.amazon.com/ecs/)
- [AWS Fargate Pricing](https://aws.amazon.com/fargate/pricing/)

---

## Quick Commands Reference

```bash
# AWS CLI commands for ECS (if needed)
aws ecs list-clusters
aws ecs describe-clusters --clusters MyCluster
aws ecs list-tasks --cluster MyCluster
aws ecs describe-tasks --cluster MyCluster --tasks [task-arn]
```

> **💡 Tip:** Always monitor your AWS costs when running ECS services, especially in production environments.