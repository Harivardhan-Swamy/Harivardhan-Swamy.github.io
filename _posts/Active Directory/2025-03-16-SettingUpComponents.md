---
title: Setting up Components
date: 2025-03-16 12:00:00 -500
categories: [homelab, active directory]
tags: [acitve-directory]
---



## DC Configuration
---

Install AD DS

```
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

![Pasted image 20250205093707.png](/assets/img/Pasted%20image%2020250205093707.png)

![Pasted image 20250205094603.png](/assets/img/Pasted%20image%2020250205094603.png)


```
Install-ADDSForest -DomainName "homelab.local" -DomainNetbiosName "HOMELAB" -SafeModeAdministratorPassword \
(ConvertTo-SecureString "**********" -AsPlainText -Force) -InstallDNS
```

![Pasted image 20250205094921.png](/assets/img/Pasted%20image%2020250205094921.png)

![Pasted image 20250205095011.png](/assets/img/Pasted%20image%2020250205095011.png)

---

Enable RDP 

![Pasted image 20250205100544.png](/assets/img/Pasted%20image%2020250205100544.png)


![Pasted image 20250205100129.png](/assets/img/Pasted%20image%2020250205100129.png)


![Pasted image 20250205100142.png](/assets/img/Pasted%20image%2020250205100142.png)

---

Create 3 OUs for Domain Users, SOC Analysts and Help Desk analysts.

![Pasted image 20250205103200.png](/assets/img/Pasted%20image%2020250205103200.png)

![Pasted image 20250205103157.png](/assets/img/Pasted%20image%2020250205103157.png)

---

Create new users and add them to the `DomainUsers` group

```Powershell

New-ADUser -Name "Good User1" -GivenName "Good" -Surname "User1" -SamAccountName "gooduser1" -UserPrincipalName "gooduser1@homelab.local" -Path "OU=DomainUsers,DC=homelab,DC=local" \ 
-AccountPassword (ConvertTo-SecureString "Fairmont55!" -AsPlainText -Force) -Enabled $true

Set-ADUser -Identity gooduser1 -PasswordNeverExpires $true

```

It is bad practice to set the password to never expire. Preferable set to change every 30 days.

![Pasted image 20250205114543.png](/assets/img/Pasted%20image%2020250205114543.png)

Similarly create the users: helpdesk1, gooduser2 and gooduser3

When creating the helpdesk user change the OU to 'HelpDesk'.

![Pasted image 20250205120441.png](/assets/img/Pasted%20image%2020250205120441.png)

![Pasted image 20250205120451.png](/assets/img/Pasted%20image%2020250205120451.png)

Create an OU for the domain admins and add the da1 to the Administrators group.

![Pasted image 20250207102402.png](/assets/img/Pasted%20image%2020250207102402.png)


![Pasted image 20250207102349.png](/assets/img/Pasted%20image%2020250207102349.png)

Alt

```
New-ADGroup -Name "DomainAdmins" -GroupScope Global -GroupCategory Security -Path "CN=DomainAdmins,OU=DomainAdmins,DC=homelab,DC=local"
```


## Workstation configuration
---

Change DNS server to the IP address of the DC

![Pasted image 20250205130929.png](/assets/img/Pasted%20image%2020250205130929.png)

The host is using the IPv6 DNS server instead of the IPv4 DNS server. 
Update both IPv4 and IPv6 DNS server IPs.

![Pasted image 20250205131445.png](/assets/img/Pasted%20image%2020250205131445.png)

![Pasted image 20250205131549.png](/assets/img/Pasted%20image%2020250205131549.png)

![Pasted image 20250205123700.png](/assets/img/Pasted%20image%2020250205123700.png)






