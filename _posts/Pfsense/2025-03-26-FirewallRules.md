---
title: Part 3 - Configuring Firewall Rules
date: 2025-03-26 18:35:00 +550
categories: [Homelab, PfSense]
tags: [Pfsense]
---


## Configuring Firewall Rules on WAN Interface


![Pasted image 20250326101209.png](/assets/img/Pasted%20image%2020250326101209.png)
#### Allow ICMP on WAN Interface

| Property       | Value                   |
| -------------- | ----------------------- |
| Action         | Pass                    |
| Interface      | WAN                     |
| Protocol       | ICMP                    |
| Address Family | IPv4                    |
| Source         | Network 192.168.29.1/24 |
| Destination    | This Firewall           |


![Pasted image 20250325100655.png](/assets/img/Pasted%20image%2020250325100655.png)

![Pasted image 20250325100954.png](/assets/img/Pasted%20image%2020250325100954.png)

Hit apply changes.

Testing if the rule works as intended by pinging the WAN address of the PfSense machine.

![Pasted image 20250325101412.png](/assets/img/Pasted%20image%2020250325101412.png)

#### Allow RDP to AD VLAN from WAN

Allow RDP access to machines that are on the Active Directory VLAN ie. VLAN 11

An additional caveat for the lab environment that I am working with is that I need to add static routes to my router to route the requests to the networks to the PfSense router.


Routing for the LAN network

| Property    | Value                                  |
| ----------- | -------------------------------------- |
| Destination | 192.168.1.0                            |
| Subnet Mask | 255.255.255.0                          |
| Gateway     | 192.168.29.73 (PfSense WAN IP Address) |
| Network     | Local                                  |
| Metric      | 15                                     |

Routing for the Active Directory network

| Property    | Value                                  |
| ----------- | -------------------------------------- |
| Destination | 172.16.11.0                            |
| Subnet Mask | 255.255.255.0                          |
| Gateway     | 192.168.29.73 (PfSense WAN IP Address) |
| Network     | Local                                  |
| Metric      | 15                                     |

![Pasted image 20250325135053.png](/assets/img/Pasted%20image%2020250325135053.png)

---


| Property               | Value                                 |
| ---------------------- | ------------------------------------- |
| Action                 | Pass                                  |
| Interface              | WAN                                   |
| Address Family         | IPv4                                  |
| Protocol               | TCP                                   |
| Source                 | WAN Subnets                           |
| Destination            | Active directory Subnets              |
| Destination Port Range | From: MS RDP (3389) To: MS RDP (3389) |


![Pasted image 20250325140137.png](/assets/img/Pasted%20image%2020250325140137.png)

![Pasted image 20250325140538.png](/assets/img/Pasted%20image%2020250325140538.png)

![Pasted image 20250325140613.png](/assets/img/Pasted%20image%2020250325140613.png)



#### Allow ICMP to the AD VLAN from WAN

| Property       | Value                    |
| -------------- | ------------------------ |
| Action         | Pass                     |
| Interface      | WAN                      |
| Address Family | IPv4                     |
| Protocol       | ICMP                     |
| Source         | WAN Subnets              |
| Destination    | Active directory Subnets |


![Pasted image 20250325140310.png](/assets/img/Pasted%20image%2020250325140310.png)

Pinging a host inside the AD VLAN from outside that network.

![Pasted image 20250325140513.png](/assets/img/Pasted%20image%2020250325140513.png)



## Configure Firewall Rules on the Malware Analysis VLAN

Deny all outgoing traffic on the Malware Analysis VLAN


| Property       | Value                    |
| -------------- | ------------------------ |
| Action         | Reject                   |
| Interface      | MALWAREANALYSIS          |
| Address Family | IPv4                     |
| Protocol       | Any                      |
| Source         | Malware analysis subnets |
| Destination    | Any                      |


![Pasted image 20250325132718.png](/assets/img/Pasted%20image%2020250325132718.png)

![Pasted image 20250325132915.png](/assets/img/Pasted%20image%2020250325132915.png)


Remove the old rule that allowed all outgoing traffic as it is no longer required.

![Pasted image 20250325133021.png](/assets/img/Pasted%20image%2020250325133021.png)


![Pasted image 20250325133244.png](/assets/img/Pasted%20image%2020250325133244.png)


![Pasted image 20250325133303.png](/assets/img/Pasted%20image%2020250325133303.png)

Now we have an isolated VLAN that does not have access to any other internal networks.

## Configure Firewall Rules on the AD VLAN


![Pasted image 20250326090200.png](/assets/img/Pasted%20image%2020250326090200.png)
#### Allow ICMP to Real WAN

Add a rule to allow ICMP requests to be sent out of the network.


| Property       | Value                    |
| -------------- | ------------------------ |
| Action         | Pass                     |
| Interface      | Active Directory         |
| Address Family | IPv4                     |
| Protocol       | ICMP                     |
| ICMP Subtypes  | Any                      |
| Source         | Active Directory Subnets |
| Destination    | Any                      |



![Pasted image 20250326090357.png](/assets/img/Pasted%20image%2020250326090357.png)


#### Block ICMP to Fake WAN

Now I do not want the AD environment to ping or have access to hosts in my private network, so I will deny ICMP traffic to 
the WAN Subnet. 

| Property       | Value                    |
| -------------- | ------------------------ |
| Action         | Block                    |
| Interface      | Active Directory         |
| Address Family | IPv4                     |
| Protocol       | ICMP                     |
| ICMP Subtypes  | Any                      |
| Source         | Active Directory Subnets |
| Destination    | WAN Subnets              |

![Pasted image 20250326091021.png](/assets/img/Pasted%20image%2020250326091021.png)


![Pasted image 20250326091449.png](/assets/img/Pasted%20image%2020250326091449.png)

The configured rule appears to be working as intended. We can still ping Google's DNS server but can no longer ping hosts in the WAN private network.

#### Allow Web Application and DNS Traffic

Here we will be creating an alias to group common web application and DNS ports for ease of configuring firewall rules.

First we will navigate to the Aliases tab under the Firewall dropdown menu. Here navigate to Ports and click on add to create 
a new alias for our firewall rule.

![Pasted image 20250326091713.png](/assets/img/Pasted%20image%2020250326091713.png)

Configure the alias as follows

| Property | Value          |
| -------- | -------------- |
| Name     | Browsing_Ports |
| Type     | Ports          |
| Port     | 80             |
|          | 443            |
|          | 8080           |
|          | 53             |


![Pasted image 20250326091927.png](/assets/img/Pasted%20image%2020250326091927.png)

Now click on apply and your new alias should be ready and available for use in the firewall rules tab. 

![Pasted image 20250326091944.png](/assets/img/Pasted%20image%2020250326091944.png)

Configuring a new rule using the newly created alias for web browsing.

| Property               | Value                    |
| ---------------------- | ------------------------ |
| Action                 | Pass                     |
| Interface              | Active Directory Subnets |
| Address Family         | IPv4                     |
| Protocol               | TCP/UDP                  |
| Source                 | Active Directory Subnets |
| Destination            | Any                      |
| Destination Port Range | Browsing_Ports           |

![Pasted image 20250326092713.png](/assets/img/Pasted%20image%2020250326092713.png)

Summary of all the configured firewall rules for the AD VLAN.

![Pasted image 20250326093122.png](/assets/img/Pasted%20image%2020250326093122.png)


