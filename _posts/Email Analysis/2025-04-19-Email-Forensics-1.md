---
title: Email Forensics - Challenge 1
date: 2025-04-19 11:35:00 +550
categories: [Digital Forensics, Email Forensics]
tags: [Digital Forensics]
---


> The email files and header data are from the SOC100 course by Level Effect. This is the documentation of my thought process and analysis of
> the email analysis challenges from this course. 


Looking at the email that was received. We see that the email is claiming to be / impersonating the IT team of the fictional organization goodcorp.
The sender is telling the user to execute some suspicious looking PowerShell code to remediate a "security incident".

![Pasted image 20250313101647.png](/assets/img/Pasted%20image%2020250313101647.png)

Start of by analyzing the email headers. Looking for anything blatantly suspicious. 

![Pasted image 20250313102008.png](/assets/img/Pasted%20image%2020250313102008.png)

SPF, DKIM and DMARC have all passed. The email is coming from the right person and is a permitted sender.


![Pasted image 20250313102045.png](/assets/img/Pasted%20image%2020250313102045.png)

The mail is coming from a `@gmail.com` domain account , not a `@goodcorp.com` domain mail ID. The fact that the mail is coming from an email address that 
is not a part of the `goodcorp` domain is suspicious as IT teams will have a company mail address.

![Pasted image 20250313102404.png](/assets/img/Pasted%20image%2020250313102404.png)

![Pasted image 20250313102657.png](/assets/img/Pasted%20image%2020250313102657.png)

We can look up the IP address in domain dossier and perform some lookups in mxtoolbox.
But since the server sending the mail is a gmail server, we don't find anything of interest.

Lets download and analyze that `PasswordReset.ps1` script that is being downloaded and run using Powershell.

![Pasted image 20250313125500.png](/assets/img/Pasted%20image%2020250313125500.png)

Uploading the hash of the PowerShell script to VirusTotal gives us an idea that the script is up to no good having been flagged by 28 of 60 AVs that it was run against.

![Pasted image 20250313125830.png](/assets/img/Pasted%20image%2020250313125830.png)

Navigating to the Behavior tab, we can take a look at some of the techniques that are being used by the script. 

It is performing some host enumeration like process discovery and file system enumeration. It also appears to be communicating with a C2 server over a non-standard 
port using TCP and UDP. 

![Pasted image 20250313130209.png](/assets/img/Pasted%20image%2020250313130209.png)

![Pasted image 20250419094205.png](/assets/img/Pasted%20image%2020250419094205.png)

Doing some deeper analysis into the script that was downloaded we find that majority of the code is hexadecimal encoded. 
This looks to be a form of detection evasion using code obfuscation. 

![Pasted image 20250313125437.png](/assets/img/Pasted%20image%2020250313125437.png)

Decoding the hexadecimal data using cyberchef. It looks to be an executable from the magic number or the first 2 bytes of the file. 
It also contains the DOS STUB further indicating that it is in fact an executable file.


![Pasted image 20250419094713.png](/assets/img/Pasted%20image%2020250419094713.png)

Since we found out from VirusTotal that the executable is communicating with a C2 server we can guess that there is a URL somewhere in the binary with an IP address.
So we can add the 'remove NULL bytes' recipe on cyber chef and apply the strings recipe on it.

On doing so we can find an IP address that is the C2 server the binary is communicating with. We also find the user agent string that is being used which
are both extremely useful IOCs that can used for detection in the future.

![Pasted image 20250419100616.png](/assets/img/Pasted%20image%2020250419100616.png)

