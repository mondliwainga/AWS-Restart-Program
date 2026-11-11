# 🛒 3D E-Commerce Platform — AWS Cloud Architecture Design

![AWS](https://img.shields.io/badge/AWS-Cloud%20Architecture-FF9900?logo=amazonaws)
![Status](https://img.shields.io/badge/Status-Design%20Complete-brightgreen)
![Role](https://img.shields.io/badge/Role-Cloud%20Practitioner-blue)

## 📌 Overview

A cloud architecture design for a **next-generation 3D e-commerce platform** where millions of global users can interact with 3D product models (furniture, gadgets, fashion) before purchasing.

This project was completed as part of a Cloud Practitioner portfolio, demonstrating the ability to design a **highly available, scalable, performant, secure, and cost-efficient** system on AWS.

**Core design philosophy:**  
> Separate *static 3D asset delivery* (CloudFront + S3) from *dynamic rendering compute* (EC2 GPU / Lambda), so each load type scales independently and cost-effectively.

---

## 🏗️ Architecture Diagram

![Architecture Diagram](docs/architecture-diagram.png)

> *Editable source: [`diagrams/architecture.drawio`](diagrams/architecture.drawio)*

---

## 🔧 AWS Services Used

| Category | Service | Purpose |
|---|---|---|
| **Networking & Delivery** | Route 53 | DNS resolution, latency-based routing, health checks |
| | CloudFront | Global CDN for 3D assets, static content, HTTPS enforcement |
| | Application Load Balancer | Cross-AZ traffic distribution, path-based routing |
| **Storage** | Amazon S3 | 3D model storage (.glb, .gltf, .obj), images, static files |
| **Compute** | EC2 Auto Scaling Group | Backend API servers (catalog, cart, orders) |
| | AWS Lambda | Serverless 3D rendering tasks, image processing, thumbnails |
| | API Gateway | Managed front door for Lambda functions |
| **Database** | Amazon RDS (Multi-AZ) | Product & customer data (relational, ACID) |
| | Amazon DynamoDB | Session data, cart, user preferences, 3D model metadata |
| **Monitoring** | CloudWatch | Metrics, logs, alarms, auto-scaling triggers |
| | Trusted Advisor | Cost, security, and fault-tolerance checks |
| **Security** | AWS WAF | SQL injection, XSS, bot protection |
| | AWS Shield | DDoS protection |
| | IAM | Least-privilege access control |
| | ACM | Free SSL/TLS certificates |

---

## ✅ How the Architecture Meets the 5 Requirements

### 1. High Availability (Target: 99.99% uptime)
- **Multi-AZ deployment** for EC2 and RDS with automatic failover
- **Route 53 health checks** route traffic away from unhealthy endpoints
- **Lambda** runs across multiple AZs automatically
- **S3** provides 11 nines of durability
- No single point of failure across ALB, Auto Scaling, and databases

### 2. Scalability (Target: 10x traffic spikes)
- **EC2 Auto Scaling Groups** scale horizontally based on CPU/request metrics
- **Lambda** scales from 0 to thousands of concurrent executions
- **DynamoDB on-demand** mode handles unpredictable traffic
- **CloudFront** absorbs traffic spikes at the edge, reducing origin load by up to 80%

### 3. Performance (Target: 3D load < 2s, page load < 1s)
- **CloudFront edge caching** at 450+ locations for sub-100ms delivery
- **DynamoDB + DAX** provides microsecond reads for session/cart data
- **Route 53 latency-based routing** directs users to the nearest region
- **Optimized EC2 instance sizing** for 3D processing workloads

### 4. Security
- **Encryption in transit:** ACM SSL/TLS on CloudFront and ALB
- **Encryption at rest:** S3 SSE, RDS encryption, DynamoDB encryption
- **Network security:** VPC with private subnets, Security Groups, NACLs
- **Application security:** AWS WAF + AWS Shield
- **Access control:** IAM roles with least privilege, no hardcoded credentials

### 5. Cost Optimization
- **Auto Scaling** ensures you only pay for what you use
- **Lambda** charges per invocation — zero cost when idle
- **S3 Intelligent-Tiering** and lifecycle policies move cold assets to Glacier
- **Spot Instances** in ASG for non-critical workloads (up to 70% savings)
- **CloudWatch + Trusted Advisor** identify waste and underutilized resources

**Estimated monthly cost (startup):** $2,000 – $8,000 depending on traffic.

---

## ⚖️ Design Trade-offs & Challenges

### Trade-offs

| Decision | Rationale |
|---|---|
| **EC2 vs. Fully Serverless** | EC2 handles persistent connections needed for 3D content serving. Fully serverless would reduce ops overhead but introduce cold-start latency and connection limits for real-time 3D interactions. |
| **RDS + DynamoDB (dual DB)** | Adds complexity but optimizes each data pattern: relational queries (products, orders) in RDS; high-speed key-value lookups (sessions, carts) in DynamoDB. |
| **Multi-AZ vs. Multi-Region** | Multi-AZ balances HA with cost. Multi-Region would improve global performance but significantly increases cost and operational complexity — a future consideration as the startup scales. |

### Challenges Addressed

1. **3D File Size Management** — Compression (Draco/meshopt), Level-of-Detail (LOD) models, and progressive loading.
2. **Cost Control at Scale** — S3 request batching, CloudFront cache optimization (high TTLs), Reserved Instances for baseline EC2 capacity.
3. **Security Compliance (PCI DSS)** — Tokenized payment processing via integrated gateways (e.g., Stripe) rather than storing card data directly.

---

## 📄 Full Solution Document

The complete 1–2 page solution document (service justifications, requirement mapping, trade-offs) is available here:

📥 **[Download Solution Document (PDF)](docs/solution-document.pdf)**

---

## 🚀 Key Takeaways

- **Managed services first:** Leveraged AWS managed services (S3, CloudFront, Lambda, DynamoDB, RDS) to minimize operational overhead.
- **Layered security:** Defense-in-depth with WAF, Shield, IAM, encryption at rest and in transit.
- **Cost-aware design:** Right-sizing, serverless where appropriate, Spot Instances, and storage tiering.
- **Growth-ready:** Architecture scales from thousands to millions of users without re-architecture.

---

## 👤 Author

**Inga Mondliwa**  
Cloud Practitioner | AWS Certified [in progress]  
[LinkedIn](https://www.linkedin.com/in/inga-mondliwa-a0734016a/) · [GitHub](https://github.com/mondliwainga)

---

