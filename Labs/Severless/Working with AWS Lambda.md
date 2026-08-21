<h1 align="center">AWS Lambda – Sales Analysis Reporting</h1>

<p align="center">
  <strong>AWS Restart Program</strong><br>
  Serverless Sales Analysis Reporting Solution
</p>

<hr>

<h2>📌 Overview</h2>

<p>
In this lab, I built and configured a serverless sales analysis reporting
solution using <strong>AWS Lambda</strong>.
</p>

<p>
The solution retrieves sales data from a café MySQL database running on an
Amazon EC2 LAMP server, processes the data using AWS Lambda, and sends the
generated sales report through Amazon SNS.
</p>

<p>
I also configured AWS IAM, AWS Systems Manager Parameter Store, Lambda Layers,
Amazon VPC, Security Groups, Amazon CloudWatch Logs, and Amazon EventBridge
to create an automated serverless workflow.
</p>

<hr>

<h2>🏗️ Architecture</h2>

<pre>
                    Amazon EventBridge
                           │
                           │ Scheduled Trigger
                           ▼
                ┌──────────────────────┐
                │ salesAnalysisReport  │
                │       Lambda         │
                └──────────┬───────────┘
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
     ┌─────────────────┐      ┌─────────────────────────┐
     │ Parameter Store │      │ Data Extractor Lambda   │
     │                 │      │                         │
     │ DB Credentials  │      └────────────┬────────────┘
     └─────────────────┘                   │
                                           ▼
                                ┌────────────────────┐
                                │ EC2 LAMP Server    │
                                │                    │
                                │ MySQL Database     │
                                └─────────┬──────────┘
                                          │
                                          ▼
                                   Sales Data
                                          │
                                          ▼
                                ┌──────────────────┐
                                │    Amazon SNS    │
                                │  Sales Report    │
                                │      Topic       │
                                └─────────┬────────┘
                                          │
                                          ▼
                                    📧 Email Report
</pre>

<hr>

<h2>🎯 Objectives</h2>

<p>During this lab, I learned how to:</p>

<ul>
  <li>Configure IAM roles for AWS Lambda</li>
  <li>Create and configure a Lambda Layer</li>
  <li>Deploy Lambda functions using ZIP packages</li>
  <li>Connect Lambda to resources inside a VPC</li>
  <li>Allow Lambda to communicate with a MySQL database</li>
  <li>Retrieve database credentials from Systems Manager Parameter Store</li>
  <li>Configure Amazon SNS email notifications</li>
  <li>Invoke one Lambda function from another Lambda function</li>
  <li>Configure Lambda environment variables</li>
  <li>Use CloudWatch Logs to troubleshoot Lambda failures</li>
  <li>Create an EventBridge scheduled trigger</li>
  <li>Test and troubleshoot a serverless AWS architecture</li>
</ul>

<hr>

<h2>☁️ AWS Services Used</h2>

<table>
<thead>
<tr>
<th>AWS Service</th>
<th>Purpose</th>
</tr>
</thead>

<tbody>

<tr>
<td><strong>AWS Lambda</strong></td>
<td>Runs the serverless application code</td>
</tr>

<tr>
<td><strong>AWS IAM</strong></td>
<td>Controls permissions for Lambda functions</td>
</tr>

<tr>
<td><strong>Amazon EC2</strong></td>
<td>Hosts the café LAMP application and MySQL database</td>
</tr>

<tr>
<td><strong>Amazon VPC</strong></td>
<td>Provides network connectivity</td>
</tr>

<tr>
<td><strong>Systems Manager Parameter Store</strong></td>
<td>Stores database connection information</td>
</tr>

<tr>
<td><strong>Amazon SNS</strong></td>
<td>Sends the sales report through email</td>
</tr>

<tr>
<td><strong>Amazon CloudWatch</strong></td>
<td>Stores Lambda execution logs</td>
</tr>

<tr>
<td><strong>Amazon EventBridge</strong></td>
<td>Triggers the report automatically</td>
</tr>

<tr>
<td><strong>Lambda Layers</strong></td>
<td>Provides the PyMySQL dependency</td>
</tr>

</tbody>
</table>

<hr>

<h2>🔐 IAM Configuration</h2>

<h3>salesAnalysisReportRole</h3>

<p>
I used the <code>salesAnalysisReportRole</code> IAM role for the main
<code>salesAnalysisReport</code> Lambda function.
</p>

<p>The role provided permissions for:</p>

<ul>
  <li>Amazon SNS</li>
  <li>Systems Manager Parameter Store</li>
  <li>CloudWatch Logs</li>
  <li>Invoking another Lambda function</li>
</ul>

<h3>salesAnalysisReportDERole</h3>

<p>
I used the <code>salesAnalysisReportDERole</code> IAM role for the
<code>salesAnalysisReportDataExtractor</code> Lambda function.
</p>

<p>The role provided permissions for:</p>

<ul>
  <li>CloudWatch Logs</li>
  <li>Lambda VPC access</li>
</ul>

<h4>📸 IAM Screenshot</h4>

<img src="screenshots/01-iam-sales-analysis-role.png"
     alt="IAM Role"
     width="850">

<hr>

<h2>🧩 Lambda Layer</h2>

<p>
I created a Lambda Layer called:
</p>

<pre>pymysqlLibrary</pre>

<p>
The layer contains the PyMySQL library required by the data extractor
Lambda function to communicate with the MySQL database.
</p>

<table>
<tr>
<th>Setting</th>
<th>Value</th>
</tr>

<tr>
<td>Layer Name</td>
<td><code>pymysqlLibrary</code></td>
</tr>

<tr>
<td>Runtime</td>
<td>Python 3.9</td>
</tr>

<tr>
<td>Description</td>
<td>PyMySQL library modules</td>
</tr>

<tr>
<td>Version</td>
<td>1</td>
</tr>
</table>

<h4>📸 Lambda Layer</h4>

<img src="screenshots/02-pymysql-lambda-layer.png"
     alt="PyMySQL Lambda Layer"
     width="850">

<hr>

<h2>⚙️ Lambda Functions</h2>

<h3>1. salesAnalysisReportDataExtractor</h3>

<p>
This Lambda function connects to the café MySQL database and retrieves
sales information.
</p>

<p>The function uses:</p>

<pre>
dbUrl
dbName
dbUser
dbPassword
</pre>

<p>
These values are stored in AWS Systems Manager Parameter Store.
</p>

<h4>📸 Data Extractor Lambda</h4>

<img src="screenshots/03-data-extractor-lambda.png"
     alt="Data Extractor Lambda"
     width="850">

<hr>

<h3>2. salesAnalysisReport</h3>

<p>
The main Lambda function performs the following tasks:
</p>

<ol>
  <li>Retrieves database credentials from Parameter Store.</li>
  <li>Invokes <code>salesAnalysisReportDataExtractor</code>.</li>
  <li>Receives the sales data.</li>
  <li>Formats the information into a report.</li>
  <li>Publishes the report to Amazon SNS.</li>
  <li>SNS sends the report to my subscribed email address.</li>
</ol>

<h4>📸 Main Lambda Function</h4>

<img src="screenshots/10-sales-analysis-lambda.png"
     alt="Sales Analysis Lambda"
     width="850">

<hr>

<h2>🔑 Parameter Store</h2>

<p>
I used AWS Systems Manager Parameter Store to store the database
connection information.
</p>

<pre>
/cafe/dbUrl
/cafe/dbName
/cafe/dbUser
/cafe/dbPassword
</pre>

<p>
I did not publish the actual database password or other sensitive
credentials to GitHub.
</p>

<hr>

<h2>🌐 VPC Configuration</h2>

<p>
The <code>salesAnalysisReportDataExtractor</code> Lambda function required
network access to the MySQL database running on the café EC2 instance.
</p>

<table>
<tr>
<th>Configuration</th>
<th>Value</th>
</tr>

<tr>
<td>VPC</td>
<td><code>Cafe VPC</code></td>
</tr>

<tr>
<td>Subnet</td>
<td><code>Cafe Public Subnet 1</code></td>
</tr>

<tr>
<td>Security Group</td>
<td><code>CafeSecurityGroup</code></td>
</tr>

<tr>
<td>Database Port</td>
<td><code>3306</code></td>
</tr>
</table>

<h4>📸 VPC Configuration</h4>

<img src="screenshots/04-lambda-vpc-configuration.png"
     alt="Lambda VPC Configuration"
     width="850">

<hr>

<h2>🔒 MySQL Security Group</h2>

<p>
The Lambda function needed to communicate with the MySQL database using
TCP port <code>3306</code>.
</p>

<p>
I configured the appropriate security group rule to allow MySQL traffic.
</p>

<h4>📸 Security Group</h4>

<img src="screenshots/05-mysql-security-group.png"
     alt="MySQL Security Group"
     width="850">

<hr>

<h2>🧪 Testing the Data Extractor Lambda</h2>

<p>
Initially, my Lambda function failed when I tried to connect to the
database.
</p>

<p>The first error I encountered was:</p>

<pre>
Task timed out after 3.00 seconds
</pre>

<p>
This indicated that the Lambda function was unable to establish a
successful connection to the MySQL database within the default Lambda
timeout.
</p>

<p>I investigated:</p>

<ul>
  <li>Lambda VPC configuration</li>
  <li>Subnet configuration</li>
  <li>Security groups</li>
  <li>MySQL port 3306</li>
  <li>Database connectivity</li>
  <li>Lambda timeout settings</li>
</ul>

<hr>

<h2>🐛 Troubleshooting</h2>

<h3>Challenge 1 – Lambda Timeout</h3>

<p><strong>Problem:</strong></p>

<pre>
Task timed out after 3.00 seconds
</pre>

<p><strong>Cause:</strong></p>

<p>
The Lambda function could not establish a connection to the MySQL
database within the default 3-second timeout.
</p>

<p><strong>Troubleshooting:</strong></p>

<ul>
  <li>Checked VPC configuration</li>
  <li>Checked subnet configuration</li>
  <li>Checked security groups</li>
  <li>Checked MySQL port 3306</li>
  <li>Checked database connectivity</li>
</ul>

<p><strong>Solution:</strong></p>

<p>
I corrected the database security group configuration and allowed the
required MySQL traffic on TCP port <code>3306</code>.
</p>

<hr>

<h3>Challenge 2 – Incorrect Database Username</h3>

<p>
After resolving the networking issue, I received:
</p>

<pre>
1045 Access denied for user 'Root'
</pre>

<p>
CloudWatch Logs showed:
</p>

<pre>
ERROR: Failed to connect to the Cafe database.

Error Details:
1045 Access denied for user 'Root'
</pre>

<p><strong>Cause:</strong></p>

<p>
I had entered the MySQL username incorrectly.
</p>

<p>I used:</p>

<pre>Root</pre>

<p>instead of:</p>

<pre>root</pre>

<p>
The username was case-sensitive.
</p>

<p><strong>Solution:</strong></p>

<p>
I corrected the Lambda test event and changed the database username to
<code>root</code>.
</p>

<h4>📸 CloudWatch Troubleshooting</h4>

<img src="screenshots/07-cloudwatch-troubleshooting.png"
     alt="CloudWatch Troubleshooting"
     width="850">

<hr>

<h3>Challenge 3 – Lambda Timeout During Testing</h3>

<p>
I also encountered:
</p>

<pre>
Sandbox.Timedout
</pre>

<p>
with:
</p>

<pre>
Task timed out after 3.00 seconds
</pre>

<p>
I reviewed the Lambda execution logs in CloudWatch and checked the
function duration.
</p>

<p>
The Lambda function was using the default timeout of
<strong>3 seconds</strong>.
</p>

<p>
Because the function needed to initialize and connect to the database,
the default timeout could sometimes be exceeded.
</p>

<p><strong>Solution:</strong></p>

<p>
I increased the Lambda timeout when necessary and tested the function
again.
</p>

<hr>

<h3>Challenge 4 – Invalid SNS Endpoint</h3>

<p>
While testing the main Lambda function, I encountered:
</p>

<pre>
Invalid endpoint: https://sns..amazonaws.com
</pre>

<p>
The error occurred around:
</p>

<pre>
snsClient = boto3.client('sns', region_name=TOPIC_REGION)
</pre>

<p><strong>Cause:</strong></p>

<p>
The SNS Region was missing or incorrectly configured.
</p>

<p><strong>Troubleshooting:</strong></p>

<ul>
  <li>Checked the SNS topic ARN</li>
  <li>Checked Lambda environment variables</li>
  <li>Checked the AWS Region</li>
  <li>Checked SNS topic configuration</li>
</ul>

<p>
I verified that the SNS topic ARN contained the correct Region.
</p>

<pre>
arn:aws:sns:us-west-2:ACCOUNT_ID:salesAnalysisReportTopic
</pre>

<p><strong>Solution:</strong></p>

<p>
I verified that the Lambda environment variable
<code>topicARN</code> contained the correct SNS topic ARN.
</p>

<h4>📸 Environment Variable</h4>

<img src="screenshots/11-lambda-environment-variable.png"
     alt="Lambda Environment Variable"
     width="850">

<hr>

<h2>📧 Amazon SNS</h2>

<p>
I created an SNS topic called:
</p>

<pre>salesAnalysisReportTopic</pre>

<p>
with the display name:
</p>

<pre>SARTopic</pre>

<p>
I then created an email subscription and confirmed the subscription
through the AWS confirmation email.
</p>

<pre>
Lambda
   ↓
SNS Topic
   ↓
Email Subscription
   ↓
Daily Sales Analysis Report
</pre>

<h4>📸 SNS Topic</h4>

<img src="screenshots/08-sns-topic.png"
     alt="SNS Topic"
     width="850">

<h4>📸 SNS Subscription</h4>

<img src="screenshots/09-sns-subscription.png"
     alt="SNS Subscription"
     width="850">

<hr>

<h2>🧪 Final Lambda Test</h2>

<p>
After troubleshooting the networking, database authentication, Lambda
timeout, and SNS configuration issues, I successfully executed the
main Lambda function.
</p>

<p>The function returned:</p>

<pre>
{
  "statusCode": 200,
  "body": "\"Sale Analysis Report sent.\""
}
</pre>

<h4>📸 Successful Lambda Test</h4>

<img src="screenshots/12-final-lambda-test.png"
     alt="Successful Lambda Test"
     width="850">

<hr>

<h2>📧 Sales Analysis Email</h2>

<p>
After successfully executing the Lambda function, I received the
generated sales analysis report through email.
</p>

<p>
The report contained sales information retrieved from the café
database.
</p>

<h4>📸 Sales Analysis Email</h4>

<img src="screenshots/13-sales-analysis-email.png"
     alt="Sales Analysis Email"
     width="850">

<hr>

<h2>⏰ EventBridge Scheduled Trigger</h2>

<p>
I configured an EventBridge trigger called:
</p>

<pre>salesAnalysisReportDailyTrigger</pre>

<p>
The trigger automatically invokes the Lambda function according to a
schedule.
</p>

<h3>Production Schedule</h3>

<p>
The lab requires the report to run:
</p>

<pre>
Monday - Saturday
8:00 PM UTC
</pre>

<p>The production cron expression is:</p>

<pre>
cron(0 20 ? * MON-SAT *)
</pre>

<p>
EventBridge scheduled expressions use UTC.
</p>

<h4>📸 EventBridge Trigger</h4>

<img src="screenshots/14-eventbridge-trigger.png"
     alt="EventBridge Trigger"
     width="850">

<hr>

<h2>📊 Final Architecture</h2>

<pre>
                 EventBridge
                     │
                     │ Scheduled Event
                     ▼
          salesAnalysisReport Lambda
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
   Parameter Store       Data Extractor Lambda
   Database Details             │
                                ▼
                         EC2 MySQL Database
                                │
                                ▼
                           Sales Data
                                │
                                ▼
                         Amazon SNS Topic
                                │
                                ▼
                         Email Notification
</pre>

<hr>

<h2>📸 Screenshots</h2>

<table>
<thead>
<tr>
<th>#</th>
<th>Screenshot</th>
<th>Description</th>
</tr>
</thead>

<tbody>

<tr>
<td>1</td>
<td>01-iam-sales-analysis-role.png</td>
<td>IAM role and permissions</td>
</tr>

<tr>
<td>2</td>
<td>02-pymysql-lambda-layer.png</td>
<td>PyMySQL Lambda Layer</td>
</tr>

<tr>
<td>3</td>
<td>03-data-extractor-lambda.png</td>
<td>Data extractor Lambda</td>
</tr>

<tr>
<td>4</td>
<td>04-lambda-vpc-configuration.png</td>
<td>Lambda VPC configuration</td>
</tr>

<tr>
<td>5</td>
<td>05-mysql-security-group.png</td>
<td>MySQL security group</td>
</tr>

<tr>
<td>6</td>
<td>06-data-extractor-success.png</td>
<td>Successful data extractor test</td>
</tr>

<tr>
<td>7</td>
<td>07-cloudwatch-troubleshooting.png</td>
<td>CloudWatch troubleshooting</td>
</tr>

<tr>
<td>8</td>
<td>08-sns-topic.png</td>
<td>SNS topic</td>
</tr>

<tr>
<td>9</td>
<td>09-sns-subscription.png</td>
<td>SNS email subscription</td>
</tr>

<tr>
<td>10</td>
<td>10-sales-analysis-lambda.png</td>
<td>Main Lambda function</td>
</tr>

<tr>
<td>11</td>
<td>11-lambda-environment-variable.png</td>
<td>Lambda environment variable</td>
</tr>

<tr>
<td>12</td>
<td>12-final-lambda-test.png</td>
<td>Successful final test</td>
</tr>

<tr>
<td>13</td>
<td>13-sales-analysis-email.png</td>
<td>Sales report email</td>
</tr>

<tr>
<td>14</td>
<td>14-eventbridge-trigger.png</td>
<td>EventBridge scheduled trigger</td>
</tr>

</tbody>
</table>

<hr>

<h2>📁 Project Structure</h2>

<pre>
AWS-Restart-Program/
│
├── Lambda/
│   └── Working-with-AWS-Lambda/
│       │
│       ├── README.md
│       │
│       └── screenshots/
│           ├── 01-iam-sales-analysis-role.png
│           ├── 02-pymysql-lambda-layer.png
│           ├── 03-data-extractor-lambda.png
│           ├── 04-lambda-vpc-configuration.png
│           ├── 05-mysql-security-group.png
│           ├── 06-data-extractor-success.png
│           ├── 07-cloudwatch-troubleshooting.png
│           ├── 08-sns-topic.png
│           ├── 09-sns-subscription.png
│           ├── 10-sales-analysis-lambda.png
│           ├── 11-lambda-environment-variable.png
│           ├── 12-final-lambda-test.png
│           ├── 13-sales-analysis-email.png
│           └── 14-eventbridge-trigger.png
</pre>

<hr>

<h2>💡 What I Learned</h2>

<p>
This lab gave me practical experience building and troubleshooting a
serverless AWS architecture.
</p>

<p>I learned how to:</p>

<ul>
  <li>Configure IAM permissions for Lambda</li>
  <li>Create and use Lambda Layers</li>
  <li>Configure Lambda VPC access</li>
  <li>Connect Lambda to an EC2-hosted MySQL database</li>
  <li>Configure security groups for database access</li>
  <li>Store configuration information in Parameter Store</li>
  <li>Use SNS for email notifications</li>
  <li>Use CloudWatch Logs to troubleshoot application errors</li>
  <li>Diagnose database authentication problems</li>
  <li>Troubleshoot Lambda timeout errors</li>
  <li>Configure Lambda environment variables</li>
  <li>Configure EventBridge scheduled automation</li>
  <li>Test an end-to-end serverless application</li>
</ul>

<p>
One of the most valuable parts of the lab was troubleshooting real AWS
errors instead of simply following the configuration steps.
</p>

<p>
I used CloudWatch Logs to identify the root causes of the failures and
corrected:
</p>

<ul>
  <li>Database network connectivity</li>
  <li>MySQL security group configuration</li>
  <li>Incorrect database username</li>
  <li>Lambda timeout</li>
  <li>SNS Region configuration</li>
  <li>Lambda environment variables</li>
</ul>

<hr>

<h2>🏆 Final Result</h2>

<p>
I successfully built, configured, tested, and troubleshot a serverless
sales reporting solution using:
</p>

<p align="center">

<strong>AWS Lambda</strong> •
<strong>AWS IAM</strong> •
<strong>Amazon EC2</strong> •
<strong>Amazon VPC</strong> •
<strong>MySQL</strong> •
<strong>Parameter Store</strong> •
<strong>Amazon SNS</strong> •
<strong>CloudWatch</strong> •
<strong>EventBridge</strong> •
<strong>Lambda Layers</strong>

</p>

<p>
The final workflow successfully retrieves sales information from the
café database, processes the information using Lambda, and sends the
generated sales analysis report through email.
</p>

<hr>

<h2>🔒 Security</h2>

<p>
I did not upload any sensitive information to GitHub.
</p>

<ul>
  <li>AWS Access Keys</li>
  <li>AWS Secret Keys</li>
  <li>Database passwords</li>
  <li>Private credentials</li>
  <li>Sensitive account information</li>
</ul>

<p>
Any screenshots containing sensitive information should be redacted
before being uploaded to GitHub.
</p>

<hr>

<h2>📝 GitHub Commit</h2>

<p>Suggested commit message:</p>

<pre>
Add AWS Lambda sales analysis reporting lab
</pre>

<p>Suggested screenshot commit:</p>

<pre>
Add Lambda lab screenshots and troubleshooting documentation
</pre>

<hr>

<h2>📚 AWS Concepts Demonstrated</h2>

<p align="center">

<code>Serverless Computing</code>
<code>AWS Lambda</code>
<code>IAM</code>
<code>Lambda Layers</code>
<code>Lambda VPC Access</code>
<code>Amazon EC2</code>
<code>MySQL</code>
<code>Amazon VPC</code>
<code>Security Groups</code>
<code>Parameter Store</code>
<code>Amazon SNS</code>
<code>CloudWatch</code>
<code>EventBridge</code>
<code>AWS CLI</code>
<code>Troubleshooting</code>

</p>

<hr>

<p align="center">
  <strong>☁️ AWS Restart Program</strong><br>
  Hands-on Cloud Computing &amp; Serverless Development
</p>
