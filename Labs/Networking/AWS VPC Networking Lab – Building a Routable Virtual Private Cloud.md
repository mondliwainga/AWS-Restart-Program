# Creating Networking Resources in an Amazon Virtual Private Cloud (VPC)

## Overview

This lab focuses on building a fully functional Amazon Virtual Private Cloud (VPC) from scratch and configuring the networking components required to provide internet connectivity to an Amazon EC2 instance.

As a Cloud Support Engineer, the objective was to troubleshoot a customer's networking issue by creating and connecting the necessary AWS networking resources. The lab is successfully completed when an EC2 instance within the VPC can communicate with the internet by successfully running the `ping google.com` command.

---

## Objectives

In this lab, I learned how to:

- Create an Amazon VPC
- Configure a public subnet
- Create and attach an Internet Gateway (IGW)
- Configure a Route Table
- Associate a subnet with a Route Table
- Create and configure a Network ACL (NACL)
- Create and configure a Security Group
- Launch an Amazon EC2 instance inside the VPC
- Verify internet connectivity using the `ping` command
- Understand how AWS networking resources work together

---

## AWS Services Used

- Amazon VPC
- Amazon EC2
- Internet Gateway (IGW)
- Route Tables
- Subnets
- Network Access Control Lists (NACLs)
- Security Groups

---

## Architecture

```
                    Internet
                        │
                Internet Gateway
                        │
                Public Route Table
             (0.0.0.0/0 → Internet Gateway)
                        │
                Public Subnet
             (192.168.1.0/28)
                        │
                Security Group
                        │
                  Amazon EC2
```

---

## Implementation Steps

### 1. Created the VPC

- Name: **Test VPC**
- CIDR Block: **192.168.0.0/18**

The VPC acts as the isolated virtual network where all AWS resources are deployed.

---

### 2. Created a Public Subnet

- Name: **Public subnet**
- CIDR Block: **192.168.1.0/28**

The subnet provides IP addresses for resources deployed inside the VPC.

---

### 3. Created a Route Table

- Name: **Public route table**

Configured the route table to direct internet-bound traffic through the Internet Gateway.

---

### 4. Created and Attached an Internet Gateway

- Name: **IGW test VPC**

Attached the Internet Gateway to the VPC to enable communication with the public internet.

---

### 5. Configured Routing

Added the following route:

| Destination | Target |
|-------------|--------|
| 0.0.0.0/0 | Internet Gateway |

This route allows outbound internet traffic.

---

### 6. Associated the Route Table

Associated the **Public subnet** with the **Public route table** so that instances within the subnet use the configured routes.

---

### 7. Configured a Network ACL

Created a Network ACL allowing:

- All inbound traffic
- All outbound traffic

This ensures traffic is not blocked at the subnet level.

---

### 8. Created a Security Group

Created a Security Group allowing:

- SSH (Port 22)
- HTTP (Port 80)
- HTTPS (Port 443)

Outbound traffic:

- Allow All

The Security Group acts as the instance-level firewall.

---

### 9. Launched an EC2 Instance

Configuration:

- Amazon Linux 2023
- t3.micro
- Public IP Enabled
- Public Security Group
- Public Subnet

---

### 10. Verified Connectivity

Connected to the EC2 instance using SSH and executed:

```bash
ping google.com
```

Successful replies confirmed:

- Internet Gateway configured correctly
- Route Table configured correctly
- Security Group configured correctly
- Network ACL configured correctly
- Public IP assigned successfully

---

## Key Concepts Learned

### Amazon VPC

A logically isolated virtual network where AWS resources are deployed securely.

### Public Subnet

A subnet that has a route to an Internet Gateway, allowing internet access.

### Internet Gateway (IGW)

Provides communication between the VPC and the internet.

### Route Table

Determines how network traffic is routed inside the VPC.

### Network ACL (NACL)

A stateless firewall that controls traffic entering and leaving a subnet.

### Security Group

A stateful firewall that controls inbound and outbound traffic at the instance level.

### Public IP Address

Allows an EC2 instance to communicate directly with the internet.

---

## Networking Flow

```
EC2 Instance
      │
Security Group
      │
Public Subnet
      │
Route Table
      │
Internet Gateway
      │
Internet
```

---

## Skills Gained

- Amazon VPC networking
- Subnet creation and management
- Internet Gateway configuration
- Route Table configuration
- Network ACL configuration
- Security Group configuration
- EC2 networking
- SSH connectivity
- AWS networking troubleshooting
- Cloud infrastructure design

---

## Outcome

Successfully designed and deployed a functional AWS network infrastructure capable of providing internet connectivity to an EC2 instance. This lab reinforced core AWS networking concepts, including VPC architecture, routing, security controls, and internet connectivity, while developing practical troubleshooting skills commonly used by AWS Cloud Support Engineers.
