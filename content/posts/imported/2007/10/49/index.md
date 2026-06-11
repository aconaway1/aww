---
title: "Monitoring the CSM with SNMP"
date: 2007-10-24
tags: 
  - "cisco"
  - "csm"
  - "snmp"
---

I had an [article](http://aconaway.com/2007/10/02/getting-started-with-the-cisco-csm/ "AConaway -- Getting Started with the Cisco CSM") a few weeks ago about the Cisco CSM, which is a load-balancer module for the 6500 series switches. This thing is a pretty good device, but monitoring the connections to each VIP and RIP is not very straightforward. If you have an SNMP monitoring system like [Cacti](http://cacti.net/ "Cacti -- Home Page") or [MRTG](http://oss.oetiker.ch/mrtg/ "MRTG -- Home Page"), you need to know the OID to monitor, but it doesn't work like anything else in the world.

Let's start with the OID for the vserver. First, there's the base OIDs that you can look up on CCO. This is just standard SNMP stuff that Cisco defined long ago. The slot number that the CSM is in is added to that base OID. You then have to add the length of the vserver name -- don't ask me...I don't know. Next comes the really stupid part -- you have to take the names of the vserver and get the ASCII values of every character in the name and add each to the end to get the full OID. Yes, it's that stupid.

> <BASE VSERVER CONN OID>.<SLOT>.<LENGTH>.<VSERVER NAME>

The serverfarm OID is pretty close -- the base OID and slot. You then add the length of the serverfarm name along with the ASCII values of the serverfarm name (quite like we did for the vserver). Finally, the only part that might make sense, you take the IP of the real server and add it to the end with the instance of "0". Again, I have no clue why it's like this or what Cisco was trying to do. It's terrible.

> <BASE SF CONN OID>.<SLOT>.<LENGTH>.<SF NAME>.<REAL IP>.0

How about an example. Let's say you have a vserver called VSERVER1 that you want to monitor. This vserver is configured to use the serverfarm FARM1, which has two reals of 192.168.1.101 and 192.168.1.102 that you also want to monitor. You also know that the CSM is in slot 3. The base OID for vserver connections is .1.3.6.1.4.1.9.9.161.1.4.1.1.17; the base OID for serverfarm connections is .1.3.6.1.4.1.9.9.161.1.3.1.1.5. This all gives you this:

> VSERVER: .1.3.6.1.4.1.9.9.161.1.4.1.1.17.3.8.86.83.69.82.86.69.82.49 RIP1: .1.3.6.1.4.1.9.9.161.1.3.1.1.5.3.5.70.65.82.77.49.192.168.1.101.0 RIP2: .1.3.6.1.4.1.9.9.161.1.3.1.1.5.3.5.70.65.82.77.49.192.168.1.102.0

Did I mention it's overly-complicated and terrible? It's actually so bad that I just wrote a Perl script to do it for me because, for God's sake, I'm not doing that by hand. Let me know if you need any help with it.
