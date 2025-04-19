---
title: Email Forensics - Challenge 2
date: 2025-04-19 11:35:00 +550
categories: [Digital Forensics, Email Forensics]
tags: [Digital Forensics]
---


> The email files and header data are from the SOC100 course by Level Effect. This is the documentation of my thought process and analysis of
> the email analysis challenges from this course. 

The email claims to be regarding a payment submitted to LevelEffect.

![Pasted image 20250419101804.png](/assets/img/Pasted%20image%2020250419101804.png)

![Pasted image 20250313132023.png](/assets/img/Pasted%20image%2020250313132023.png)

Along with the email is an attachment with a file extension of `pdf.htm`. But if we were to view it without the full file extensions visible you could assume it would
be a standard pdf file. The fact that this is a html file disguised as a pdf is suspicious. 

![Pasted image 20250419101433.png](/assets/img/Pasted%20image%2020250419101433.png)

![Pasted image 20250419101603.png](/assets/img/Pasted%20image%2020250419101603.png)

The first step would be to take a look at the Email headers. In this case both SPF and DMARC have failed so the mail is 
most likely a phishing email. 

![Pasted image 20250314123245.png](/assets/img/Pasted%20image%2020250314123245.png)

![Pasted image 20250314123322.png](/assets/img/Pasted%20image%2020250314123322.png)

Now analyzing the attachment that was sent along with the email. 

The first script tag is declaring a variable in hexadecimal. Decoding it using cyber chef.

![Pasted image 20250419104047.png](/assets/img/Pasted%20image%2020250419104047.png)


![Pasted image 20250313194455.png](/assets/img/Pasted%20image%2020250313194455.png)


![Pasted image 20250419103805.png](/assets/img/Pasted%20image%2020250419103805.png)

Observe that there is some JS being executed to decode some base64 data, we can use cyber chef to do the same.
The code being executed looks like some animation that is being rendered on the web page.

![Pasted image 20250419103855.png](/assets/img/Pasted%20image%2020250419103855.png)


![Pasted image 20250313194712.png](/assets/img/Pasted%20image%2020250313194712.png)

The code sets the `href` attribute of an anchor tag to a URL that is being decoded from its Unicode value. Decoding this URL using cyber chef.

![Pasted image 20250313195046.png](/assets/img/Pasted%20image%2020250313195046.png)

This value gets added with the variable `vqTlpKfWbn`

![Pasted image 20250313194907.png](/assets/img/Pasted%20image%2020250313194907.png)

The `setTimeout()` method will set a timer before executing a script. In this case after 3 seconds the `click()` method is called on the `ytePwyksnG` element to trigger the redirect to the malicious website


![Pasted image 20250313194918.png](/assets/img/Pasted%20image%2020250313194918.png)

![Pasted image 20250313194952.png](/assets/img/Pasted%20image%2020250313194952.png)

