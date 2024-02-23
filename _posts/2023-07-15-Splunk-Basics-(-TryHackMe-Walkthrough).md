## Splunk Basics (THM Walkthrough)

---
title: "Splunk Basics (TryHackMe Splunk 101 Walkthrough)"
categories:
  - Blog
tags:
  - Cybersecurity
---

<images src="/assets/images/splunk_thm_basics_header.png">
<a href="https://tryhackme.com/room/splunk101">https://tryhackme.com/room/splunk101</a>

 {% include video id="zyaof9kP54I" provider="youtube" %}

<h4>Learning Objectives and Pre-requisites</h4>
If you are new to SIEM, please complete the Introduction to SIEM. This room covers the following learning objectives:
<ul>
	<li>Splunk overview</li>
	<li>Splunk components and how they work</li>
	<li>Different ways to ingest logs</li>
    <li>Normalization of logs</li>
</ul>

<h5>Task 1 Introduction </h5>
Splunk is one of the leading SIEM solutions in the market that provides the ability to collect, analyze and correlate the network and machine logs in real-time. In this room, we will explore the basics of Splunk and its functionalities and how it provides better visibility of network activities and help in speeding up the detection.<br>


Continue with the next task.

<h5>Task 2 Connect with the Lab </h5>

<h5>Room Machine </h5>

Before moving forward, deploy the machine. When you deploy the machine, it will be assigned an IP. Access this room in a web browser on the AttackBox, or via the VPN at http://MACHINE_IP. The machine will take up to 3-5 minutes to start.

<images src="/assets/images/Splunk-THM_machine_ip.png">

<b>Continue with the next task. </b> <br>

<h5>Task 3 Splunk Components </h5>


<b>Q1 - Which component is used to collect and send data over the Splunk instance?</b><br>
<em> Answer: Forwarder</em>

<b>Splunk Forwarder</b>

Splunk Forwarder is a lightweight agent installed on the endpoint intended to be monitored, and its main task is to collect the data and send it to the Splunk instance. It does not affect the endpoint's performance as it takes very few resources to process. Some of the key data sources are:
<ul>
<li>Web server generating web traffic.</li>
<li>Windows machine generating Windows Event Logs, PowerShell, and Sysmon data. </li>
<li>Linux host generating host-centric logs. </li>
<li>Database generating DB connection requests, responses, and errors.</li>
</ul>

<h5>Task 4 Navigating Splunk </h5>

<b>Q1 - In the Add Data tab, which option is used to collect data from files and ports?</b><br>
<em> Answer: </em>


<h5>Task 5 Adding Data </h5>

<b>Q1 - Upload the data attached to this task and create an index "VPN_Logs". How many events are present in the log file?</b><br>
<images src="/assets/images/splunk_thm_basics_q1events.png">

<em>Answer: 2862</em>

<b>Q2 - How many log events by the user Maleena are captured?</b>
<images src="/assets/images/splunk_thm_basics_q2user.png">


<em>Answer: 60</em>

<b>Q3 - What is the name associated with IP 107.14.182.38?</b>
<images src="/assets/images/splunk_thm_basics_q3IP.png">
<em>Answer: Smith</em>

<b>Q4 - What is the number of events that originated from all countries except France?</b>
Remove the IP address from the query search bar that was added in the previous question.
Scroll down the interesting fields panel on the left and click on source_country. 
<images src="/assets/images/splunk_thm_basics_q4countries.png">
<images src="/assets/images/splunk_thm_basics_q4countries2.png">


<em>Answer: 2814</em>

<b>Q5 - How many VPN Events were observed by the IP 107.3.206.58?</b><br>
<em>Answer: 14</em>
<images src="/assets/images/splunk_thm_basics_q5IP2.png">


<h5>Task 6 Conclusion </h5>

In this room, we explored Splunk, its components, and how it works. Please check the following Splunk walkthrough and challenge rooms to understand how Splunk is effectively used in investigating the incidents.
<ul>
<li>Incident Handling with Splunk</li>
<li>Investigating With Splunk</li>
<li>Benign - Challenge</li>
<li>PoshEclipse - Challenge</li>
</ul>