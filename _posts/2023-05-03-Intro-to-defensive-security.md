---
title: "Introduction to Defensive Security - THM Walkthrough by Nisha"
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
categories:
  - Blog
tags:
  - Cybersecurity
  - Defensive
  - Blue Team
  - TryHackMe

tagline: "Introducing defensive security and related topics, such as threat intelligence, SOC, DFIR, and SIEM."
header:
  teaser: assets/images/thm/thm-intro-to-defensive-security1.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/thm/thm-intro-to-defensive-security0.png
  caption: "Photo credit: [**TryHackMe**](https://tryhackme.com)"
---

<strong> Room Link: </strong> <a href="https://tryhackme.com/r/room/defensivesecurity">https://tryhackme.com/r/room/defensivesecurity</a>

## Task 1 - Introduction to Defensive Security

<strong> Learning Objectives: </strong>
Offensive security primarily aims at breaking into systems through various methods like exploiting bugs, abusing insecure setups, and bypassing access control policies. This is the realm of red teams and penetration testers.

On the other hand, defensive security focuses on two key tasks:
<ul>
    <li>Preventing intrusions from occurring.</li>
    <li>Detecting intrusions when they happen and responding appropriately.</li>
</ul>

<p>

Blue teams are central to the defensive security field, and their responsibilities include:

<ul>

    <li>User Cyber Security Awareness: Training users to protect against attacks targeting their systems.</li>
    <li>Documenting and Managing Assets: Keeping track of systems and devices to manage and protect them effectively.</li>
    <li>Updating and Patching Systems: Ensuring all systems are updated and patched to defend against known vulnerabilities.</li>
    <li>Setting Up Preventative Security Devices: Implementing firewalls and intrusion prevention systems (IPS) to control and block network traffic based on rules and attack signatures.</li>
    <li>Setting Up Logging and Monitoring Devices: Monitoring the network to detect malicious activities and intrusions, such as unauthorized devices appearing on the network.</li> <br>
</ul>

This room covers various critical topics in defensive security, including:

<ul>
    <li> Security Operations Center (SOC)</li>
    <li> Threat Intelligence</li>
    <li> Digital Forensics and Incident Response (DFIR)</li>
    <li> Malware Analysis</li>

</ul>


By completing this room, you will gain a comprehensive understanding of these essential aspects of defensive security.

<strong> Question: Which team focuses on defensive security? </strong> <br>

<em>Answer: Blue Team</em>  <br>

</p>


## Task 2 - Areas of Defensive Security

<p>
Areas of Defensive Security: An Overview <br>
In the "Intro to Defensive Security" room, two primary topics are covered:
<ol>
    <li> Security Operations Center (SOC) and Threat Intelligence </li>
    <li>Digital Forensics and Incident Response (DFIR), including Malware Analysis </li>
</ol>

</p>


<strong> Security Operations Center (SOC) </strong>
A Security Operations Center (SOC) is a team of cybersecurity experts who monitor network systems to detect and respond to malicious activities. Key areas of focus for a SOC include:

<ul>
    <li>Vulnerabilities: Identifying and patching system weaknesses to prevent exploitation.</li>
    <li>Policy Violations: Ensuring compliance with security policies, such as preventing the unauthorized uploading of sensitive data.</li>
    <li>Unauthorized Activity: Detecting and blocking unauthorized access, such as stolen login credentials.</li>
    <li>Network Intrusions: Identifying and mitigating intrusions, whether through malicious links or exploited servers.</li>
</ul>

SOC responsibilities also include threat intelligence, which involves gathering information on potential threats to prepare and defend against adversaries.

<strong>Threat Intelligence</strong>
Threat intelligence involves collecting and analyzing data on actual and potential threats to inform a company's defense strategies. This process includes:

<ul>
    <li>Data Collection: Gathering information from network logs and public sources. </li>
   <li>Data Processing and Analysis: Organizing data for analysis to understand attackers' motives and tactics.
    <li>Actionable Insights: Developing recommendations to mitigate threats and enhance security.</li>
    <li>Understanding adversaries' tactics helps predict and counteract their actions effectively.</li>
</ul>


<strong>Digital Forensics and Incident Response (DFIR)</strong>

<p>
DFIR encompasses the following areas:

    1. Digital Forensics: Investigating and analyzing digital evidence to understand attacks and identify perpetrators. 

Key areas include:
<ul>
    <li>File System Analysis: Examining storage for programs, files, and deleted content.</li>
    <li>System Memory Analysis: Investigating system memory for malicious programs.</li>
    <li>System Logs: Reviewing logs for activity and traces of attacks.</li>
    <li>Network Logs: Analyzing network traffic to detect ongoing or past attacks.</li>
</ul>

    2. Incident Response: Managing and responding to cyber incidents to minimize damage and recover quickly. The incident response process includes:

<ul>
    <li> Preparation: Training teams and implementing preventive measures.</li>
    <li>Detection and Analysis: Identifying and assessing incidents.</li>
    <li>Containment, Eradication, and Recovery: Stopping the spread, eliminating threats, and restoring systems.</li>
    <li>Post-Incident Activity: Reporting and learning from incidents to prevent future occurrences.</li>
</ul>

<img src="/assets/images/thm/thm-intro-to-defensive-security3.png">

<strong>Malware Analysis</strong>
Malware analysis involves studying malicious software to understand its behavior and impact. Types of malware include:

<ul>
    <li>Virus: Code that spreads by altering files.</li>
    <li>Trojan Horse: Malicious software disguised as a legitimate program.</li>
    <li>Ransomware: Software that encrypts files and demands a ransom for decryption.</li>
</ul>


<H5>Analysis methods include:</H5>

<ul>

    <li>Static Analysis: Inspecting malware without executing it, often requiring knowledge of assembly language.</li>
    <li>Dynamic Analysis: Running malware in a controlled environment to observe its behavior.</li>


By understanding malware, security professionals can develop strategies to detect, prevent, and respond to such threats effectively.

<strong>Question:</strong>What would you call a team of cyber security professionals that monitors a network and its systems for malicious events?<br>
<em>Answer: Security Operations Center </em>

<strong>Question:</strong>What does DFIR stand for?<br>
<em>Answer: Digital Forensics and Incident Response<br>
<strong>Question:<strong>Which kind of malware requires the user to pay money to regain access to their files?<br>

<em> Answer: ransomware </em>

</p>

## Task 3 - Practical Example of Defensive Security

<img src="/assets/images/thm/thm-intro-to-defensive-security2.png">
<p>
In this exercise, we will interact with a SIEM to monitor the different events on our network and systems in real-time. Some of the events are typical and harmless; others might require further intervention from us. Find the event flagged in red, take note of it, and click on it for further inspection.

Next, we want to learn more about the suspicious activity or event. The suspicious event might have been triggered by an event, such as a local user, a local computer, or a remote IP address. To send and receive postal mail, you need a physical address; similarly, you need an IP address to send and receive data over the Internet. An IP address is a logical address that allows you to communicate over the Internet. We inspect the cause of the trigger to confirm whether the event is indeed malicious. If it is malicious, we need to take due action, such as reporting to someone else in the SOC and blocking the IP address.

<img src="/assets/images/thm/thm-intro-def.gif">

</p>


<strong> Question: </strong>What is the flag that you obtained by following along?<br>
<em> Answer: THM{THREAT-BLOCKED}</em>

