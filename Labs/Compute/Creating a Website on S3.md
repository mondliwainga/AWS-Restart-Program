# Creating a Static Website on Amazon S3 using AWS CLI

## Project Overview

In this lab, I learned how to host a static website on Amazon S3 by using the AWS Command Line Interface (AWS CLI). I also created an IAM user with Amazon S3 permissions, uploaded website files to an S3 bucket, enabled static website hosting, and automated future website updates by creating a Bash deployment script.

This project helped me gain hands-on experience with AWS services, including Amazon EC2, Amazon S3, IAM, AWS Systems Manager (SSM), and the AWS CLI.

---

## Objectives

By completing this project, I was able to:

- Connect to an Amazon EC2 instance using AWS Systems Manager Session Manager.
- Configure the AWS CLI with AWS credentials.
- Create an Amazon S3 bucket using AWS CLI.
- Upload static website files to Amazon S3.
- Enable static website hosting.
- Make the website publicly accessible.
- Create a Bash script to automate future website updates.

---

## AWS Services Used

- Amazon EC2
- Amazon S3
- AWS Identity and Access Management (IAM)
- AWS Systems Manager (SSM)
- AWS CLI

---

## Project Workflow

### Step 1 – Connect to the EC2 Instance

I connected to the Amazon Linux EC2 instance using AWS Systems Manager Session Manager and switched to the EC2 user.

```bash
sudo su -l ec2-user
pwd
```

---

### Step 2 – Configure AWS CLI

I configured the AWS CLI with the credentials provided for the lab.

```bash
aws configure
```

---

### Step 3 – Create an S3 Bucket

I created a unique S3 bucket in the **us-west-2** Region.

```bash
aws s3api create-bucket \
--bucket <my-bucket-name> \
--region us-west-2 \
--create-bucket-configuration LocationConstraint=us-west-2
```

---

### Step 4 – Extract Website Files

The website files were provided in a compressed archive. I extracted them and confirmed that all required files were available.

```bash
cd ~/sysops-activity-files
tar xvzf static-website-v2.tar.gz
cd static-website
ls
```

The extracted folder contained:

- index.html
- css/
- images/

---

### Step 5 – Enable Static Website Hosting

I configured the S3 bucket so that **index.html** would be used as the website's home page.

```bash
aws s3 website s3://<my-bucket-name>/ \
--index-document index.html
```

---

### Step 6 – Upload Website Files

Next, I uploaded all website files from my local directory to the S3 bucket.

```bash
aws s3 cp \
/home/ec2-user/sysops-activity-files/static-website/ \
s3://<my-bucket-name>/ \
--recursive \
--acl public-read
```

---

### Step 7 – Verify the Upload

To confirm that the files were uploaded successfully, I listed the contents of the bucket.

```bash
aws s3 ls s3://<my-bucket-name>
```

I also opened the **Bucket Website Endpoint** from the AWS Management Console and confirmed that the website loaded successfully.

---

### Step 8 – Automate Website Updates

To avoid typing the upload command every time I made changes to the website, I created a Bash deployment script.

First, I created an empty file.

```bash
touch update-website.sh
```

Then I edited the file using the VI editor.

```bash
vi update-website.sh
```

The script contains the following:

```bash
#!/bin/bash

aws s3 cp \
/home/ec2-user/sysops-activity-files/static-website/ \
s3://<my-bucket-name>/ \
--recursive \
--acl public-read
```

I made the script executable.

```bash
chmod +x update-website.sh
```

---

### Step 9 – Update the Website

After modifying the **index.html** file by changing several background colors, I deployed the updated website by running:

```bash
./update-website.sh
```

The updated files were copied to Amazon S3, and after refreshing the browser, the changes appeared on the website.

---

## Skills Demonstrated

During this project, I gained practical experience with:

- Using AWS CLI commands
- Managing Amazon S3 buckets
- Hosting static websites on Amazon S3
- Working with IAM permissions
- Connecting to EC2 instances using Session Manager
- Using Linux terminal commands
- Writing and executing Bash scripts
- Automating deployment tasks

---

## Key Takeaways

This project showed me how simple it is to host a static website on Amazon S3 without using a traditional web server. I also learned how automation can save time by creating a reusable deployment script for future website updates. Working through this lab strengthened my understanding of AWS CLI, Linux commands, and cloud storage services.

---

## Author

**Inga Mondliwa**

AWS Cloud Practitioner | Cloud & Linux Enthusiast
