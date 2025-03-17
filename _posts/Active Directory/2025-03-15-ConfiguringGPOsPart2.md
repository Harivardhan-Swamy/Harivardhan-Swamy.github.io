---
title: Part 4 - Configuring GPOs (Part 2)
date: 2025-03-15 9:30:00 +550
categories: [homelab, active directory]
tags: [acitve-directory]
---

## Allow Help Desk users to reset passwords and create accounts
---

Create a `HelpDesk` security group and add the two help desk users.

![Pasted image 20250207122137.png](/assets/img/Pasted%20image%2020250207122137.png)

---

Allow help desk users to perform basic tasks like
- Reset passwords for user accounts.
- Create new user accounts.

Utilizing task delegation we can give control over users in an OU/group to another user/group of users.

Navigate to ADUC and right click on the OU you want to delegate control over. 

![Pasted image 20250207121429.png](/assets/img/Pasted%20image%2020250207121429.png)

Give the `Helpdesk` security group control over the `DomainUsers` OU.

![Pasted image 20250207121507.png](/assets/img/Pasted%20image%2020250207121507.png)


![Pasted image 20250207121539.png](/assets/img/Pasted%20image%2020250207121539.png)

![Pasted image 20250207121602.png](/assets/img/Pasted%20image%2020250207121602.png)

---

Log in to the help desk user and test the functionality.

Password reset.

```Powershell
Set-ADAccountPassword -Identity gooduser1 -Reset -NewPassword (ConvertTo-SecureString "Fairmont55!" -AsPlainText -Force)
```

![Pasted image 20250207124155.png](/assets/img/Pasted%20image%2020250207124155.png)

We can see that the password was just recently modified.

![Pasted image 20250208101221.png](/assets/img/Pasted%20image%2020250208101221.png)

The `Helpdesk` group users can not reset passwords of users not in the scope of their delegated control.

![Pasted image 20250207124220.png](/assets/img/Pasted%20image%2020250207124220.png)

---

User creation.

```Powershell
New-ADUser -Name "Good User4" -GivenName "Good" -Surname "User4" -SamAccountName "gooduser4" -UserPrincipalName "gooduser4@homelab.local" -Path "OU=DomainUsers,DC=homelab,DC=local" -AccountPassword (ConvertTo-SecureString "Fairmont55!" -AsPlainText -Force) -Enabled $true
```

Trying to create a new user in the OU `DomainAdmins` results in a `Access is denied` error.
The user we are using does not have sufficient privilege to create users in the `DomainAdmins` OU

![Pasted image 20250207124725.png](/assets/img/Pasted%20image%2020250207124725.png)

## Folder Redirection and User restrictions

---

**User Configuration>>Policies>>Administrative Templates>>Control Panel**

Here configure both `Hide Specified Control Panel Item` and `Prohibit access to Control Panel and PC Settings

![Pasted image 20250207131202.png](/assets/img/Pasted%20image%2020250207131202.png)

![Pasted image 20250207131152.png](/assets/img/Pasted%20image%2020250207131152.png)

![Pasted image 20250207131242.png](/assets/img/Pasted%20image%2020250207131242.png)

**User Configuration>>Policies>>Administrative Templates>>Control Panel>>Personalization**


Configure 

![Pasted image 20250207131530.png](/assets/img/Pasted%20image%2020250207131530.png)

![Pasted image 20250207132218.png](/assets/img/Pasted%20image%2020250207132218.png)

---

Folder redirection
Data in Documents is stored on a network share.

Set up 

Create a share called `Folder Redirection - DomainUsers`
We should first configure the NTFS permissions for the directory

![Pasted image 20250209100521.png](/assets/img/Pasted%20image%2020250209100521.png)

Full control for all other users 

Set the following for authenticated users.

![Pasted image 20250209122644.png](/assets/img/Pasted%20image%2020250209122644.png)

Remove `Authenticated Users` group and add the `DomainUsers` security group.

![Pasted image 20250209123849.png](/assets/img/Pasted%20image%2020250209123849.png)

Here `CREATOR OWNER` group configuration allows the creator of the folder complete control over the files that they create, nothing else.
The `Domain Users`  group are given permissions to List folders and read metadata and permissions for only the parent folder. The result is 
users can access the folders that they create.

Why can't `user1` view `user2s` documents folder?

>On a Windows Server, even if you and another user belong to the same security group, you generally cannot view their folders because file system 
permissions are set at the Folder level, meaning that just being in the same group does not automatically grant access to another user's personal 
folders; you need explicit permission to access those folders, which the folder owner must grant individually or through specific group permissions.


Enable the access-based enumeration feature.
This prevents users from viewing folders that they do not have permission to access.

**Server manager >> File and Storage Services >> Shares**

![Pasted image 20250209105223.png](/assets/img/Pasted%20image%2020250209105223.png)

![Pasted image 20250209105211.png](/assets/img/Pasted%20image%2020250209105211.png)

Configuring share permissions.

![Pasted image 20250209101311.png](/assets/img/Pasted%20image%2020250209101311.png)

![Pasted image 20250209101322.png](/assets/img/Pasted%20image%2020250209101322.png)


**User Configuration > Policies > Windows Settings > Folder Redirection.**


![Pasted image 20250209101615.png](/assets/img/Pasted%20image%2020250209101615.png)

Under settings uncheck 'Grant user exclusive rights...'

![Pasted image 20250209101622.png](/assets/img/Pasted%20image%2020250209101622.png)

![Pasted image 20250209130857.png](/assets/img/Pasted%20image%2020250209130857.png)

DC file share has 3 folders, but user `gooduser1` does not have permissions, so cant access the other folders as a result they can't even view them due to access-based enumeration.

![Pasted image 20250209130909.png](/assets/img/Pasted%20image%2020250209130909.png)


## Mount file share as a drive for users


![Pasted image 20250209134106.png](/assets/img/Pasted%20image%2020250209134106.png)

Hidden Share created using `$` at end of share name.

![Pasted image 20250209140337.png](/assets/img/Pasted%20image%2020250209140337.png)


![Pasted image 20250209134325.png](/assets/img/Pasted%20image%2020250209134325.png)


![Pasted image 20250209134311.png](/assets/img/Pasted%20image%2020250209134311.png)

Publish the Shared folder

![Pasted image 20250209140928.png](/assets/img/Pasted%20image%2020250209140928.png)


![Pasted image 20250209145701.png](/assets/img/Pasted%20image%2020250209145701.png)

Publishing the folder makes it discoverable by the Group Policy Management Console for mapping.

Go to User Configuration > Preferences > Windows Settings > Drive Maps.


![Pasted image 20250209145638.png](/assets/img/Pasted%20image%2020250209145638.png)

![Pasted image 20250209141316.png](/assets/img/Pasted%20image%2020250209141316.png)

![Pasted image 20250209141332.png](/assets/img/Pasted%20image%2020250209141332.png)

![Pasted image 20250209150039.png](/assets/img/Pasted%20image%2020250209150039.png)


![Pasted image 20250209150015.png](/assets/img/Pasted%20image%2020250209150015.png)

