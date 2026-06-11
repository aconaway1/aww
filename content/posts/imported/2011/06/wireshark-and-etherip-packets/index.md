---
title: "Wireshark and EtherIP Packets"
date: 2011-06-04
categories: 
  - "cisco"
tags: 
  - "capture"
  - "etherip"
  - "header"
  - "wireshark"
---

I got a call from our Systems and Security guys today to talk about a Wireshark capture they had done from a user VLAN.  They had noticed two frames that were destined for some seemingly random host in the same network as they were in, but the source and destination IP addresses reported by Wireshark made no sense.  The frames were from a web server to an IP address on our wireless network.  The web server is on the other side of the firewall, and the wireless network is on the other side of the controller; there was no reason at all that a packet with that source and destination would show up here.

We took a closer look and figured out that Wireshark was showing us the wrong destination and source IPs for the frame.  What we were actually seeing was a pair of frames that were part of an EtherIP tunnel from a wireless the controller on the other side of campus to the local AP.  If you've ever seen an EtherIP frame, you know it can be confusing.  A full Ethernet frame is shoved into an EtherIP packet to be sent via IP to the destination.  Of course, the new packet then gets put into an Ethernet frame and put on the wire.  When all is said and done, you wind up with both an inner and an outer set of IP and MAC addresses among the 8 different "headers" that Wireshark shows.  Wireshark was reporting the inner IP addresses in the packet list, so that was throwing everybody off.  A government-quality screen cap is below for your pleasure.

Once we figured out which addresses the switches and routers were using, it was easy to figure out that there were only 2 frames showing up destined for a MAC that doesn't see much action.  The CAM table entry had timed out for that guy, so the switch had flooded the unknown unicast frame.  After the AP responded, we all lived happily ever after.

I love calls that get the brain working.  It just goes to show that you have to look at the packets closely sometimes to figure out what's going on.

[![](images/wireshark-etherip1-150x150.png "Wireshark EtherIP")](http://aconaway.com/wp-content/uploads/2011/06/wireshark-etherip1.png)
