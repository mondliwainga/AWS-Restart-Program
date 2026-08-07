# Introduction to AWS Identity and Access Management (IAM)

## Overview

In this lab, I worked with **AWS Identity and Access Management (IAM)** to understand how users, groups, and policies are used to control access to AWS resources.

I worked with three IAM users and three different groups, each with different permissions. I also tested the permissions by signing in as each user and trying to access Amazon S3 and Amazon EC2.

The main goal was to understand how permissions can be assigned based on a user's role and how AWS prevents users from accessing resources they are not authorized to use.

---

## What I Learned

During this lab, I learned how to:

- Create an IAM password policy
- View and manage IAM users
- Work with IAM user groups
- Understand IAM managed policies and inline policies
- Add users to groups
- Understand how permissions are inherited through group membership
- Test IAM permissions using different users
- Understand the difference between read-only and administrative permissions
- Troubleshoot an EC2 instance that was not showing because I was in the wrong AWS Region

---

# Task 1: Creating the IAM Password Policy

The first thing I did was create a stronger password policy for the AWS account.

I went to:

**IAM → Account settings → Password policy**

I changed the settings to:

| Setting | Value |
|---|---|
| Minimum password length | 10 characters |
| Password expiration | 90 days |
| Prevent password reuse | 5 passwords |
| Password expiration requires administrator reset | Disabled |
| Password requirements | Enabled |

After configuring the settings, I saved the changes.

This helped me understand that IAM password policies can be applied at the account level to improve the security of IAM users.

---

# Task 2: Exploring IAM Users and Groups

The lab provided three IAM users:

- `user-1`
- `user-2`
- `user-3`

It also provided three IAM groups:

- `S3-Support`
- `EC2-Support`
- `EC2-Admin`

I first checked `user-1` and noticed that the user did not have any permissions directly attached and was not initially part of a group.

I then explored the different groups and looked at the policies attached to each one.

---

## EC2-Support

The `EC2-Support` group had the AWS managed policy:

`AmazonEC2ReadOnlyAccess`

This policy allows a user to view EC2 resources but does not allow them to make changes.

I understood this as:

> I can view the EC2 resources, but I cannot change them.

---

## S3-Support

The `S3-Support` group had:

`AmazonS3ReadOnlyAccess`

This gives the user read-only access to Amazon S3.

The user can view buckets and their contents but cannot make changes to the resources.

---

## EC2-Admin

The `EC2-Admin` group had an inline policy called:

`EC2-Admin-Policy`

This policy gives the user permission to view EC2 resources and also start and stop EC2 instances.

This group therefore has more permissions than the `EC2-Support` group.

---

# Understanding IAM Policies

One of the things I learned from this lab was the basic structure of an IAM policy.

The main elements are:

- **Effect** – determines whether access is allowed or denied
- **Action** – defines what operation the user can perform
- **Resource** – defines which AWS resource the permission applies to

For example:

```json
{
    "Effect": "Allow",
    "Action": "ec2:DescribeInstances",
    "Resource": "*"
}
