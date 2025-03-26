---
title: Part 2 - Configuring VLANs on PfSense
date: 2025-03-16 9:35:00 +550
categories: [Homelab, PfSense]
tags: [Pfsense]
---


## Configuring VLANs on the pfSense router

#### Create and Assign VLANs to interfaces
---

Creating VLANs for 2 networks.

![Pasted image 20250309103432.png](/assets/img/Pasted%20image%2020250309103432.png)

VLAN 10

![Pasted image 20250310090143.png](/assets/img/Pasted%20image%2020250310090143.png)

VLAN 11

![Pasted image 20250310090409.png](/assets/img/Pasted%20image%2020250310090409.png)

---


![Pasted image 20250309215526.png](/assets/img/Pasted%20image%2020250309215526.png)

![Pasted image 20250309215605.png](/assets/img/Pasted%20image%2020250309215605.png)

Enable each interface that was just created and assign a static IP address to the router on each interface.


![Pasted image 20250310090802.png](/assets/img/Pasted%20image%2020250310090802.png)

![Pasted image 20250310090823.png](/assets/img/Pasted%20image%2020250310090823.png)

Save and then click apply to apply the changes.

Configuring VLAN 11s interface. 

![Pasted image 20250310090859.png](/assets/img/Pasted%20image%2020250310090859.png)

![Pasted image 20250310090933.png](/assets/img/Pasted%20image%2020250310090933.png)


#### Configuring DHCP and DNS Settings
---

Navigate to the DHCP Server tab under services. From here we will configure the DHCP server address pool and DNS server settings for each of the VLANs.

![Pasted image 20250310091025.png](/assets/img/Pasted%20image%2020250310091025.png)


![Pasted image 20250310091135.png](/assets/img/Pasted%20image%2020250310091135.png)

This mistake will come to bite my in the ass later...
The IP range is supposed to be `172.16.10.100` To `172.16.10.200`

![Pasted image 20250310091759.png](/assets/img/Pasted%20image%2020250310091759.png)

I spent a good 2 hours troubleshooting Proxmox, network connections and VLAN configurations on my managed switch. Finally come to see that 
the IP range was misconfigured. :)

Update the IP address pool range for the VLAN 10.

![Pasted image 20250310090027.png](/assets/img/Pasted%20image%2020250310090027.png)

Set the DNS server as well as explicitly configure the default gateway.

![Pasted image 20250310091538.png](/assets/img/Pasted%20image%2020250310091538.png)

![Pasted image 20250310091624.png](/assets/img/Pasted%20image%2020250310091624.png)

For VLAN 11

![Pasted image 20250309220813.png](/assets/img/Pasted%20image%2020250309220813.png)

#### Configuring Firewall Rules
---


VLAN 10

![Pasted image 20250310101832.png](/assets/img/Pasted%20image%2020250310101832.png)

![Pasted image 20250310101851.png](/assets/img/Pasted%20image%2020250310101851.png)


VLAN 11

![Pasted image 20250310101940.png](/assets/img/Pasted%20image%2020250310101940.png)



![Pasted image 20250310101909.png](/assets/img/Pasted%20image%2020250310101909.png)

#### Allow access to management console from WAN

Since the Pfsense VM has been configured within a private network, I want to be able to manage it from the WAN side.

Creating a firewall rule to allow hosts from my internal network to access the WAN side of the pfSense router.

| Firewall Field         | Value to be set                               |
| ---------------------- | --------------------------------------------- |
| Action                 | Pass                                          |
| Interface              | WAN                                           |
| Protocol               | TCP                                           |
| Source                 | 192.168.29.1/24                               |
| Destination            | WAN Address of PfSense                        |
| Destination Port range | HTTPS or Custom Port for management interface |


![Pasted image 20250310102842.png](/assets/img/Pasted%20image%2020250310102842.png)


![Pasted image 20250310102900.png](/assets/img/Pasted%20image%2020250310102900.png)

![Pasted image 20250310102923.png](/assets/img/Pasted%20image%2020250310102923.png)

Remove the rule that prevents RFC 1918 communication from the WAN interface's firewall rules.


