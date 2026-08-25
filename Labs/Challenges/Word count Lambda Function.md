<h1 align="center">AWS Lambda Word Count Challenge</h1>

<p align="center">
  <strong>Serverless File Processing with Amazon S3, AWS Lambda and Amazon SNS</strong>
</p>

<hr>

<h2>📌 Project Overview</h2>

<p>
This project was completed as part of an AWS Lambda Challenge Lab.
The objective was to build a serverless application that automatically counts
the number of words in a text file uploaded to an Amazon S3 bucket and sends
the result to an email address using Amazon SNS.
</p>

<p>
The final solution uses:
</p>

<p align="center">
  <strong>Amazon S3 → AWS Lambda → Amazon SNS → Email</strong>
</p>

<p>
When a text file is uploaded to the S3 bucket, the upload event automatically
triggers the Lambda function. Lambda retrieves the file, counts the words,
and publishes the result to an SNS topic. SNS then sends the result to a
subscribed email address.
</p>

<hr>

<h2>🎯 Lab Objectives</h2>

<ul>
  <li>Create an AWS Lambda function using Python.</li>
  <li>Count the number of words in a text file.</li>
  <li>Create an Amazon S3 bucket.</li>
  <li>Configure S3 to automatically trigger Lambda when a file is uploaded.</li>
  <li>Create an Amazon SNS topic.</li>
  <li>Configure an email subscription to the SNS topic.</li>
  <li>Publish the word count result to SNS.</li>
  <li>Test the solution using multiple text files.</li>
  <li>Troubleshoot the solution using CloudWatch logs and AWS service configuration.</li>
</ul>

<hr>

<h2>🏗️ Architecture</h2>

<pre>
                    Text File Upload
                           │
                           ▼
                   ┌──────────────┐
                   │  Amazon S3   │
                   │    Bucket    │
                   └──────┬───────┘
                          │
                    S3 Event
                 Object Created
                          │
                          ▼
                   ┌──────────────┐
                   │ AWS Lambda   │
                   │ Word Counter │
                   └──────┬───────┘
                          │
                    Count Words
                          │
                          ▼
                   ┌──────────────┐
                   │  Amazon SNS  │
                   │    Topic     │
                   └──────┬───────┘
                          │
                          ▼
                       📧 Email
</pre>

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
      <td>Processes the uploaded text file and counts the words.</td>
    </tr>
    <tr>
      <td><strong>Amazon S3</strong></td>
      <td>Stores text files and triggers Lambda when a file is uploaded.</td>
    </tr>
    <tr>
      <td><strong>Amazon SNS</strong></td>
      <td>Sends the word count result by email.</td>
    </tr>
    <tr>
      <td><strong>AWS IAM</strong></td>
      <td>Provides Lambda with the required permissions.</td>
    </tr>
    <tr>
      <td><strong>Amazon CloudWatch</strong></td>
      <td>Stores Lambda execution logs and assists with troubleshooting.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>🚀 Step-by-Step Implementation</h2>

<h3>1. Launch the Lab Environment</h3>

<p>
I started the AWS challenge lab and waited for the lab environment to become
ready.
</p>

<p>
Once the lab status showed:
</p>

<pre>Lab status: ready</pre>

<p>
I opened the AWS Management Console.
All resources were created in the same AWS Region as recommended by the lab.
</p>

<hr>

<h3>2. Create the Amazon SNS Topic</h3>

<p>
I opened Amazon SNS from the AWS Management Console.
</p>

<p>
I navigated to:
</p>

<pre>SNS → Topics → Create topic</pre>

<p>
I created a Standard SNS topic named:
</p>

<pre>WordCountTopic</pre>

<p>
After creating the topic, I copied the SNS Topic ARN because it would be
required later when configuring the Lambda function.
</p>


<hr>

<h3>3. Create the SNS Email Subscription</h3>

<p>
I opened the <strong>WordCountTopic</strong> and selected
<strong>Create subscription</strong>.
</p>

<p>The subscription was configured as:</p>

<pre>
Protocol: Email
Endpoint: My email address
</pre>

<p>
After creating the subscription, the status initially showed:
</p>

<pre>Pending confirmation</pre>

<p>
I opened the confirmation email from Amazon SNS and confirmed the
subscription.
</p>

<p>
The subscription status then changed to:
</p>

<pre>Confirmed</pre>

<h4>Screenshot</h4>



<hr>

<h3>4. Create the AWS Lambda Function</h3>

<p>
I opened AWS Lambda and selected:
</p>

<pre>Functions → Create function</pre>

<p>
I selected <strong>Author from scratch</strong>.
</p>

<p>The function was configured as:</p>

<table>
  <tr>
    <th>Setting</th>
    <th>Value</th>
  </tr>
  <tr>
    <td>Function Name</td>
    <td><code>WordCountFunction</code></td>
  </tr>
  <tr>
    <td>Runtime</td>
    <td>Python 3.x</td>
  </tr>
  <tr>
    <td>Execution Role</td>
    <td><code>LambdaAccessRole</code></td>
  </tr>
</table>

<hr>

<h3>5. Configure the IAM Role</h3>

<p>
The challenge lab provided an existing IAM role called:
</p>

<pre>LambdaAccessRole</pre>

<p>
Because the lab did not allow the creation of a new IAM role, I selected
the existing role when creating the Lambda function.
</p>

<p>
The role provided the permissions required for Lambda to interact with
services such as:
</p>

<ul>
  <li>Amazon S3</li>
  <li>Amazon SNS</li>
  <li>Amazon CloudWatch</li>
  <li>AWS Lambda execution</li>
</ul>

<hr>

<h3>6. Write the Lambda Function</h3>

<p>
The Lambda function was written in Python using Boto3.
</p>

<p>
The function performs the following tasks:
</p>

<ol>
  <li>Receives the S3 event.</li>
  <li>Gets the S3 bucket name.</li>
  <li>Gets the uploaded file name.</li>
  <li>Retrieves the file from S3.</li>
  <li>Reads the contents of the file.</li>
  <li>Counts the words.</li>
  <li>Creates the result message.</li>
  <li>Publishes the result to SNS.</li>
  <li>Returns the result.</li>
</ol>

<h4>Lambda Code</h4>

<pre><code>import boto3
import urllib.parse

s3 = boto3.client('s3')
sns = boto3.client('sns')

SNS_TOPIC_ARN = "YOUR_SNS_TOPIC_ARN"


def lambda_handler(event, context):

    # Get bucket name and object name from the S3 event
    bucket_name = event['Records'][0]['s3']['bucket']['name']
    object_key = event['Records'][0]['s3']['object']['key']

    # Decode the S3 object key
    object_key = urllib.parse.unquote_plus(object_key)

    # Get the file from S3
    response = s3.get_object(
        Bucket=bucket_name,
        Key=object_key
    )

    # Read the file contents
    file_contents = response['Body'].read().decode('utf-8')

    # Count words
    word_count = len(file_contents.split())

    # Create the message
    message = f"The word count in the {object_key} file is {word_count}."

    # Publish the result to SNS
    sns.publish(
        TopicArn=SNS_TOPIC_ARN,
        Subject="Word Count Result",
        Message=message
    )

    return {
        'statusCode': 200,
        'body': message
    }</code></pre>

<p>
The SNS Topic ARN was inserted into the Lambda function where indicated.
</p>



<hr>

<h3>7. Deploy the Lambda Function</h3>

<p>
After entering the Python code, I selected:
</p>

<pre>Deploy</pre>

<p>
This saved and deployed the Lambda function.
The function was now ready to be invoked by Amazon S3.
</p>

<hr>

<h3>8. Create the Amazon S3 Bucket</h3>

<p>
I opened Amazon S3 and selected:
</p>

<pre>Create bucket</pre>

<p>
I created a globally unique bucket name and ensured that the bucket was
created in the same AWS Region as the Lambda function.
</p>



<hr>

<h3>9. Configure the S3 Event Notification</h3>

<p>
After creating the bucket, I opened the bucket and navigated to:
</p>

<pre>Properties → Event notifications</pre>

<p>
I selected:
</p>

<pre>Create event notification</pre>

<p>
The event notification was configured to trigger the Lambda function when
an object was created in the bucket.
</p>

<table>
  <tr>
    <th>Setting</th>
    <th>Configuration</th>
  </tr>
  <tr>
    <td>Event Type</td>
    <td>Object Created</td>
  </tr>
  <tr>
    <td>Destination</td>
    <td>Lambda Function</td>
  </tr>
  <tr>
    <td>Lambda Function</td>
    <td><code>WordCountFunction</code></td>
  </tr>
</table>




<hr>

<h3>10. Test the S3 to Lambda Trigger</h3>

<p>
I created a text file named:
</p>

<pre>test1.txt</pre>

<p>The file contained:</p>

<pre>Hello AWS Lambda this is my first test.</pre>

<p>
The expected word count was:
</p>

<pre>7 words</pre>

<p>
I uploaded the file to the S3 bucket.
</p>

<p>
The upload generated an S3 Object Created event, which automatically
invoked the Lambda function.
</p>

<hr>

<h3>11. Lambda Retrieves and Processes the File</h3>

<p>
Lambda received the S3 event and extracted the bucket name and file name.
</p>

<pre><code>bucket_name = event['Records'][0]['s3']['bucket']['name']
object_key = event['Records'][0]['s3']['object']['key']</code></pre>

<p>
Lambda then retrieved the uploaded file from S3:
</p>

<pre><code>response = s3.get_object(
    Bucket=bucket_name,
    Key=object_key
)</code></pre>

<p>
The contents of the file were read and converted to text:
</p>

<pre><code>file_contents = response['Body'].read().decode('utf-8')</code></pre>

<p>
The word count was calculated using:
</p>

<pre><code>word_count = len(file_contents.split())</code></pre>

<hr>

<h3>12. Lambda Publishes the Result to SNS</h3>

<p>
After calculating the word count, Lambda created the required message:
</p>

<pre>The word count in the test1.txt file is 7.</pre>

<p>
The result was then published to the SNS topic using:
</p>

<pre><code>sns.publish(
    TopicArn=SNS_TOPIC_ARN,
    Subject="Word Count Result",
    Message=message
)</code></pre>

<hr>

<h3>13. SNS Sends the Email</h3>

<p>
Amazon SNS received the message from Lambda and delivered it to the
confirmed email subscription.
</p>

<p>
The email subject was:
</p>

<pre>Word Count Result</pre>

<p>
The email message was:
</p>

<pre>The word count in the test1.txt file is 7.</pre>


<hr>

<h2>🧪 Testing</h2>

<p>
I tested the solution using multiple text files containing different
numbers of words.
</p>

<table>
  <thead>
    <tr>
      <th>Test</th>
      <th>File</th>
      <th>Expected Word Count</th>
      <th>Result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Test 1</td>
      <td><code>test1.txt</code></td>
      <td>8</td>
      <td>Successful</td>
    </tr>
    <tr>
      <td>Test 2</td>
      <td><code>test2.txt</code></td>
      <td>7</td>
      <td>Successful</td>
    </tr>
    
  </tbody>
</table>

<h3>Test 1</h3>

<pre>test1.txt</pre>

<pre>Hello AWS Lambda this is my first test.</pre>

<p><strong>Result:</strong></p>

<pre>The word count in the test1.txt file is 7.</pre>

<h3>Test 2</h3>

<pre>test2.txt</pre>
<img width="1006" height="579" alt="image" src="https://github.com/user-attachments/assets/2fe72274-1210-42d0-a62e-8bfaf4782879" />


<hr>

<h2>🔍 Troubleshooting</h2>

<h3>AWS Console UI Differences</h3>

<p>
One of the challenges during the lab was that the current AWS Management
Console interface differed from some of the screenshots and instructions
provided by the lab.
</p>

<p>
I had to locate the equivalent options in the updated console for:
</p>

<ul>
  <li>Lambda function creation</li>
  <li>IAM role selection</li>
  <li>S3 event notifications</li>
  <li>SNS subscriptions</li>
  <li>Lambda monitoring</li>
</ul>

<p>
Working through these differences helped me become more comfortable with
the current AWS Management Console.
</p>

<h3>Lambda Troubleshooting</h3>

<p>
I used Lambda monitoring and Amazon CloudWatch Logs to troubleshoot the
function execution.
</p>

<p>
The troubleshooting process involved checking whether:
</p>

<ol>
  <li>S3 successfully triggered Lambda.</li>
  <li>Lambda received the S3 event.</li>
  <li>Lambda could access the uploaded file.</li>
  <li>The file could be read successfully.</li>
  <li>The word count was calculated correctly.</li>
  <li>The SNS message was published successfully.</li>
  <li>The email was received.</li>
</ol>

<h3>SNS Troubleshooting</h3>

<p>
If an SNS email is not received, the subscription status needs to be checked.
</p>

<p>
The subscription must show:
</p>

<pre>Confirmed</pre>

<p>
If the subscription shows:
</p>

<pre>Pending confirmation</pre>

<p>
the subscription must be confirmed using the email sent by Amazon SNS.
</p>

<hr>

<h2>🔐 Security and Permissions</h2>

<p>
The challenge lab provided an existing IAM role:
</p>

<pre>LambdaAccessRole</pre>

<p>
Because the lab did not allow creation of a new IAM role, I used the
provided role for the Lambda function.
</p>

<p>
The role provided permissions required by the application, including
access to:
</p>

<ul>
  <li>Amazon S3</li>
  <li>Amazon SNS</li>
  <li>Amazon CloudWatch Logs</li>
</ul>

<p>
No AWS access keys or credentials were stored in the source code or
GitHub repository.
</p>

<hr>

<h2>🧠 What I Learned</h2>

<p>
This challenge provided hands-on experience building a serverless,
event-driven application using multiple AWS services.
</p>

<ul>
  <li>Creating AWS Lambda functions using Python.</li>
  <li>Using Boto3 to interact with AWS services.</li>
  <li>Reading objects from Amazon S3.</li>
  <li>Processing files using Lambda.</li>
  <li>Configuring S3 event notifications.</li>
  <li>Connecting Amazon S3 to AWS Lambda.</li>
  <li>Creating Amazon SNS topics.</li>
  <li>Creating and confirming SNS email subscriptions.</li>
  <li>Publishing messages from Lambda to SNS.</li>
  <li>Monitoring Lambda using CloudWatch.</li>
  <li>Troubleshooting serverless applications.</li>
  <li>Working with IAM roles and permissions.</li>
  <li>Building event-driven serverless architectures.</li>
</ul>

<hr>

<h2>📚 Key AWS Concepts</h2>

<h3>Serverless Computing</h3>

<p>
AWS Lambda allows code to run without managing servers. The function is
executed only when it is triggered by an event.
</p>

<h3>Event-Driven Architecture</h3>

<p>
This project demonstrates an event-driven architecture where one service
generates an event that triggers another service.
</p>

<pre>
S3 Event
   ↓
Lambda
   ↓
SNS
   ↓
Email
</pre>

<h3>Amazon S3 Events</h3>

<p>
Amazon S3 can generate events when objects are created, modified, or
deleted. In this project, an object creation event was used to invoke
the Lambda function.
</p>

<h3>Amazon SNS</h3>

<p>
Amazon SNS uses a publish/subscribe messaging model. Lambda publishes the
word count result to the SNS topic, and SNS delivers the message to the
confirmed email subscription.
</p>

<hr>

<h2>📈 Future Improvements</h2>

<ul>
  <li>Restrict the S3 trigger to <code>.txt</code> files only.</li>
  <li>Add error handling for unsupported files.</li>
  <li>Add structured logging.</li>
  <li>Support larger text files.</li>
  <li>Send SMS notifications using SNS.</li>
  <li>Store word count results in DynamoDB.</li>
  <li>Add CloudWatch alarms.</li>
  <li>Add a dead-letter queue for failed Lambda executions.</li>
  <li>Use Lambda environment variables for the SNS Topic ARN.</li>
  <li>Add automated testing.</li>
  <li>Deploy the infrastructure using AWS CloudFormation or Terraform.</li>
</ul>

<hr>

<h2>🏆 Final Result</h2>

<p>
The challenge successfully demonstrated how multiple AWS services can be
combined to build a practical serverless application.
</p>

<p>
A text file can be uploaded to Amazon S3, which automatically triggers
AWS Lambda. Lambda retrieves the file, counts the words, and publishes
the result to Amazon SNS. SNS then delivers the result to the confirmed
email subscription.
</p>

<pre>
              Upload Text File
                     │
                     ▼
              ┌────────────┐
              │  Amazon S3 │
              └─────┬──────┘
                    │
              Object Created
                    │
                    ▼
              ┌────────────┐
              │   Lambda   │
              └─────┬──────┘
                    │
               Count Words
                    │
                    ▼
              ┌────────────┐
              │    SNS     │
              └─────┬──────┘
                    │
                    ▼
                📧 Email
</pre>

<hr>

<h2>🛠️ Technologies Used</h2>

<p>
<img src="https://img.shields.io/badge/AWS-Lambda-orange">
<img src="https://img.shields.io/badge/AWS-S3-orange">
<img src="https://img.shields.io/badge/AWS-SNS-orange">
<img src="https://img.shields.io/badge/AWS-IAM-orange">
<img src="https://img.shields.io/badge/AWS-CloudWatch-orange">
<img src="https://img.shields.io/badge/Python-3.x-blue">
<img src="https://img.shields.io/badge/Boto3-AWS%20SDK-yellow">
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

<h2>⭐ Key Takeaway</h2>

<p>
This project demonstrated how to build a practical serverless workflow
using AWS managed services.
</p>

<p>
Instead of running a dedicated server to process uploaded files, the
solution uses an event-driven architecture where <strong>Amazon S3
triggers AWS Lambda</strong>, Lambda processes the file and publishes
the result to <strong>Amazon SNS</strong>, and SNS delivers the result
through email.
</p>

<p>
This demonstrates the benefits of serverless computing, including
reduced infrastructure management, automatic execution, scalability,
and easy integration between AWS services.
</p>
