# Amazon EC2 Instance Challenge Lab

## Overview

This challenge lab demonstrates how to deploy a simple web application on an Amazon EC2 instance running Amazon Linux. The lab covers the creation of a custom Virtual Private Cloud (VPC), networking configuration, security groups, EC2 instance deployment, Apache web server installation, and hosting a static HTML webpage.

---

## Objectives

By completing this lab, I was able to:

- Create a custom Amazon VPC.
- Configure a public subnet.
- Create and attach an Internet Gateway.
- Configure routing for internet connectivity.
- Create a Security Group allowing SSH and HTTP traffic.
- Launch an Amazon Linux EC2 instance.
- Automatically install and configure the Apache (httpd) web server using User Data.
- Connect to the EC2 instance using EC2 Instance Connect.
- Deploy a simple HTML webpage.
- Access the webpage through the instance's public IPv4 address.

---

# AWS Services Used

- Amazon EC2
- Amazon VPC
- Internet Gateway
- Route Tables
- Security Groups
- EC2 Instance Connect
- Amazon Linux
- Apache HTTP Server (httpd)

---

# Architecture

```
                 Internet
                     │
                     ▼
            Internet Gateway
                     │
                     ▼
             Custom VPC (10.0.0.0/16)
                     │
                     ▼
         Public Subnet (10.0.1.0/24)
                     │
                     ▼
             Amazon EC2 Instance
             (Amazon Linux 2023)
                     │
              Apache Web Server
                     │
                     ▼
              projects.html webpage
```

---

# Step 1 - Create a Custom VPC

Created a new VPC with the following configuration:

| Setting | Value |
|----------|-------|
| Name | Lab-VPC |
| IPv4 CIDR | 10.0.0.0/16 |
| IPv6 | None |
| Tenancy | Default |

---

# Step 2 - Create a Public Subnet

Created a subnet within the VPC.

| Setting | Value |
|----------|-------|
| Name | Public-Subnet |
| CIDR | 10.0.1.0/24 |
| Auto Assign Public IP | Enabled |

---

# Step 3 - Create an Internet Gateway

Created and attached an Internet Gateway to the VPC.

Purpose:

- Allows resources inside the VPC to communicate with the Internet.

---

# Step 4 - Configure Route Table

Configured the route table with:

| Destination | Target |
|--------------|---------|
| 10.0.0.0/16 | Local |
| 0.0.0.0/0 | Internet Gateway |

This allows outbound internet access for resources inside the subnet.

---

# Step 5 - Configure Security Group

Created a security group named **Web-SG**.

Inbound Rules

| Protocol | Port | Source |
|----------|------|--------|
| SSH | 22 | My IP / Anywhere |
| HTTP | 80 | 0.0.0.0/0 |

Purpose:

- SSH allows remote administration.
- HTTP allows browsers to access the hosted webpage.

---

# Step 6 - Launch Amazon EC2 Instance

Instance configuration:

| Setting | Value |
|----------|-------|
| Operating System | Amazon Linux |
| Instance Type | t3.micro |
| Storage | gp2 |
| Network | Lab-VPC |
| Subnet | Public-Subnet |
| Public IPv4 | Enabled |

---

# Step 7 - Configure User Data

The following User Data script automatically installs and starts the Apache web server during instance launch.

```bash
#!/bin/bash

yum update -y

yum install -y httpd

systemctl enable httpd

systemctl start httpd

chmod -R 777 /var/www/html
```

### Script Explanation

- Updates installed packages
- Installs Apache HTTP Server
- Enables Apache to start after reboot
- Starts the web server
- Grants write permissions to the web directory

---

# Step 8 - Verify Installation

After the instance launched successfully, I viewed the EC2 System Log to confirm Apache was installed correctly.

### Screenshot - EC2 System Log

> Replace the image below with your own screenshot.

![EC2 System Log](images/system-log.png)

---

# Step 9 - Connect Using EC2 Instance Connect

Connected to the EC2 instance through the AWS Management Console using EC2 Instance Connect.

---

# Step 10 - Create the Webpage

Created a file named:

```
projects.html
```

Contents:

```html
<!DOCTYPE html>
<html>
<body>

<h1>YOUR NAME's re/Start Project Work</h1>

<p>EC2 Instance Challenge Lab</p>

</body>
</html>
```

Copied the webpage into the Apache document root.

```bash
sudo cp projects.html /var/www/html/
```

---

# Step 11 - Test the Web Server

Opened a web browser and navigated to:

```
http://<Public-IP>/projects.html
```

The webpage loaded successfully, confirming that the Apache web server was configured correctly.

---

## Screenshot - Hosted Webpage

> Replace the image below with your own screenshot.

![Hosted Website](images/projects-page.png)

---

# Key Concepts Learned

- Creating custom VPCs
- Public networking
- Internet Gateway configuration
- Route Table configuration
- Security Groups
- Amazon EC2
- EC2 Instance Connect
- Apache (httpd)
- Linux permissions
- User Data scripts
- Static website hosting

---

# Skills Demonstrated

- AWS Networking
- Amazon EC2
- Linux Administration
- SSH Connectivity
- Web Server Installation
- Static Website Deployment
- Infrastructure Configuration
- Cloud Troubleshooting

---

# Outcome

Successfully deployed a web application on an Amazon EC2 instance running Amazon Linux.

The project demonstrates fundamental AWS cloud infrastructure skills including networking, compute provisioning, Linux administration, and web server deployment.

---

# Troubleshooting

## Issue: Unable to Associate the Public Subnet with the Route Table

### Problem

While configuring networking, I attempted to manually associate my **Public-Subnet** with the route table. However, the subnet did not appear under the list of available subnets.

### Investigation

To troubleshoot the issue, I verified the following:

- Confirmed that the subnet and route table belonged to the same VPC.
- Verified that the Internet Gateway was attached to the VPC.
- Checked that the subnet had a valid CIDR block.
- Compared the VPC IDs of both the subnet and the route table.

The subnet and route table were both associated with the same VPC, so the problem was not caused by a VPC mismatch.

### Root Cause

I discovered that the route table was marked as the **Main Route Table** for the VPC.

AWS automatically associates new subnets with the main route table unless a different route table is explicitly assigned. Because of this automatic association, the subnet did not appear in the list of available subnets for manual association.

### Resolution

Instead of trying to associate the subnet manually, I:

1. Verified that the subnet was already using the main route table.
2. Added the default route (`0.0.0.0/0`) pointing to the Internet Gateway.
3. Confirmed that **Auto-assign Public IPv4 Address** was enabled on the subnet.
4. Continued with the EC2 instance deployment.

### Lesson Learned

One important lesson from this lab is that **AWS automatically associates subnets with the Main Route Table** within a VPC. If a subnet does not appear when attempting a manual association, it may already be associated with the main route table. Checking whether the route table is marked as **Main** can save significant troubleshooting time.

---

## Repository Structure

```
.
├── README.md
├── projects.html
└── images
    ├── system-log.png
    └── projects-page.png
```

---

## Author

**Inga Mondliwa**

AWS re/Start Program
