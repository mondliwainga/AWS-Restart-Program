# Create Subnets and Allocate IP Addresses in an Amazon VPC

## Overview

This lab demonstrates how to create and configure an Amazon Virtual Private Cloud (Amazon VPC) using the AWS Management Console. The objective is to design a secure network that meets a customer's requirements by creating a VPC, configuring a public subnet, and allocating private IPv4 address ranges using CIDR notation.

## Objectives

By completing this lab, I was able to:

- Summarize the customer's networking requirements.
- Create an Amazon VPC using the AWS Management Console.
- Understand how CIDR blocks determine available IP addresses.
- Configure a public subnet with an appropriate IP address range.
- Allocate private IPv4 address space using RFC 1918 private address ranges.
- Understand the purpose of Internet Gateways, route tables, and subnets within a VPC.
- Verify the created networking resources.

## Customer Scenario

A startup customer required assistance creating their first Amazon VPC.

### Customer Requirements

- Use a private IPv4 address range beginning with **192.x.x.x**.
- Provide approximately **15,000 private IP addresses** within the VPC.
- Create a **public subnet** with at least **50 available IP addresses**.
- Configure networking components required for internet connectivity.

## Solution

To satisfy the customer's requirements, the following network configuration was implemented:

| Resource | Configuration |
|----------|---------------|
| VPC Name | First VPC |
| VPC IPv4 CIDR | 192.168.0.0/18 |
| IPv6 | None |
| Public Subnet | 192.168.1.0/26 |
| Availability Zone | No Preference |
| Internet Gateway | Automatically created |
| Route Table | Automatically configured |

## CIDR Planning

### VPC

```
192.168.0.0/18
```

- Total IP addresses: **16,384**
- Meets the requirement for approximately **15,000 private IP addresses**

### Public Subnet

```
192.168.1.0/26
```

- Total IP addresses: **64**
- Satisfies the requirement of at least **50 IP addresses**

## AWS Services Used

- Amazon VPC
- Subnets
- Internet Gateway
- Route Tables
- AWS Management Console

## Steps Performed

1. Opened the Amazon VPC console.
2. Selected **Create VPC**.
3. Chose **VPC and more**.
4. Configured the VPC using a private IPv4 CIDR block.
5. Created a public subnet.
6. Allowed AWS to automatically create the Internet Gateway and route table.
7. Verified the successful creation of all networking resources.

## Networking Concepts Learned

### Amazon VPC

A logically isolated virtual network that enables AWS resources to communicate securely.

### CIDR

CIDR (Classless Inter-Domain Routing) determines the size of an IP address range assigned to a VPC or subnet.

### Public Subnet

A subnet associated with a route table that directs internet-bound traffic through an Internet Gateway, allowing resources to communicate with the internet.

### Internet Gateway

A highly available gateway that enables communication between resources inside a VPC and the public internet.

### Private IP Addresses

Private IPv4 addresses are not routable over the public internet and are used for secure communication within a VPC.

## Outcome

Successfully deployed a Virtual Private Cloud with:

- A private IPv4 CIDR block
- A public subnet
- Internet connectivity through an Internet Gateway
- Correct IP address allocation based on customer requirements

This lab reinforced fundamental AWS networking concepts including VPC architecture, subnetting, CIDR notation, and IP address planning.
