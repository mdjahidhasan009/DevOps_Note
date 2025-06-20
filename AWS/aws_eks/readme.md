# Complete Kubernetes and Amazon EKS Guide

## What is Kubernetes?

An open-source platform for automating the deployment, scaling, and management of containerized applications.

Key capabilities:
* **Automating Deployment** - Streamlined container orchestration
* **Management of Containerized apps** - Centralized application lifecycle management
* **Scaling** - Horizontal and vertical scaling capabilities

## Kubernetes Architecture Overview

When you deploy Kubernetes, you get a cluster.

Two important parts are:
* **Master (Control Plane)** - Manages the cluster
* **Worker nodes** - Run your applications

---

## Prerequisites

Before you begin, ensure you have the following prerequisites set up to use Amazon EKS:

* Set up AWS CLI and configure credentials
* Install eksctl
* Install kubectl

---

## Installation Guide

### Installation of AWS CLI

**Official Documentation:** https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

**Platform-specific Installation:**
* **Windows** - install using msi
* **MAC** - install using pkg
* **Verify installation:** `aws --version`

#### AWS Credential Setup

Follow these steps to configure your AWS credentials:

1. **Create a IAM user** in AWS Console
2. **Provide sufficient permissions** (EKS, EC2, VPC permissions)
3. **Create or generate Secret Keys** (Access Key ID and Secret Access Key)
4. **AWS CONFIGURE** - command to setup keys
5. **To verify user identity:**
   ```bash
   aws sts get-caller-identity
   ```

**Example Configuration:**
```bash
jahid@<pc-name>:<path>$ aws configure
AWS Access Key ID [****************xxxx]: <your-access-key-id>
AWS Secret Access Key [****************xxxx]: <your-secret-access-key>
Default region name [ap-southeast-1a]: ap-southeast-1
Default output format [json]: 
jahid@<pc-name>:<path>$ 
jahid@<pc-name>:<path>$ aws sts get-caller-identity
{
    "UserId": "",
    "Account": "",
    "Arn": ""
}
jahid@<pc-name>:<path>$ 
```

---

### Installation of eksctl

#### Windows (using chocolatey)
* Install Chocolatey: https://chocolatey.org/install
* Verify: `$ choco`
* Install eksctl: `choco install eksctl`

#### MAC (using homebrew)
* Install Homebrew: https://brew.sh/
* Add tap: `brew tap weaveworks/tap`
* Install: `brew install weaveworks/tap/eksctl`

#### Linux (Manual Installation)
```bash
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
sudo mv /tmp/eksctl /usr/local/bin
```

**Verify Installation:**
```bash
eksctl version
# Output: 0.210.0
```

---

### Installation of kubectl

**Official Documentation:** https://kubernetes.io/docs/tasks/tools/

* **MAC** - `brew install kubectl`
* **Windows** - `choco install kubernetes-cli`
* **Linux** - Follow official documentation for package manager installation

---

## Creating EKS Cluster

### To create EKS Cluster in AutoMode

**Basic Command:**
```bash
eksctl create cluster --name=<cluster-name> --enable-auto-mode
```

**Advanced Options:**
```bash
eksctl create cluster --name=my-cluster \
  --enable-auto-mode \
  --nodes=3 \
  --nodes-min=2 \
  --nodes-max=5 \
  --region us-west-2 \
  --node-type t3.medium \
  --managed/fargate
```

**Example Creation Process:**
```bash
jahid@<pc-name>:<path>$ eksctl create cluster --name="my-cluster" --enable-auto-mode
2025-06-20 15:38:27 [ℹ]  eksctl version 0.210.0
2025-06-20 15:38:27 [ℹ]  using region ap-southeast-1
2025-06-20 15:38:28 [ℹ]  setting availability zones to [ap-southeast-1a ap-southeast-1b ap-southeast-1c]
2025-06-20 15:38:28 [ℹ]  subnets for ap-southeast-1a - public:192.168.0.0/19 private:192.168.96.0/19
2025-06-20 15:38:28 [ℹ]  subnets for ap-southeast-1b - public:192.168.32.0/19 private:192.168.128.0/19
2025-06-20 15:38:28 [ℹ]  subnets for ap-southeast-1c - public:192.168.64.0/19 private:192.168.160.0/19
2025-06-20 15:38:28 [ℹ]  using Kubernetes version 1.32
2025-06-20 15:38:28 [ℹ]  creating EKS cluster "my-cluster" in "ap-southeast-1" region with 
2025-06-20 15:38:28 [ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=ap-southeast-1 --cluster=my-cluster'
2025-06-20 15:38:28 [ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "my-cluster" in "ap-southeast-1"
2025-06-20 15:38:28 [ℹ]  CloudWatch logging will not be enabled for cluster "my-cluster" in "ap-southeast-1"
2025-06-20 15:38:28 [ℹ]  you can enable it with 'eksctl utils update-cluster-logging --enable-types={SPECIFY-YOUR-LOG-TYPES-HERE (e.g. all)} --region=ap-southeast-1 --cluster=my-cluster'
2025-06-20 15:38:28 [ℹ]  default addons metrics-server were not specified, will install them as EKS addons
2025-06-20 15:38:28 [ℹ]  
2 sequential tasks: { create cluster control plane "my-cluster", 
    2 sequential sub-tasks: { 
        1 task: { create addons },
        wait for control plane to become ready,
    } 
}
2025-06-20 15:38:28 [ℹ]  building cluster stack "eksctl-my-cluster-cluster"
2025-06-20 15:38:28 [ℹ]  deploying stack "eksctl-my-cluster-cluster"
2025-06-20 15:38:58 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:39:28 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:40:29 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:41:29 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:42:30 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:43:30 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:44:30 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:45:31 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:46:31 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:47:31 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:48:31 [ℹ]  waiting for CloudFormation stack "eksctl-my-cluster-cluster"
2025-06-20 15:48:33 [ℹ]  creating addon: metrics-server
2025-06-20 15:48:33 [ℹ]  successfully created addon: metrics-server
2025-06-20 15:50:34 [ℹ]  waiting for the control plane to become ready
2025-06-20 15:50:35 [✔]  saved kubeconfig as "/home/jahid/.kube/config"
2025-06-20 15:50:35 [ℹ]  no tasks
2025-06-20 15:50:35 [✔]  all EKS cluster resources for "my-cluster" have been created
2025-06-20 15:50:37 [ℹ]  kubectl command should work with "/home/jahid/.kube/config", try 'kubectl get nodes'
2025-06-20 15:50:37 [✔]  EKS cluster "my-cluster" in "ap-southeast-1" region is ready
```

**Verify Cluster:**
```bash
jahid@<pc-name>:<path>$ kubectl cluster-info
Kubernetes control plane is running at https://153A0BE3FD9A2AB86725F6674DD8141A.yl4.ap-southeast-1.eks.amazonaws.com

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
jahid@<pc-name>:<path>$ cd ./AWS/aws_eks/
```

---

## What is Ingress?

Ingress exposes HTTP and HTTPS routes from outside the cluster to **services** within the cluster. Traffic routing is controlled by **rules** defined on the Ingress resource.

Here is a simple example where an Ingress sends all its traffic to one Service:

```
┌──────┐  Ingress-managed   ┌──────────────────────────────────────┐
│      │ .........................        cluster                  │
│client│   load balancer    │     ↓                                │
│      │                    │  ┌─────────┐  routing  ┌───────┐     │
└──────┘                    │  │         │  rule     │       │     │
                            │  │ Ingress │ ────────> │Service│     │
                            │  │         │           │       │     │
                            │  └─────────┘           └───────┘     │
                            │                           │  │       │
                            │                           ▼  ▼       │
                            │                      ┌─────┐─────┐   │
                            │                      │ Pod │ Pod │   │
                            │                      └─────┘─────┘   │
                            └──────────────────────────────────────┘
```

**Diagram Description:** The diagram shows a flow where a `client` connects to an `Ingress-managed load balancer`. The
traffic is then sent to an `Ingress` resource within the cluster. The `Ingress` uses a `routing rule` to forward the 
traffic to a `Service`, which in turn distributes it to one or more `Pods`.

---

## EKS Cluster Types

| Cluster Type          | Best For                              | Managed By AWS  | Cost Model         |
|-----------------------|---------------------------------------|-----------------|--------------------|
| EC2 Worker Nodes      | Customizable, performance-intensive   | Partially       | Pay per instance   |
| Fargate               | Serverless, lightweight workloads     | Fully           | Pay per pod        |
| Managed Node Groups   | Simplified node management            | Mostly          | Pay per instance   |
| EKS Anywhere          | Hybrid and on-premises deployments    | No              | License-based      |
| Spot Instances        | Cost-sensitive, fault-tolerant apps   | Partially       | Discounted pricing |
| Outposts              | On-prem Kubernetes workloads          | Mostly          | Outpost pricing    |
| Multi-Cluster         | Large-scale, isolated environments    | Partially       | Per cluster        |

---

# Amazon EKS (Elastic Kubernetes Service)

Fully managed Kubernetes cluster infrastructure.

**Node Definition:** One EC2 instance / VM that runs the Kubernetes worker node components.

## EKS Architecture

```
               Master Node
                   |
                   | Control Plane
                   |
                   +-----------------+
                   |                 |
                   |                 |
                   |                 |
                   |                 |
                   |                 |
                   |                 |
                   |                 |
                   +-----------------+
                    |        |       |
                    |        |       |
                    |        |       |
                    |        |       |
        Worker Node 1   Worker Node 2  Worker Node N
                    |        |       |
                    |        |       |
                    |        |       |
                    +--------+-------+
                             |
                             |
                        Kubernetes 
```

**AWS EKS Benefits:**
- **Master Node Management:** AWS manages the master node across multiple AZs for high availability
- **High Availability:** If one AZ goes down, other AZs remain available
- **Worker Node Options:** Choose between Fargate (serverless) and EC2 (managed instances)

**Reference Architecture:** https://docs.aws.amazon.com/images/eks/latest/userguide/images/k8sinaction.png

## Practical Implementation Example

### Step 1: Configure AWS Credentials

```bash
jahid@<pc-name>:<path>$ aws configure
AWS Access Key ID [****************xxxx]: <your-access-key-id>
AWS Secret Access Key [****************xxxx]: <your-secret-access-key>
Default region name [ap-southeast-1a]: ap-southeast-1
Default output format [json]: 
jahid@<pc-name>:<path>$ 
jahid@<pc-name>:<path>$ aws sts get-caller-identity
{
    "UserId": "",
    "Account": "",
    "Arn": ""
}
jahid@<pc-name>:<path>$ 
```

### Step 2: Install eksctl

```bash
jahid@<pc-name>:<path>$ eksctl --version
eksctl: command not found
jahid@<pc-name>:<path>$ ARCH=amd64
PLATFORM=$(uname -s)_$ARCH
jahid@<pc-name>:<path>$ curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
jahid@<pc-name>:<path>$ tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
jahid@<pc-name>:<path>$ sudo mv /tmp/eksctl /usr/local/bin
[sudo] password for jahid: 
jahid@<pc-name>:<path>$ eksctl version
0.210.0
```

### Step 3: Create EKS Cluster

```bash
jahid@<pc-name>:<path>$ eksctl create cluster --name="my-cluster" --enable-auto-mode
```

*(See full output in the cluster creation section above)*

### Step 4: Configure Ingress

Create IngressClass configuration:

```yaml
---
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: alb
  annotations:
    ingressclass.kubernetes.io/is-default-class: "true"
spec:
  controller: eks.amazonaws.com/alb
```

**Apply the configuration:**
```bash
jahid@<pc-name>:<path>/AWS/aws_eks$ kubectl apply -f ingressclass.yaml
ingressclass.networking.k8s.io/alb created
```

### Step 5: Deploy Sample Application (2048 Game)

Create namespace and deploy application:

```bash
jahid@<pc-name>:<path>/AWS/aws_eks$ kubectl create namespace game-2048 --save-config
namespace/game-2048 created
jahid@<pc-name>:<path>/AWS/aws_eks$ kubectl apply -n game-2048 -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.8.0/docs/examples/2048/2048_full.yaml
namespace/game-2048 configured
deployment.apps/deployment-2048 created
service/service-2048 created
ingress.networking.k8s.io/ingress-2048 created
jahid@<pc-name>:<path>/AWS/aws_eks$ kubectl get ingress -n game-2048
NAME           CLASS   HOSTS   ADDRESS                                                                        PORTS   AGE
ingress-2048   alb     *       k8s-game2048-ingress2-184c5765e8-1918956120.ap-southeast-1.elb.amazonaws.com   80      15m
jahid@<pc-name>:<path>/AWS/aws_eks$ 
```

**Access the application:** Use the ADDRESS URL to access the 2048 game in your browser.

**AWS Resources Created:**
Now if we goes to EC2 will see one EC2 instance is running. Also created a load balancer.
- One EC2 instance running in your cluster
- Application Load Balancer (ALB) for ingress

## Cluster Management Commands

### List Clusters

```bash
jahid@<pc-name>:<path>/AWS/aws_eks$ eksctl get cluster
NAME            REGION          EKSCTL CREATED
my-cluster      ap-southeast-1  True
```

### Delete Cluster

```bash
jahid@<pc-name>:<path>/AWS/aws_eks$ eksctl delete cluster --name my-cluster
2025-06-20 16:28:07 [ℹ]  deleting EKS cluster "my-cluster"
2025-06-20 16:28:08 [ℹ]  deleted 0 Fargate profile(s)
2025-06-20 16:28:08 [✔]  kubeconfig has been updated
2025-06-20 16:28:08 [ℹ]  cleaning up AWS load balancers created by Kubernetes objects of Kind Service or Ingress
2025-06-20 16:28:15 [ℹ]  1 task: { delete cluster control plane "my-cluster" [async] }
2025-06-20 16:28:15 [ℹ]  will delete stack "eksctl-my-cluster-cluster"
2025-06-20 16:28:16 [✔]  all cluster resources were deleted
```

This will delete EC2, load balancer, and all other resources created by the cluster.

**⚠️ Important:** This command will delete ALL resources created by the cluster including EC2, load balancer, and all
other resources created by the cluster.

---

## Useful kubectl Commands

```bash
# Check cluster info
kubectl cluster-info

# Get nodes
kubectl get nodes

# Get all resources in a namespace
kubectl get all -n game-2048

# Get ingress resources
kubectl get ingress -n game-2048

# Describe a resource
kubectl describe ingress ingress-2048 -n game-2048

# Get cluster context
kubectl config current-context

# Switch context (if multiple clusters)
kubectl config use-context <context-name>
```

---

## Troubleshooting Tips

### Common Issues

1. **eksctl command not found**
    - Ensure eksctl is properly installed and in PATH
    - Verify with `eksctl version`

2. **AWS credentials not configured**
    - Run `aws configure` to set up credentials
    - Verify with `aws sts get-caller-identity`

3. **Cluster creation fails**
    - Check CloudFormation console for detailed error messages
    - Ensure IAM permissions are sufficient

4. **Ingress not getting external IP**
    - Verify AWS Load Balancer Controller is installed
    - Check security groups and subnet configurations

### Log Analysis

```bash
# Check CloudFormation events
eksctl utils describe-stacks --region=ap-southeast-1 --cluster=my-cluster

# Enable cluster logging
eksctl utils update-cluster-logging --enable-types=all --region=ap-southeast-1 --cluster=my-cluster

# Check pod logs
kubectl logs -n game-2048 deployment/deployment-2048
```

---

## Best Practices

### Security
- Use IAM roles for service accounts (IRSA)
- Enable cluster endpoint private access
- Implement network policies
- Use AWS Secrets Manager for sensitive data

### Cost Optimization
- Use Spot instances for non-critical workloads
- Implement cluster autoscaler
- Monitor resource usage with CloudWatch
- Consider Fargate for serverless workloads

### Performance
- Use appropriate instance types for workloads
- Configure resource requests and limits
- Implement horizontal pod autoscaler
- Use multi-AZ deployments for high availability

---

# Resources

* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
* [Amazon EKS Official Documentation](https://docs.aws.amazon.com/eks/)
* [eksctl Official Documentation](https://eksctl.io/)
* [Kubernetes Official Documentation](https://kubernetes.io/docs/home/)
* [AWS Load Balancer Controller](https://kubernetes-sigs.github.io/aws-load-balancer-controller/)
* [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---

## Quick Reference Commands

```bash
# Cluster lifecycle
eksctl create cluster --name=my-cluster --enable-auto-mode
eksctl get cluster
eksctl delete cluster --name my-cluster

# Application deployment
kubectl create namespace <namespace>
kubectl apply -f <file.yaml>
kubectl get ingress -n <namespace>

# Troubleshooting
kubectl get pods -n <namespace>
kubectl logs <pod-name> -n <namespace>
kubectl describe <resource> <name> -n <namespace>
```