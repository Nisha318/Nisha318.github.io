---
title: "Steel Mountain - TryHackMe Walkthrough by Nisha"
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
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
teaser: assets/images/thm/thm-ice-00.png  
tagline: "Steel Mountain - TryHackMe Walkthrough by Nisha"
header:
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/thm/thm-ice-0.png
  caption: "Photo credit: [**imgur**](https://imgur.com/6Ijftag)"
---

<h5>Hack into a Mr. Robot themed Windows machine. Use metasploit for initial access, utilise powershell for Windows privilege escalation enumeration and learn a new technique to get Administrator access. </h5>

<img src="/assets/images/thm/ice-1.png">

<strong> Room Link: </strong> <a href="https://tryhackme.com/r/room/ice"> https://tryhackme.com/r/room/ice</a>


{% include toc %}

## Task 1 - Introduction

Deploy the machine.

<img src="/assets/images/thm/thm-steel-mountain-1.png">

Q: Who is the employee of the month?

A: Bill Harper


<img src="/assets/images/thm/thm-steel-mountain-2.png">

<img src="/assets/images/thm/thm-steel-mountain-3.png">

<img src="/assets/images/thm/thm-steel-mountain-4.png">


## Task 2 - Initial Access

<strong>Q: Scan the machine with nmap. What is the other port running a web server on? </strong> <br>

<em> A: 8080 </em>

<strong>Q: Take a look at the other web server. What file server is running?</strong> <br>
<img src="/assets/images/thm/thm-steel-mountain-6.png">

<em>A: Rejetto HTTP File Server</em>


<strong>Q: What is the CVE number to exploit this file server?</strong> <br>

<img src="/assets/images/thm/thm-steel-mountain-5.png">

<em>A: 2014-6287</em>

<strong>Q: Use Metasploit to get an initial shell. What is the user flag?</strong> <br>

<img src="/assets/images/thm/thm-steel-mountain-7.png">

<img src="/assets/images/thm/thm-steel-mountain-8.png">
<img src="/assets/images/thm/thm-steel-mountain-9.png">
<img src="/assets/images/thm/thm-steel-mountain-10.png">

<img src="/assets/images/thm/thm-steel-mountain-11.png">
<img src="/assets/images/thm/thm-steel-mountain-12.png">

<em>A: b04763b6fcf51fcd7c13abc7db4fd365</em>


## Task 3 - Privilege Escalation

We have an initial shell on this machine as user Bill.  Now, we will further enumerate the machine and escalate our privileges to root to obtain the final flag!

We can use the PowerUp PowerShell script to esclate our privileges.

Download the script using the following command:

wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1


We will use the upload command in Metasploit to upload the script. 


To execute this using Meterpreter, we will type load powershell into meterpreter. Then we will enter powershell by entering powershell_shell:


## Task 4 - Access and Escalation Without Metasploit 