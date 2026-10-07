<h1>☁️ AWS re/Start Challenge Lab</h1>
<h3>Using AWS CloudFormation to Create an AWS VPC and Amazon EC2 Instance</h3>

<hr />

<h2>📋 Lab Overview</h2>
<p>
  In this AWS re/Start challenge, I used AWS CloudFormation to create an AWS networking environment and an Amazon EC2 instance using Infrastructure as Code (IaC).
</p>
<p>
  The objective was to create and successfully deploy a CloudFormation template containing:
</p>
<ul>
  <li>An Amazon VPC</li>
  <li>An Internet Gateway attached to the VPC</li>
  <li>A security group allowing SSH access</li>
  <li>A private subnet</li>
  <li>A route table associated with the private subnet</li>
  <li>An Amazon EC2 <code>t3.micro</code> instance</li>
  <li>Supporting networking resources</li>
</ul>
<p>
  I also tested and troubleshot the CloudFormation deployment until the stack successfully reached <code>CREATE_COMPLETE</code>.
</p>

<h2>🎯 Objectives</h2>
<p>The main objectives of this challenge were to:</p>
<ul>
  <li>Create an Amazon VPC using CloudFormation.</li>
  <li>Create an Internet Gateway.</li>
  <li>Attach the Internet Gateway to the VPC.</li>
  <li>Create a private subnet.</li>
  <li>Create a route table for the private subnet.</li>
  <li>Create a security group allowing SSH traffic.</li>
  <li>Deploy a <code>t3.micro</code> EC2 instance into the private subnet.</li>
  <li>Validate the CloudFormation template.</li>
  <li>Deploy the infrastructure using the AWS CLI.</li>
  <li>Troubleshoot CloudFormation deployment errors.</li>
  <li>Verify that all resources were successfully created.</li>
</ul>

<h2>🏗️ Architecture</h2>
<p>The CloudFormation template creates the following architecture:</p>
<pre><code>                    AWS CloudFormation
                           |
                           v
                 +---------------------+
                 |      Amazon VPC     |
                 |     10.0.0.0/16     |
                 |                     |
                 |  +---------------+  |
                 |  | Internet      |  |
                 |  | Gateway       |  |
                 |  +---------------+  |
                 |                     |
                 |  +---------------+  |
                 |  | Private       |  |
                 |  | Subnet        |  |
                 |  | 10.0.1.0/24   |  |
                 |  |               |  |
                 |  | +-----------+ |  |
                 |  | | EC2       | |  |
                 |  | | t3.micro  | |  |
                 |  | +-----------+ |  |
                 |  +---------------+  |
                 |                     |
                 |  +---------------+  |
                 |  | Security      |  |
                 |  | Group         |  |
                 |  | SSH :22       |  |
                 |  +---------------+  |
                 +---------------------+</code></pre>

<h2>🛠️ AWS Resources Created</h2>
<table>
  <thead>
    <tr>
      <th>Resource</th>
      <th>CloudFormation Type</th>
      <th>Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>VPC</td>
      <td><code>AWS::EC2::VPC</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
    <tr>
      <td>Internet Gateway</td>
      <td><code>AWS::EC2::InternetGateway</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
    <tr>
      <td>Gateway Attachment</td>
      <td><code>AWS::EC2::VPCGatewayAttachment</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
    <tr>
      <td>Private Subnet</td>
      <td><code>AWS::EC2::Subnet</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
    <tr>
      <td>Route Table</td>
      <td><code>AWS::EC2::RouteTable</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
    <tr>
      <td>Subnet Association</td>
      <td><code>AWS::EC2::SubnetRouteTableAssociation</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
    <tr>
      <td>Security Group</td>
      <td><code>AWS::EC2::SecurityGroup</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
    <tr>
      <td>EC2 Instance</td>
      <td><code>AWS::EC2::Instance</code></td>
      <td>✅ CREATE_COMPLETE</td>
    </tr>
  </tbody>
</table>

<h2>📝 CloudFormation Template</h2>
<p>I created a CloudFormation template called:</p>
<pre><code>template.yaml</code></pre>
<p>
  The template defines the AWS infrastructure as code instead of manually creating each resource through the AWS Management Console.
</p>
<p>The template contains resources for:</p>
<ul>
  <li>VPC</li>
  <li>Internet Gateway</li>
  <li>Gateway attachment</li>
  <li>Private subnet</li>
  <li>Route table</li>
  <li>Route table association</li>
  <li>Security group</li>
  <li>EC2 instance</li>
</ul>

<h2>🔍 Step 1 — Validate the Template</h2>
<p>Before deploying the infrastructure, I validated the CloudFormation template using the AWS CLI.</p>
<pre><code class="language-bash">aws cloudformation validate-template \
--template-body file://template.yaml</code></pre>
<p>The template validation completed successfully. The response included:</p>
<pre><code class="language-json">{
    "Parameters": [],
    "Description": "AWS re/Start Challenge Lab - VPC and EC2 Instance"
}</code></pre>
<p>This confirmed that the CloudFormation template syntax was valid.</p>

<h2>🚀 Step 2 — Create the CloudFormation Stack</h2>
<p>I created the CloudFormation stack using:</p>
<pre><code class="language-bash">aws cloudformation create-stack \
--stack-name challenge-vpc \
--template-body file://template.yaml</code></pre>
<p>The stack was named: <code>challenge-vpc</code></p>

<h2>⚠️ Step 3 — Troubleshooting the Initial Deployment</h2>
<p>During the first deployment, the CloudFormation stack entered:</p>
<pre><code>ROLLBACK_COMPLETE</code></pre>
<p>I investigated the CloudFormation stack events to determine the cause using:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-events \
--stack-name challenge-vpc \
--query 'StackEvents[*].[LogicalResourceId,ResourceType,ResourceStatus,ResourceStatusReason]' \
--output table</code></pre>
<p>The failure was related to the EC2 AMI configuration. CloudFormation attempted to retrieve an Amazon Linux AMI through the Systems Manager Parameter Store:</p>
<pre><code>ssm:GetParameters</code></pre>
<p>However, the restricted AWS re/Start lab environment did not provide permission for this operation. The error included <code>AccessDeniedException</code> and <code>is not authorized to perform: ssm:GetParameters</code>.</p>

<h2>🔧 Step 4 — Troubleshooting AWS Permissions</h2>
<p>I also discovered that the restricted lab environment prevented certain EC2 API operations.</p>
<p>For example, running:</p>
<pre><code class="language-bash">aws ec2 describe-vpcs</code></pre>
<p>returned <code>UnauthorizedOperation</code>. Similarly, attempting to retrieve AMIs using:</p>
<pre><code class="language-bash">aws ec2 describe-images</code></pre>
<p>was restricted.</p>
<p>
  This helped me understand that AWS training environments can intentionally limit permissions and that CloudFormation templates must work within the permissions provided by the lab.
</p>

<h2>🔄 Step 5 — Redeploying the Stack</h2>
<p>After correcting the CloudFormation configuration, I deployed the stack again:</p>
<pre><code class="language-bash">aws cloudformation create-stack \
--stack-name challenge-vpc \
--template-body file://template.yaml</code></pre>
<p>The stack was successfully created.</p>

<h2>✅ Step 6 — Verify the Stack</h2>
<p>I checked the final CloudFormation stack status with:</p>
<pre><code class="language-bash">aws cloudformation describe-stacks \
--stack-name challenge-vpc \
--query 'Stacks[0].[StackName,StackStatus]' \
--output table</code></pre>
<p>The final result was:</p>
<pre><code>--------------------------------
|       DescribeStacks         |
+----------------+-------------+
| challenge-vpc  | CREATE_COMPLETE |
+----------------+-------------+</code></pre>

<h3>🎉 Final Status</h3>
<pre><code>CREATE_COMPLETE</code></pre>
<p>This confirmed that the CloudFormation deployment completed successfully.</p>

<h2>🔎 Step 7 — Verify CloudFormation Resources</h2>
<p>I verified the resources created by the CloudFormation stack using:</p>
<pre><code class="language-bash">aws cloudformation describe-stack-resources \
--stack-name challenge-vpc \
--query 'StackResources[*].[LogicalResourceId,ResourceType,ResourceStatus,PhysicalResourceId]' \
--output table</code></pre>
<p>The expected resources included:</p>
<ul>
  <li><code>MyVPC</code></li>
  <li><code>MyInternetGateway</code></li>
  <li><code>AttachGateway</code></li>
  <li><code>PrivateSubnet</code></li>
  <li><code>PrivateRouteTable</code></li>
  <li><code>PrivateSubnetRouteTableAssociation</code></li>
  <li><code>MySecurityGroup</code></li>
  <li><code>MyEC2Instance</code></li>
</ul>
<p>Each resource successfully reached <code>CREATE_COMPLETE</code>.</p>

<h2>🔐 Security Group Configuration</h2>
<p>The security group was configured to allow SSH access from anywhere, as required by the challenge.</p>
<p>The rule configuration:</p>
<table>
  <thead>
    <tr>
      <th>Protocol</th>
      <th>Port</th>
      <th>Source</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>TCP</td>
      <td>22</td>
      <td><code>0.0.0.0/0</code></td>
    </tr>
  </tbody>
</table>
<p>
  <em>Note: Although SSH access was permitted by the security group, the EC2 instance was placed inside a private subnet and did not need to be accessed directly for this challenge.</em>
</p>

<h2>🌐 Networking Configuration</h2>
<p>The VPC used the CIDR block: <code>10.0.0.0/16</code></p>
<p>The private subnet used: <code>10.0.1.0/24</code></p>
<p>The subnet was associated with its own route table. The Internet Gateway was attached to the VPC as required by the challenge.</p>

<h2>💻 AWS CLI Commands Used</h2>

<p><strong>Validate CloudFormation Template</strong></p>
<pre><code class="language-bash">aws cloudformation validate-template \
--template-body file://template.yaml</code></pre>

<p><strong>Create Stack</strong></p>
<pre><code class="language-bash">aws cloudformation create-stack \
--stack-name challenge-vpc \
--template-body file://template.yaml</code></pre>

<p><strong>Check Stack Status</strong></p>
<pre><code class="language-bash">aws cloudformation describe-stacks \
--stack-name challenge-vpc \
--query 'Stacks[0].[StackName,StackStatus]' \
--output table</code></pre>

<p><strong>View CloudFormation Events</strong></p>
<pre><code class="language-bash">aws cloudformation describe-stack-events \
--stack-name challenge-vpc \
--query 'StackEvents[*].[LogicalResourceId,ResourceType,ResourceStatus,ResourceStatusReason]' \
--output table</code></pre>

<p><strong>View Stack Resources</strong></p>
<pre><code class="language-bash">aws cloudformation describe-stack-resources \
--stack-name challenge-vpc \
--query 'StackResources[*].[LogicalResourceId,ResourceType,ResourceStatus,PhysicalResourceId]' \
--output table</code></pre>

<h2>📚 What I Learned</h2>
<p>Through this challenge, I learned how to:</p>
<ul>
  <li>Build AWS infrastructure using CloudFormation.</li>
  <li>Write CloudFormation templates in YAML.</li>
  <li>Create a VPC using Infrastructure as Code.</li>
  <li>Create and attach an Internet Gateway.</li>
  <li>Create private subnets.</li>
  <li>Create and associate route tables.</li>
  <li>Configure security groups.</li>
  <li>Deploy an EC2 <code>t3.micro</code> instance.</li>
  <li>Validate CloudFormation templates.</li>
  <li>Deploy CloudFormation stacks using the AWS CLI.</li>
  <li>Read CloudFormation stack events.</li>
  <li>Troubleshoot <code>ROLLBACK_COMPLETE</code> deployments.</li>
  <li>Identify AWS IAM permission restrictions.</li>
  <li>Understand how restricted AWS training environments affect deployments.</li>
  <li>Verify AWS infrastructure using CloudFormation and the AWS CLI.</li>
</ul>

<h2>🎓 Skills Demonstrated</h2>
<table>
  <thead>
    <tr>
      <th>Skill</th>
      <th>Demonstrated</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>AWS CloudFormation</td><td>✅</td></tr>
    <tr><td>Infrastructure as Code</td><td>✅</td></tr>
    <tr><td>Amazon VPC</td><td>✅</td></tr>
    <tr><td>Amazon EC2</td><td>✅</td></tr>
    <tr><td>Security Groups</td><td>✅</td></tr>
    <tr><td>Subnets</td><td>✅</td></tr>
    <tr><td>Route Tables</td><td>✅</td></tr>
    <tr><td>Internet Gateway</td><td>✅</td></tr>
    <tr><td>AWS CLI</td><td>✅</td></tr>
    <tr><td>YAML</td><td>✅</td></tr>
    <tr><td>CloudFormation Troubleshooting</td><td>✅</td></tr>
    <tr><td>IAM Permission Troubleshooting</td><td>✅</td></tr>
  </tbody>
</table>

<h2>📸 Screenshots</h2>
<img width="866" height="514" alt="image" src="https://github.com/user-attachments/assets/5eea140b-b073-493f-9e90-c8bf9129ad20" />


<h3>CloudFormation Stack</h3>
<p>
 <img width="622" height="461" alt="image" src="https://github.com/user-attachments/assets/421b8235-dee2-4b4a-b494-e9c1eeb5354e" />

</p>

<h2>🏁 Final Outcome</h2>
<p>The challenge was successfully completed. I created an AWS CloudFormation template that deployed:</p>
<ul>
  <li>✅ Amazon VPC</li>
  <li>✅ Internet Gateway</li>
  <li>✅ Private subnet</li>
  <li>✅ Route table</li>
  <li>✅ Security group</li>
  <li>✅ Amazon EC2 <code>t3.micro</code> instance</li>
</ul>
<p>
  After troubleshooting the initial CloudFormation deployment failure and working within the restricted AWS re/Start permissions, the final stack successfully reached:
</p>
<pre><code>CREATE_COMPLETE</code></pre>

<h2>💼 Portfolio Summary</h2>
<p>
  This challenge demonstrates my practical experience with AWS Infrastructure as Code, CloudFormation, VPC networking, EC2, security groups, AWS CLI, and troubleshooting AWS deployment failures.
</p>
<p>
  I gained hands-on experience creating AWS infrastructure programmatically rather than manually through the AWS Management Console.
</p>

<h2>🚀 Key Takeaway</h2>
<p>
  Infrastructure as Code allows AWS resources to be defined, deployed, tested, and managed consistently using configuration files rather than manually creating resources.
</p>
<p>
  This challenge strengthened my understanding of AWS CloudFormation, VPC networking, EC2 deployment, IAM permissions, AWS CLI, and cloud troubleshooting.
</p>

<hr />
<p>
  <code>AWS re/Start</code> | <code>CloudFormation</code> | <code>VPC</code> | <code>EC2</code> | <code>AWS CLI</code> | <code>Infrastructure as Code</code>
</p>
