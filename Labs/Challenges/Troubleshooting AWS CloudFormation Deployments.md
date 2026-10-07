<h1>☁️ AWS CloudFormation Troubleshooting Lab</h1>

<h2>📌 Overview</h2>
<p>In this hands-on lab, I practiced troubleshooting an AWS CloudFormation deployment using the AWS CLI, Linux commands, EC2 logs, JMESPath queries, and CloudFormation drift detection.</p>
<p>The lab was based on a Café Proof of Concept (POC) environment where infrastructure was deployed using Infrastructure as Code (IaC).</p>
<p>I investigated a failed CloudFormation deployment, identified the root cause, corrected the CloudFormation template, successfully redeployed the infrastructure, detected configuration drift, and troubleshot a failed stack deletion.</p>

<h2>🎯 Objectives</h2>
<p>In this lab, I learned how to:</p>
<ul>
  <li>Use JMESPath to query JSON data.</li>
  <li>Deploy infrastructure using AWS CloudFormation.</li>
  <li>Troubleshoot CloudFormation deployment failures.</li>
  <li>Analyze EC2 cloud-init logs.</li>
  <li>Investigate CloudFormation stack events.</li>
  <li>Troubleshoot EC2 UserData.</li>
  <li>Detect CloudFormation drift.</li>
  <li>Troubleshoot failed stack deletion.</li>
  <li>Retain resources during CloudFormation deletion.</li>
</ul>

<h2>🛠️ AWS Services &amp; Technologies</h2>
<table>
  <thead>
    <tr>
      <th>Service / Technology</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>AWS CloudFormation</td><td>Infrastructure as Code</td></tr>
    <tr><td>AWS CLI</td><td>AWS resource management</td></tr>
    <tr><td>Amazon EC2</td><td>Web server and troubleshooting</td></tr>
    <tr><td>Amazon S3</td><td>Object storage</td></tr>
    <tr><td>Amazon VPC</td><td>Networking</td></tr>
    <tr><td>Security Groups</td><td>Network access control</td></tr>
    <tr><td>IAM</td><td>Identity and permissions</td></tr>
    <tr><td>Linux</td><td>Server administration</td></tr>
    <tr><td>SSH</td><td>Remote server access</td></tr>
    <tr><td>JMESPath</td><td>JSON querying</td></tr>
    <tr><td>Cloud-init</td><td>EC2 initialization</td></tr>
    <tr><td>Vim</td><td>Template editing</td></tr>
  </tbody>
</table>

<h2>🔎 Task 1 — JMESPath Queries</h2>
<p>I first practiced using JMESPath to query JSON data.</p>

<p>Query all desserts</p>
<pre><code>desserts</code></pre>

<p>Get the second dessert</p>
<pre><code>desserts[1]</code></pre>

<p>Get the name of the first dessert</p>
<pre><code>desserts[0].name</code></pre>

<p>Get the name and price</p>
<pre><code>desserts[0].[name,price]</code></pre>

<p>Get all dessert names</p>
<pre><code>desserts[].name</code></pre>

<p>Find the Carrot cake</p>
<pre><code>desserts[?name=='Carrot cake']</code></pre>

<p>I also used JMESPath with CloudFormation:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-resources \
--stack-name myStack \
--query 'StackResources[?ResourceType == `AWS::EC2::Instance`].LogicalResourceId'</code></pre>
<p>This helped me understand how to filter AWS CLI output and retrieve only the information I needed.</p>

<h2>☁️ Task 2 — Troubleshooting CloudFormation</h2>

<h3>1. Determine the AWS Region</h3>
<p>I used the EC2 Instance Metadata Service to determine the Region:</p>
<pre><code class="language-bash">curl http://169.254.169.254/latest/dynamic/instance-identity/document | grep region</code></pre>

<p>I then configured the AWS CLI:</p>
<pre><code class="language-bash">aws configure</code></pre>

<h3>2. Inspect the CloudFormation Template</h3>
<p>I inspected the template using:</p>
<pre><code class="language-bash">less template1.yaml</code></pre>
<p>The template contained the infrastructure configuration and EC2 UserData used to configure the web server.</p>

<h3>3. Create the CloudFormation Stack</h3>
<p>I created the stack using:</p>
<pre><code class="language-bash">aws cloudformation create-stack \
--stack-name myStack \
--template-body file://template1.yaml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters ParameterKey=KeyName,ParameterValue=vockey</code></pre>

<p>I monitored the resources using:</p>
<pre><code class="language-bash">watch -n 5 -d \
aws cloudformation describe-stack-resources \
--stack-name myStack \
--query 'StackResources[*].[ResourceType,ResourceStatus]' \
--output table</code></pre>

<h3>❌ CloudFormation Deployment Failure</h3>
<p>The CloudFormation stack failed during creation.</p>
<p>The stack eventually reached:</p>
<pre><code>CREATE_FAILED</code></pre>
<p>and then:</p>
<pre><code>ROLLBACK_COMPLETE</code></pre>

<p>I investigated the failed CloudFormation events:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-events \
--stack-name myStack \
--query "StackEvents[?ResourceStatus == 'CREATE_FAILED']"</code></pre>
<p>This helped me identify the resource responsible for the deployment failure.</p>

<h3>🔍 Investigating the EC2 Instance</h3>
<p>To investigate the failure, I recreated the stack using:</p>
<pre><code class="language-bash">aws cloudformation create-stack \
--stack-name myStack \
--template-body file://template1.yaml \
--capabilities CAPABILITY_NAMED_IAM \
--on-failure DO_NOTHING \
--parameters ParameterKey=KeyName,ParameterValue=vockey</code></pre>
<p>Using <code>DO_NOTHING</code> allowed the resources to remain available for investigation.</p>

<p>I then found the Web Server's public IP:</p>
<pre><code class="language-bash">aws ec2 describe-instances \
--filters "Name=tag:Name,Values='Web Server'" \
--query 'Reservations[].Instances[].[State.Name,PublicIpAddress]'</code></pre>
<p>I connected to the EC2 instance using SSH.</p>

<h3>🐧 Investigating Cloud-init</h3>
<p>After connecting to the EC2 instance, I checked the cloud-init logs:</p>
<pre><code class="language-bash">tail -50 /var/log/cloud-init-output.log</code></pre>

<p>The important error was:</p>
<pre><code>No package http available</code></pre>

<p>I also found:</p>
<pre><code>Failed running /var/lib/cloud/instance/scripts/part-001</code></pre>

<p>I then inspected the UserData script:</p>
<pre><code class="language-bash">sudo cat /var/lib/cloud/instance/scripts/part-001</code></pre>

<h3>🐛 Root Cause</h3>
<p>I discovered that the CloudFormation UserData script attempted to install Apache using:</p>
<pre><code class="language-bash">yum install -y http</code></pre>
<p>The correct package name is:</p>
<pre><code class="language-bash">yum install -y httpd</code></pre>

<p>The UserData script also used:</p>
<pre><code class="language-bash">#!/bin/bash -e</code></pre>
<p>The <code>-e</code> option causes the script to stop when a command fails.</p>

<h4>Failure Flow</h4>
<pre><code>CloudFormation
      ↓
EC2 Instance Created
      ↓
UserData Executed
      ↓
yum install -y http
      ↓
Package Not Found
      ↓
UserData Failed
      ↓
WaitCondition Not Signaled
      ↓
WaitCondition Timed Out
      ↓
CREATE_FAILED</code></pre>

<h3>🔧 Fixing the Template</h3>
<p>I opened the CloudFormation template:</p>
<pre><code class="language-bash">vim template1.yaml</code></pre>

<p>I changed:</p>
<pre><code class="language-bash">yum install -y http</code></pre>
<p>to:</p>
<pre><code class="language-bash">yum install -y httpd</code></pre>

<p>I saved the file:</p>
<pre><code>:wq</code></pre>

<p>I then verified the change:</p>
<pre><code class="language-bash">cat template1.yaml | grep httpd</code></pre>

<h3>✅ Successful Deployment</h3>
<p>After correcting the package name, I deleted the failed stack and recreated it using the corrected template.</p>
<p>The CloudFormation stack successfully reached:</p>
<pre><code>CREATE_COMPLETE</code></pre>

<p>I then tested the Web Server. The web page displayed:</p>
<pre><code>Hello from your web server!</code></pre>
<p>This confirmed that the EC2 UserData script executed successfully and the web server was configured correctly.</p>

<h2>🔄 Task 3 — CloudFormation Drift Detection</h2>
<p>I then practiced CloudFormation drift detection.</p>
<p>Drift occurs when an AWS resource is manually changed outside of CloudFormation.</p>
<p>For the exercise, I manually changed the Security Group SSH source from:</p>
<pre><code>0.0.0.0/0</code></pre>
<p>to:</p>
<pre><code>My IP</code></pre>
<p>This created a difference between the CloudFormation configuration and the actual AWS configuration.</p>

<h3>Upload an Object to S3</h3>
<p>I retrieved the S3 bucket name:</p>
<pre><code class="language-bash">bucketName=$( \
aws cloudformation describe-stacks \
--stack-name myStack \
--query "Stacks[*].Outputs[?OutputKey == 'BucketName'].[OutputValue]" \
--output text)</code></pre>

<p>I displayed the bucket name:</p>
<pre><code class="language-bash">echo "bucketName = "$bucketName</code></pre>

<p>I created a file:</p>
<pre><code class="language-bash">touch myfile</code></pre>

<p>I uploaded it to S3:</p>
<pre><code class="language-bash">aws s3 cp myfile s3://$bucketName/</code></pre>

<p>I verified the object:</p>
<pre><code class="language-bash">aws s3 ls $bucketName/</code></pre>

<h3>Detect Stack Drift</h3>
<p>I started drift detection:</p>
<pre><code class="language-bash">aws cloudformation detect-stack-drift \
--stack-name myStack</code></pre>

<p>I then checked the status:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-drift-detection-status \
--stack-drift-detection-id &lt;driftId&gt;</code></pre>

<p>The expected result was:</p>
<pre><code>StackDriftStatus: DRIFTED</code></pre>

<h3>Identify Drifted Resources</h3>
<p>I used:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-resources \
--stack-name myStack \
--query 'StackResources[*].[ResourceType,ResourceStatus,DriftInformation.StackResourceDriftStatus]' \
--output table</code></pre>

<p>The Security Group was identified as:</p>
<pre><code>MODIFIED</code></pre>

<p>The S3 bucket remained:</p>
<pre><code>IN_SYNC</code></pre>
<p>Adding an object to the S3 bucket did not change the bucket's CloudFormation resource properties, so the bucket itself did not become drifted.</p>

<h3>View Drift Details</h3>
<p>I used:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-resource-drifts \
--stack-name myStack \
--stack-resource-drift-status-filters MODIFIED</code></pre>
<p>This showed the difference between the expected CloudFormation configuration and the actual Security Group configuration.</p>

<h2>🗑️ Task 4 — Troubleshooting Stack Deletion</h2>
<p>I attempted to delete the CloudFormation stack:</p>
<pre><code class="language-bash">aws cloudformation delete-stack \
--stack-name myStack</code></pre>
<p>Most resources were successfully deleted.</p>
<p>However, the S3 bucket could not be deleted because it still contained:</p>
<pre><code>myfile</code></pre>

<p>The CloudFormation stack therefore entered:</p>
<pre><code>DELETE_FAILED</code></pre>

<h4>Why did this happen?</h4>
<p>S3 buckets must be empty before CloudFormation can delete them.</p>

<h3>🧩 Challenge — Retaining the S3 Bucket</h3>
<p>The challenge was to:</p>
<ul>
  <li>Delete the CloudFormation stack.</li>
  <li>Keep the S3 bucket.</li>
  <li>Keep the object inside the bucket.</li>
</ul>

<p>First, I found the S3 bucket's logical ID:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-resources \
--stack-name myStack \
--query "StackResources[?ResourceType == 'AWS::S3::Bucket'].LogicalResourceId" \
--output text</code></pre>

<p>I then used the returned logical ID with:</p>
<pre><code class="language-bash">aws cloudformation delete-stack \
--stack-name myStack \
--retain-resources &lt;BucketLogicalId&gt;</code></pre>

<p>For example, if the logical ID was <code>MyBucket</code>, I would use:</p>
<pre><code class="language-bash">aws cloudformation delete-stack \
--stack-name myStack \
--retain-resources MyBucket</code></pre>
<p>This allowed me to delete the CloudFormation stack while retaining the S3 bucket and its contents.</p>

<h2>💻 Important AWS CLI Commands</h2>

<p><strong>Create a Stack</strong></p>
<pre><code class="language-bash">aws cloudformation create-stack \
--stack-name myStack \
--template-body file://template1.yaml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters ParameterKey=KeyName,ParameterValue=vockey</code></pre>

<p><strong>View Stack Resources</strong></p>
<pre><code class="language-bash">aws cloudformation describe-stack-resources \
--stack-name myStack</code></pre>

<p><strong>View Failed Events</strong></p>
<pre><code class="language-bash">aws cloudformation describe-stack-events \
--stack-name myStack \
--query "StackEvents[?ResourceStatus == 'CREATE_FAILED']"</code></pre>

<p><strong>Delete a Stack</strong></p>
<pre><code class="language-bash">aws cloudformation delete-stack \
--stack-name myStack</code></pre>

<p><strong>Detect Drift</strong></p>
<pre><code class="language-bash">aws cloudformation detect-stack-drift \
--stack-name myStack</code></pre>

<p><strong>Check Drift Status</strong></p>
<pre><code class="language-bash">aws cloudformation describe-stack-drift-detection-status \
--stack-drift-detection-id &lt;driftId&gt;</code></pre>

<p><strong>View Drifted Resources</strong></p>
<pre><code class="language-bash">aws cloudformation describe-stack-resource-drifts \
--stack-name myStack \
--stack-resource-drift-status-filters MODIFIED</code></pre>

<p><strong>Retain a Resource</strong></p>
<pre><code class="language-bash">aws cloudformation delete-stack \
--stack-name myStack \
--retain-resources &lt;LogicalResourceId&gt;</code></pre>

<h2>🧠 Troubleshooting Process</h2>
<pre><code>Deploy CloudFormation Stack
          ↓
Monitor Stack Status
          ↓
Identify CREATE_FAILED
          ↓
Review CloudFormation Events
          ↓
Identify EC2 Instance
          ↓
SSH Into EC2
          ↓
Review cloud-init Logs
          ↓
Inspect UserData
          ↓
Identify Incorrect Package
          ↓
Correct CloudFormation Template
          ↓
Redeploy Stack
          ↓
Verify CREATE_COMPLETE
          ↓
Test Web Server
          ↓
Detect Configuration Drift
          ↓
Troubleshoot DELETE_FAILED
          ↓
Retain Required Resource</code></pre>

<h2>📚 What I Learned</h2>
<p>Through this lab, I learned how to:</p>
<ul>
  <li>Deploy infrastructure using CloudFormation.</li>
  <li>Manage AWS resources using the AWS CLI.</li>
  <li>Use JMESPath to filter AWS CLI output.</li>
  <li>Read CloudFormation stack events.</li>
  <li>Troubleshoot EC2 UserData failures.</li>
  <li>Analyze Linux cloud-init logs.</li>
  <li>Identify package installation errors.</li>
  <li>Understand the effect of <code>#!/bin/bash -e</code>.</li>
  <li>Understand CloudFormation WaitConditions.</li>
  <li>Detect infrastructure drift.</li>
  <li>Identify manually modified AWS resources.</li>
  <li>Troubleshoot failed CloudFormation deletions.</li>
  <li>Retain specific resources during stack deletion.</li>
  <li>Apply Infrastructure as Code troubleshooting techniques.</li>
</ul>

<h2>🏆 Skills Demonstrated</h2>
<ul>
  <li><strong>AWS:</strong> Amazon CloudFormation, Amazon EC2, Amazon S3, Amazon VPC, Security Groups, IAM, AWS CLI</li>
  <li><strong>Linux:</strong> SSH, Linux command line, Log analysis, Cloud-init, Package management, Vim</li>
  <li><strong>Cloud Engineering:</strong> Infrastructure as Code, Deployment troubleshooting, Root-cause analysis, Configuration drift detection, Resource lifecycle management, AWS automation</li>
</ul>



<h2>📈 Key Takeaway</h2>
<p>The biggest lesson I took from this lab was that troubleshooting cloud infrastructure requires following the problem through multiple layers.</p>
<p>I did not stop at the CloudFormation <code>CREATE_FAILED</code> message. I investigated the CloudFormation events, connected to the EC2 instance, reviewed the cloud-init logs, inspected the UserData script, and identified the actual root cause.</p>
<p>The root cause was using <code>http</code> instead of <code>httpd</code>.</p>
<p>After correcting the template, I successfully deployed the infrastructure and verified the installation.</p>
