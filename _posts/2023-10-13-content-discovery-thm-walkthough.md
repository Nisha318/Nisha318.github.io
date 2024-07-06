---
title: "Content Discovery - THM Walkthrough by Nisha"
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
categories:
  - Blog
  - TryHackMe
tags:
  - Cybersecurity
  - Offensive
  - Red Team
  - Penetration Testing
  - Ethical Hacking
  - web security


 
tagline: "Learn the various ways of discovering hidden or private content on a webserver that could lead to new vulnerabilities."
header:
  teaser: assets/images/thm/thm-content-discovery-0.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/thm/thm-content-discovery-1.png
  caption: "Photo credit: [**TryHackMe**](https://tryhackme.com/r/room/contentdiscovery)"
---
<strong> Room Link: </strong>  <a href="https://tryhackme.com/r/room/contentdiscovery" target="_blank">https://tryhackme.com/r/room/contentdiscovery</a>


<p>

In cybersecurity, discovering hidden or non-obvious content on web servers is critical for any aspiring ethical hacker. The "Content Discovery" room on TryHackMe offers a detailed, hands-on approach to mastering techniques for uncovering various types of web content that can reveal vulnerabilities or sensitive data. Ideal for learners looking to deepen their web penetration testing skills, this room enhances the ability to pinpoint potential entry points on a target website.</p>

<strong> Learning Objectives </strong>

<ul>
    <li><strong>Understanding the Basics of Content Discovery:</strong> Learn the fundamental concepts behind discovering hidden files and directories on a web server using manual methods.</li>
    <li><strong>Employing OSINT Techniques:</strong> Utilize Open Source Intelligence (OSINT) methods to gather publicly available information that can guide and enhance your content discovery efforts.</li>
    <li><strong>Using Tools for Automated Content Discovery:</strong> Gain practical experience with popular automated tools like <code>Dirb</code>, <code>Dirbuster</code>, and <code>Gobuster</code>. These tools automate the search for hidden content, increasing efficiency and thoroughness.</li>
    <li><strong>Analyzing Web Server Responses:</strong> Develop the ability to analyze HTTP responses to identify useful information that could lead to further exploitation opportunities.</li>
    <li><strong>Advanced Techniques and Methodologies:</strong> Explore advanced content discovery techniques, including the use of wordlists, recursive searches, and the handling of different response codes, integrating both manual and automated methods.</li>
    <li><strong>Real-World Application and Scenarios:</strong> Apply your skills in realistic scenarios provided by TryHackMe to simulate the process of conducting content discovery during a penetration test using a blend of manual, OSINT, and automated methods.</li>
</ul>





## Task 1 -  What Is Content Discovery?

<p>

Certainly! Here's the revised text without using the word "realm":
</p>
<p>
First, let's clarify what we mean by "content" in web application security. Content can include various elements like files, videos, images, backups, and specific website features. Content discovery focuses on identifying not just the visible elements of a website but also those that are hidden or not meant for public access.</p>
<p>
This hidden content might include pages or portals meant for staff use, older website versions, backup files, configuration files, and administrative panels.
</p>
</p>
We will examine three primary methods for discovering content on a website: Manual discovery, Automated tools, and OSINT (Open-Source Intelligence).
</p>
</p>
To get started, launch the AttackBox by clicking the blue "Start AttackBox" button, and also initiate the machine associated with this task.
</p>

<strong>Question:</strong>  What is the Content Discovery method that begins with M?
<br>
<em>Answer: Manually </em></p>

<p>
<strong>Question:</strong>What is the Content Discovery method that begins with A?<br>

<em>Answer: Automated</em></p>

<p>
<strong>Question:</strong>What is the Content Discovery method that begins with O?<br>
<em> Answer: OSINT </em> </p>


## Task 2 -  Manual Discovery - Robots.txt

<p>There are various areas on a website that we can manually inspect to begin uncovering additional content.</p>

<strong>Robots.txt</strong>

<p>The <code>robots.txt</code> file is a guide for search engines, specifying which pages should or should not be indexed in their search results or even prohibiting certain search engines from scanning the site. Often, this file is used to hide specific parts of the site, like administrative portals or customer-only files, from appearing in search results. For penetration testers, this file can be an invaluable source, revealing the very areas of the website that the owners prefer to keep hidden.</p>
<p>

Take a look at the robots.txt file on the Acme IT Support website to see if they have anything they don't want to list </p>

<a href="/assets/images/thm/thm-content-discovery-2">

<p>
<strong>Question:</strong>What is the directory in the robots.txt that isn't allowed to be viewed by web crawlers?<br>
<em> Answer: /staff-portal </em> </p>



## Task 3 -  Manual Discovery - Favicon

<p>There are various areas on a website that we can manually inspect to begin uncovering additional content. One such area is the favicon.</p>

<h3> Favicon</h3>

<p>The favicon is a small icon that appears in the browser's address bar or tab, primarily used for branding purposes. Sometimes, when developers use specific frameworks to build a website, they may leave the default favicon provided by the framework. If this default is not replaced with a custom one, it can indicate which framework was used to construct the site. The OWASP organization maintains a database of common framework icons, which can be accessed at <a href="https://wiki.owasp.org/index.php/OWASP_favicon_database">OWASP Favicon Database</a>. Identifying the framework helps us to leverage external resources for deeper insights into the website’s structure.</p>

<h3> Practical Exercise</h3>

<p>Launch Firefox in the AttackBox and navigate to <a href="https://static-labs.tryhackme.cloud/sites/favicon/">this site</a>. You will see a page indicating "Website coming soon..." and, upon checking the tabs, you'll notice the favicon that hints at the underlying framework. Viewing the page source reveals a link to the favicon at <code>images/favicon.ico</code>.</p>

<p>To determine the framework via the favicon, download the icon and compute its MD5 hash using the following commands:</p>

<pre>
<code>curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico | md5sum</code>
</pre>

<p><strong>Note:</strong> If using the free version of AttackBox, this command may fail. Alternatively, use a VM, or on Windows, run the following PowerShell commands:</p>

<pre>
<code>PS C:\&gt; curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico -UseBasicParsing -o favicon.ico
PS C:\&gt; Get-FileHash .\favicon.ico -Algorithm MD5</code>
</pre>

<strong> Q: What framework did the favicon belong to?
 </strong><br>
<em> Answer: cgiirc </em><br>



## Task 4 -  Manual Discovery - Sitemap.xml

<h3>Sitemap.xml</h3>

<p>Contrary to the <code>robots.txt</code> file, which limits what search engine crawlers can access, the <code>sitemap.xml</code> file provides a comprehensive list of all files that the website owner wants to be indexed by search engines. This list may include harder-to-navigate sections of the website or even reveal older webpages that are no longer visible on the current site but are still accessible.</p>

<p>Examine the <code>sitemap.xml</code> file on the Acme IT Support website to uncover any content that might not have been discovered yet. Access it by opening the following URL in the FireFox browser on the AttackBox: <a href="http://10.10.185.115/sitemap.xml">http://10.10.185.115/sitemap.xml</a>.</p>


## Task 5 -  Manual Discovery - HTTP Headers
## Task 6 -  Manual Discovery - Framework Stack
## Task 7 -  OSINT - Google Hacking / Dorking
## Task 8 -  OSINT - Wappalyzer
## Task 9 -  OSINT - Wayback Machine
## Task 10 - OSINT - GitHub
## Task 11 - OSINT - S3 Buckets
## Task 12 - Automated Discovery
