---
title: Email Forensics - Challenge 3
date: 2025-04-19 11:35:00 +550
categories: [Digital Forensics, Email Forensics]
tags: [Digital Forensics]
---


> The email files and header data are from the SOC100 course by Level Effect. This is the documentation of my thought process and analysis of
> the email analysis challenges from this course. 

For this challenge, along with the email header we are given an `html` file that contains a connection request from a LinkedIn user claiming to 
have found many vulnerabilities in our website. 

We start the investigation by taking a look at the email headers.

Firstly we observe that the mail is coming from a Microsoft email server.

![Pasted image 20250314122934.png](/assets/img/Pasted%20image%2020250314122934.png)

SPF, DKIM and DMARC have all passed, so the mail is coming from a verified sender.

![Pasted image 20250314122133.png](/assets/img/Pasted%20image%2020250314122133.png)


![Pasted image 20250314122656.png](/assets/img/Pasted%20image%2020250314122656.png)

Next analyzing the `From`, `X-Google-Original-From` and `X-mailer` headers.

![Pasted image 20250314122834.png](/assets/img/Pasted%20image%2020250314122834.png)

The `From` header tells us that the email is coming form `itgoodcorp@gmail.com` contrary to what you'd expect when you get a new connection request 
on LinkedIn. 

The `X-Google-Original-From` header is used to preserve the original sender's email address when the email is sent from an alias or Google Group, 
which might otherwise rewrite the "From:" header.

If someone sends an email from `support@twitter.com`, which redirects to an internal user or CRM tool, the `From:` header might show `support@twitter.com`, but the `X-Google-Original-From` header will show the original sender's address.

`X-mailer` is a header of an email message that shows which software the sender used to send the message.
_Gophish_ is a powerful, open-source phishing framework that makes it easy to test your organization's exposure to phishing.

This mail is most likely an internal phishing campaign performed by the company to test employee awareness. 


![Pasted image 20250314123121.png](/assets/img/Pasted%20image%2020250314123121.png)


Finally we can analyze the links that are present in html file. All links in the request contain a private IP address with a parameter, this effectively confirms that this is a phishing campaign launched by the security team to track users that fall for phishing campaigns.