---
title: "DHCP ACK Error on Avaya Phones"
date: 2010-12-27
categories: 
  - "voice"
tags: 
  - "ack"
  - "avaya"
  - "dhcp"
  - "error"
  - "port"
  - "switch"
---

We're an Avaya voice shop (for now if I have my way) and have Avaya systems of various sizes and shapes all around the Enterprise.  I was at one of our remote locations a few weeks back and helped the guys there replace a non-PoE switch so they could get the old power injector panel out of their rack.  When we moved stuff around, the phones didn't come back and had the dreaded DHCP Ack Error.<!--more-->

In the environment, we have the usual data and voice VLANs on every switch port, and PCs are connected through the built-in switch in Avaya 4610SW IP phones.  The DHCP server is a Windows something-something-something server (maybe 2008) serving both VLANs.  When the phones boot, they come up on the data VLAN, get DHCP option 176 (remember it's Avaya and not Cisco), reboot onto the voice VLAN, and wait for an address; after a few seconds, they get the DHCP Ack Error displayed on the phone.  Thanks for that very descriptive error, Avaya.  The data VLAN was working fine, and, when I put my switch port into the voice VLAN natively, I got an address on my laptop.  It's just the phones.

A quick search showed a solution that I've seen a thousand times but keep forgetting (another in a long list).  If the DHCP server is in the data VLAN and is also tagged in the voice VLAN, the phones won't get an address.  Huh?  We didn't change the DHCP server.  Well, it turns out that the server was in the original non-PoE switch, and we had moved it to a port configured for a workstation (a fact I left out in the original edit of this post).  As soon as I took the _switchport voice vlan X_ off of the server port, the phones started getting addresses, and we could finally hear the sweet sound of dial tone.  Problem solved.

According to [this Avaya users group post](http://avayausers.com/showthread.php?t=3251&highlight=dhcp+ack+error), this happens because the DHCP server receives a DHCP request in a tagged packet; the server doesn't like it and NAKs it.  The fix works, but I'm not really satisfied with the answer of "it doesn't like it".  I may take a day and test this myself in my home lab.
