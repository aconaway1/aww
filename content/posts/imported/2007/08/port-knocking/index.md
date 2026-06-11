---
title: "Port Knocking"
date: 2007-08-11
tags: 
  - "linux"
  - "security"
  - "tools"
---

A few months ago, a friend of mine told me about the concept of [port knocking](http://en.wikipedia.org/wiki/Port_knocking "Wikipedia Article"), where you send packets to a server on certain ports to authenticate access to the box. A daemon running on your server detects the sequence of packets that you send and runs a script (usually IPtables commands), waits a certain amount of time, then runs another script (usually to take the IPtables commands out). This seems like a good way to get access to your home firewall from anywhere without having to open up access to the whole Internet.

To set it up, you have to install _knock_, which is the daemon that listens to the port knocking. Just use _yum_ or _apt-get_ to install it and you'll wind up with the configuration file in _/etc/knockd.conf_. This is where you set up one or more knock sequences to do what you want. I won't go into the internals of how it works or how you should set it up but I will go into a few examples.

I use port knocking on my home network to protect administrative access to everything on the network. I wrote a custom IPtables script that, when activated, open access from my IP on the wireless network to SSH (TCP/22) on my firewall, file server, access point, and switch. After 30 seconds, another script runs, and those rules are removed. Here's an example of a config file that opens up SSH when you hit ports 1234, 5678, 9876, and 5432. After 30 seconds, it kills the rule.

> \[options\] logfile = /var/log/knockd.log
> 
> \[openssh\] sequence = 1234, 5678, 9876, 5432 seq\_timeout = 5 tcpflags = syn start\_command = -A INPUT -s %IP% -d 192.168.1.1 --dport 22 -j ACCEPT cmd\_timeout = 30 stop\_command = -D INPUT -s %IP% -d 192.168.1.1 --dport 22 -j ACCEPT

So, how do you generate these packets? On my CentOS boxes, you get the _knock_ command which is the port knocking client. On Windows, I use [_KnockKnock_](http://sourceforge.net/projects/knockknock/ "Sourceforge -- KnockKnock"). I have no clue about Macs, but there are lots and lots of clients out there, so just look around and I'm sure you'll find one.
