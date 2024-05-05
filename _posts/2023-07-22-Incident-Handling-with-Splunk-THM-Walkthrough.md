---
title: "Incident Handling With Splunk / Splunk 201 (TryHackMe Walkthrough)"
teaser: /assets/images/thm_splunk_incident_handling_featured.PNG
og_image: /assets/images/thm_splunk_incident_handling_featured.PNG
categories:
  - Blog
tags:
  - Cybersecurity
  - SIEM
  - Splunk
  - Log Management
  - Monitoring

---


### Learn to use Splunk for incident handling through interactive scenarios.

<img src="/assets/images/thm_splunk_incident_handling.PNG">
Room:<a href="https://tryhackme.com/r/room/splunk201"> https://tryhackme.com/r/room/splunk201</a>


<h4>Learning Objectives and Pre-requisites</h4>
Before going through this room, it is expected that the participants will have a basic understanding of Splunk. If not, consider going through this room, Splunk 101 (https://tryhackme.com/jr/splunk101).
<ul>
	<li>Learn how to leverage OSINT sites during an investigation</li>
	<li>How to map Attacker's activities to Cyber Kill Chain Phases</li>
	<li>How to utilize effective Splunk searches to investigate logs</li>
  <li>Understand the importance of host-centric and network-centric log sources</li>
</ul>

<h5>Task 1 Introduction: Incident Handling </h5>

<strong> Q: Read the above and continue to the next task. </strong> <br>
<em> Answer: No answer needed </em>


<h5>Task 2 Incident Handling - Life Cycle </h5>

<strong> Q: Ccontinue to the Next task. </strong><br>
<em> Answer: No answer needed </em>


<h5>Task 3 Incident Handling: Scenario </h5>

<strong> Q: Continue with the lab </strong><br>
<em> Answer: No answer needed </em>

<h5>Task 4 Reconnaissance Phase </h5>

<strong> Q1: One suricata alert highlighted the CVE value associated with the attack attempt. What is the CVE value? </strong><br>
<em> Answer: CVE-2014-6271 </em><br>

<strong> Q2: What is the CMS our web server is using? </strong><br>
<em> Answer: joomla </em><br>

<strong> Q3: What is the web scanner, the attacker used to perform the scanning attempts? </strong><br>
<em> Answer: acunetix </em><br>

<strong> Q4: What is the IP address of the server imreallynotbatman.com? </strong><br>
<em> Answer: 192.168.250.70 </em><br>


<h5>Task 5 Exploitation Phase </h5>

<strong> Q1: What was the URI which got multiple brute force attempts?  </strong><br>
<em> Answer: /joomla/administrator/index.php  </em><br>

<strong> Q2: Against which username was the brute force attempt made?
  </strong><br>
<em> Answer:  admin </em><br>

<strong> Q3: What was the correct password for admin access to the content management system running imreallynotbatman.com?

  </strong><br>
<em> Answer: batman  </em><br>

<strong> Q4:  What IP address is likely attempting a brute force password attack against imreallynotbatman.com </strong><br>
<em> Answer:  23.22.63.114 </em><br>

<strong> Q5:  After finding the correct password, which IP did the attacker use to log in to the admin panel?
 </strong><br>
<em> Answer:  40.80.148.42 </em><br>


<h5>Task 6 Installation Phase</h5>
<strong> Q1: Sysmon also collects the Hash value of the processes being created. What is the MD5 HASH of the program 3791.exe?  </strong><br>
<em> Answer: AAE3F5A29935E6ABCC2C2754D12A9AF0  </em><br>

<strong> Q2:  Looking at the logs, which user executed the program 3791.exe on the server?</strong><br>
<em> Answer:  NT AUTHORITY\IUSR </em><br>


<strong> Q3:  Search hash on the virustotal. What other name is associated with this file 3791.exe? </strong><br>
<em> Answer:  ab.exe </em><br>


<h5>Task 7 Action on Objectives </h5>
<strong> Q1:  What is the name of the file that defaced the imreallynotbatman.com website ? </strong><br>
<em> Answer:   poisonivy-is-coming-for-you-batman.jpeg</em><br>

<strong> Q2:  Fortigate Firewall 'fortigate_utm' detected SQL attempt from the attacker's IP 40.80.148.42. What is the name of the rule that was triggered during the SQL Injection attempt?</strong><br>
<em> Answer:  HTTP.URI.SQL.Injection </em><br>

<h5>Task 8 Command and Control Phase </h5>
<strong> Q1: This attack used dynamic DNS to resolve to the malicious IP. What fully qualified domain name (FQDN) is associated with this attack?</strong><br>
<em> Answer: prankglassinebracket.jumpingcrab.com  </em><br>

<h5>Task 9 Weaponization Phase </h5>

<strong> Q1:   What IP address has P01s0n1vy tied to domains that are pre-staged to attack Wayne Enterprises?</strong><br>
<em> Answer:   23.22.63.114</em><br>

<strong> Q2:  Based on the data gathered from this attack and common open-source intelligence sources for domain names, what is the email address that is most likely associated with the P01s0n1vy APT group? </strong><br>
<em> Answer:  lillian.rose@po1s0n1vy.com </em><br>

<h5>Task 10 Delivery Phase </h5>

<strong> Q1: What is the HASH of the Malware associated with the APT group?  </strong><br>
<em> Answer: c99131e0169171935c5ac32615ed6261  </em><br>


<strong> Q2:  What is the name of the Malware associated with the Poison Ivy Infrastructure? </strong><br>
<em> Answer:  MirandaTateScreensaver.scr.exe </em><br>



<h5>Task 11 Conclusion </h5>


<strong> Q4:  Read the above. </strong><br>
<em> Answer:  No answer needed </em><br>