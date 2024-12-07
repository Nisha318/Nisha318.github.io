---
title: "TryHackMe: Enumeration & Brute Force Room Walkthrough"
layout: single
author_profile: true
toc: true
toc_label: "Table of Contents"
toc_sticky: true
categories:
  - TryHackMe
  - Blog
tags:
  - Penetration Testing
  - Enumeration
  - Brute Force
  - OSINT
  - Web App Security
header:
  overlay_image: "/assets/images/thm/thm-enum-br-cover.png"
  caption: "Image Credit: TryHackMe"
---

## Overview
This blog documents my walkthrough of the **Enumeration & Brute Force** room on TryHackMe. The room covers various techniques for enumeration, brute-forcing, and exploiting vulnerabilities in web applications. Here’s a breakdown of each task and what I learned along the way.
Link to room: 
---

## Task 1
- No answer needed

---

## Task 2: Authentication Enumeration
**Question:** What type of error messages can unintentionally provide attackers with confirmation of valid usernames?  
**Answer:** Verbose errors  

<img src="/assets/images/thm/thm-enum-br-02.png" alt="Task 2 Screenshot">

---

## Task 3: Enumerating Users
**Question:** What is the valid email address from the list?  
**Answer:** canderson@gmail.com  

[Source of the email list](https://github.com/nyxgeek/username-lists/blob/master/usernames-top100/usernames_gmail.com.txt)

<img src="/assets/images/thm/thm-enum-br-03.png" alt="Task 3 Screenshot">
<img src="/assets/images/thm/thm-enum-br-04.png" alt="Task 3 Screenshot">
<img src="/assets/images/thm/thm-enum-br-05.png" alt="Task 3 Screenshot">
<img src="/assets/images/thm/thm-enum-br-06.png" alt="Task 3 Screenshot">

---

## Task 4: Exploiting Vulnerable Password Reset Logic
**Question:** What is the flag?  
**Answer:** THM{50_pr3d1ct4BL333!!}  

<img src="/assets/images/thm/thm-enum-br-07.png" alt="Task 4 Screenshot">
<img src="/assets/images/thm/thm-enum-br-08.png" alt="Task 4 Screenshot">
<img src="/assets/images/thm/thm-enum-br-09.png" alt="Task 4 Screenshot">

---

## Task 5: Exploiting HTTP Basic Authentication
**Question:** What is the flag?  
**Answer:** THM{b4$$1C_AuTTHHH}  

<img src="/assets/images/thm/thm-enum-br-19.png" alt="Task 5 Screenshot">
<img src="/assets/images/thm/thm-enum-br-20.png" alt="Task 5 Screenshot">
<img src="/assets/images/thm/thm-enum-br-21.png" alt="Task 5 Screenshot">

---

## Task 6: OSINT
- No answer needed

---

## Task 7: Conclusion
This room provided hands-on experience in identifying and exploiting common vulnerabilities in web applications, such as authentication enumeration, weak password reset mechanisms, and insecure HTTP Basic Authentication. Understanding these techniques enhances my ability to better protect systems from such attacks.  

If you'd like to try this room yourself, check it out on [TryHackMe](https://tryhackme.com).  

---
