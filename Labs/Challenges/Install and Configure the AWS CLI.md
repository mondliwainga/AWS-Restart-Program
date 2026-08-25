<h1 align="center">AWS CLI Installation & IAM Configuration Lab</h1>

<p align="center">
  <strong>Installing and Configuring the AWS Command Line Interface on a Red Hat EC2 Instance</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/AWS-Cloud-orange" alt="AWS">
  <img src="https://img.shields.io/badge/AWS%20CLI-v2-orange" alt="AWS CLI">
  <img src="https://img.shields.io/badge/Amazon%20EC2-Red%20Hat-red" alt="Amazon EC2">
  <img src="https://img.shields.io/badge/IAM-Identity%20%26%20Access%20Management-blue" alt="IAM">
  <img src="https://img.shields.io/badge/Linux-Red%20Hat-red" alt="Linux">
  <img src="https://img.shields.io/badge/SSH-Secure%20Shell-green" alt="SSH">
</p>

<hr>

<h2>📌 Project Overview</h2>

<p>
This project was completed as part of an AWS hands-on lab focused on
installing and configuring the AWS Command Line Interface (AWS CLI) on a
Red Hat Linux Amazon EC2 instance.
</p>

<p>
The lab involved establishing an SSH connection to an existing EC2
instance, installing AWS CLI version 2, configuring the CLI to connect to
an AWS account, and using AWS CLI commands to interact with AWS Identity
and Access Management (IAM).
</p>

<p>
As part of the challenge, I used the AWS CLI to locate the customer-managed
<code>lab_policy</code> IAM policy and retrieve its policy version in JSON
format.
</p>

<hr>

<h2>🎯 Objectives</h2>

<ul>
  <li>Connect to a Red Hat EC2 instance using SSH.</li>
  <li>Install AWS CLI version 2 on Linux.</li>
  <li>Verify the AWS CLI installation.</li>
  <li>Configure AWS CLI to connect to an AWS account.</li>
  <li>Use AWS CLI to interact with IAM.</li>
  <li>List IAM users using the AWS CLI.</li>
  <li>Locate the customer-managed <code>lab_policy</code>.</li>
  <li>Retrieve the IAM policy version using AWS CLI.</li>
  <li>Save the policy information as a JSON file.</li>
</ul>

<hr>

<h2>🏗️ Architecture</h2>

<pre>
                    AWS Account
                         │
                         ▼
                  ┌─────────────┐
                  │     IAM     │
                  │             │
                  │ awsstudent  │
                  │ lab_policy  │
                  └──────▲──────┘
                         │
                    AWS CLI
                         │
                    SSH Connection
                         │
                  ┌──────▼──────┐
                  │ Amazon EC2  │
                  │ Red Hat     │
                  │ Linux       │
                  │             │
                  │ AWS CLI v2  │
                  └─────────────┘
</pre>

<hr>

<h2>☁️ AWS Services & Technologies</h2>

<table>
  <thead>
    <tr>
      <th>Service / Technology</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Amazon EC2</strong></td>
      <td>Provided the Red Hat Linux environment used for the lab.</td>
    </tr>
    <tr>
      <td><strong>AWS CLI</strong></td>
      <td>Provided command-line access to AWS services.</td>
    </tr>
    <tr>
      <td><strong>AWS IAM</strong></td>
      <td>Managed users, permissions, and IAM policies.</td>
    </tr>
    <tr>
      <td><strong>SSH</strong></td>
      <td>Provided secure remote access to the EC2 instance.</td>
    </tr>
    <tr>
      <td><strong>Red Hat Linux</strong></td>
      <td>Operating system running on the EC2 instance.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>1️⃣ Connect to the Red Hat EC2 Instance</h2>

<p>
I started the lab environment and accessed the AWS Management Console.
The lab provided an existing Red Hat Linux EC2 instance.
</p>

<p>
Because I was working from macOS, I used the provided PEM private key
and the built-in SSH client in Terminal.
</p>

<p>
The private key permissions were secured using:
</p>

<pre><code>chmod 400 labsuser.pem</code></pre>

<p>
I then established an SSH connection to the EC2 instance:
</p>

<pre><code>ssh -i labsuser.pem ec2-user@&lt;PUBLIC-IP&gt;</code></pre>

<p>
Once connected, I received a Linux shell prompt and was able to continue
working directly on the EC2 instance.
</p>

<hr>

<h2>2️⃣ Download the AWS CLI</h2>

<p>
The Red Hat instance did not have the AWS CLI pre-installed, so I
downloaded AWS CLI version 2 using <code>curl</code>.
</p>

<pre><code>curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"</code></pre>

<hr>

<h2>3️⃣ Extract the AWS CLI Installer</h2>

<p>
I extracted the AWS CLI installation package using:
</p>

<pre><code>unzip -u awscliv2.zip</code></pre>

<p>
This extracted the AWS CLI installation files into an
<code>aws</code> directory.
</p>

<hr>

<h2>4️⃣ Install AWS CLI Version 2</h2>

<p>
I ran the AWS CLI installation script using elevated permissions:
</p>

<pre><code>sudo ./aws/install</code></pre>

<p>
The installation completed successfully and the AWS CLI was added to
the system.
</p>

<hr>

<h2>5️⃣ Verify the AWS CLI Installation</h2>

<p>
I verified the installation by running:
</p>

<pre><code>aws --version</code></pre>

<p>
The command returned the installed AWS CLI version, confirming that the
AWS CLI was successfully installed on the Red Hat EC2 instance.
</p>

<h3>📸 AWS CLI Installation Verification</h3>

<img width="646" height="92" alt="image" src="https://github.com/user-attachments/assets/f272e9bd-3134-4bfd-8c6e-1b082ea9aae5" />

  
</p>

<hr>

<h2>6️⃣ Test the AWS CLI Help System</h2>

<p>
I tested the AWS CLI help system to verify that the CLI commands were
available.
</p>

<pre><code>aws help</code></pre>

<p>
The help interface displayed information about AWS CLI commands and
available services.
</p>

<p>
I exited the help interface by pressing:
</p>

<pre><code>q</code></pre>

<hr>

<h2>7️⃣ Inspect IAM Configuration</h2>

<p>
I opened the IAM console and navigated to the lab-provided
<code>awsstudent</code> user.
</p>

<p>
I inspected the permissions associated with the user, including the
customer-managed policy:
</p>

<pre><code>lab_policy</code></pre>

<p>
The lab policy controls which AWS actions the IAM user is permitted
to perform.
</p>

<hr>

<h2>8️⃣ Configure the AWS CLI</h2>

<p>
I configured the AWS CLI using the credentials provided by the lab.
</p>

<p>
The configuration command was:
</p>

<pre><code>aws configure</code></pre>

<p>
The following configuration was used:
</p>

<table>
  <tr>
    <th>Configuration</th>
    <th>Value</th>
  </tr>
  <tr>
    <td>AWS Access Key ID</td>
    <td>Provided by the lab</td>
  </tr>
  <tr>
    <td>AWS Secret Access Key</td>
    <td>Provided by the lab</td>
  </tr>
  <tr>
    <td>Default Region</td>
    <td><code>us-west-2</code></td>
  </tr>
  <tr>
    <td>Default Output Format</td>
    <td><code>json</code></td>
  </tr>
</table>

<p>
The access credentials were not included in this repository.
</p>

<hr>

<h2>9️⃣ Verify IAM Access Using AWS CLI</h2>

<p>
After configuring the AWS CLI, I tested the connection to AWS IAM by
running:
</p>

<pre><code>aws iam list-users</code></pre>

<p>
The command successfully returned IAM users in JSON format.
This confirmed that the AWS CLI was correctly configured and able to
communicate with AWS IAM.
</p>

<h3>📸 IAM Access Verification</h3>

<img width="752" height="188" alt="image" src="https://github.com/user-attachments/assets/02b381d5-5abe-4557-90a8-87896a192cf1" />

</p>

<hr>

<h2>🔟 Activity 1 Challenge</h2>

<p>
The challenge required retrieving the <code>lab_policy</code> IAM policy
document using only the AWS CLI rather than relying on the AWS
Management Console.
</p>

<p>
The challenge involved:
</p>

<ol>
  <li>Listing customer-managed IAM policies.</li>
  <li>Finding the <code>lab_policy</code> policy.</li>
  <li>Identifying the policy ARN.</li>
  <li>Identifying the default policy version.</li>
  <li>Retrieving the policy version.</li>
  <li>Saving the output as a JSON file.</li>
</ol>

<hr>

<h2>1️⃣1️⃣ List Customer-Managed Policies</h2>

<p>
I used the following AWS CLI command to list customer-managed IAM
policies:
</p>

<pre><code>aws iam list-policies --scope Local</code></pre>

<p>
The <code>--scope Local</code> option limits the results to customer-managed
policies.
</p>

<p>
From the results, I located:
</p>

<pre><code>lab_policy</code></pre>

<hr>

<h2>1️⃣2️⃣ Retrieve the Policy Version</h2>

<p>
After identifying the policy ARN and default version ID, I used the
<code>get-policy-version</code> command to retrieve the policy document.
</p>

<pre><code>aws iam get-policy-version \
--policy-arn arn:aws:iam::ACCOUNT-ID:policy/lab_policy \
--version-id v1</code></pre>

<p>
The command returned the policy version and its JSON document.
</p>

<hr>

<h2>1️⃣3️⃣ Save the Policy as JSON</h2>

<p>
I used output redirection to save the policy information to a JSON file:
</p>

<pre><code>aws iam get-policy-version \
--policy-arn arn:aws:iam::ACCOUNT-ID:policy/lab_policy \
--version-id v1 &gt; lab_policy.json</code></pre>

<p>
The resulting file was:
</p>

<pre><code>lab_policy.json</code></pre>

<p>
The file could then be inspected using:
</p>

<pre><code>cat lab_policy.json</code></pre>

<hr>

<h2>🔐 Security Considerations</h2>

<p>
AWS credentials were required to configure the AWS CLI during this lab.
However, sensitive credentials were not included in this GitHub repository.
</p>

<p>
The following should never be committed to GitHub:
</p>

<ul>
  <li>AWS Secret Access Keys</li>
  <li>AWS Access Keys</li>
  <li>Private SSH keys</li>
  <li><code>.pem</code> files</li>
  <li><code>.ppk</code> files</li>
  <li><code>~/.aws/credentials</code></li>
  <li>Temporary AWS credentials</li>
</ul>

<hr>

<h2>📁 Repository Structure</h2>

<pre>
aws-cli-installation-lab/
│
├── README.md
├── lab_policy.json
├── .gitignore
│
└── screenshots/
    ├── 03-aws-cli-version.png
    └── 07-iam-list-users.png
</pre>

<hr>

<h2>🧪 Commands Used</h2>

<pre><code># Navigate to Downloads
cd ~/Downloads

# Secure the SSH private key
chmod 400 labsuser.pem

# Connect to the EC2 instance
ssh -i labsuser.pem ec2-user@&lt;PUBLIC-IP&gt;

# Download AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Extract AWS CLI
unzip -u awscliv2.zip

# Install AWS CLI
sudo ./aws/install

# Verify installation
aws --version

# Display AWS CLI help
aws help

# Configure AWS CLI
aws configure

# List IAM users
aws iam list-users

# List customer-managed policies
aws iam list-policies --scope Local

# Retrieve the policy version
aws iam get-policy-version \
--policy-arn arn:aws:iam::ACCOUNT-ID:policy/lab_policy \
--version-id v1

# Save the policy document
aws iam get-policy-version \
--policy-arn arn:aws:iam::ACCOUNT-ID:policy/lab_policy \
--version-id v1 &gt; lab_policy.json</code></pre>

<hr>

<h2>🧠 What I Learned</h2>

<ul>
  <li>How to connect to a Linux EC2 instance using SSH.</li>
  <li>How to securely manage an SSH private key.</li>
  <li>How to install AWS CLI version 2 on Red Hat Linux.</li>
  <li>How to verify an AWS CLI installation.</li>
  <li>How to configure AWS CLI with IAM credentials.</li>
  <li>How IAM controls access to AWS services.</li>
  <li>How to use AWS CLI to interact with IAM.</li>
  <li>How to list IAM users from the command line.</li>
  <li>How to locate customer-managed IAM policies.</li>
  <li>How to retrieve IAM policy versions.</li>
  <li>How to save AWS CLI output to a JSON file.</li>
</ul>

<hr>

<h2>💡 Key Takeaways</h2>

<p>
This lab demonstrated that AWS resources can be managed not only through
the AWS Management Console but also through the AWS Command Line Interface.
</p>

<p>
Using the AWS CLI makes it possible to perform AWS operations directly
from a terminal and provides a foundation for scripting, automation,
and infrastructure management.
</p>

<p>
The challenge also demonstrated how IAM permissions determine what
operations an authenticated AWS CLI user can perform.
</p>

<hr>

<h2>🏆 Final Result</h2>

<p>
The AWS CLI was successfully installed on a Red Hat Linux EC2 instance,
configured with the lab AWS account, and used to interact with IAM.
</p>

<p>
The <code>lab_policy</code> policy was located and its policy version was
retrieved using AWS CLI commands.
</p>

<p align="center">
  <strong>EC2 → SSH → AWS CLI → IAM → lab_policy.json</strong>
</p>

<hr>

<h2>👨‍💻 Author</h2>

<p>
<strong>Inga Mondliwa</strong>
</p>

<p>
AWS Cloud & Technology Projects
</p>

<hr>

<h2>📚 Skills Demonstrated</h2>

<p align="center">
  <code>AWS CLI</code> •
  <code>Amazon EC2</code> •
  <code>IAM</code> •
  <code>Linux</code> •
  <code>SSH</code> •
  <code>Cloud Security</code> •
  <code>Command Line</code> •
  <code>AWS Troubleshooting</code>
</p>
