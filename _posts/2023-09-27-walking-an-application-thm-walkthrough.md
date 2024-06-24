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

<strong> Learning Objectives</strong><br>
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

  
  To begin, start the virtual machine on this task, wait 2 minutes.<br>
Use either the in-browser attackbox or your own device over VPN to connect to the URL provided for your machine. 

</section>

## Task 2 - Exploring The Website

<!-- Task 2: Importance of Completing a Site Review for Discovered Pages -->
<div id="task-2-site-review">
<strong>Importance of Completing a Site Review for Discovered Pages</strong>
  <p>As a penetration tester, one of your primary responsibilities is to identify features within a website or web application that could be vulnerable to exploitation. These typically include interactive elements that engage with the user.</p>

  <p>Identifying these interactive parts can range from easily spotting a login form to thoroughly examining the website's JavaScript. A good starting point is to use your browser to explore the website, taking notes on each page, area, and feature, and summarizing your findings.</p>

  <p>Conducting a comprehensive site review is essential as it ensures that all components and functionalities that might be susceptible to attacks are identified. By meticulously documenting your observations, you can guarantee that no significant areas are missed, allowing for a complete assessment of the site's security.</p>

<img src="/assets/images/thm/thm-walk-app-2.png">
<br>
  <p>Here's an example of a site review for the Acme IT Support website:</p>
  <ul>
    <li><strong>Home Page</strong>: Provides an overview of services, with no interactive elements.</li>
    <li><strong>News Page</strong>: Features the latest news at Acme IT Support.</li>
    <li><strong>Contact Page</strong>: Includes a form for submitting contact information.</li>
    <li><strong>Customer Support Portal</strong>: Requires user login, offering various support resources.</li>
  </ul>

  <p>Performing a detailed site review sets the stage for a thorough and effective penetration test, ensuring that all potential vulnerabilities are identified and examined.</p>
</div>

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

  <p>Websites often use frameworks—prebuilt code collections for common features like blogs, user management, and form processing. Viewing the page source can indicate if a framework is in use and its version. Knowing the framework and version can help identify known vulnerabilities. For example, a comment at the bottom of the page might indicate the framework and version, along with a link to the framework's website. Reviewing this information can lead you to another flag by identifying if the framework is outdated.

Right-click on the page and select "View page source" 
Visit the page that is referenced in the page comment to retrieve the flag.</p>

<strong> Q: What is the flag from the HTML comment? </strong><br>
<em> Answer: THM{HTML_COMMENTS_ARE_DANGEROUS}</em>
<p>

</p>

<img src="/assets/images/thm/thm_wap_1.png"><br>
<p>

</p>

<strong> Q: What is the flag from the secret link? </strong><br>
<em> Answer: THM{NOT_A_SECRET_ANYMORE} </em><br>
<p>

</p>

<img src="/assets/images/thm/thm_wap_2.png">

<p>

</p>


<strong> Q: What is the directory listing flag? </strong><br>
<em> Answer: THM{INVALID_DIRECTORY_PERMISSIONS} </em><br>
<p></p>

<img src="/assets/images/thm/thm_wap_3.png"><br>
<p></p>

<strong> Q: What is the framework flag? </strong><br>
<em> Answer: THM{KEEP_YOUR_SOFTWARE_UPDATED} </em><br>
<p></p>
<img src="/assets/images/thm/thm_wap_4.png"><br>

<p></p>

</div>

## Task 4 - Developer Tools - Inspector

<!-- Task 4: Using the Inspector Tool -->

<div id="task-4-inspector">

<strong>Developer Tools</strong><br>
<p>
Every modern browser includes developer tools; this is a tool kit used to aid web developers in debugging web applications and gives you a peek under the hood of a website to see what is going on. As a pentester, we can leverage these tools to provide us with a much better understanding of the web application. We're specifically focusing on three features of the developer tool kit, Inspector, Debugger and Network.</p>


<strong>Opening Developer Tools</strong><br>
<p>The way to access developer tools is different for every browser. The example below is demonstrated on a Firefox browser:</p><br>

<img src="/assets/images/thm/thm-walk-app-4.gif"><br>

<p>

</p>

<strong> Inspector </strong><br>

  <p>The page source does not always reflect the current state of a webpage, as CSS, JavaScript, and user interactions can alter its content and style. To see what is being displayed in the browser at any given moment, we use the Element Inspector. This tool provides a live view of the webpage as it appears currently.</p>

  <p>In addition to viewing this live representation, we can edit and interact with the page elements. This functionality is particularly useful for web developers to troubleshoot and debug issues.</p>

  <p>On the Acme IT Support website, navigate to the news section, where you will find three news articles.</p>

<p>
The first two articles are readable, but the third has been blocked with a floating notice above the content stating you have to be a premium customer to view the article. These floating boxes blocking the page contents are often referred to as paywalls as they put up a metaphorical wall in front of the content you wish to see until you pay.

Right-clicking on the premium notice ( paywall ), you should be able to select the Inspect option from the menu, which opens the developer tools either on the bottom or right-hand side depending on your browser or preferences. You'll now see the elements/HTML that make up the website.

Locate the DIV element with the class premium-customer-blocker and click on it. You'll see all the CSS styles in the styles box that apply to this element, such as margin-top: 60px and text-align: center. The style we're interested in is the display: block. If you click on the word block, you can type a value of your own choice. Try typing none, and this will make the box disappear, revealing the content underneath it and a flag. If the element didn't have a display field, you could click below the last style and add in your own. Have a play with the element inspector, and you'll see you can change any of the information on the website, including the content. Remember this is only edited on your browser window, and when you press refresh, everything will be back to normal.
</p> <br>

<img src="/assets/images/thm/thm-walk-app-5.gif"><br>

<p> 
</p>
<strong> Q: What is the flag behind the paywall?</strong><br>
<em> Answer: THM{NOT_SO_HIDDEN} </em><br>
</div>


## Task 5 - Developer Tools - Debugger



<p>The Debugger panel in developer tools is primarily designed for debugging JavaScript, making it a valuable resource for web developers trying to identify issues in their code. For penetration testers, it offers the capability to delve into the JavaScript code. In Firefox and Safari, this tool is named Debugger, while in Google Chrome, it's referred to as Sources.</p>

<p>On the Acme IT Support website, navigate to the contact page. Each time the page loads, you might observe a quick red flash on the screen. We will use the Debugger to investigate this red flash and determine if it contains any interesting information. Although debugging a red dot is not a typical task for a penetration tester, it helps us familiarize ourselves with the Debugger's functionality.</p>

<p>In both browsers, you'll find a list of all resources used by the current webpage on the left-hand side. By navigating to the assets folder, you will find a file named <code>flash.min.js</code>. Clicking on this file will display its JavaScript content.</p>

<p>Often, JavaScript files are minified, meaning all formatting (tabs, spaces, and newlines) is removed to reduce the file size. This file is no different and is also obfuscated, making it deliberately hard to read to prevent easy copying by other developers.</p>

<p>We can partially restore the formatting using the "Pretty Print" option, represented by two braces <code>{ }</code>, to make the code somewhat more readable. However, due to the obfuscation, it may still be challenging to understand. By scrolling to the bottom of the <code>flash.min.js</code> file, you will find the line: <code>flash['remove']();</code></p>


<img src="/assets/images/thm/thm-walk-app-6.gif"> <br>



<strong> Q: What is the flag in the red box? </strong><br>
<em> Answer: THM{CATCH_ME_IF_YOU_CAN} </em><br>


## Task 6 - Developer Tools - Network

<strong> Q: What is the flag shown on the contact-msg network request? </strong><br>
<em> Answer: THM{GOT_AJAX_FLAG} </em><br>

</div>

