---
title: Configurig GPOs Part 1
date: 2025-03-16 12:00:00 -500
categories: [homelab, active directory]
tags: [acitve-directory]
---


RDP access and WinRM configuration using GPOs.

## RDP configuration
---

Create a new security group called `RDP Users`.

```Powershell
New-ADGroup -Name "RDP Users" -GroupScope Global -GroupCategory Security -Path "CN=Users,DC=homelab,DC=local"
```

Add the users you want to provide RDP access to, into the newly created `RDP Users` security group.

```Powershell
Add-ADGroupMember -Identity "RDP Users" -Members gooduser1,gooduser2,gooduser3,helpdesk1
```

![Pasted image 20250205191412.png](/assets/img/Pasted%20image%2020250205191412.png)

---

We need to configure a new GPO to allow RDP access to the `RDP Users` group. Create a new GPO and configure the `Allow log on through Remote Desktop Services` 
policy to allow users in the `RDP Users` group.

The policy is present at **Computer Configuration>>Policies>>Windows Settings>>Security Settings>>Local Policies>>
User Rights Assignments>>Allow log on through Remote Desktop Services**.

![Pasted image 20250205190003.png](/assets/img/Pasted%20image%2020250205190003.png)


Add the newly created domain admin group here as well.

![Pasted image 20250207103056.png](/assets/img/Pasted%20image%2020250207103056.png)

---

**What are restricted groups and why do we use them?**
> Restricted groups creates a list of users / groups with explicit access to specific functionality provided denying those not a member of the groups.
To be able to connect to a machine via remote desktop, the user logging in must belong to the `Remote Desktop Users` group. You can use the restricted groups policy to add users / groups to the local `Remote Desktop Users` group for every machine that joins a domain.

>Now when you add the `RDP Users` group to the `Remote Desktop Users` group, we ensure that only these users are able to gain RDP access 
to workstations. When you need to add new users you simply add them to the `RDP Users` group.

Right click on Restricted Groups and click on add group

![Pasted image 20250205193211.png](/assets/img/Pasted%20image%2020250205193211.png)

Click on the Remote Desktop Users group and add the RDP Users group to it.

![Pasted image 20250205193114.png](/assets/img/Pasted%20image%2020250205193114.png)

![Pasted image 20250205200205.png](/assets/img/Pasted%20image%2020250205200205.png)


---

Verify that the `RDP Users` group is a member of the `Remote Desktop Users` builtin group.

![Pasted image 20250205200418.png](/assets/img/Pasted%20image%2020250205200418.png)

![Pasted image 20250205200429.png](/assets/img/Pasted%20image%2020250205200429.png)

**Why configure RDP like this?**
>Configuring RDP like this results in an implicit deny to all users not a part of the `Remote Desktop Users` security group. To allow users RDP
access, they must be explicitly configured to be allowed to do so.

>This results in better organizational security by explicitly allowing only 
those that require RDP access. Principle of least privilege?

---

Enable WinRM on WS1

![Pasted image 20250205201917.png](/assets/img/Pasted%20image%2020250205201917.png)

![Pasted image 20250205201946.png](/assets/img/Pasted%20image%2020250205201946.png)

## WinRM GPO

Configure a GPO to allow remote administration via WinRM on all workstations.

![Pasted image 20250205204839.png](/assets/img/Pasted%20image%2020250205204839.png)


![Pasted image 20250205205049.png](/assets/img/Pasted%20image%2020250205205049.png)

Now all computer that join the domain will have WinRM enabled without any additional configuration.




