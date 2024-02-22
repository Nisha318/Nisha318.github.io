## Splunk Basics (THM Walkthorugh)

<img src="/assets/images/splunk_thm_basics_header.png">
<a href="https://tryhackme.com/room/splunk101">

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

