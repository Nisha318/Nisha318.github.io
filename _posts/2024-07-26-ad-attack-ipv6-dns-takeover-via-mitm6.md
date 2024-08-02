---
title: "IPv6 DNS Takeover with mitm6 in an Active Directory Environment"
date: 2024-07-22
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
categories:
  - Blog
tags:
  - Cybersecurity
  - Offensive
  - mitm6
  - Penetration Testing
  - Ethical Hacking
  - Active Directory
  - NTLM Relay
  - PNPT Exam

tagline: "Unveiling Mitm6 and NTLM Relay in an Active Directory Environment"
header:
  teaser: assets/images/htb/htb-meow-0.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/ctf_hero_header_image.png
  caption: "Photo credit: [**AI**](https://chatgpt.com/)"
---

## Table of Contents
1. [Introduction](#introduction)
2. [Understanding IPv6 DNS Takeover](#understanding-ipv6-dns-takeover)
3. [Mitigation Strategies](#mitigation-strategies)
4. [Demonstration Steps](#demonstration-steps)
    - [Step 1: Setup NTLM Relay using LDAPS](#step-1-setup-ntlm-relay-using-ldaps)
    - [Step 2: Run MITM6](#step-2-run-mitm6)
    - [Simulate Event and Capture Data](#simulate-event-and-capture-data)
    - [Simulate Login and Gain Access](#simulate-login-and-gain-access)
5. [Conclusion](#conclusion)

## Introduction

In this blog post, I will walk you through a demonstration of an IPv6 DNS takeover attack using the mitm6 (Man in the Middle for IPv6) tool in an Active Directory (AD) pentesting environment. This type of attack exploits weaknesses in the network's handling of IPv6, allowing an attacker to become a Man-in-the-Middle (MITM) and relay NTLM authentication requests to a Domain Controller (DC).

<img src="/assets/images/tcm-academy/tcm-mitm6-1.png">


## Understanding IPv6 DNS Takeover

IPv6 DNS takeover is a network attack where an attacker uses IPv6 to become the default DNS server for the network. This allows them to intercept and manipulate DNS requests and responses. In combination with tools like NTLMRelayx, attackers can relay authentication requests to a Domain Controller, gaining unauthorized access to network resources.

## Mitigation Strategies

To protect against IPv6 DNS takeover attacks, consider implementing the following mitigation strategies:
- **Disable IPv6** if it is not required in your environment.
- **Use strong authentication mechanisms**, such as Kerberos, which are not vulnerable to relay attacks.
- **Monitor network traffic** for unusual IPv6 activity.
- **Implement network segmentation** to limit the impact of compromised devices.
- **Regularly update and patch systems** to fix known vulnerabilities.

## Demonstration Steps

Below are the steps I took to implement the IPv6 DNS takeover attack using mitm6 and NTLMRelayx tools in my Active Directory pentesting lab.

### Step 1: Setup NTLM Relay using LDAPS

First, I set up NTLM relay using LDAPS with the Domain Controller (DC) as the target. This step involves using a previously set up certificate.

```bash
sudo ntlmrelayx.py -6 -t ldaps://192.168.86.30 -wh fakewpad.marvel.local -l lootme
```
<img src="/assets/images/tcm-academy/tcm-mitm6-2.png">


### Step 2: Run MITM6 (Man in the Middle 6)

Next, I executed mitm6 to start the Man-in-the-Middle attack for IPv6 on the target domain marvel.local.

```bash
sudo mitm6 -d marvel.local
```



<img src="/assets/images/tcm-academy/tcm-mitm6-3.png">

IP addresses start to get assigned here, we are becoming the MITM for IPv6 for these devices. 



When an event occurs on network, reboot, logging into a device, will allow us to take that event and relay it via NTLM relay to the Domain Controller. 



<img src="/assets/images/tcm-academy/tcm-mitm6-4.png">


<img src="/assets/images/tcm-academy/tcm-mitm6-5.png">

<img src="/assets/images/tcm-academy/tcm-mitm6-6.png">


To trigger the attack, I simulated an event by rebooting a machine, specifically Punisher. This action allowed us to capture and relay authentication events via NTLM relay to the Domain Controller.

After rebooting Punisher, we captured a significant amount of data from the LDAP domain dump, including details about domain computers, groups, and users.
<img src="/assets/images/tcm-academy/tcm-mitm6-7.png">

<img src="/assets/images/tcm-academy/tcm-mitm6-8.png">

Finally, I simulated a login on the network by logging into THEPUNISHER as MARVEL\administrator. The attack was successful, allowing us to set access controls and create a new user account with elevated privileges.


Domain Computers

<img src="/assets/images/tcm-academy/tcm-mitm6-9.png">


Domain Groups

<img src="/assets/images/tcm-academy/tcm-mitm6-10.png">


Domain Users

<img src="/assets/images/tcm-academy/tcm-mitm6-11.png">

Simulate login on the network, log into THEPUNISHER, as MARVEL\administrator

Attack is successful, Access Controls are set and a new user account gets created for us and we are able to login with this username and password. 

<img src="/assets/images/tcm-academy/tcm-mitm6-12.png">

<img src="/assets/images/tcm-academy/tcm-mitm6-13.png">

<img src="/assets/images/tcm-academy/tcm-mitm6-14.png">

<img src="/assets/images/tcm-academy/tcm-mitm6-15.png">



With the newly created account, we could perform further attacks, such as a DCSync attack using secretsdump.py, to compromise the entire domain.


<img src="/assets/images/tcm-academy/tcm-mitm6-16.png">

This user account was created as just a domain admin, so it will not have access to every single computer in the domain. However, they will have access to the Enterprise Admins group and they will have very specific access to run secretsdump.py against the domain controller, dump out the entire secrets of the domain, and they'll compromise the domain.  And then, you can utilize the hashes that you find there to completely own the domain whichever way you want. 


<img src="/assets/images/tcm-academy/tcm-mitm6-17.png">

## Conclusion

This lab demonstration highlights the critical vulnerabilities associated with IPv6 DNS takeover attacks. By exploiting weaknesses in network configurations and authentication protocols, attackers can gain unauthorized access to sensitive information and control over network resources.

To protect your organization from such attacks, it's essential to implement effective security measures, such as disabling IPv6 if not needed, using strong authentication mechanisms like Kerberos, and continuously monitoring for unusual network activity. Regularly updating and patching systems also plays a crucial role in mitigating these risks.

Understanding and addressing these vulnerabilities is vital for maintaining a secure and resilient network infrastructure. As always, staying informed about the latest threats and best practices in cybersecurity is key to defending against potential attacks.

Feel free to share your thoughts and experiences with similar attacks in the comments below. Stay safe and secure!