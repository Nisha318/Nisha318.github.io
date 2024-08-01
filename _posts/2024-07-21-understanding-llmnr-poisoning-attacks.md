---
title: "Understanding LLMNR Poisoning and Mitigation Techniques"
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
  - Active Directory
  - LLMNR

 
tagline: "Master the tactics and defenses against LLMNR poisoning to secure your network."
header:
  teaser: assets/images/tcm-academy/tcm-llmnr-0.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/tcm-academy/tcm-llmnr-0.png
  caption: "Photo credit: [**AI**](https://chatgpt.com/)"
---


## Introduction
In this blog post, I will be discussing a common network attack called Link-Local Multicast Name Resolution (LLMNR) poisoning. This type of attack can be highly effective in capturing NTLMv2 hashes, which can then be used to gain unauthorized access to systems in a Windows environment. I will also share insights on how to mitigate this this type of vulnerability attack, ensuring your network remains secure. To illustrate this attack, I have included screenshots from my recent lab demonstration where I successfully captured NTLMv2 hashes using Responder.

## What is LLMNR Poisoning?
Link-Local Multicast Name Resolution (LLMNR) is a protocol that allows computers on the same local network to perform name resolution for hosts when DNS queries fail. LLMNR operates similarly to NetBIOS over TCP/IP, enabling name resolution by sending multicast queries to all devices in the same subnet.

LLMNR poisoning is an attack technique where an attacker responds to these multicast queries pretending to be the requested host. By doing so, the attacker can capture sensitive information such as usernames and hashed passwords (NTLMv2 hashes). This information can be further exploited to perform pass-the-hash attacks or brute-force the password offline.
<img src="/assets/images/tcm-academy/tcm-llmnr-1.png">

## How LLMNR Poisoning Works
<ol>
<li> <strong>Network Monitoring:</strong> The attacker monitors the network for LLMNR and NetBIOS Name Service (NBT-NS) requests using tools like Responder.</li>

<li><strong>Spoofing Responses:</strong> When a legitimate LLMNR request is broadcasted (e.g., a user mistypes a network resource name), the attacker’s machine responds to the query, pretending to be the requested resource.</li>

<li><strong>Capturing Hashes:</strong> The requesting machine, believing it has found the correct resource, sends its credentials (in the form of NTLMv2 hashes) to the attacker.</li>

<li><strong>Hash Exploitation:</strong> The attacker captures these hashes and can either brute-force them offline or use them in pass-the-hash attacks to gain unauthorized access to the network.</li>

</ol>

## Demonstration: Capturing NTLMv2 Hashes with Responder
In my lab setup, I simulated an LLMNR poisoning attack using the Responder tool. Below are the steps I followed, along with screenshots from the demonstration:
<ol>
<li><strong>Setting up Responder: </strong>I configured Responder to listen for LLMNR and NBT-NS queries on my local network.</li>

<img src="/assets/images/tcm-academy/llmnr-capture-ntlmv2hash-1.png">

<li><strong>Triggering Event:</strong> I triggered an LLMNR event by initiating a connection to the SMB share from a legitimate user account via a client machine on the network. </li>

<img src="/assets/images/tcm-academy/llmnr-capture-ntlmv2hash-3.png">

<li><strong>Capturing Queries:</strong> As soon as a legitimate user made an LLMNR request, Responder intercepted and responded to the query.</li>


<li><strong>Capturing Hashes:</strong> The user’s machine sent its NTLMv2 hash to my attacking machine, which was captured and displayed by Responder.</li>

<img src="/assets/images/tcm-academy/llmnr-capture-ntlmv2hash-4.png">
<ul>
  <li>The IP address of the victim (in this example: 192.168.86.38)</li>
  <li>The domain and username of the victim (in this example: MARVEL\fcastle)</li>
  <li>The victim’s password hash</li>
</ul>

With the victim’s hash in hand, I can attempt to use several tools to crack it and uncover the user's password.  


<li> <strong>Preparing Hashes for Cracking: </strong> I saved the hashes into files that are suitable for use with some of the popular hash cracking tools on Kali Linux. 
</li>

<img src="/assets/images/tcm-academy/llmnr-capture-ntlmv2hash-5.png">


<li> <strong>Cracking the Hashes to Uncover the User's Password:</strong>  I used Hashcat and John the Ripper tools to crack the hashes and reveal the password for the victim user. 
</li>

Here is used the <a href="https://hashcat.net/hashcat/">Hashcat</a> tool on my initial attempt to crack the user's password hash.
</ol>
```
 hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt 
 ```


<img src="/assets/images/tcm-academy/llmnr-capture-ntlmv2hash-6.png">

<img src="/assets/images/tcm-academy/llmnr-capture-ntlmv2hash-8.png">

Next, I used <a href=" "> John the Ripper</a> to take another crack at it:

```
 sudo john --wordlist=/usr/share/wordlists/rockyou.txt punisher.hash
 ```


<img src="/assets/images/tcm-academy/llmnr-capture-ntlmv2hash-7.png">






## Mitigating LLMNR Poisoning Attacks

To protect your network from LLMNR poisoning attacks, it is essential to implement the following mitigation strategies:
<ol>
  <li>Disable LLMNR and NBT-NS:</li>
<ul>

    <li>Group Policy: On Windows networks, you can disable LLMNR through Group Policy. 
    Navigate to Computer Configuration -> Administrative Templates -> Network -> DNS Client and set the Turn off multicast name resolution policy to Enabled.</li>

    <li>Registry: You can also disable NBT-NS by modifying the registry. Set Start to 4 in the following registry path: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NetBT\Parameters\Interfaces.</li>
</ul>
    <li><strong> Use Strong Passwords:</strong> Ensure that all user accounts have strong, complex passwords to make brute-forcing NTLMv2 hashes more difficult.</li>

    <li> <strong>Network Segmentation:</strong> Implement network segmentation to limit the exposure of sensitive information and reduce the attack surface.</li>

    <li><strong>Monitor Network Traffic:</strong> Use network monitoring tools to detect unusual LLMNR and NBT-NS traffic, which could indicate an ongoing attack.</li>

    <li><strong>Implement SMB Signing:</strong> Enable SMB signing to protect against man-in-the-middle attacks, ensuring the authenticity of SMB communications.</li>

    <li><strong>Use DNSSEC:</strong> Implement DNS Security Extensions (DNSSEC) to ensure the authenticity and integrity of DNS responses.</li>

    <li><strong>Educate Users:</strong> Train users to recognize and report unusual network behavior, such as frequent authentication prompts, which could indicate an attack.</li>

</ol>

## Conclusion
LLMNR poisoning is a serious threat that can compromise network security by capturing NTLMv2 hashes. However, by understanding how the attack works and implementing the mitigation strategies outlined in this post, you can protect your network from such vulnerabilities. Always stay vigilant and proactive in securing your network infrastructure.

Stay safe and secure!