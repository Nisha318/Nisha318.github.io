---
layout: single
title: "Investigate Web Attacks Challenge Walkthrough (Let's Defend)"
date: 2024-10-16
author: Nisha McDonnell
excerpt: "A detailed walkthrough of how to solve the 'Investigating Web Attacks Challenge' on Let's Defend using the bWAPP web application as the victim."
categories:
  - Blog
tags:
  - Web Application Security
  - Incident Response
  - Cybersecurity
  - Let's Defend
tagline: "A detailed walkthrough of how to solve the 'Investigating Web Attacks Challenge' on Let's Defend using the bWAPP web application as the victim."
header:
  image: /assets/images/letsdefend/webattack-0.png
  caption: "Web Attack Investigated"
  teaser: assets/images/letsdefend/webattack-00.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/ctf_hero_header_image.png
  caption: "Photo credit: [**AI**](https://chatgpt.com/)"
---

## Investigating Web Attacks Challenge Walkthrough (Let's Defend)

In this post, I'll walk you through solving the "Investigating Web Attacks Challenge" from Let's Defend. The challenge uses logs sourced from the **bWAPP** web application, an intentionally vulnerable app designed to help security professionals practice identifying and analyzing real-world attack patterns. In this guide, I'll show you how I analyzed the logs to answer each question.

<img src="/assets/images/letsdefend/webattack-0.png">
---

### Question 1: Which automated scan tool did the attacker use for web reconnaissance?

At line 30, I found **Nikto** in the User-Agent string, confirming that the attacker used Nikto, a popular web vulnerability scanning tool.

**Answer:** `Nikto`

<img src="/assets/images/letsdefend/webattack-1.png">
---

### Question 2: After web reconnaissance, which technique did the attacker use for directory listing discovery?

The Nikto User-Agent strings continued throughout the log. I used this command to jump to the last Nikto entry:

```bash
grep -n "Nikto" access.log | tail -n 1
```
<img src="/assets/images/letsdefend/webattack-2.png"> 

<img src="/assets/images/letsdefend/webattack-3.png">

Reviewing the subsequent requests, I noticed the attacker was attempting to enumerate directory paths in alphabetical order, many of which returned **404 Not Found** responses. This pattern is consistent with **directory brute forcing**.

**Answer:** `Directory brute force`

<img src="/assets/images/letsdefend/webattack-4.png">
---

### Question 3: What is the third attack type after directory listing discovery?

After directory brute force, the attacker shifted to a **brute-force login attack**. Logs showed repeated **POST** requests to `/bWAPP/login.php` with the same User-Agent string, indicating attempts to guess credentials.

**Answer:** `Brute force`

<img src="/assets/images/letsdefend/webattack-5.png">
---

### Question 4: Is the third attack successful?

Yes. The brute force login attempt was successful. The logs showed HTTP status codes changing from **200 OK** to **302 Found**, which indicates redirection after a successful login. Additionally, the response size increased significantly, confirming the login.

**Answer:** `Yes`

<img src="/assets/images/letsdefend/webattack-6.png">
---

### Question 5: What is the name of the fourth attack?

After gaining access, the attacker launched a **code injection attack**. Logs revealed payloads with system commands passed into URL parameters.

**Answer:** `Code injection`

<img src="/assets/images/letsdefend/webattack-7.png">
---

### Question 6: What is the first payload for the fourth attack?

The first injected command was:

```bash
whoami
```

This was sent through the vulnerable `phpi.php` script to determine the current system user.

**Answer:** `whoami`

<img src="/assets/images/letsdefend/webattack-8.png">
---

### Question 7: Is there any persistency clue for the victim machine in the log file? If yes, what is the related payload?

Yes. The attacker attempted persistence by creating a new user account. The payload was:

```bash
system('net user hacker Asd123!! /add')
```
This would create a user named **hacker** with the password `Asd123!!`. Persistence could be extended by adding this account to the administrator group.

**Answer:** `%27net%20user%20hacker%20Asd123!!%20/add%27`

<img src="/assets/images/letsdefend/webattack-9.png">
---

## Conclusion

By analyzing the logs step by step, I identified that the attacker:

- Used **Nikto** for reconnaissance  
- Performed **directory brute forcing**  
- Launched a **brute-force login attack**  
- Exploited the system with **code injection**  
- Attempted **persistence** by creating a new user  

This challenge highlights the importance of monitoring web server logs for suspicious activity and demonstrates how attackers chain multiple techniques to compromise systems.

I hope this walkthrough helps you approach the *"Investigating Web Attacks Challenge"* on Let's Defend with confidence.

