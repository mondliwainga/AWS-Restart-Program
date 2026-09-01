<div align="center">

# ☁️ AWS RDS MariaDB Database Migration

### Hands-On AWS Cloud Project

<p>
  <strong>Amazon EC2</strong> •
  <strong>Amazon RDS</strong> •
  <strong>MariaDB</strong> •
  <strong>AWS CLI</strong> •
  <strong>CloudWatch</strong>
</p>

</div>

<hr>

<h2>📌 Project Overview</h2>

<p>
In this hands-on AWS project, I created an <strong>Amazon RDS MariaDB database</strong>
and migrated an existing MariaDB database from an <strong>Amazon EC2 instance</strong>
to Amazon RDS.
</p>

<p>
I used the <strong>AWS CLI</strong> to create the required infrastructure, configured
security groups and private subnets, migrated the café application's database using
<code>mysqldump</code>, configured the application to use the new RDS endpoint,
and monitored the database using <strong>Amazon CloudWatch</strong>.
</p>

<p>
This project gave me practical experience with migrating a database from a
self-managed EC2 environment to a managed AWS database service.
</p>

<hr>

<h2>🎯 Objectives</h2>

<p>During this project, I learned how to:</p>

<ul>
  <li>Create an Amazon RDS MariaDB instance using the AWS CLI.</li>
  <li>Configure AWS CLI credentials and region settings.</li>
  <li>Create and configure an RDS security group.</li>
  <li>Create private subnets and an RDS DB subnet group.</li>
  <li>Deploy a private Amazon RDS MariaDB instance.</li>
  <li>Back up a MariaDB database using <code>mysqldump</code>.</li>
  <li>Migrate database schema and data from EC2 to RDS.</li>
  <li>Connect securely to RDS using SSL/TLS.</li>
  <li>Configure AWS Systems Manager Parameter Store.</li>
  <li>Configure the café application to use the RDS database.</li>
  <li>Monitor RDS database activity using CloudWatch.</li>
</ul>

<hr>

<h2>🏗️ Architecture</h2>

<p>
The café application initially used a MariaDB database running on an EC2 instance.
I migrated the database to Amazon RDS and updated the application configuration
to use the new RDS database.
</p>

<pre>
                    ┌──────────────────────┐
                    │    Café Website      │
                    │     EC2 Instance     │
                    └──────────┬───────────┘
                               │
                               │ MySQL / MariaDB
                               │ Port 3306
                               ▼
                    ┌──────────────────────┐
                    │     Amazon RDS       │
                    │       MariaDB        │
                    │    CafeDBInstance    │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │    Amazon CloudWatch │
                    │      Monitoring      │
                    └──────────────────────┘
</pre>

<hr>

<h2>☁️ AWS Services Used</h2>

<table>
  <thead>
    <tr>
      <th>Service</th>
      <th>How I Used It</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Amazon EC2</strong></td>
      <td>Hosted the café application and provided terminal access.</td>
    </tr>
    <tr>
      <td><strong>Amazon RDS</strong></td>
      <td>Hosted the migrated MariaDB database.</td>
    </tr>
    <tr>
      <td><strong>Amazon VPC</strong></td>
      <td>Provided the network environment.</td>
    </tr>
    <tr>
      <td><strong>Security Groups</strong></td>
      <td>Controlled access to the RDS database.</td>
    </tr>
    <tr>
      <td><strong>Systems Manager Parameter Store</strong></td>
      <td>Stored the application's database endpoint.</td>
    </tr>
    <tr>
      <td><strong>Amazon CloudWatch</strong></td>
      <td>Monitored RDS database activity and connections.</td>
    </tr>
    <tr>
      <td><strong>AWS CLI</strong></td>
      <td>Created and configured AWS resources from the command line.</td>
    </tr>
  </tbody>
</table>

<h3>Other Technologies</h3>

<p>
  <code>Linux</code>
  <code>MariaDB</code>
  <code>MySQL</code>
  <code>SQL</code>
  <code>mysqldump</code>
  <code>SSL/TLS</code>
</p>

<hr>

<h2>1️⃣ Generating Café Order Data</h2>

<p>
I started by accessing the café website and placing customer orders.
The purpose was to generate application data in the existing database
before performing the migration.
</p>

<p>
I then checked the <strong>Order History</strong> page and recorded the
number of orders so I could compare the data after migrating the database
to Amazon RDS.
</p>

<hr>

<h2>2️⃣ Configuring the AWS CLI</h2>

<p>
I connected to the <strong>CLI Host EC2 instance</strong> using
EC2 Instance Connect and configured the AWS CLI.
</p>

<pre><code>aws configure</code></pre>

<p>
I entered the AWS credentials and region provided by the lab environment
and configured the default output format as <code>json</code>.
</p>

<p>
After completing the configuration, I was able to create and manage
AWS resources directly from the terminal.
</p>

<hr>

<h2>3️⃣ Creating the RDS Security Group</h2>

<p>
I created a dedicated security group for my RDS database:
</p>

<pre><code>aws ec2 create-security-group \
--group-name CafeDatabaseSG \
--description "Security group for Cafe database" \
--vpc-id &lt;CafeVpcID&gt;</code></pre>

<p>
I then configured an inbound rule allowing MySQL/MariaDB traffic on
<strong>TCP port 3306</strong> only from the café application's security group.
</p>

<pre><code>aws ec2 authorize-security-group-ingress \
--group-id &lt;CafeDatabaseSG-ID&gt; \
--protocol tcp \
--port 3306 \
--source-group &lt;CafeSecurityGroup-ID&gt;</code></pre>

<h3>🔐 Security</h3>

<p>
I restricted database access to the café application rather than allowing
connections from anywhere. This helped me apply the
<strong>principle of least privilege</strong> to the database.
</p>

<hr>

<h2>4️⃣ Creating Private Subnets</h2>

<p>
I created two private subnets to use with the RDS DB subnet group.
</p>

<h3>Private Subnet 1</h3>

<pre><code>aws ec2 create-subnet \
--vpc-id &lt;CafeVpcID&gt; \
--cidr-block 10.200.2.0/23 \
--availability-zone &lt;CafeInstanceAZ&gt;</code></pre>

<h3>Private Subnet 2</h3>

<pre><code>aws ec2 create-subnet \
--vpc-id &lt;CafeVpcID&gt; \
--cidr-block 10.200.10.0/23 \
--availability-zone &lt;Different-AZ&gt;</code></pre>

<p>
I used non-overlapping CIDR ranges within the café VPC and placed the
subnets in different Availability Zones.
</p>

<hr>

<h2>5️⃣ Creating the RDS DB Subnet Group</h2>

<p>
I created an RDS DB subnet group using the two private subnets:
</p>

<pre><code>aws rds create-db-subnet-group \
--db-subnet-group-name "CafeDB Subnet Group" \
--db-subnet-group-description "DB subnet group for Cafe" \
--subnet-ids &lt;Private-Subnet-1-ID&gt; &lt;Private-Subnet-2-ID&gt; \
--tags "Key=Name,Value=CafeDatabaseSubnetGroup"</code></pre>

<p>
This allowed my RDS database to be deployed inside the private network.
</p>

<hr>

<h2>6️⃣ Creating the Amazon RDS MariaDB Instance</h2>

<p>
I created the RDS MariaDB instance using the AWS CLI:
</p>

<pre><code>aws rds create-db-instance \
--db-instance-identifier CafeDBInstance \
--engine mariadb \
--engine-version 10.11.11 \
--db-instance-class db.t3.micro \
--allocated-storage 20 \
--availability-zone &lt;CafeInstanceAZ&gt; \
--db-subnet-group-name "CafeDB Subnet Group" \
--vpc-security-group-ids &lt;CafeDatabaseSG-ID&gt; \
--no-publicly-accessible \
--master-username root \
--master-user-password '&lt;LAB_PASSWORD&gt;'</code></pre>

<h3>RDS Configuration</h3>

<table>
  <tr>
    <th>Configuration</th>
    <th>Value</th>
  </tr>
  <tr>
    <td>DB Identifier</td>
    <td><code>CafeDBInstance</code></td>
  </tr>
  <tr>
    <td>Engine</td>
    <td>MariaDB</td>
  </tr>
  <tr>
    <td>Instance Class</td>
    <td><code>db.t3.micro</code></td>
  </tr>
  <tr>
    <td>Storage</td>
    <td>20 GB</td>
  </tr>
  <tr>
    <td>Public Access</td>
    <td>Disabled</td>
  </tr>
  <tr>
    <td>Port</td>
    <td><code>3306</code></td>
  </tr>
  <tr>
    <td>DB Subnet Group</td>
    <td><code>CafeDB Subnet Group</code></td>
  </tr>
  <tr>
    <td>Security Group</td>
    <td><code>CafeDatabaseSG</code></td>
  </tr>
</table>

<p>
I configured the RDS instance as <strong>not publicly accessible</strong>,
keeping the database inside the private network.
</p>

<h3>📸 RDS Instance</h3>

<img width="1235" height="665" alt="image" src="https://github.com/user-attachments/assets/263991e0-a45d-41ba-b75a-3d9b0eb47dc3" />


<hr>

<h2>7️⃣ Checking the RDS Instance Status</h2>

<p>
I monitored the RDS instance using the AWS CLI while it was being created:
</p>

<pre><code>aws rds describe-db-instances \
--db-instance-identifier CafeDBInstance \
--query "DBInstances[*].[Endpoint.Address,AvailabilityZone,PreferredBackupWindow,BackupRetentionPeriod,DBInstanceStatus]"</code></pre>

<p>
I waited for the status to change to <code>available</code>.
</p>

<p>
The RDS endpoint I used was:
</p>

<pre><code>cafedbinstance.cmmpfqh1zr6p.us-west-2.rds.amazonaws.com</code></pre>

<h3>📸 RDS Available</h3>

<img width="403" height="176" alt="image" src="https://github.com/user-attachments/assets/6d5953c2-37d8-4893-addc-0f91ff4993bd" />


<hr>

<h2>8️⃣ Backing Up the Existing MariaDB Database</h2>

<p>
I connected to the café EC2 instance and created a backup of the existing
<code>cafe_db</code> database using <code>mysqldump</code>.
</p>

<pre><code>mysqldump --user=root --password='&lt;LAB_PASSWORD&gt;' \
--databases cafe_db \
--add-drop-database &gt; cafedb-backup.sql</code></pre>

<p>
This created the database backup file:
</p>

<pre><code>cafedb-backup.sql</code></pre>

<p>
The backup contained the SQL statements required to recreate the database,
tables, indexes, and application data.
</p>

<hr>

<h2>9️⃣ Configuring SSL/TLS</h2>

<p>
The RDS MariaDB instance required an encrypted database connection.
I downloaded the Amazon RDS CA certificate bundle:
</p>

<pre><code>curl -o global-bundle.pem \
https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem</code></pre>

<p>
I verified that the certificate was available:
</p>

<pre><code>ls -l global-bundle.pem</code></pre>

<hr>

<h2>🔟 Restoring the Database to RDS</h2>

<p>
I restored the database backup to my RDS MariaDB instance:
</p>

<pre><code>mysql --user=root --password='&lt;LAB_PASSWORD&gt;' \
--host=&lt;RDS-ENDPOINT&gt; \
--ssl-ca=./global-bundle.pem \
&lt; cafedb-backup.sql</code></pre>

<p>
This migrated the <code>cafe_db</code> database from the EC2-hosted MariaDB
database into Amazon RDS.
</p>

<hr>

<h2>1️⃣1️⃣ Verifying the Database Migration</h2>

<p>
I connected directly to the RDS database:
</p>

<pre><code>mysql --user=root --password='&lt;LAB_PASSWORD&gt;' \
--host=&lt;RDS-ENDPOINT&gt; \
--ssl-ca=./global-bundle.pem \
cafe_db</code></pre>

<p>
I checked the migrated tables using:
</p>

<pre><code>SHOW TABLES;</code></pre>

<p>
The following tables were successfully migrated:
</p>

<ul>
  <li><code>order</code></li>
  <li><code>order_item</code></li>
  <li><code>product</code></li>
  <li><code>product_group</code></li>
</ul>

<p>
I then verified the product data:
</p>

<pre><code>SELECT * FROM product;</code></pre>

<p>
The migrated database contained <strong>9 products</strong>, including
Croissant, Donut, Chocolate Chip Cookie, Muffin, Strawberry Blueberry Tart,
Strawberry Tart, Coffee, Hot Chocolate, and Latte.
</p>

<p>
I also checked the orders using:
</p>

<pre><code>SELECT * FROM `order`;</code></pre>

<p>
At the time of verification, the database contained <strong>2 orders</strong>.
</p>

<h3>📸 Migrated Database</h3>

<img width="1352" height="204" alt="image" src="https://github.com/user-attachments/assets/44cf4b59-e91c-4440-9a89-e32008e62141" />


<hr>

<h2>🛠️ Troubleshooting</h2>

<h3>SSL Connection Error</h3>

<p>
While connecting to the RDS database, I initially received:
</p>

<pre><code>ERROR 2026 (HY000): SSL connection error: No such file or directory</code></pre>

<p>
I checked whether the RDS certificate existed:
</p>

<pre><code>ls -l global-bundle.pem</code></pre>

<p>
The certificate was present on the EC2 instance. I then used the correct
certificate path:
</p>

<pre><code>--ssl-ca=./global-bundle.pem</code></pre>

<p>
After retrying the connection, I successfully connected to the RDS
MariaDB database.
</p>

<pre><code>Welcome to the MariaDB monitor.
MariaDB [cafe_db]&gt;</code></pre>

<h3>Linux Commands vs SQL Commands</h3>

<p>
I also accidentally entered Linux commands inside the MariaDB client.
MariaDB interpreted the commands as SQL input.
</p>

<p>
I cancelled the unfinished command using:
</p>

<pre><code>Ctrl + C</code></pre>

<p>
This helped me understand the difference between working in the Linux
shell and working inside the MariaDB command-line interface.
</p>

<table>
  <tr>
    <th>Environment</th>
    <th>Example</th>
  </tr>
  <tr>
    <td>Linux Shell</td>
    <td><code>[ec2-user@ip-10-200-0-160 ~]$</code></td>
  </tr>
  <tr>
    <td>MariaDB</td>
    <td><code>MariaDB [cafe_db]&gt;</code></td>
  </tr>
</table>

<hr>

<h2>1️⃣2️⃣ Updating Systems Manager Parameter Store</h2>

<p>
After migrating the database, I updated the café application's database
configuration in <strong>AWS Systems Manager Parameter Store</strong>.
</p>

<p>
I opened:
</p>

<p>
<strong>Systems Manager → Parameter Store → /cafe/dbUrl</strong>
</p>

<p>
I changed the parameter value to the RDS endpoint:
</p>

<pre><code>cafedbinstance.cmmpfqh1zr6p.us-west-2.rds.amazonaws.com</code></pre>

<p>
This allowed the café application to use the new RDS database without
modifying the application's source code.
</p>

<h3>📸 Parameter Store</h3>

<img width="1497" height="473" alt="image" src="https://github.com/user-attachments/assets/85524b2a-46fa-474b-9bc0-ce3a2153c2e7" />


<hr>

<h2>1️⃣3️⃣ Testing the Café Application</h2>

<p>
After updating Parameter Store, I accessed the café website again and
checked the <strong>Order History</strong> page.
</p>

<p>
I confirmed that the existing orders were still available after the
database migration.
</p>

<p>
This demonstrated that:
</p>

<ul>
  <li>The database migration was successful.</li>
  <li>The application could connect to Amazon RDS.</li>
  <li>The existing application data was preserved.</li>
  <li>The application was using the new RDS database.</li>
</ul>

<hr>

<h2>1️⃣4️⃣ Monitoring Amazon RDS with CloudWatch</h2>

<p>
I opened:
</p>

<p>
<strong>Amazon RDS → Databases → CafeDBInstance → Monitoring</strong>
</p>

<p>
I reviewed several CloudWatch metrics, including:
</p>

<ul>
  <li><strong>CPUUtilization</strong></li>
  <li><strong>DatabaseConnections</strong></li>
  <li><strong>FreeStorageSpace</strong></li>
  <li><strong>FreeableMemory</strong></li>
  <li><strong>WriteIOPS</strong></li>
  <li><strong>ReadIOPS</strong></li>
</ul>

<h3>Testing Database Connections</h3>

<p>
I opened an interactive MariaDB session against the RDS database:
</p>

<pre><code>mysql --user=root --password='&lt;LAB_PASSWORD&gt;' \
--host=&lt;RDS-ENDPOINT&gt; \
--ssl-ca=./global-bundle.pem \
cafe_db</code></pre>

<p>
I ran:
</p>

<pre><code>SELECT * FROM product;</code></pre>

<p>
While the MariaDB session remained open, I monitored the
<strong>DatabaseConnections</strong> metric.
</p>

<p>
The metric showed:
</p>

<pre><code>1 database connection</code></pre>

<p>
This represented my active MariaDB session.
</p>

<p>
I then closed the connection:
</p>

<pre><code>exit</code></pre>

<p>
After the next CloudWatch update, the metric returned to:
</p>

<pre><code>0 database connections</code></pre>

<p>
This demonstrated how CloudWatch can be used to monitor active
connections to an RDS database.
</p>

<h3>📸 CloudWatch Monitoring</h3>

<img width="388" height="264" alt="image" src="https://github.com/user-attachments/assets/671635d9-b21c-4375-9e61-af89d69c5661" />


<hr>

<h2>🧠 What I Learned</h2>

<p>
This project gave me practical experience with a complete AWS database
migration workflow.
</p>

<pre>
MariaDB on EC2
      ↓
   mysqldump
      ↓
Database Backup
      ↓
Amazon RDS MariaDB
      ↓
Restore Database
      ↓
Update Parameter Store
      ↓
Café Application
      ↓
CloudWatch Monitoring
</pre>

<p>
One of my biggest takeaways was that database migration involves more
than simply copying data.
</p>

<p>
I had to consider:
</p>

<ul>
  <li>Network configuration</li>
  <li>Private subnets</li>
  <li>Security groups</li>
  <li>Database connectivity</li>
  <li>SSL/TLS encryption</li>
  <li>Application configuration</li>
  <li>Monitoring</li>
</ul>

<p>
I also gained practical troubleshooting experience by diagnosing an SSL
connection issue and learning to distinguish between Linux shell commands
and MariaDB commands.
</p>

<hr>

<h2>🚀 Final Result</h2>

<p>
I successfully created an <strong>Amazon RDS MariaDB database</strong>,
migrated the café application's database from EC2, configured the
application to use the new RDS endpoint, verified the migrated data,
and monitored database connections using CloudWatch.
</p>

<h3>💻 Skills Demonstrated</h3>

<p>
  <code>AWS CLI</code>
  <code>Amazon EC2</code>
  <code>Amazon RDS</code>
  <code>MariaDB</code>
  <code>MySQL</code>
  <code>AWS VPC</code>
  <code>Security Groups</code>
  <code>Private Subnets</code>
  <code>Systems Manager</code>
  <code>Parameter Store</code>
  <code>Amazon CloudWatch</code>
  <code>Linux</code>
  <code>SQL</code>
  <code>mysqldump</code>
  <code>Database Migration</code>
  <code>SSL/TLS</code>
</p>

<hr>

<h2>📂 Repository Structure</h2>

<pre>
AWS-RDS-MariaDB-Migration/
│
├── README.md
│
└── screenshots/
    ├── 01-rds-instance.png
    ├── 02-rds-available.png
    ├── 03-database-migration.png
    ├── 04-parameter-store.png
    └── 05-cloudwatch.png
</pre>

<hr>

<div align="center">

<h3>☁️ AWS Cloud Project</h3>

<p>
<strong>Database Migration from Amazon EC2 to Amazon RDS</strong>
</p>

<p>
Built and documented as a hands-on AWS learning project.
</p>

</div>
