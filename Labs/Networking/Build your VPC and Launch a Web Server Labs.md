# ☁️ AWS Lab: Build Your VPC and Launch a Web Server

> **Scenario:** Designed and deployed a custom Virtual Private Cloud (VPC) for a Fortune 100 customer. The architecture includes public and private subnets across multiple Availability Zones, a NAT Gateway, custom route tables, and a web server running on an Amazon EC2 instance.

---

## 🏗️ Architecture Built
- **VPC:** `Lab VPC` (CIDR: `10.0.0.0/16`)
- **Public Subnets:** `Public Subnet 1` (`10.0.0.0/24`) & `Public Subnet 2` (`10.0.2.0/24`)
- **Private Subnets:** `Private Subnet 1` (`10.0.1.0/24`) & `Private Subnet 2` (`10.0.3.0/24`)
- **Gateways:** Internet Gateway (IGW) for public subnets, NAT Gateway for private subnets.
- **Security Group:** `Web Security Group` (Allows HTTP Port 80 and SSH Port 22).

---

## 🛠️ Implementation Steps

### 1. VPC & Subnet Creation
- Used the VPC Wizard to auto-generate the initial VPC, IGW, NAT Gateway, and the first set of public/private subnets.
- Manually created a second set of public and private subnets in a different Availability Zone for high availability.

### 2. Route Table Associations
- Associated `Public Subnet 2` with the **Public Route Table** (`0.0.0.0/0` -> IGW).
- Associated `Private Subnet 2` with the **Private Route Table** (`0.0.0.0/0` -> NAT Gateway).

### 3. EC2 Web Server Launch
- Launched a `t3.micro` Amazon Linux instance into `Public Subnet 2`.
- Assigned the `Web Security Group` and enabled auto-assign public IP.
- Provided a `bash` User Data script to automatically install Apache, PHP, and a custom web app.

---

## 🚨 Real-World Troubleshooting (Amazon Linux 2023)

During the initial launch, the provided User Data script failed. 

**Root Cause:** The AWS Quick Start defaulted to **Amazon Linux 2023** instead of Amazon Linux 2. The legacy script used the `yum` package manager and requested the `mysql` package, which does not exist on AL2023 (it uses `dnf` and `mariadb105`).

**The Fix:** I connected via SSH and manually installed the dependencies using the updated package manager commands:

```bash
# Updated commands for AL2023
sudo dnf install -y httpd php
sudo dnf install -y mariadb105
sudo mkdir -p /var/www/html
sudo unzip lab-app.zip -d /var/www/html/
sudo systemctl enable httpd
sudo systemctl start httpd
```
<img width="1248" height="511" alt="image" src="https://github.com/user-attachments/assets/39e452a6-0a59-4ddf-a439-d2ece9fb56d0" />
