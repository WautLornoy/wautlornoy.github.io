---
layout: post
title: THM: Steel Mountain Writeup
description: >
  In this post I will show how I gained root privileges on a windows machine.
tags:
  - ctf
  - writeup
  - tryhackme
published: true
---

In this walkthrough on [TryHackMe](https://tryhackme.com/room/steelmountain) we will enumerate and attack a Windows machine using metasploit. 
{: .lead}

# Introduction
Our first task is to find the employee of the month, we will do an nmap scan to find all services running on the machine.
``nmap -sV -sC -p- -oN steelmountain.nmap $TARGET``
![nmap-scan](/uploads/posts/steelmountain/nmap.png)
Here we can see that a webserver is running on port 80, so we will browse to the website. An image of a man with the title "Employee of the month" is displayed. Looking at the source of the page we can see that the image file is called "BillHarper.png", so the employee of the month is Bill Harper.
![website](/uploads/posts/steelmountain/website.png)

# Initial Access
Looking back at the nmap scan results we see that another webserver is running. Browing to this service reveals a file server, under "Server Information" we can see that this is a HttpFileServer on version 2.3. When we click on the text we are redirected to "www.rejetto.com". 
![fileserver](/uploads/posts/steelmountain/fileserver.png)
Using this information we can search if there is an exploit available, we will use metasploit to search and use an exploit. Here we find "exploit/windows/http/rejetto_hfs_exec", doing some additional research we can see that the CVE number is "2014-6287". We will set our options and run the exploit.
![exploit](/uploads/posts/steelmountain/exploit.png)
We now have a meterpreter shell, we can now browse to the Desktop directory of the current user and we will find the "user.txt" file containing the flag.

# Privilege Escaltion
Now we need to gain root privileges, a powershell script called "PowerUp.ps1" is provided to us. Once downloaded on our attacking machine we can upload this script to the target. Once we execute it the service "AdvancedSystemCareService9" is shown to have a misconfiguration that allows us to write to the directory and restart the service. We can leverage this to put our own executable in the place of the service executable and restart the service.
![powerup](/uploads/posts/steelmountain/powerup.png)
With msfvenom we will generate a tcp reverse shell with the name of the service. Then we will upload the file into the directory of the service to replace the original executable with our reverse shell. We will now start a netcat listener by executing ``nc -lnvp 4443`` and then restart the service.
Now we get a response on our netcat listener with a windows cmd line.
![reverseshell](/uploads/posts/steelmountain/reverseshell.png)
This is where we find the "root.txt" file containing the last flag. (The last task will be left out as a challenge for you ;) )