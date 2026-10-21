

<header>
  <h1>My EC2 Lab Log</h1>
  <p>What I did — launching a bastion host and a web server with the AWS CLI</p>
</header>

<main>

  <div class="meta">
    <span class="badge">⏱ ~45 minutes</span>
    <span class="badge">🌍 Region: us-west-2</span>
    <span class="badge">☁️ AWS EC2</span>
    <span class="badge">🖥 Console + CLI</span>
  </div>


  <h2 id="start">1. Getting the lab ready</h2>
  <p>I started by clicking <strong>Start Lab</strong> and waiting until the status showed <em>“Lab status: ready”</em>. Then I opened the AWS Management Console, arranged it alongside the instructions, and left the Region set to <strong>us-west-2</strong> as instructed — I didn't touch it for the rest of the lab.</p>

  <h2 id="bastion">2. Launching the Bastion host</h2>
  <p>I went into <strong>EC2 → Launch instance</strong> and configured the bastion host with these settings:</p>
  <table>
    <thead><tr><th>Section</th><th>Setting</th><th>Value I chose</th></tr></thead>
    <tbody>
      <tr><td>Name and tags</td><td>Name</td><td><code>Bastion host</code></td></tr>
      <tr><td>AMI</td><td>Quick Start</td><td>Amazon Linux (Amazon Linux 2)</td></tr>
      <tr><td>Instance type</td><td>—</td><td><code>t3.micro</code></td></tr>
      <tr><td>Key pair</td><td>—</td><td>Proceed without key pair</td></tr>
      <tr><td>Network → VPC</td><td>—</td><td><code>Lab VPC</code></td></tr>
      <tr><td>Subnet</td><td>—</td><td><code>Public Subnet</code> (default)</td></tr>
      <tr><td>Auto-assign public IP</td><td>—</td><td>Enable (default)</td></tr>
      <tr><td>Security group name</td><td>—</td><td><code>Bastion security group</code></td></tr>
      <tr><td>Description</td><td>—</td><td><code>Permit SSH connections</code></td></tr>
      <tr><td>Storage</td><td>—</td><td>Default 8 GiB</td></tr>
      <tr><td>Advanced → IAM profile</td><td>—</td><td><code>Bastion-Role</code></td></tr>
    </tbody>
  </table>
  <p>I attached the <code>Bastion-Role</code> IAM profile because I knew I'd need the instance to call AWS APIs from the CLI later. Then I clicked <strong>Launch instance</strong> → <strong>View all instances</strong>.</p>

  <h2 id="login">3. Connecting via EC2 Instance Connect</h2>
  <p>From the instance list I selected <strong>Bastion host</strong>, clicked <strong>Connect</strong>, and used the <strong>EC2 Instance Connect</strong> tab. My session opened with a prompt like:</p>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>[ec2-user@ip-10-0-x-x ~]$</code></pre>

  <h2 id="web">4. Launching the Web Server via AWS CLI</h2>
  <p>I ran the following commands in the bastion host terminal, in order. I kept the terminal open and didn't disconnect — the environment variables only live for the session.</p>

  <h3>Step 1 — Retrieved the latest Amazon Linux 2 AMI</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>AZ=`curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone`
export AWS_DEFAULT_REGION=${AZ::-1}
AMI=$(aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 --query 'Parameters[0].[Value]' --output text)
echo $AMI</code></pre>

  <h3>Step 2 — Retrieved the public subnet ID</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>SUBNET=$(aws ec2 describe-subnets --filters 'Name=tag:Name,Values=Public Subnet' --query Subnets[].SubnetId --output text)
echo $SUBNET</code></pre>

  <h3>Step 3 — Retrieved the WebSecurityGroup ID</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>SG=$(aws ec2 describe-security-groups --filters Name=group-name,Values=WebSecurityGroup --query SecurityGroups[].GroupId --output text)
echo $SG</code></pre>

  <h3>Step 4 — Downloaded the user data script</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-RSJAWS-1-23732/171-lab-JAWS-create-ec2/s3/UserData.txt
cat UserData.txt</code></pre>
  <p>I read through it — it installs Apache, downloads the web app zip, and installs the app.</p>

  <h3>Step 5 — Launched the web server instance</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>INSTANCE=$(\
aws ec2 run-instances \
--image-id $AMI \
--subnet-id $SUBNET \
--security-group-ids $SG \
--user-data file:///home/ec2-user/UserData.txt \
--instance-type t3.micro \
--tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Web Server}]' \
--query 'Instances[*].InstanceId' \
--output text \
)
echo $INSTANCE</code></pre>

  <h3>Step 6 — Waited until the instance was running</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>aws ec2 describe-instances --instance-ids $INSTANCE --query 'Reservations[].Instances[].State.Name' --output text</code></pre>
  <p>I ran this a few times until it returned <code>running</code>.</p>

  <h3>Step 7 — Tested the web server</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>aws ec2 describe-instances --instance-ids $INSTANCE --query Reservations[].Instances[].PublicDnsName --output text</code></pre>
  <p>I copied the public DNS name, pasted it in a new browser tab, and the web app loaded successfully. I also refreshed the EC2 console and confirmed the <strong>Web Server</strong> instance showed up there too.</p>
  <div class="callout success">✅ My CLI-launched web server was live.</div>

  <h2 id="challenge1">5. Challenge 1 — fixing SSH access on the Misconfigured Web Server</h2>
  <p>I tried to connect to <strong>Misconfigured Web Server</strong> via EC2 Instance Connect and it failed. I checked the security group's <strong>Inbound rules</strong> and found there was no SSH rule — so EC2 Instance Connect couldn't reach the instance.</p>
  <p><strong>What I did:</strong> I opened the instance's security group → <strong>Edit inbound rules</strong> → <strong>Add rule</strong>:</p>
  <ul class="done">
    <li>Type: <strong>SSH</strong></li>
    <li>Port: <strong>22</strong></li>
    <li>Source: <code>0.0.0.0/0</code></li>
  </ul>
  <p>Then I saved the rules and retried EC2 Instance Connect — the connection succeeded.</p>
  <div class="callout">
    <strong>The problem was:</strong> the security group had no inbound SSH (port 22) rule.<br>
    <strong>I fixed it by:</strong> adding SSH (22) from <code>0.0.0.0/0</code>.
  </div>

  <h2 id="challenge2">6. Challenge 2 — fixing the web server on the Misconfigured instance</h2>
  <p>From inside my new SSH session I checked the web server status:</p>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>sudo systemctl status httpd</code></pre>
  <p>I found the web server was not serving the app. I added the missing inbound HTTP rule to the instance's security group:</p>
  <ul class="done">
    <li>Type: <strong>HTTP</strong></li>
    <li>Port: <strong>80</strong></li>
    <li>Source: <code>0.0.0.0/0</code></li>
  </ul>
  <p>And I made sure Apache was installed and running:</p>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd</code></pre>
  <p>After that I grabbed the instance's public DNS and reloaded it in the browser — the page displayed correctly.</p>
  <div class="callout">
    <strong>The problem was:</strong> the security group didn't allow inbound HTTP (port 80), and Apache wasn't installed/running.<br>
    <strong>I fixed it by:</strong> adding an HTTP (80) inbound rule and installing/starting/enabling <code>httpd</code>.
  </div>

  <h2 id="issues">7. Issues I ran into</h2>
  <table>
    <thead><tr><th>What happened</th><th>What I did</th></tr></thead>
    <tbody>
      <tr><td>In my earlier Aurora lab, I got <code>explicit deny</code> on <code>rds:CreateDBInstance</code> for my lab role.</td><td>I re-checked every parameter against the instructions. That error was a lab environment policy restriction, not a mistake on my end — I restarted the lab to refresh credentials.</td></tr>
      <tr><td>EC2 Instance Connect failed on Misconfigured Web Server.</td><td>Added inbound SSH (22) rule.</td></tr>
      <tr><td>Web page didn't load on Misconfigured Web Server.</td><td>Added inbound HTTP (80) rule and started <code>httpd</code>.</td></tr>
      <tr><td>CLI env vars would vanish if I disconnected.</td><td>I kept the session alive and, when I had to, re-ran Steps 1–3 from the top.</td></tr>
    </tbody>
  </table>

  <h2 id="summary">8. Summary of what I completed</h2>
  <ul class="done">
    <li>Launched a bastion host from the console with the <code>Bastion-Role</code> IAM profile</li>
    <li>Connected to it via EC2 Instance Connect</li>
    <li>Used the AWS CLI from the bastion host to launch a <code>t3.micro</code> web server with user data</li>
    <li>Verified the web app was reachable in the browser</li>
    <li>Fixed SSH (22) access on the Misconfigured Web Server</li>
    <li>Fixed HTTP (80) access and started Apache on the Misconfigured Web Server</li>
  </ul>

</main>

<footer>
  My EC2 lab notes · Region: us-west-2
</footer>

<script>
function copyCode(btn) {
  const code = btn.parentElement.querySelector('code').innerText;
  navigator.clipboard.writeText(code).then(() => {
    btn.textContent = 'Copied!';
    setTimeout(() => btn.textContent = 'Copy', 1500);
  });
}
</script>

</body>
</html>
