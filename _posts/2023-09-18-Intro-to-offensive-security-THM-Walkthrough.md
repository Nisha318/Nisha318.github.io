---
title: "Introduction to Offensive Security - THM Walkthrough by Nisha"
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
  - TryHackMe

teaser: assets/images/thm/thm-intro-to-offensive-security0.png  
tagline: "Hack your first website (legally in a safe environment) and experience an ethical hacker's job."
header:
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/thm/thm-intro-to-offensive-security.png
  caption: "Photo credit: [**TryHackMe**](https://tryhackme.com)"
---

## Task 1 - 

<strong> Question: Which of the following options better represents the process where you simulate a hacker's actions to find vulnerabilities in a system? </strong> <br>
<ul>
  <li>Offensive Security</li>
  <li>Defensive Security</li>

</ul>

<em>Answer: Offensive Security</em>  <br>

## Task 2 - Hacking Your First Machine

This TryHackMe module introduces beginners to hacking by guiding them through a simulated and legal exercise. The task involves using a command-line tool called GoBuster to brute-force the website of a fake bank application, FakeBank, to uncover hidden directories and pages. Participants start by opening a terminal on a virtual machine provided by the platform. <br>

The task includes: <br>
<ul>

  <li> Starting the Machine: Load a virtual machine in Split View to access FakeBank. </li>
  <li> Using GoBuster: Execute a command in the terminal to find hidden pages on the FakeBank website by brute-forcing with a list of potential page names. </li>
  <li> Accessing Hidden Pages: Locate a hidden bank transfer page and perform a simulated hack by transferring money between accounts, demonstrating how an attacker might exploit such vulnerabilities. </li>
  <li> Verify the success of the transfer and answer related questions before terminating the virtual machine. This exercise simulates real-world penetration testing to identify and report vulnerabilities in web applications.
</ul>

<strong> Your First Hack </strong>


   <strong> Step 1 - Open a terminal </strong>
     On the machine, open the terminal using the Terminal icon:  <img src="/assets/images/thm/thm-introcyber-terminal-icon.png"> <br>
 <strong> Step 2 - Find hidden website pages </strong>

  Execute the following command on the terminal:

  gobuster -u http://fakebank.com -w wordlist.txt dir
  <img src="/assets/images/thm/thm-gify-introcyber1.gif">

  <strong> Step 3 - Hack the Bank </strong>

  Transfer $2000 from the bank account 2276, to your account (account number 8881). <br>

<img src="/assets/images/thm/thm-introcyber2.gif">
If your transfer was successful, you should now be able to see your new balance reflected on your account page. Go there now and confirm you got the money! (You may need to hit Refresh for the changes to appear)<br>

<strong> Question: Above your account balance, you should now see a message indicating the answer to this question. Can you find the answer you need? </strong> <br>
  
<em>Answer: BANK-HACKED</em>


If you were a penetration tester or security consultant, this is an exercise you’d perform for companies to test for vulnerabilities in their web applications; find hidden pages to investigate for vulnerabilities. <br>

## Task 3 - Careers in Cyber Security

<H3> How to Start Learning Cybersecurity </H3>
Many people wonder how to become hackers (security consultants) or defenders (security analysts fighting cybercrime). The process is straightforward: focus on a specific area of cybersecurity, and practice regularly through hands-on exercises. By dedicating a little time each day to learning on TryHackMe, you can build the skills necessary to land your first job in the industry. <br>

<strong>Real Success Stories: </strong><br>

<a href="https://tryhackme.com/resources/blog/construction-worker-to-security-engineer-how-paul-used-tryhackme-to-land-his-first-job-in-security">Paul </a> transitioned from a construction worker to a security engineer. <br>
<a href="https://tryhackme.com/resources/blog/the-teacher-becomes-the-student">Kassandra</a> moved from being a music teacher to a security professional. <br>
<a href="https://tryhackme.com/resources/blog/brandons-success-story">Brandon </a>leveraged TryHackMe during school to secure his first job in cybersecurity. <br>

<H3> Career Paths in Cybersecurity:</H3>
For more detailed insights into various cybersecurity careers, explore the cyber careers room on TryHackMe. Here’s a brief overview of a few offensive security roles:<br>
<ul>
  <li> Penetration Tester: Tests technology products to identify security vulnerabilities. </li>
  <li> Red Teamer: Simulates adversary attacks to provide feedback from an attacker’s perspective.</li>
  <li> Security Engineer: Designs, monitors, and maintains security controls, networks, and systems to prevent cyberattacks. </li>
</ul>

By committing to daily practice and exploring different career paths, you can successfully enter the cybersecurity field.

