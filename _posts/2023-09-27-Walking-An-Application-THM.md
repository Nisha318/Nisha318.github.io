---
title: "Walking An Application (TryHackMe Walkthrough)"
categories:
  - Blog
tags:
  - Cybersecurity
  - SIEM
  - Splunk
  - Log Management
  - Monitoring
#teaser: assets/images/thm_splunk_incident_handling_featured.PNG   Path to the teaser image
tagline: "Manually review a web application for security issues using only your browsers developer tools.  Hacking with just your browser, no tools or scripts."
header:
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/nessus logo.png
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
---


### Manually review a web application for security issues using only your browsers developer tools.  Hacking with just your browser, no tools or scripts.

<img src="/assets/images/thm/intro_web_app_header_image.png">
Room:<a href="https://tryhackme.com/r/room/walkinganapplication"> https://tryhackme.com/r/room/walkinganapplication</a>


<h4>Learning Objectives</h4>

The learning objectives for the TryHackMe room "Walking An Application" include:

<ul>
<li> Understanding web application structures and their components. </li>
<li> Learning to navigate and interact with a web application's frontend and backend. </li>
<li> Exploring common vulnerabilities and security issues within web applications. </li>
<li> Practicing techniques for discovering and exploiting these vulnerabilities. </li>
<li> Gaining hands-on experience with tools and methods used in web application security testing. </li>
<li> For more details, visit the room on TryHackMe: <a href="https://tryhackme.com/r/room/walkinganapplication">Walking An Application. </a></li>


<h5> Room Contents: </h5>
<ul>
  Task 1: Walking an Application
  Task 2: Exploring The Website
Task 3: Viewing The Page Source
Task 4: Developer Tools — Inspector
Task 5: Developer Tools — Debugger
Task 6: Developer Tools — Network
</ul>
<img src="/assets/images/thm/thm_wap_0.png">

</ul>

<h5>Task 1: Walking An Application </h5>

<em> Answer: No answer needed </em>


<h5>Task 2: Exploring the Website </h5>

<em> Answer: No answer needed </em>

<h5>Task 3: Viewing the Page Source </h5>
Right-click on the page and select "View page source" </br>
Visit the page that is referenced in the page comment to retrieve the flag. </br>


<strong> Q: What is the flag from the HTML comment? </strong><br>
<em> Answer: THM{HTML_COMMENTS_ARE_DANGEROUS}</em>


<img src="/assets/images/thm/thm_wap_1.png">

<strong> Q: What is the flag from the secret link? </strong><br>
<em> Answer: THM{NOT_A_SECRET_ANYMORE} </em>

<img src="/assets/images/thm/thm_wap_2.png">

<strong> Q: What is the directory listing flag? </strong><br>
<em> Answer: THM{INVALID_DIRECTORY_PERMISSIONS} </em>

<img src="/assets/images/thm/thm_wap_3.png">

<strong> Q: What is the framework flag? </strong><br>
<em> Answer: THM{KEEP_YOUR_SOFTWARE_UPDATED} </em>

<img src="/assets/images/thm/thm_wap_4.png">

<h5>Task 4: Developer Tools - Inspector </h5>

<strong> Q: What is the flag behind the paywall?</strong><br>
<em> Answer: THM{NOT_SO_HIDDEN} </em>

<h5>Task 5: Developer Tools - Debugger  </h5>
<strong> Q: What is the flag in the red box? </strong><br>
<em> Answer: THM{CATCH_ME_IF_YOU_CAN} </em>

<h5>Task 6: Developer Tools - Network </h5>
<strong> Q: What is the flag shown on the contact-msg network request? </strong><br>
<em> Answer: THM{GOT_AJAX_FLAG} </em>
