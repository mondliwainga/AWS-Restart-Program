# ☁️ AWS Network Troubleshooting Lab

> **Scenario:** A customer (Ana) deployed an Apache Web Server on an Amazon EC2 instance within a Virtual Private Cloud (VPC). She reported that she could not ping the instance and received an error when trying to load the webpage in her browser.

---

## 🛠️ Troubleshooting Process

### 1. EC2 Instance & Service Check
- Connected to the Amazon Linux EC2 instance using SSH.
- Checked the Apache status: `sudo systemctl status httpd.service` (Found it was running).
- Tested `http://<public-IP>` in the browser -> **Connection Timed Out.**

### 2. VPC Infrastructure Check
I investigated the VPC components to ensure routing was correct:
- **Internet Gateway:** ✅ Verified it was attached to the VPC.
- **Route Tables:** ✅ Verified `0.0.0.0/0` was routing to the IGW.
- **Network ACLs:** ✅ Verified the Default NACL allowed all inbound/outbound traffic.
- **Subnets:** ✅ Verified the subnet was public and auto-assigning IPs.

---

## 🔍 Root Cause Analysis

The issue was isolated to the **EC2 Security Group**. 
By default, the Security Group was overly restrictive. It only had an inbound rule allowing **SSH (Port 22)**. It was blocking:
1. Web traffic (**Port 80**)
2. Ping traffic (**ICMP**)

---

## ✅ Resolution

I modified the Security Group to include the missing inbound rules:

| Type | Protocol | Port Range | Source | Description |
| :--- | :--- | :--- | :--- | :--- |
| **HTTP** | TCP | 80 | `0.0.0.0/0` | Allow web traffic |
| **All ICMP - IPv4** | ICMP | N/A | `0.0.0.0/0` | Allow ping |

---

## 🎉 Verification

- **Browser Test:** Refreshed the browser at `http://<public-IP>` and the **Apache Test Page** loaded successfully.
- **Ping Test:** Executed a ping command from the local terminal -> **Successful replies received.**

- <br>
<div align="center">
  <img width="3016" height="1054" alt="image" src="https://github.com/user-attachments/assets/b1628d00-f196-43a9-b85c-7774e3919260" />

  <p><i>Verification: The Apache test page successfully loads in the browser.</i></p>
</div>
<br>

**Lab Status:** COMPLETE 🟢
