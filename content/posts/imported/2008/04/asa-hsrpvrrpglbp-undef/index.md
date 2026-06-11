---
title: "ASA + HSRP/VRRP/GLBP = undef"
date: 2008-04-05
tags: 
  - "asa"
  - "firewall"
  - "glbp"
---

I use Google Analytics to track the 2 or 3 hits I get a day, and sometimes I see some interesting search terms. Yesterday, some googled up the term "does the ASA 5505 run HSRP"; I think that deserves a short article.

The ASA and PIX firewalls don't actually run any of the usual HA solutions you use on routers. They don't do HSPR, VRRP, or GLBP at all. Since firewalls have all sorts of state tables, connection tables, translation tables, blah, blah, blah, they need to share more information than just if they're alive or not, so they use different methods to provide HA.

Cisco uses two different methods to handle this issue - a failover cable or a failover interface.  On a PIX (above the 501s and 506s), you'll see a DB-15 interface labelled...wait for it..."failover".  When a very expensive Cisco cable is placed between the failover ports of two PIXes, the boxes do some election stuff over it and decide on an active and a standby state for each (it does all sorts of stuff, but I won't go into it).  The other method is basically the same, but, instead of an expensive, proprietary Cisco cable, it uses one of the interfaces to connect the two.  You basically connect a crossover cable between two of them, do some configuration, and we're good.   The ASA doesn't come with a failover cable so it uses the interface method, but you need a license for that (imagine that).

Just for the record, if an ASA or PIX becomes the active member of the cluster, it takes over both the IP and MAC address of the primary.  There's no election and configuration of a standby address.  The use of the real addresses, along with the constant sync of the state data, means a very fast and seamless failover.  I actually SSHed through an FWSM (think of it as a PIX on a blade) and pulled the power plugs from it; I actually only lost one packet.
