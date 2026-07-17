# AWS RDS MySQL Database Lab

This project demonstrates how to deploy and interact with a managed relational database using **Amazon Relational Database Service (Amazon RDS)**. The lab focuses on provisioning a MySQL database, securely connecting from an Amazon EC2 Linux instance, and performing common SQL operations including table creation, data insertion, querying, and table joins.

---

## Project Overview

The objective of this lab was to:

- Deploy an Amazon RDS MySQL database instance.
- Configure secure connectivity between an EC2 Linux server and the RDS instance.
- Connect to the database using the MySQL client.
- Create and populate relational database tables.
- Execute SQL queries to retrieve and combine data.

---

## AWS Services Used

- Amazon RDS (MySQL)
- Amazon EC2 (Amazon Linux 2)
- Amazon VPC
- Amazon EC2 Security Groups
- SSH
- MySQL Client

---

## Architecture

```
User (SSH)
      │
      ▼
Amazon EC2 (Amazon Linux 2)
      │
 MySQL Client
      │
      ▼
Amazon RDS MySQL
```

---

## Lab Tasks Completed

### 1. Provisioned an Amazon RDS Database

- Selected MySQL database engine
- Used the Free Tier / Dev-Test template
- Configured a db.t3.micro instance
- Enabled General Purpose SSD (gp2) storage
- Disabled Multi-AZ deployment
- Configured networking within the Lab VPC
- Created a security group allowing MySQL (Port 3306) access from the Linux server

---

### 2. Connected to the Linux Server

Connected securely using SSH and the provided PEM key.

Example:

```bash
ssh -i lab.pem ec2-user@54.186.90.160
```

---

### 3. Connected to the MySQL Database

Example:

```bash
mysql -h studentdb.cbwyuhdbe7tl.us-west-2.rds.amazonaws.com -u admin -p
```

---

### 4. Created the Database

```sql
CREATE DATABASE StudentDB;

USE StudentDB;
```

---

### 5. Created the RESTART Table

```sql
CREATE TABLE RESTART (
    Student_ID INT,
    Student_Name VARCHAR(100),
    Restart_City VARCHAR(100),
    Graduation_Date DATETIME
);
```

---

### 6. Inserted Sample Data

Inserted ten student records into the **RESTART** table.

Example:

```sql
INSERT INTO RESTART VALUES
(1001,'John Smith','Cape Town','2025-12-01 10:00:00');
```

---

### 7. Retrieved Table Data

```sql
SELECT * FROM RESTART;
```

---

### 8. Created the CLOUD_PRACTITIONER Table

```sql
CREATE TABLE CLOUD_PRACTITIONER (
    Student_ID INT,
    Certification_Date DATETIME
);
```

---

### 9. Inserted Certification Records

Inserted five certification records.

```sql
INSERT INTO CLOUD_PRACTITIONER VALUES
(1001,'2026-01-01 09:00:00');
```

---

### 10. Queried the Certification Table

```sql
SELECT * FROM CLOUD_PRACTITIONER;
```

---

### 11. Performed an INNER JOIN

Joined both tables to display student names alongside their certification dates.

```sql
SELECT
    RESTART.Student_ID,
    RESTART.Student_Name,
    CLOUD_PRACTITIONER.Certification_Date
FROM RESTART
INNER JOIN CLOUD_PRACTITIONER
ON RESTART.Student_ID = CLOUD_PRACTITIONER.Student_ID;
```

---

## Skills Demonstrated

- Amazon RDS deployment
- MySQL database administration
- Amazon EC2 connectivity
- Linux command line
- SSH authentication
- AWS networking
- Security Group configuration
- SQL DDL (CREATE DATABASE, CREATE TABLE)
- SQL DML (INSERT)
- SQL Queries
- SQL INNER JOIN operations

---

## Troubleshooting

During the lab, several common issues were encountered and resolved:

- Configured the RDS Security Group to allow MySQL (Port 3306) traffic.
- Installed a compatible MySQL client after encountering an authentication plugin mismatch with the default MariaDB client.
- Verified VPC networking and connectivity between the EC2 instance and the RDS database.
- Selected the target database before creating tables to resolve the `ERROR 1046 (3D000): No database selected` error.

---

## Screenshots

Include screenshots demonstrating:

- Amazon RDS instance creation
- Successful SSH connection to the Linux server
- Successful MySQL connection
- RESTART table creation
- RESTART table data
- CLOUD_PRACTITIONER table creation
- CLOUD_PRACTITIONER table data
- INNER JOIN query results

---

## Learning Outcomes

By completing this lab, I gained practical experience with:

- Deploying managed relational databases on AWS.
- Securely connecting EC2 instances to Amazon RDS.
- Configuring networking and security groups.
- Managing MySQL databases from the Linux command line.
- Performing SQL operations to create, populate, query, and join relational tables.
