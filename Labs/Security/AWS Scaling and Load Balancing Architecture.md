# AWS Scaling and Load Balancing Architecture

## 📌 Project Overview

I built a highly available and scalable web application architecture on AWS using Amazon EC2, Elastic Load Balancing, EC2 Auto Scaling, and Amazon CloudWatch.

I started with a single EC2 web server and then redesigned the environment to support load balancing, automatic scaling, health checks, and deployment across multiple Availability Zones.

The final architecture uses an internet-facing Application Load Balancer in public subnets to distribute traffic to EC2 instances running in private subnets. An Auto Scaling Group manages the EC2 instances and automatically adjusts capacity based on CPU utilization.

This project gave me practical experience designing and implementing a scalable AWS infrastructure rather than relying on a single web server.

---

## 🏗️ Architecture I Built

The architecture I implemented follows this flow:

```text
                         Internet
                            |
                            v
                 +---------------------+
                 | Application Load    |
                 | Balancer - LabELB    |
                 +----------+----------+
                            |
                     Target Group
                            |
              +-------------+-------------+
              |                           |
              v                           v
       Private Subnet 1            Private Subnet 2
          AZ 1                         AZ 2
              |                           |
              v                           v
        EC2 Instance               EC2 Instance
        Lab Instance               Lab Instance
              ^                           ^
              |                           |
              +-------------+-------------+
                            |
                    Auto Scaling Group
                            |
                     CloudWatch Metrics
                            |
                     CPU Target = 50%
```

I deployed the Application Load Balancer across two public subnets and configured the Auto Scaling Group to launch application servers across two private subnets.

This provides better availability because the application is not dependent on a single EC2 instance or Availability Zone.

---

# 🚀 What I Implemented

### 1. Created an Amazon Machine Image

I created an AMI called:

```text
Web Server AMI
```

from the existing web server.

This allowed me to capture the server configuration and use it as the base image for new EC2 instances.

![Web Server AMI](screenshots/01-web-server-ami.png)

---

### 2. Created an Application Load Balancer

I created an internet-facing Application Load Balancer named:

```text
LabELB
```

I configured it across:

- Public Subnet 1
- Public Subnet 2

I associated the load balancer with the existing `Web Security Group`.

![Application Load Balancer](screenshots/02-load-balancer.png)

---

### 3. Created a Target Group

I created the target group:

```text
lab-target-group
```

with EC2 instances as the target type.

The target group provides the connection between the Application Load Balancer and the EC2 instances managed by Auto Scaling.

![Target Group](screenshots/03-target-group.png)

---

### 4. Created an EC2 Launch Template

I created the launch template:

```text
lab-app-launch-template
```

and configured it to use my `Web Server AMI`.

The launch template specifies the configuration that Auto Scaling uses whenever it needs to launch a new EC2 instance.

My configuration included:

| Configuration | Value |
|---|---|
| AMI | Web Server AMI |
| Instance Type | t3.micro |
| Security Group | Web Security Group |
| Key Pair | Not included |

![Launch Template](screenshots/04-launch-template.png)

---

### 5. Created an Auto Scaling Group

I created:

```text
Lab Auto Scaling Group
```

and configured it to launch instances into:

- Private Subnet 1
- Private Subnet 2

I configured the capacity as:

| Setting | Configuration |
|---|---:|
| Minimum | 2 |
| Desired | 2 |
| Maximum | 4 |

I also connected the Auto Scaling Group to my `lab-target-group` and configured ELB health checks.

![Auto Scaling Group](screenshots/05-auto-scaling-group.png)

---

### 6. Configured Target Tracking Scaling

I configured a target tracking scaling policy based on:

```text
Average CPU Utilization
```

with a target of:

```text
50%
```

This allows the Auto Scaling Group to automatically increase or decrease the number of EC2 instances based on application demand.

---

### 7. Deployed EC2 Instances Across Multiple Availability Zones

The Auto Scaling Group launched two initial EC2 instances across the private subnets.

This provided redundancy across two Availability Zones.

![EC2 Instances](screenshots/06-private-subnet-instances.png)

---

### 8. Verified Load Balancer Health Checks

I checked the target group and verified that the EC2 instances successfully passed the Application Load Balancer health checks.

Both instances eventually showed:

```text
Healthy
```

![Healthy Targets](screenshots/07-healthy-targets.png)

---

### 9. Tested the Application Through the Load Balancer

I accessed the application using the DNS name of my Application Load Balancer.

The request was successfully routed through:

```text
Internet
   ↓
LabELB
   ↓
lab-target-group
   ↓
EC2 instance
```

![Load Test Application](screenshots/08-load-test.png)

---

### 10. Tested Automatic Scaling

I used the Load Test application to generate additional CPU utilization.

I monitored the environment using Amazon CloudWatch and observed the scaling alarm transition as CPU utilization increased.

![CloudWatch Alarms](screenshots/09-cloudwatch-alarms.png)

As CPU utilization remained above the configured target, the Auto Scaling Group launched additional EC2 instances.

This demonstrated that the infrastructure could automatically scale out in response to increased demand.

![Scaling Out](screenshots/10-scaling-out.png)

---

### 11. Removed the Original Web Server

Once the new architecture was working correctly, I terminated the original `Web Server 1` instance.

The application was now running on EC2 instances managed by the Auto Scaling Group rather than depending on the original server.

![Web Server Terminated](screenshots/11-web-server-terminated.png)

---

# 🔄 How My Architecture Scales

The Auto Scaling configuration allows the environment to operate between two and four EC2 instances.

Under normal conditions:

```text
2 EC2 Instances
```

When CPU utilization increases:

```text
2 Instances
     ↓
High CPU
     ↓
CloudWatch Alarm
     ↓
Auto Scaling
     ↓
3 Instances
```

If demand continues to increase:

```text
3 Instances
     ↓
High CPU
     ↓
Auto Scaling
     ↓
4 Instances
```

The Auto Scaling Group will not scale below two instances because I configured the minimum capacity as two.

---

# 🧠 Key AWS Concepts I Practiced

Through this project, I gained practical experience with:

- Amazon EC2
- Amazon Machine Images (AMI)
- Application Load Balancers
- Target Groups
- EC2 Launch Templates
- EC2 Auto Scaling
- Amazon CloudWatch
- VPC networking
- Public and private subnets
- Availability Zones
- Health checks
- Target tracking scaling
- Horizontal scaling
- High availability
- Fault tolerance

---

# 💡 What I Learned

The main lesson from this project was understanding how multiple AWS services work together to create a scalable architecture.

Instead of having users connect directly to a single EC2 instance, I used an Application Load Balancer as the entry point to the application.

The Auto Scaling Group then manages the application servers behind the load balancer and automatically adjusts capacity based on demand.

The architecture therefore follows:

```text
User
 ↓
Application Load Balancer
 ↓
Target Group
 ↓
Auto Scaling Group
 ↓
EC2 Instances
```

with CloudWatch monitoring CPU utilization and driving the scaling policy.

---

# 🎯 Final Architecture

I transformed the initial single-server environment into a more resilient architecture using:

```text
                Application Load Balancer
                          |
                   Target Group
                    /          \
                   /            \
          EC2 Instance      EC2 Instance
             AZ 1               AZ 2
                \                /
                 \              /
                  Auto Scaling
                      |
                  CloudWatch
```

The final configuration maintained a minimum of two instances and could automatically scale up to four instances when demand increased.

This project strengthened my practical understanding of **AWS high availability, load balancing, monitoring, and automatic scaling**.

---

## 👤 Author

**Inga Mondliwa**

AWS Cloud / IT Infrastructure Portfolio