# AWS Virtual Private Cloud (VPC) Notes

## ☁️ Virtual Private Cloud (VPC)
A private, isolated network within the AWS cloud where you can launch and manage your resources securely.

## Why we need VPC?
To securely isolate and control network environments.


## VPC CIDR Block
When you create a VPC, you specify a CIDR block that defines the IP address range for the entire VPC. For example:

```sh
10.0.0.0/16
```

This block allows for 65,536 IP addresses (but in reality, 65,531 usable addresses).

**CIDR (Classless Inter-Domain Routing)** is a method for allocating IP addresses and routing Internet Protocol (IP)
packets.



## What is Subnets?
A subnet is a smaller, segmented part of a larger network that isolates and organizes devices within a specific IP 
address range.
*Diagram Description: A VPC with 100 IPs is shown splitting into two public subnets, each with 50 IPs.*

## What happens when creating subnet?

**CIDR Block Allocation:**
You specify a range of IP addresses (CIDR block) within the VPC's IP address range for the subnet.

This determines the pool of IP addresses available for instances in the subnet.

> ### Subnet CIDR Blocks
>
> Within the VPC, you can create subnets by allocating smaller CIDR blocks from the VPC's range. For example:
>
> *   Public Subnet: `10.0.1.0/24`
> *   Private Subnet: `10.0.2.0/24`
>
> Each of these subnets has 256 IP addresses (251 usable).

## Route Table

> ### What is a Route Table?
> A route table is a set of rules, called routes, that are used to determine where network traffic from your subnets or
> gateway is directed. Each subnet in your VPC must be associated with a route table, which controls the routing for
> that subnet.

*Image Description: A screenshot of an AWS Route Table.*
```
Routes (2)
| Destination     | Target                  |
|-----------------|-------------------------|
| 0.0.0.0/0       | igw-0bc1bb62e4e4f3c3c  |
| 172.31.0.0/16   | local                   |
```

## Internet Gateway
An Internet Gateway is a component that allows communication between instances in your VPC and the internet.

## Security Groups
Network firewall rules that control inbound and outbound traffic for instances.


## Network ACLs (Access Control Lists)

Optional layer of security for your VPC that acts as a firewall for controlling traffic in and out of one or more
subnets.

Allow or Deny Rule.


## NAT (Network Address Translation) Gateway

Enables instances in a private subnet to connect to the internet or other AWS services, but prevents the internet from
initiating connections to those instances.

*Diagram Description: A diagram shows instances in a private subnet connecting out to the internet for "Downloading
Updates". The traffic flows from the instances, through a NAT Gateway located in a public subnet, which then routes the
traffic through an Internet Gateway to the internet.*

## VPC Peering

A networking connection between two VPCs that enables you to route traffic between them privately.

*Diagram Description: Two separate VPCs, "VPC A" and "VPC B", are shown. A peering connection links them, allowing
private communication between the instances in their respective subnets.*

## VPC Endpoints

Allows you to privately connect your VPC to supported AWS services and VPC endpoint services powered by AWS
PrivateLink.

*Diagram Description: Instances inside a VPC are shown connecting directly to an S3 bucket icon via a private
connection (VPC Endpoint), bypassing the need to go through an Internet Gateway.*

## Bastion Host

A special-purpose instance that provides secure access to your instances in private subnets.

*Diagram Description: A VPC is divided into a Public Subnet and a Private Subnet. A Bastion Host (an instance) resides
in the Public Subnet and acts as a secure jump point to access the instances located in the Private Subnet.*


## Elastic IP Addresses
Static IP addresses designed for dynamic cloud computing.

## VPC Flow Logs
Capture information about the IP traffic going to and from network interfaces in your VPC.



## Direct Connect

Establishes a dedicated network connection from your premises to AWS.
*Diagram Description: An office building is shown with a dedicated, private network line connecting directly to its AWS
VPC, bypassing the public internet.*

## AWS Client VPN
Managed VPN service that enables secure remote access to AWS resources and on-premises networks using OpenVPN-based 
clients.


CIDR visualizer https://www.cidr.xyz/


# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)