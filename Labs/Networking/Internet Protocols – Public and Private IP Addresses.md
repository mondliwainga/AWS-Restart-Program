# Internet Protocols – Public and Private IP Addresses

## Overview

This lab focused on understanding the difference between **public** and **private IPv4 addresses** within an Amazon Virtual Private Cloud (VPC). The objective was to investigate why one Amazon EC2 instance could access the internet while another could not, despite both instances being deployed within the same VPC and subnet configuration.

## Objectives

* Investigate a customer networking issue.
* Compare public and private IPv4 addresses.
* Troubleshoot EC2 connectivity.
* Explain why AWS recommends using private IP ranges for VPC CIDR blocks.
* Provide a professional support response based on the findings.

---

## Scenario

A customer reported that they had two Amazon EC2 instances running in the same VPC (`10.0.0.0/16`):

* **Instance A** could **not** access the internet.
* **Instance B** **could** access the internet.

The customer also wanted to know whether using a **public IP range (12.0.0.0/16)** for a new VPC would be a good idea.

---

## AWS Services Used

* Amazon EC2
* Amazon VPC
* Internet Gateway (IGW)
* SSH
* Public & Private IPv4 Addressing

---

## Investigation

I inspected both EC2 instances and compared their networking configurations.

| Instance   | Private IP | Public IP  | Internet Access |
| ---------- | ---------- | ---------- | --------------- |
| Instance A | ✅ Assigned | ❌ None     | ❌ No            |
| Instance B | ✅ Assigned | ✅ Assigned | ✅ Yes           |

### Findings

Although both instances were deployed in the same VPC and subnet, **Instance A only had a private IPv4 address**, while **Instance B had both a private and public IPv4 address**.

Because private IP addresses are only routable within the VPC, Instance A could not communicate directly with the internet or accept SSH connections from an external network.

Instance B was assigned a public IPv4 address, allowing internet connectivity through the Internet Gateway.

---

## SSH Connectivity Test

Attempting to connect to **Instance A** from my local machine was unsuccessful because it only had a private IPv4 address.

Connecting to **Instance B** using its public IPv4 address was successful.

This confirmed that public IPv4 addressing is required for direct internet access and remote SSH connections (unless alternative methods such as AWS Systems Manager Session Manager, VPN, or a bastion host are used).

---

## Customer Question

**Can a VPC use the public CIDR block `12.0.0.0/16`?**

### Answer

No.

AWS recommends creating VPCs using **private RFC 1918 address ranges**:

* `10.0.0.0/8`
* `172.16.0.0/12`
* `192.168.0.0/16`

Using a public IP range inside a VPC may cause:

* Routing conflicts
* Connectivity issues
* Problems with VPN or Direct Connect integrations
* Difficulty communicating with legitimate public IP addresses

Using private address ranges follows networking best practices and ensures compatibility with AWS networking services.

---

## Key Concepts Learned

* Private IPv4 addresses are intended for internal communication within a VPC.
* Public IPv4 addresses allow EC2 instances to communicate with the internet.
* An Internet Gateway alone does not provide internet access—an EC2 instance also requires a public IP address (or another supported outbound networking solution).
* SSH connections from an external computer require a reachable public IP address unless a private connectivity solution is in place.
* AWS recommends using RFC 1918 private IP ranges when creating VPC CIDR blocks.

---

## Conclusion

This lab reinforced the importance of understanding IP addressing in AWS networking. By comparing two EC2 instances with different IP configurations, I identified that the absence of a public IPv4 address prevented one instance from accessing the internet. I also confirmed why AWS recommends using private CIDR ranges for VPCs to avoid routing conflicts and ensure reliable network communication.
