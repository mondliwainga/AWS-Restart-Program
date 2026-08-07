# Data Protection Using Encryption with AWS KMS

## Overview

In this lab, I worked with **AWS Key Management Service (AWS KMS)** to understand how encryption can be used to protect sensitive data.

I created a customer-managed KMS key, configured an EC2 File Server, connected to the server using AWS Systems Manager Session Manager, and used the AWS Encryption CLI to encrypt and decrypt a file.

The main goal of the lab was to understand the difference between **plaintext and ciphertext**, and how AWS KMS can be used to protect encryption keys.

---

## What I Learned

During this lab, I learned how to:

- Create a customer-managed AWS KMS key
- Configure a symmetric KMS key
- Assign key administrators and key users
- Connect to an EC2 instance using Session Manager
- Configure AWS CLI credentials
- Install and use the AWS Encryption CLI
- Encrypt a file using AWS KMS
- Decrypt an encrypted file
- Understand plaintext and ciphertext
- Use an encryption context
- Troubleshoot encryption-related errors

---

# Task 1: Creating the KMS Key

I started the lab by opening **AWS Key Management Service (KMS)** from the AWS Console.

I navigated to:

**KMS → Customer managed keys → Create key**

For the key type, I selected:

**Symmetric**

A symmetric key uses the same key for encryption and decryption.

### Key Configuration

I configured the key with the following details:

| Setting | Value |
|---|---|
| Key Type | Symmetric |
| Alias | `MyKMSKey` |
| Description | Key used to encrypt and decrypt data files. |
| Key Administrator | `voclabs` |
| Key User | `voclabs` |

After reviewing the configuration, I created the key.

The final key structure was:

```text
AWS KMS
   |
   └── MyKMSKey
