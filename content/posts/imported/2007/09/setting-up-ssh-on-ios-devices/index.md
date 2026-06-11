---
title: "Setting Up SSH on IOS Devices"
date: 2007-09-05
tags: 
  - "cisco"
  - "security"
---

By default, most Cisco \[tag\]IOS\[/tag\] devices come configured to be accessed via telnet. This is probably fine for your house, but I really cringe when I run across corporate networks that use \[tag\]telnet\[/tag\] to access the devices. Telnet is old and out-dated and can be very dangerous. It's in plain-text, which means that anyone who sees the packets can get your username and password. It also has no remote identification mechanism, so you can't guarantee you're talking to the device you think you are; you could be telnetting to a rogue device on your network without knowing it. \[tag\]SSH\[/tag\] gives you both things and more.

On IOS devices, SSH is encrypted based on host \[tag\]keys\[/tag\]. This means your stream cannot be read by anyone but you and the box. Since the \[tag\]encryption\[/tag\] is based on host keys, it guarantees that you are talking to the router or whatever you meant to. If you wind up on a rogue box or something, your SSH client tells you that the key the host provided isn't the same as the one that was used the last time you SSHed over. Possibly the most important thing to know about SSH is that it is required for PCI or SarBox compliance where telnet is a no-no. Yes, I hate compliance, too, but what's an admin gonna do?

So, how do we do this? Let's work on the prerequisites first.

First of all, you have to be sure your IOS version actually supports it. Since there are two IOS naming standards, there's not a magic number or letter to look for to know if yours is supported. I suspect that the letters "k9" will guarantee that SSH is supported, but, if in doubt, the easiest thing to do is to look at the Cisco [Software Advisor](http://tools.cisco.com/Support/Fusion/FusionHome.do "Cisco -- Software Advisor") and look for a version that support "Secure Shell SSH Version 2" (SSH version 1 is a terrible \[tag\]implementation\[/tag\] and should be avoided). Upgrade your box if needed.

The next prerequisite is pretty elementary: you simply have to add a domain name. You may have this configured already, but you need it to generate a host key. Here's how.

> ip domain-name your.domain.name

There's another thing that is not really a prerequisite, but, in my mind, it is a wonderful thing to do. You should have a way to authenticate users instead of the telnet password/enable password thing. I mean where you put in a username and password and authenticate against some source somewhere. I recommend TACACS+ since it loves Cisco devices, but Radius or even local \[tag\]authentication\[/tag\] are fine. I'll show you how to set up local authentication here since it's the easiest thing to do here.

> aaa new-model username myuser password mypass username myuser privilege 15

This is very simple, and I would never run it like this, but here's what it means. The "aaa new-model" says to use the advanced authentication model to authenticate users. The default is to use local authentication, but it is fully customizable (this sounds like another article for later). The "username myuser password mypass" sets the password, duh! The last line sets your privilege level to 15, which is the same as getting into enabled mode. I suggest you set your users to other privilege levels and have everyone enables; it's the same as logging into a Linux box and doing a _sudo_. _**NOTE**_: Try to log into the box from another window before you log off to make sure AAA is working. You may get locked out if you log out before testing!

Alright, now for the SSH part. First we need to generate a key.

> cryto key generate rsa

It'll ask you for the length. The standard is 2048, so just type that in for now. You can always re-generate your keys later. That's actually all you need to configure to enable SSH. Yes, I realize 95% of the configure time is prerequisites. It's comical, really.

After you get the key generated, open another window and try to SSH to it from your local SSH client or Linux box. It should ask if you want to add the key to the key ring or known\_hosts file. Just say yes so you can track the keys in the future (remember the remote identification thing?). If it works, you're golden. If not, check your firewall or AAA for problems.

Done, right? Wrong. Try to telnet over to your box -- it's still available. We should turn off everything but SSH. It's not hard. The lines here will turn off all access to the CLI on the box except for SSH.

> line vty 0 15 transport input ssh

One more thing and we're done. You should set your SSH version to 2 instead of the default negotiate. This will make sure you're not running the vulnerable and exploitable SSH version 1.

> ip ssh version 2

The configuration is done, and we all still have all our toes, but what would a Cisco article be without a show commands. Here's how to list all the settings for SSH on your box.

> show ip ssh

Don't you dare complain that this was hard because it wasn't. Leave a comment or drop me an email if you have any questions or problems. I'll be glad to help.
