---
title: "Walking An Application - THM Walkthrough by Nisha"
toc: true
toc_label: "Table of Contents"
toc_icon: "list-alt"
categories:
  - Blog
tags:
  - Cybersecurity
  - Offensive
  - Red Team
  - Web App
  - TryHackMe

tagline: "Manually review a web application for security issues using only your browsers developer tools. Hacking with just your browser, no tools or scripts."
header:
 teaser: assets/images/thm/thm-walk-app-1.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/thm/thm-walk-app-0.png
  caption: "Photo credit: [**TryHackMe**](https://tryhackme.com/r/room/walkinganapplication)"
---
<strong> Room Link: </strong>  <a href="https://tryhackme.com/r/room/walkinganapplication" target="_blank">https://tryhackme.com/r/room/walkinganapplication</a>

<p>In the "Walking An Application" room on TryHackMe, you will learn how to manually assess a web application for security vulnerabilities using only the built-in tools available in your web browser. Automated security tools and scripts often miss many potential vulnerabilities and valuable information. This room emphasizes the importance of manual review to identify and understand these overlooked issues.</p>

  <h4>Objectives</h4>
  <p>In this room, you will use the following built-in browser tools to uncover and analyze security issues:</p>
  <ul>
    <li><strong>View Source</strong>: Learn to view the human-readable source code of a website directly through your browser.</li>
    <li><strong>Inspector</strong>: Understand how to inspect and modify page elements to access content that is typically restricted.</li>
    <li><strong>Debugger</strong>: Explore how to inspect and control the flow of a page's JavaScript to uncover hidden functionalities and vulnerabilities.</li>
    <li><strong>Network</strong>: Monitor all network requests made by a page to identify potential security issues and data exposures.</li>
  </ul>
  <p>By mastering these tools, you will be equipped to conduct a thorough and effective manual review of web applications, enhancing your ability to find and address security vulnerabilities that automated tools might miss.</p>


## Task 1 - Walking An Application

<!-- Introduction to the TryHackMe Room: "Walking An Application" -->
<section id="walking-an-application">

  
  To begin, start the virtual machine on this task, wait 2 minutes.  Use either the in-browser attackbox or your own device over VPN to connect to the URL provided for your machine. 

</section>

## Task 2 - Exploring The Website

<!-- Task 2: Importance of Completing a Site Review for Discovered Pages -->
<div id="task-2-site-review">
  <h4>Importance of Completing a Site Review for Discovered Pages</h4>
  <p>As a penetration tester, one of your primary responsibilities is to identify features within a website or web application that could be vulnerable to exploitation. These typically include interactive elements that engage with the user.</p>

  <p>Identifying these interactive parts can range from easily spotting a login form to thoroughly examining the website's JavaScript. A good starting point is to use your browser to explore the website, taking notes on each page, area, and feature, and summarizing your findings.</p>

  <p>Conducting a comprehensive site review is essential as it ensures that all components and functionalities that might be susceptible to attacks are identified. By meticulously documenting your observations, you can guarantee that no significant areas are missed, allowing for a complete assessment of the site's security.</p>

<img src="/assets/images/thm/thm-walk-app-2.png">

  <p>Here's an example of a site review for the Acme IT Support website:</p>
  <ul>
    <li><strong>Home Page</strong>: Provides an overview of services, with no interactive elements.</li>
    <li><strong>News Page</strong>: Features the latest news at Acme IT Support.</li>
    <li><strong>Contact Page</strong>: Includes a form for submitting contact information.</li>
    <li><strong>Customer Support Portal</strong>: Requires user login, offering various support resources.</li>
  </ul>

  <p>Performing a detailed site review sets the stage for a thorough and effective penetration test, ensuring that all potential vulnerabilities are identified and examined.</p>
</div>

<em> Answer: No answer needed </em>

## Task 3 - Viewing The Page Source

<!-- Task 4: Viewing the Page Source -->
<div id="task-4-page-source">

 {% include video id="IlTq-AORjFI" provider="youtube" %}

  <p>The page source is the human-readable code returned to our browser from the web server each time we make a request. This code comprises HTML (HyperText Markup Language), CSS (Cascading Style Sheets), and JavaScript, which collectively dictate the content, layout, and interactivity of the webpage.</p>

  <p>Viewing the page source can reveal important information about the web application. It helps in identifying hidden content, comments left by developers, and potential vulnerabilities.</p>

  <h4>How to View the Page Source</h4>
  <ul>
    <li>Right-click on the webpage and select <strong>View Page Source</strong> from the context menu.</li>
    <li>Alternatively, you can prepend <code>view-source:</code> to the URL in the browser’s address bar (e.g., <code>view-source:https://www.google.com/</code>).</li>
    <li>Most browsers also have an option to view the page source within the menu, often located under <strong>Developer Tools</strong> or <strong>More Tools</strong>.</li>
  </ul>

  <h4>Let's View Some Page Source!</h4>
  <p>Try viewing the page source of the Acme IT Support website’s homepage. While a complete explanation of the code is beyond the scope of this room, we can highlight critical elements:</p>

  <ul>
    <li><strong>Comments:</strong> Look for code sections starting with <code>&lt;!--</code> and ending with <code>--&gt;</code>. These are developer comments that can provide insights or notes. For example, a comment may indicate that the homepage is temporary while a new one is under development. Follow any links in the comments to find your first flag.</li>
    <li><strong>Anchor Tags:</strong> Links to different pages are written in anchor tags (<code>&lt;a&gt;</code>) with the URL specified in the <code>href</code> attribute. For instance, the contact page link might be found on line 31.</li>
    <li><strong>Hidden Links:</strong> Further down the page source, you may find hidden links starting with "secr". Viewing these links might reveal private areas used for storing sensitive information. In this room, these links lead to flags.</li>
    <li><strong>External Files:</strong> CSS, JavaScript, and images can be included via HTML. Sometimes, directory listing is enabled by mistake, revealing all files in a directory. This can expose confidential information like backup files or source code. Check the directories for misconfigurations and find the <code>flag.txt</code> file.</li>
  </ul>

  <p>Websites often use frameworks—prebuilt code collections for common features like blogs, user management, and form processing. Viewing the page source can indicate if a framework is in use and its version. Knowing the framework and version can help identify known vulnerabilities. For example, a comment at the bottom of the page might indicate the framework and version, along with a link to the framework's website. Reviewing this information can lead you to another flag by identifying if the framework is outdated.</p>


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

</div>

## Task 4 - Developer Tools - Inspector
## Task 5 - Developer Tools - Debugger
## Task 6 - Developer Tools - Network
