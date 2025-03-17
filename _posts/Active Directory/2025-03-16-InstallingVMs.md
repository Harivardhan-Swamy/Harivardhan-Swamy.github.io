---
title: Part 1 Installing VMs for AD
date: 2025-03-16 12:00:00 -500
categories: [homelab, active directory]
tags: [acitve-directory]
---



## Installing Windows Server 2022

Uploading the necessary ISO files to Proxmox.


![Pasted image 20250203101408.png](/assets/img/Pasted%20image%2020250203101408.png)


---

![[Pasted image 20250203092826.png]](/assets/img/Pasted%20image%2020250203092826.png)



![Pasted image 20250203092401.png](/assets/img/Pasted%20image%2020250203092401.png)


![Pasted image 20250203095740.png](/assets/img/Pasted%20image%2020250203095740.png)

Select the same storage drives under TPM and EFI storage.

![Pasted image 20250203095620.png](/assets/img/Pasted%20image%2020250203095620.png)

![Pasted image 20250203095700.png](/assets/img/Pasted%20image%2020250203095700.png)


![Pasted image 20250203095826.png](/assets/img/Pasted%20image%2020250203095826.png)

![Pasted image 20250203093557.png](/assets/img/Pasted%20image%2020250203093557.png)

![Pasted image 20250203093616.png](/assets/img/Pasted%20image%2020250203093616.png)


![Pasted image 20250203093730.png](/assets/img/Pasted%20image%2020250203093730.png)

---

If you find that a machine is not powering off, chances are there is a shutdown job that is not able to power the machine off.
Stop this job and then stop the machine instead of powering it off.

![Pasted image 20250203094750.png](/assets/img/Pasted%20image%2020250203094750.png)

Double click on the shutdown task and stop the Shutdown task.

![Pasted image 20250203095004.png](/assets/img/Pasted%20image%2020250203095004.png)



![Pasted image 20250203094950.png](/assets/img/Pasted%20image%2020250203094950.png)

---


![Pasted image 20250203101540.png](/assets/img/Pasted%20image%2020250203101540.png)

![Pasted image 20250203101603.png](/assets/img/Pasted%20image%2020250203101603.png)


---

![Pasted image 20250203103500.png](/assets/img/Pasted%20image%2020250203103500.png)

With GUI, choose Desktop Experience.

![Pasted image 20250203103531.png](/assets/img/Pasted%20image%2020250203103531.png)


![Pasted image 20250203103717.png](/assets/img/Pasted%20image%2020250203103717.png)

![Pasted image 20250203103759.png](/assets/img/Pasted%20image%2020250203103759.png)

![Pasted image 20250203103843.png](/assets/img/Pasted%20image%2020250203103843.png)

![Pasted image 20250203103857.png](/assets/img/Pasted%20image%2020250203103857.png)


![Pasted image 20250203103950.png](/assets/img/Pasted%20image%2020250203103950.png)

Here select New and click on apply when prompted.

![Pasted image 20250203104036.png](/assets/img/Pasted%20image%2020250203104036.png)

Select Next and then the install process will begin.

---

Change the display resolution for the VMs

![Pasted image 20250310095331.png](/assets/img/Pasted%20image%2020250310095331.png)


![Pasted image 20250310095350.png](/assets/img/Pasted%20image%2020250310095350.png)

![Pasted image 20250310095405.png](/assets/img/Pasted%20image%2020250310095405.png)


![Pasted image 20250310095423.png](/assets/img/Pasted%20image%2020250310095423.png)




## Install Windows 11 
---

![Pasted image 20250204184619.png](/assets/img/Pasted%20image%2020250204184619.png)


Workstation install

![Pasted image 20250204144348.png](/assets/img/Pasted%20image%2020250204144348.png)


![Pasted image 20250204184754.png](/assets/img/Pasted%20image%2020250204184754.png)


![Pasted image 20250204184828.png](/assets/img/Pasted%20image%2020250204184828.png)

![Pasted image 20250204184902.png](/assets/img/Pasted%20image%2020250204184902.png)

![Pasted image 20250204185030.png](/assets/img/Pasted%20image%2020250204185030.png)


![Pasted image 20250204185156.png](/assets/img/Pasted%20image%2020250204185156.png)


![Pasted image 20250204185219.png](/assets/img/Pasted%20image%2020250204185219.png)

Driver setup


![Pasted image 20250309095108.png](/assets/img/Pasted%20image%2020250309095108.png)

Just default install, hit next and you are good to go.

![Pasted image 20250309095134.png](/assets/img/Pasted%20image%2020250309095134.png)


