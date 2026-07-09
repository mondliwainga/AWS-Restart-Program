# Introduction to Amazon EC2

## Project Overview

In this lab, I explored the core features of Amazon Elastic Compute Cloud (Amazon EC2) by launching, configuring, monitoring, resizing, and terminating an EC2 instance. I also learned how to configure security groups, enable termination protection, deploy a simple web server using user data, and resize both the instance type and its storage volume.

This project gave me practical experience with managing virtual servers in AWS and understanding how EC2 instances are configured and maintained.

---

## Objectives

By completing this project, I was able to:

- Launch an Amazon EC2 instance.
- Configure networking and security groups.
- Deploy a web server using EC2 user data.
- Monitor EC2 instance health and performance.
- Allow HTTP traffic through a security group.
- Resize an EC2 instance and increase its storage.
- Test and manage termination protection.
- Safely terminate an EC2 instance.

---

## AWS Services Used

- Amazon EC2
- Amazon Elastic Block Store (EBS)
- Amazon VPC
- Amazon EC2 Security Groups
- AWS Management Console

---

## Project Workflow

### Step 1 – Launch an EC2 Instance

I launched a new EC2 instance using the AWS Management Console with the following configuration:

| Setting | Value |
|---------|-------|
| Instance Name | Web Server |
| Amazon Machine Image | Amazon Linux 2023 |
| Instance Type | t3.micro |
| Key Pair | None |
| VPC | Lab VPC |
| Security Group | Web Server security group |
| Storage | 8 GiB (Default) |
| Termination Protection | Enabled |

---

### Step 2 – Configure User Data

To automatically configure the instance after launch, I added a User Data script that installs and starts an Apache web server.

```bash
#!/bin/bash
yum -y install httpd
systemctl enable httpd
systemctl start httpd
echo '<html><h1>Hello From Your Web Server!</h1></html>' > /var/www/html/index.html
```

This script automatically:

- Installs Apache (httpd)
- Starts the web server
- Configures Apache to start on boot
- Creates a simple HTML webpage

---

### Step 3 – Verify the Instance

After launching the instance, I confirmed that:

- The instance state changed to **Running**
- Both system and instance status checks passed (2/2)
- A public IPv4 address was assigned

---

### Step 4 – Monitor the Instance

I explored several monitoring options available in Amazon EC2, including:

- Status Checks
- Monitoring metrics
- Instance Screenshot
- Health information

These tools help diagnose issues and monitor the overall health of an EC2 instance.

---

### Step 5 – Configure the Security Group

Initially, the security group had no inbound rules, so the web server could not be accessed.

To allow HTTP traffic, I added the following inbound rule:

| Type | Protocol | Source |
|------|----------|--------|
| HTTP | TCP (80) | Anywhere (IPv4) |

After saving the rule, I opened the instance's public IP address in my browser and successfully accessed the web page.

The webpage displayed:

```
Hello From Your Web Server!
```

---

### Step 6 – Resize the EC2 Instance

To simulate scaling resources, I stopped the instance and changed its instance type.

#### Instance Type

From:

```
t3.micro
```

To:

```
t3.small
```

---

### Step 7 – Resize the EBS Volume

I also increased the root storage volume.

| Before | After |
|---------|-------|
| 8 GiB | 10 GiB |

Once the changes were complete, I started the EC2 instance again.

---

### Step 8 – Test Termination Protection

Since termination protection was enabled when I launched the instance, AWS prevented me from deleting it.

To terminate the instance, I first:

- Disabled termination protection
- Saved the changes
- Terminated the instance

This demonstrated how termination protection helps prevent accidental deletion of important resources.

---

## Skills Demonstrated

Throughout this project, I gained hands-on experience with:

- Launching Amazon EC2 instances
- Configuring Linux web servers
- Using EC2 User Data for automation
- Managing Security Groups
- Monitoring EC2 health and performance
- Scaling compute resources
- Resizing Amazon EBS volumes
- Managing termination protection
- Working with AWS networking concepts

---

## Key Takeaways

This project strengthened my understanding of Amazon EC2 and how virtual servers are managed in AWS. I learned how to automate server configuration using User Data, configure networking with security groups, monitor instance health, scale resources by changing instance types and storage, and protect resources from accidental deletion using termination protection.

These are essential skills for deploying and managing cloud infrastructure in AWS.

---

## Author

**Inga Mondliwa**

AWS Cloud Practitioner | Cloud & Linux Enthusiast
