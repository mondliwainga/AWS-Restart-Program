# ☁️ 3D E-Commerce Platform - AWS Sandbox Implementation

![AWS](https://img.shields.io/badge/AWS-Cloud%20Architecture-FF9900?logo=amazonaws)
![Status](https://img.shields.io/badge/Status-Completed-success)

## 📌 Project Overview
In this project, I built a practical, scaled-down implementation of a **3D E-Commerce Platform** architecture within an **AWS Sandbox Environment**. 

While my original enterprise design (documented in my full solution document) included global Multi-AZ deployments, heavy GPU rendering instances, and fully isolated IAM roles, I had to adapt my approach for this lab. I successfully built a functional **Proof of Concept (PoC)** that strictly complies with the sandbox restrictions (e.g., using `t3.micro` instances, the pre-provisioned `LabRole` for IAM, and Single-AZ databases).

## 🏗️ Architecture Implemented in the Sandbox

I successfully deployed and configured the following AWS services:
*   **Amazon S3:** I created a bucket to host static 3D assets (simulated with a `.glb` file).
*   **Amazon CloudFront:** I configured a CDN to securely deliver my S3 assets over HTTPS.
*   **Amazon EC2 & ALB:** I deployed a Linux backend server and placed it behind an Application Load Balancer to distribute incoming traffic.
*   **AWS Lambda:** I wrote and deployed a serverless API function, ensuring it used the allowed `LabRole`.
*   **Amazon DynamoDB:** I provisioned a NoSQL table to simulate storing shopping cart and session data.

---

## 📸 Implementation Proof (Screenshots)

Here is the visual proof of the environment I built and tested:

### 1. Amazon S3 (3D Asset Storage)
I created an S3 bucket and uploaded a dummy 3D model file (`model.glb`) to act as the origin for my platform's assets.
<img width="699" height="337" alt="Screenshot 2026-11-11 at 17 55 59" src="https://github.com/user-attachments/assets/be966314-1e11-48ae-b01e-ed0058f89611" />



### 2. Amazon EC2 & Application Load Balancer
I launched an EC2 instance (using the `LabInstanceProfile`) and configured an ALB to balance traffic to my backend server.
<img width="1059" height="233" alt="Screenshot 2026-11-11 at 18 07 29" src="https://github.com/user-attachments/assets/99b15912-f3dc-4011-b867-db576582b981" />


### 3. Backend Server Test
To verify my infrastructure was working, I copied the ALB's DNS endpoint into a web browser and successfully loaded the backend welcome page.
<img width="995" height="358" alt="Screenshot 2026-11-11 at 18 02 16" src="https://github.com/user-attachments/assets/44998b9c-1040-430b-9293-c4b041c6e034" />


### 4. AWS Lambda & Amazon DynamoDB
I created a serverless function using `LabRole` permissions and initialized a DynamoDB table to handle backend data.
<img width="1021" height="214" alt="Screenshot 2026-11-11 at 18 07 57" src="https://github.com/user-attachments/assets/e023c8ee-76a9-4d88-894e-844b13de9b84" />


---

## 🧹 Lab Cleanup
To ensure best practices in cloud cost management and to comply with sandbox rules, I meticulously cleaned up my environment after taking these screenshots:
*   I disabled and deleted my CloudFront distribution.
*   I terminated my EC2 instance and deleted the ALB and Target Group.
*   I emptied and deleted my S3 bucket.
*   I deleted the Lambda function and DynamoDB table.

## 💡 Key Takeaways
*   I learned how to navigate strict IAM, instance type, and multi-AZ restrictions in an AWS Sandbox environment.
*   I successfully mapped my enterprise architecture design to available sandbox services without losing the core business logic.
*   I practiced essential cloud hygiene by tearing down all infrastructure post-testing to avoid unnecessary costs.
