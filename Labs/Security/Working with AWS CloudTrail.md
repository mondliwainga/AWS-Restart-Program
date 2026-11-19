<h1 align="center">AWS CloudTrail Investigation Lab</h1>

<p align="center">
  <strong>Scenario:</strong> The Café website was hacked. Using AWS CloudTrail, I traced the attacker,
  identified the root cause, and secured the environment.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Services-EC2%20%7C%20CloudTrail%20%7C%20S3%20%7C%20IAM%20%7C%20Athena%20%7C%20AWS%20CLI-blue" alt="Services">
  <img src="https://img.shields.io/badge/Status-Completed-brightgreen" alt="Status">
  <img src="https://img.shields.io/badge/Difficulty-Intermediate-yellow" alt="Difficulty">
</p>

---

<h2>Objectives</h2>

<ul>
  <li>Configure a CloudTrail trail</li>
  <li>Analyze logs with grep, AWS CLI, and Athena</li>
  <li>Identify the attacker</li>
  <li>Remediate and harden the environment</li>
</ul>

---

<h2>What I Did</h2>

<ol>
  <li><strong>Task 1</strong> — Added an SSH rule (My IP only) and confirmed the Café site worked.</li>
  <li><strong>Task 2</strong> — Created a trail named <code>monitor</code> with an S3 bucket (<code>monitoring####</code>). The site was then hacked, and an unauthorized SSH <code>0.0.0.0/0</code> rule appeared.</li>
  <li><strong>Task 3</strong> — Downloaded logs from S3 to the EC2 instance and filtered with <code>grep</code> and <code>jq</code>. Queried CloudTrail directly:
    <pre><code>aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=AuthorizeSecurityGroupIngress \
  --output text</code></pre>
  </li>
  <li><strong>Task 4</strong> — Imported logs into Athena and confirmed the hack with SQL:
    <pre><code>SELECT useridentity.userName, eventtime, eventname, sourceipaddress, useragent
FROM cloudtrail_logs_monitoring####
WHERE eventname = 'AuthorizeSecurityGroupIngress'
ORDER BY eventtime;</code></pre>
  </li>
  <li><strong>Task 5</strong> — Removed the <code>chaos-user</code> OS account, disabled SSH password auth, deleted the <code>0.0.0.0/0</code> rule, restored the website image, and deleted the <code>chaos</code> IAM user.</li>
</ol>

---

<h2>Findings</h2>

<table>
  <thead>
    <tr>
      <th align="left">Field</th>
      <th align="left">Value</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>IAM User</td><td><code>chaos</code></td></tr>
    <tr><td>Time</td><td><code>2026-11-19T13:54:35Z</code></td></tr>
    <tr><td>Source IP</td><td><code>44.243.249.38</code></td></tr>
    <tr><td>Method</td><td>AWS CLI (programmatic)</td></tr>
    <tr><td>Action</td><td><code>AuthorizeSecurityGroupIngress</code> — opened SSH to <code>0.0.0.0/0</code></td></tr>
    <tr><td>Root Cause</td><td>SSH password authentication enabled on the instance</td></tr>
  </tbody>
</table>

---

<h2>Lessons Learned</h2>

<ol>
  <li>Disable SSH password auth — use key pairs.</li>
  <li>Never open SSH to <code>0.0.0.0/0</code>.</li>
  <li>Enable CloudTrail in all regions.</li>
  <li>Audit IAM users regularly.</li>
  <li>Use Athena for fast log forensics.</li>
</ol>

---

<h2>Troubleshooting</h2>

<table>
  <thead>
    <tr>
      <th align="left">Issue</th>
      <th align="left">Fix</th>
    </tr>
  </thead>
  <tbody>
    <tr><td><code>python: command not found</code></td><td>Use <code>python3</code> or <code>jq</code></td></tr>
    <tr><td><code>gunzip *.gz</code> fails</td><td><code>find AWSLogs -name "*.gz" -exec gunzip {} \;</code></td></tr>
    <tr><td>No logs yet</td><td>Wait ~5 min — CloudTrail publishes every 5 minutes</td></tr>
    <tr><td>Nested <code>ctraillogs</code> folder</td><td><code>cd ~/ctraillogs/ctraillogs</code></td></tr>
    <tr><td>Hack missing from logs</td><td>Query the API directly with <code>aws cloudtrail lookup-events</code></td></tr>
  </tbody>
</table>

---

<h2>Key Takeaway</h2>

<blockquote>
  <p><em>You can't defend what you can't see — enable CloudTrail everywhere.</em></p>
</blockquote>
