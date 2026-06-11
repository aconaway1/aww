---
title: "Remembering the Little Things"
date: 2008-02-07
---

Back in the day, when I used to put a new piece of IOS-based gear on the network, I would have to go through the gear already in production to remember what all those "little configurations" were that kept the devices running. Guess how many times I remembered to set the NTP server or turn off the HTTP server? Never.

To fix that problem, I started to keep a list of IOS commands that every IOS device on the network was configured with. That way, if I had another device to configure and deploy, I could just paste the list in and then do the IP and hostname stuff. It makes me feel a little more confident that the gear I deploy is more standardized and maybe even a little more secure.

Here's a sample for you that I just made up off the top of my head. Obviously you need to change some stuff for your purposes, but you get the idea. Use the sample to generate your own list and save yourself from the embarrassment of having your box run SSH v1.

> service timestamps debug datetime msec localtime show-timezone service timestamps log datetime msec localtime show-timezone service password-encryption
> 
> no logging console logging buffered informational logging 192.168.0.1 logging 172.16.0.1
> 
> no cdp run
> 
> ip domain-name mydomain.com ip ssh version 2
> 
> no ip http server
> 
> access-list 1 remark \*\*\* SNMP access \*\*\* access-list 1 permit 192.168.1.4 access-list 1 permit 10.1.0.1 access-list 1 permit 172.31.0.1 access-list 2 remark \*\*\* Remote access \*\*\* access-list 2 permit 192.168.1.5 access-list 2 permit 10.1.0.2
> 
> snmp-server community mysecurecomm ro 1
> 
> line con 0 transport output none logging synchronous
> 
> line vty 0 15 access-class 51 in transport output none transport input ssh logging synchronous
> 
> ntp server x.x.x.x
