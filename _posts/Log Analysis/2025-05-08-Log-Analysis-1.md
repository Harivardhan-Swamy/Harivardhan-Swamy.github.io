---
title: Log Analysis - Challenge 1
date: 2025-05-08 11:35:00 +550
categories: [Digital Forensics, Log Analysis]
tags: [Digital Forensics]
---


Preface
> The log data is from the SOC100 course by Level Effect. This is the documentation of my thought process and analysis of
> the log analysis challenges from this course. 

Scenario
>The SOC was alerted to suspicious activity on a customer endpoint surrounding the process `epm_free_installer_20250112.478.exe`. Use this information as
> a jump point to unravel more details about the activity.

Since we are alerted to the execution of a suspicious process called `epm_free_installer_20250112.478.exe` we can directly filter for the process name.
When analyzing malware behaviour the 3 areas we can look for indicators of compromise are process, persistence and network. 

First we can look for process and persistence related artefacts.

![Pasted image 20250307203411.png](/assets/img/Pasted%20image%2020250307203411.png)

Let us look for processes spawned by the installer. To do this we search for any process with the same parent process ID as the malicious executable.
In the search bar we can filter the field `process.parent.pid` for the value 8508.

![Pasted image 20250307203640.png](/assets/img/Pasted%20image%2020250307203640.png)

The parent process is creating a new run key that is executing a binary in the `\AppData\Roaming\Cache` folder. 

```
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v AppSetup /d C:\Users\gooduser\AppData\Roaming\Cache\tmp_tG31y678.exe /f
```

Lets take a closer look at the original executable for now.

![Pasted image 20250307204146.png](/assets/img/Pasted%20image%2020250307204146.png)

The logs contain a file hash that we can use for future threat detection. 

![Pasted image 20250307204235.png](/assets/img/Pasted%20image%2020250307204235.png)

`f0d57ee37c0cb34cae8d0b34b8d590a4498efc9332f0183d21193e446291bfff`

We also can look for any files or artifacts that were created by this malware. We find that the executable has created another file

![Pasted image 20250307204508.png](/assets/img/Pasted%20image%2020250307204508.png)

Moving on to network artifacts, we can filter for DNS queries to look for signs of data exfiltration.

![Pasted image 20250307204640.png](/assets/img/Pasted%20image%2020250307204640.png)

Looking for any DNS requests to websites that are not standard web requests, we find a request for `api.ipify.com` which returns the IP address of the user making the request. 

![Pasted image 20250307204733.png](/assets/img/Pasted%20image%2020250307204733.png)




