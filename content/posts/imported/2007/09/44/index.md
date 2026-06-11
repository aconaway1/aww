---
title: "Finding Hosts on Layer 2"
date: 2007-09-27
tags: 
  - "cisco"
  - "linux"
---

Most firewalls should block \[tag\]ICMP\[/tag\] requests to them, so how do you know that your router or server has layer-2 connectivity to one? It's pretty elementary, actually, but I've found that not a lot of people know this trick. If you ping the firewall, it will receive the ICMP packet and drop it per the rulebase. In this process, though, the firewall has to answer \[tag\]ARP\[/tag\] requests, which will be stored in the router or server's ARP table. If you see it in there, you have connectivity.

On \[tag\]IOS\[/tag\]:

> show arp

On \[tag\]Linux\[/tag\]:

> /sbin/arp -an

This won't help you if you're not on the same network as the firewall, but it's very helpful -- especially if the firewall group is separate from the group you're in. These commands have saved me a lot of time by not having to get a bunch of people on the phone to sniff packets as I generate them only to find that the firewall isn't talking to my router.
