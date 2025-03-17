---
title: Part 1 - Installing PfSense
date: 2025-03-16 9:30:00 +550
categories: [homelab, Pfsense]
tags: [Pfsense]
---

## Installing PfSense on Proxmox

Download the PfSense iso from the website and upload it to Proxmox.

![Pasted image 20250223164758.png](/assets/img/Pasted%20image%2020250223164758.png)


![Pasted image 20250223180413.png](/assets/img/Pasted%20image%2020250223180413.png)


Important to set pfsense to start at boot

![Pasted image 20250223180522.png](/assets/img/Pasted%20image%2020250223180522.png)

![Pasted image 20250223181055.png](/assets/img/Pasted%20image%2020250223181055.png)

![Pasted image 20250223181120.png](/assets/img/Pasted%20image%2020250223181120.png)

![Pasted image 20250223181324.png](/assets/img/Pasted%20image%2020250223181324.png)

![Pasted image 20250223182125.png](/assets/img/Pasted%20image%2020250223182125.png)

![Pasted image 20250223182136.png](/assets/img/Pasted%20image%2020250223182136.png)

Navigate back to the pfsense VM

![Pasted image 20250223182244.png](/assets/img/Pasted%20image%2020250223182244.png)

Add the newly created `vmbr1` network adapter here by clicking on Add. 


![Pasted image 20250223182227.png](/assets/img/Pasted%20image%2020250223182227.png)


![Pasted image 20250223190012.png](/assets/img/Pasted%20image%2020250223190012.png)



Login using the default creds of `admin` and `pfsense`

![Pasted image 20250309095859.png](/assets/img/Pasted%20image%2020250309095859.png)


Run the setup wizard when you login


![Pasted image 20250309101208.png](/assets/img/Pasted%20image%2020250309101208.png)

![Pasted image 20250309101234.png](/assets/img/Pasted%20image%2020250309101234.png)

![Pasted image 20250309101251.png](/assets/img/Pasted%20image%2020250309101251.png)

Leave DHCP as default

![Pasted image 20250309101335.png](/assets/img/Pasted%20image%2020250309101335.png)

![Pasted image 20250309101343.png](/assets/img/Pasted%20image%2020250309101343.png)



