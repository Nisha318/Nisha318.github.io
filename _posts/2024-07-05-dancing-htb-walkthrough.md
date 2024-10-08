---
title: "Dancing - HTB Walkthrough by Nisha"
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
  - Hack the Box
  - Networking Services
  - SMB
  - Reconnaissance
  
# tagline: "Hack your first website (legally in a safe environment) and experience an ethical hacker's job."
header:
  teaser: assets/images/htb/htb-dancing-0.PNG 
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/htb/htb-dancing-0.PNG
  caption: "Photo credit: [**AI**](https://chatgpt.com/)"
---
<strong> Room Link: </strong>  <a href="https://app.hackthebox.com/starting-point?tier=0" target="_blank">https://app.hackthebox.com/starting-point?tier=0</a>

## Task 1 
What does the 3-letter acronym SMB stand for? 

Server Message Block

## Task 2 
What port does SMB use to operate at?

445

```
nmap -p- -vv -T4 10.129.237.107
```

<img src="/assets/images/htb/htb-dancing-1.png">


## Task 3 

What is the service name for port 445 that came up in our Nmap scan?

microsoft-ds

```
nmap -sC -sV -p135,445,1339,47001,49665-49669 10.129.237.107
```


<img src="/assets/images/htb/htb-dancing-2.png">

## Task 4

What is the 'flag' or 'switch' that we can use with the smbclient utility to 'list' the available shares on Dancing? 

-L

```
sudo smbclient -L 10.129.237.107 -N
```


<img src="/assets/images/htb/htb-dancing-3.png">

## Task 5

How many shares are there on Dancing? 

4

## Task 6

What is the name of the share we are able to access in the end with a blank password?  

WorkShares


```
sudo smbclient //10.129.237.107/WorkShares -N
```

<img src="/assets/images/htb/htb-dancing-4.png">




## Task 7

What is the command we can use within the SMB shell to download the files we find?  

get

<img src="/assets/images/htb/htb-dancing-5.png">

<img src="/assets/images/htb/htb-dancing-6.png">

<img src="/assets/images/htb/htb-dancing-7.png">


<img src="/assets/images/htb/htb-dancing-8.png">


Submit root flag



<img src="/assets/images/htb/htb-dancing-9.png">



