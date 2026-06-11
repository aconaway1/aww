---
title: "Filtering Out the Noise on the Edge"
date: 2009-01-21
tags: 
  - "ios"
  - "security"
---

There's a lot of noise on the Internet.  I'm not talking about certain news sites, either; I'm talking about stuff like port scans or attempts on weak services from all sorts of bad people on the Internet.  A large chunk of that noise can be filtered by the edge routers, taking some of the load off of the network and firewalls.

Here are a few things that we filter inbound on our Internet links.  Your mileage will vary.

- Packets from [RFC 1918 space](http://www.faqs.org/rfcs/rfc1918.html "FAQs.org -- Address Allocation for Private Internets") -- You should never see a packet from 10/8, 172.16/12, or 192.168/16.
- Packets from your IP space -- Why would you receive packets from yourself from the Internet?
- SSH, telnet, cmd, rlogin, RDP, etc. --  You should be doing all your admin stuff from the internal network or from a VPN, right?
- Windows ports -- For God's sake, drop these at the edge.
- Packets to your network services subnets -- If you use public addresses for things like your FWSM or CSM sync networks, no one should ever talk to those subnets.
- SNMP, SNMPTrap -- No monitoring from the Internet!
- SMTP to non-MX hosts -- If you have a lot of hosts, you probably have email run amongst them.  Only the MX hosts should accept connections from the Internet.
- TCP/UDP small services -- whois, finger, chargen, etc., are just waiting to be used for something bad.
- DNS, RNDC -- You may have some name caching servers or hidden masters somewhere that shouldn't be reachable from the Desolate Plains of the Internet™.
- Syslog -- No logging from the Internet.  Use a VPN tunnel or something if you really need it.
- NTP -- You're not a time service, are you?

That should cut out a significant amont of noise for you.  Remember to allow stuff, too.  You may want to end your ACLs with an old-fashioned _permit ip any any log_ to see what else is coming through and maybe block some of that noise, too.
