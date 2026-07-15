# Building a DB Server and Interacting with my Database Using Amazon RDS

## Overview

This project demonstrates how to deploy a highly available relational database using **Amazon Relational Database Service (Amazon RDS)** and connect it to a web application running on an **Amazon EC2** instance.

Amazon RDS is a fully managed database service that simplifies the setup, operation, scaling, and maintenance of relational databases in the AWS Cloud. It automates routine administrative tasks such as backups, software patching, monitoring, and failover, allowing developers to focus on building applications.

Supported database engines include:

- Amazon Aurora
- MySQL
- PostgreSQL
- MariaDB
- Oracle
- Microsoft SQL Server

---

## Objectives

By completing this lab, I learned how to:

- Create a Security Group for an Amazon RDS instance
- Configure a DB Subnet Group across multiple Availability Zones
- Launch a highly available Multi-AZ Amazon RDS MySQL database
- Securely connect an EC2 web server to the database
- Configure a web application to communicate with Amazon RDS
- Test data persistence and automatic replication between Availability Zones

---

# Architecture

## Initial Infrastructure

Before deploying Amazon RDS, the environment consists of:

- Amazon VPC
- Public and Private Subnets
- Internet Gateway
- NAT Gateway
- Amazon EC2 Web Server

<img width="1067" height="511" alt="image" src="https://github.com/user-attachments/assets/dbe551b3-8512-46bf-92fd-d3a6c349f5e3" />


---

## Final Infrastructure

After completing the lab, the architecture includes:

- Amazon RDS MySQL (Primary)
- Amazon RDS MySQL (Standby)
- Multi-AZ Deployment
- DB Security Group
- DB Subnet Group
- Secure communication between EC2 and RDS

<img width="1064" height="507" alt="image" src="https://github.com/user-attachments/assets/0628f9f9-0bc5-4057-a355-894555253f54" />


---

# Architecture Components

| AWS Service | Description |
|-------------|-------------|
| Amazon VPC | Provides an isolated virtual network for AWS resources. |
| Public Subnets | Host internet-facing resources such as the EC2 web server. |
| Private Subnets | Securely host the Amazon RDS database instances. |
| Internet Gateway | Enables internet access for public resources. |
| NAT Gateway | Allows private subnet resources to access the internet without exposing them publicly. |
| Amazon EC2 | Hosts the Address Book web application. |
| Amazon RDS | Managed MySQL database service. |
| Security Groups | Virtual firewalls controlling inbound and outbound traffic. |
| Multi-AZ Deployment | Provides automatic failover and high availability. |

---

# Lab Tasks

## Task 1: Create an RDS Security Group

Created a dedicated security group to allow the EC2 Web Server to communicate with the RDS database.

### Configuration

| Setting | Value |
|---------|-------|
| Security Group Name | DB Security Group |
| Description | Permit access from Web Security Group |
| VPC | Lab VPC |

### Inbound Rule

| Type | Port | Source |
|------|------|--------|
| MySQL/Aurora | 3306 | Web Security Group |

This configuration ensures that only EC2 instances associated with the **Web Security Group** can access the database.

---

## Task 2: Create a DB Subnet Group

Created a DB Subnet Group spanning two Availability Zones to support Multi-AZ deployment.

### Configuration

| Setting | Value |
|---------|-------|
| Name | DB Subnet Group |
| Description | DB Subnet Group |
| VPC | Lab VPC |

### Selected Subnets

- Private Subnet 1 (10.0.1.0/24)
- Private Subnet 2 (10.0.3.0/24)

---

## Task 3: Deploy an Amazon RDS MySQL Database

Configured and launched a Multi-AZ Amazon RDS MySQL database.

### Database Configuration

| Setting | Value |
|---------|-------|
| Engine | MySQL |
| Template | Dev/Test |
| Deployment | Multi-AZ DB Instance |
| DB Identifier | lab-db |
| Username | main |
| Password | lab-password |

### Instance Configuration

| Setting | Value |
|---------|-------|
| Instance Type | db.t3.medium |
| Storage Type | General Purpose SSD (gp3) |
| Allocated Storage | 20 GB |

### Connectivity

| Setting | Value |
|---------|-------|
| VPC | Lab VPC |
| DB Subnet Group | DB Subnet Group |
| Public Access | No |
| Security Group | DB Security Group |

### Additional Configuration

| Setting | Value |
|---------|-------|
| Initial Database | lab |
| Automated Backups | Disabled (Lab Only) |
| Enhanced Monitoring | Disabled |
| Performance Insights | Disabled |

> **Note:** Automated backups were disabled only to reduce deployment time for the lab. In production environments, backups should always remain enabled.

---

## Task 4: Connect the Web Application

After the database became available:

1. Copied the RDS Endpoint.
2. Opened the EC2 Web Server.
3. Navigated to the **RDS** page.
4. Configured the application using:

| Setting | Value |
|---------|-------|
| Endpoint | RDS Endpoint |
| Database | lab |
| Username | main |
| Password | lab-password |

After submitting the configuration, the Address Book application successfully connected to Amazon RDS.
<img width="2482" height="792" alt="image" src="https://github.com/user-attachments/assets/98dafb98-eef6-473b-916b-713405ffa550" />


---

# Testing

Verified the database connection by performing CRUD (Create, Read, Update, Delete) operations within the Address Book application:

- ✅ Add contacts
- ✅ Edit contacts
- ✅ Delete contacts
- ✅ Store data in Amazon RDS

The database automatically replicates all data to the standby instance located in the second Availability Zone.

---

# High Availability

Amazon RDS Multi-AZ deployment provides:

- Automatic synchronous replication
- High availability
- Automatic failover
- Increased durability
- Minimal downtime during infrastructure failures

---

# Skills Demonstrated

- Amazon VPC
- Amazon EC2
- Amazon RDS
- Multi-AZ Deployments
- Security Groups
- DB Subnet Groups
- High Availability Architecture
- Private Networking
- Web Application Integration

---

# Technologies Used

- AWS VPC
- Amazon EC2
- Amazon RDS (MySQL)
- Security Groups
- DB Subnet Groups
- NAT Gateway
- Internet Gateway
- Multi-AZ Deployment

---

# Key Learning Outcomes

Through this project, I gained hands-on experience with:

- Deploying managed relational databases using Amazon RDS
- Designing secure VPC networking
- Configuring Security Groups for controlled database access
- Implementing private subnet architecture
- Connecting EC2 applications to Amazon RDS
- Understanding Multi-AZ deployments and automatic failover
- Building highly available cloud infrastructure following AWS best practices
