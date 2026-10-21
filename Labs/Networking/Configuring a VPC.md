

<header>
  <h1>My VPC Lab Log</h1>
  <p>Building a secure network with Public/Private Subnets, NAT Gateway, and a Bastion Host</p>
</header>

<main>



  <h2 id="vpc">1. Creating the VPC</h2>
  <p>I opened <strong>VPC → Your VPCs</strong> and clicked <strong>Create VPC</strong> with these settings:</p>
  <table>
    <thead><tr><th>Setting</th><th>Value I used</th></tr></thead>
    <tbody>
      <tr><td>Resources to create</td><td>VPC only</td></tr>
      <tr><td>Name tag</td><td><code>Lab VPC</code></td></tr>
      <tr><td>IPv4 CIDR</td><td><code>10.0.0.0/16</code> (manual input)</td></tr>
      <tr><td>IPv6 CIDR</td><td>No IPv6 CIDR block</td></tr>
      <tr><td>Tenancy</td><td>Default</td></tr>
    </tbody>
  </table>
  <p>After creating it, I selected <strong>Lab VPC</strong> → <strong>Actions → Edit VPC settings</strong> and enabled <strong>DNS hostnames</strong>, so instances in the VPC automatically receive public DNS names.</p>

  <h2 id="subnets">2. Creating subnets</h2>
  <h3>Public Subnet</h3>
  <table>
    <thead><tr><th>Setting</th><th>Value</th></tr></thead>
    <tbody>
      <tr><td>VPC ID</td><td>Lab VPC</td></tr>
      <tr><td>Subnet name</td><td><code>Public Subnet</code></td></tr>
      <tr><td>Availability Zone</td><td>First AZ in the list</td></tr>
      <tr><td>IPv4 CIDR block</td><td><code>10.0.0.0/24</code></td></tr>
    </tbody>
  </table>
  <p>Then I went to <strong>Actions → Edit subnet settings</strong> and checked ✅ <strong>Enable auto-assign public IPv4 address</strong>.</p>

  <h3>Private Subnet</h3>
  <p>I repeated the process with these differences:</p>
  <table>
    <thead><tr><th>Setting</th><th>Value</th></tr></thead>
    <tbody>
      <tr><td>Subnet name</td><td><code>Private Subnet</code></td></tr>
      <tr><td>Availability Zone</td><td>Same first AZ</td></tr>
      <tr><td>IPv4 CIDR block</td><td><code>10.0.2.0/23</code></td></tr>
    </tbody>
  </table>
  <div class="callout">💡 <code>10.0.2.0/23</code> covers <code>10.0.2.x</code> and <code>10.0.3.x</code>, giving the private subnet twice as many IPs since most resources live there.</div>

  <h2 id="igw">3. Attaching an Internet Gateway</h2>
  <p>I created a gateway named <strong>Lab IGW</strong> and attached it to <strong>Lab VPC</strong> via <strong>Actions → Attach to a VPC</strong>.</p>

  <h2 id="routes">4. Configuring route tables</h2>
  <h3>Renamed the existing route table</h3>
  <p>I selected the route table whose VPC column was <strong>Lab VPC</strong>, renamed it to <strong>Private Route Table</strong>, and confirmed the only existing route was <code>10.0.0.0/16 → local</code>.</p>

  <h3>Created the public route table</h3>
  <p>I created <strong>Public Route Table</strong> (VPC: Lab VPC), then in <strong>Routes → Edit routes</strong> added:</p>
  <ul class="done">
    <li>Destination: <code>0.0.0.0/0</code></li>
    <li>Target: <strong>Internet Gateway → Lab IGW</strong></li>
  </ul>
  <p>Then in <strong>Subnet associations → Edit subnet associations</strong>, I associated <strong>Public Subnet</strong> with this route table. The public subnet is now genuinely public.</p>

  <h2 id="bastion">5. Launching the Bastion Server</h2>
  <p>In EC2 → Launch instance I configured:</p>
  <table>
    <thead><tr><th>Section</th><th>Value</th></tr></thead>
    <tbody>
      <tr><td>Name</td><td><code>Bastion Server</code></td></tr>
      <tr><td>AMI</td><td>Amazon Linux 2023 AMI</td></tr>
      <tr><td>Instance type</td><td><code>t3.micro</code></td></tr>
      <tr><td>Key pair</td><td>Proceed without a key pair</td></tr>
      <tr><td>VPC</td><td>Lab VPC</td></tr>
      <tr><td>Subnet</td><td>Public Subnet</td></tr>
      <tr><td>Auto-assign public IP</td><td>Enable</td></tr>
      <tr><td>Security group</td><td><code>Bastion Security Group</code> — Allow SSH</td></tr>
      <tr><td>Inbound rule</td><td>SSH from Anywhere (0.0.0.0/0)</td></tr>
    </tbody>
  </table>
  <p>I clicked <strong>Launch instance</strong> and moved on — I didn't wait for it to finish booting.</p>

  <h2 id="nat">6. Creating the NAT Gateway</h2>
  <p>I opened <strong>NAT gateways</strong> → <strong>Create NAT gateway</strong>:</p>
  <ul class="done">
    <li>Name: <code>Lab NAT gateway</code></li>
    <li>Subnet: <strong>Public Subnet</strong></li>
    <li>Clicked <strong>Allocate Elastic IP</strong></li>
  </ul>
  <p>Then I opened <strong>Private Route Table → Routes → Edit routes</strong> and added:</p>
  <ul class="done">
    <li>Destination: <code>0.0.0.0/0</code></li>
    <li>Target: <strong>NAT Gateway → nat-...</strong></li>
  </ul>
  <p>Private subnet traffic to the internet now flows out through the NAT gateway.</p>

  <h2 id="challenge">7. Optional challenge — private instance test</h2>
  <h3>Launched the Private Instance</h3>
  <table>
    <thead><tr><th>Section</th><th>Value</th></tr></thead>
    <tbody>
      <tr><td>Name</td><td><code>Private Instance</code></td></tr>
      <tr><td>Subnet</td><td>Private Subnet</td></tr>
      <tr><td>Security group</td><td><code>Private Instance SG</code> — Allow SSH from Bastion</td></tr>
      <tr><td>Inbound rule</td><td>SSH from <code>10.0.0.0/16</code></td></tr>
    </tbody>
  </table>
  <p>In <strong>Advanced details → User data</strong>, I pasted the lab's password-login script:</p>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>#!/bin/bash
echo 'lab-password' | passwd ec2-user --stdin
sed -i 's|[#]*PasswordAuthentication no|PasswordAuthentication yes|g' /etc/ssh/sshd_config
systemctl restart sshd.service</code></pre>

  <h3>Connected to the Bastion, then to the Private Instance</h3>
  <p>From the bastion host's EC2 Instance Connect session, I copied the private instance's <strong>Private IPv4</strong> from the console and ran:</p>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>ssh 10.0.2.146</code></pre>
  <p>Answered <code>yes</code> to the fingerprint prompt and entered the password <code>lab-password</code>. I was in!</p>

  <h3>Verified NAT works</h3>
  <pre><button class="copy-btn" onclick="copyCode(this)">Copy</button><code>ping -c 3 google.com</code></pre>
  <p>I got replies with <code>0% packet loss</code>, confirming traffic successfully flowed <strong>Private Subnet → NAT Gateway → Internet</strong>. ✅</p>

  <h2 id="issues">8. Issues I ran into</h2>
  <table>
    <thead><tr><th>What happened</th><th>What I did</th></tr></thead>
    <tbody>
      <tr><td>The public route table was accidentally associated with the Private Subnet, so EC2 Instance Connect failed on the Bastion server.</td><td>I went to the Public Subnet's route table association settings and switched it to the correct Public Route Table (routing <code>0.0.0.0/0</code> to the IGW).</td></tr>
      <tr><td>When I initially added the NAT route, I accidentally added an IGW route to the Private Route Table, causing a conflict.</td><td>I edited the Private Route Table, removed the IGW route, and saved, leaving only the NAT and local routes.</td></tr>
      <tr><td>Pinging <code>amazon.com</code> resulted in 100% packet loss from the Private Instance.</td><td>I realized some Amazon servers drop ICMP (ping) traffic. I tested with <code>ping google.com</code> instead, which succeeded and proved the NAT Gateway was working.</td></tr>
    </tbody>
  </table>

  <h2 id="summary">9. Summary of what I completed</h2>
  <ul class="done">
    <li>Created a VPC <code>10.0.0.0/16</code> with DNS hostnames enabled</li>
    <li>Created Public (<code>10.0.0.0/24</code>) and Private (<code>10.0.2.0/23</code>) subnets in one AZ</li>
    <li>Attached Internet Gateway <strong>Lab IGW</strong></li>
    <li>Configured route tables: public via IGW, private via NAT</li>
    <li>Launched a Bastion Server in the public subnet</li>
    <li>Created a NAT Gateway and pointed private-subnet traffic to it</li>
    <li>Connected to a private instance through the bastion and confirmed internet via <code>ping</code></li>
  </ul>

</main>

<footer>
  My VPC lab notes · Region: us-west-2
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
