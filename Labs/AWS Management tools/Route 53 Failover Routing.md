# AWS Route 53 Failover Routing

## 📌 Lab Overview

I configured **Amazon Route 53 failover routing** to provide high availability for a web application.

I worked with two Amazon EC2 web servers deployed across different Availability Zones. I configured Route 53 to direct normal traffic to the primary web server and automatically route traffic to the secondary server when the primary server becomes unhealthy.

I also simulated a web server failure by stopping the primary EC2 instance and verified that Route 53 detected the failure and redirected traffic to the secondary instance.

---

## 🏗️ Architecture

```text
                         User
                           |
                           v
                    Amazon Route 53
                           |
                    Failover Routing
                    /               \
                   /                 \
                  v                   v
        Primary Record          Secondary Record
               |                       |
               v                       v
        CafeInstance1            CafeInstance2
        Availability Zone 1      Availability Zone 2
               |
               v
        Route 53 Health Check
               |
        Healthy / Unhealthy
```

### Normal operation

```text
User
  |
  v
Route 53
  |
  v
CafeInstance1
```

### After primary failure

```text
User
  |
  v
Route 53
  |
  v
CafeInstance2
```

---

## ☁️ AWS Services I Used

### Amazon EC2

I used two EC2 instances as web servers:

* **CafeInstance1** — Primary web server
* **CafeInstance2** — Secondary web server

I used separate Availability Zones to improve the availability and resilience of the application.

### Amazon Route 53

I used Route 53 for:

* DNS resolution
* Health monitoring
* Failover routing
* Directing users to the healthy web server

### Route 53 Health Checks

I created a health check for the primary web server using:

* Endpoint monitoring
* Public IPv4 address
* `/cafe` application path
* 10-second request interval
* Failure threshold of 2

### Amazon SNS

I configured an SNS notification to send an email alert when the primary Route 53 health check became unhealthy.

---

## ⚙️ Configuration

### Primary Health Check

| Configuration     | Value                    |
| ----------------- | ------------------------ |
| Name              | `Primary-Website-Health` |
| Monitor           | Endpoint                 |
| Endpoint Type     | IP Address               |
| Path              | `/cafe`                  |
| Request Interval  | 10 seconds               |
| Failure Threshold | 2                        |
| Notification      | Amazon SNS               |

### Primary DNS Record

| Configuration  | Value                    |
| -------------- | ------------------------ |
| Record Name    | `www`                    |
| Record Type    | A                        |
| Routing Policy | Failover                 |
| Failover Type  | Primary                  |
| TTL            | 15 seconds               |
| Health Check   | `Primary-Website-Health` |
| Record ID      | `FailoverPrimary`        |

### Secondary DNS Record

| Configuration  | Value               |
| -------------- | ------------------- |
| Record Name    | `www`               |
| Record Type    | A                   |
| Routing Policy | Failover            |
| Failover Type  | Secondary           |
| TTL            | 15 seconds          |
| Health Check   | None                |
| Record ID      | `FailoverSecondary` |

---

## 🧪 Failover Testing

I tested the failover functionality by intentionally stopping the primary EC2 instance.

### Test procedure

1. I confirmed that both EC2 instances were running.
2. I verified that the primary café website was accessible.
3. I created a Route 53 health check for the primary server.
4. I created primary and secondary Route 53 failover records.
5. I verified DNS resolution to the primary server.
6. I stopped the primary EC2 instance to simulate a server failure.
7. I waited for the Route 53 health check to report the primary endpoint as unhealthy.
8. I verified that traffic was redirected to the secondary EC2 instance.
9. I confirmed that the café application remained accessible from the secondary Availability Zone.
10. I verified that an SNS notification was generated for the failed health check.

---

## ✅ Results

I successfully configured Route 53 to provide automatic failover between two EC2 web servers.

Under normal conditions, traffic was directed to:

**Route 53 → CafeInstance1**

After I stopped the primary server, traffic was redirected to:

**Route 53 → CafeInstance2**

This demonstrated how Route 53 health checks and failover routing can help maintain application availability when a primary server fails.

---

## 🎯 What I Learned

Through this project, I gained practical experience with:

* Amazon Route 53 DNS
* Route 53 failover routing
* Route 53 health checks
* Amazon EC2
* Multi-AZ architecture
* DNS-based high availability
* Amazon SNS notifications
* Failure simulation and recovery
* Monitoring application availability
* Designing fault-tolerant AWS architectures

---

## 💡 Key Takeaway

I learned that relying on a single EC2 instance can create a single point of failure. By deploying web servers across multiple Availability Zones and using Route 53 health checks with failover routing, I can automatically redirect traffic when the primary server becomes unavailable.

This project gave me practical experience with designing **highly available and fault-tolerant architectures on AWS**.

---

## 🛠️ Technologies

* AWS
* Amazon EC2
* Amazon Route 53
* Route 53 Health Checks
* Amazon SNS
* DNS
* HTTP
* Linux
* LAMP Stack

---

## 📚 AWS Concepts I Practiced

* High Availability
* Fault Tolerance
* DNS Failover
* Health Monitoring
* Multi-AZ Deployment
* Automated Traffic Routing
* Infrastructure Resilience

---

## 👤 Author

**Inga Mondliwa**

AWS Cloud / IT Labs
