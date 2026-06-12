---
title: "Trunking on a Catalyst Switch"
date: 2008-03-21
tags: 
  - "catalyst"
  - "lan"
  - "switching"
---

If you didn't now already, trunks are connections between switches that carry traffic for all VLANs. It allows you to have, say, VLAN 10 and VLAN 20 on two switches appear as the same network. Unless you're a really small shop, you've already dealt with trunks, so there's no need for an introduction.

Let's say we have a Catlyst 2950 switch with multiple VLANs connected to another 2950 configured with those same VLANs. We'll say we have VLANs 10, 20, and 30 and that the switches are connected to port F0/24 of each switch. First, let's turn on the trunk.

> interface F0/24 switchport trunk encapsulation dot1q switchport mode trunk

Quite easy there. With this configuration on each switch, the connection between them will carry traffic for all VLANs. The _encapsulation_ directive tells the switches to use the IEEE standard [802.1Q](http://en.wikipedia.org/wiki/IEEE_802.1Q "Wikipedia -- 802.1Q") for the trunk, which is _VLAN tagging_. Cisco has its own trunk encapsulation called [ISL](http://en.wikipedia.org/wiki/Cisco_Inter-Switch_Link "Wikipedia -- ISL"), but that's not compatible with non-Cisco gear. If you have a mix of switches, just use the dot1q encapsulation so you don't hurt yourself later.

A note on the word "encapsulation" here. Dot1q does not actually encapsulate; it adds 4 bytes to the frame header that marks the VLAN the frame is for. ISL, however, does encapsulate; it takes the whole frame, shoves it into an ISL frame, and sends it on. Since Cisco's preferred method for a trunk is an encapsulation method, we have the directive "encapsulation" in the configs.

At this point, all VLANs are being carried across the trunk, but what if you want to use multiple trunks and send different traffic across each one? For example, let's say that you want to have VLAN 10 traffic use a second trunk while the other VLANs use our original trunk. To do that, you get into pruning.

> interface F0/24 ... switchport trunk allowed vlan 20, 30
> 
> interface F0/23 switchport trunk encapsulation dot1q switchport mode trunk switchport trunk allowed vlan 10

The _switchport trunk allowed vlan_ directive says that only traffic on VLANs 20 and 30 are allowed across F0/24 and only VLAN 10 across F0/23. I use this type of setup to give high-bandwidth VLANs (like VLANs for backups) their own trunk so they won't eat all the bandwidth of the other VLANs. To use the terminology, F0/24 is pruned to VLANs 20 and 30, while F0/23 is pruned to VLAN 10.

I also want to mention that the word _trunking_ is used differently across different platforms. We have a nearly-totally Cisco LAN, and trunks are the connections that carry all VLANs as described. On other LAN gear, trunking is actually the act of combining port, links, cables, whatever, together to form a single logical connection (Cisco calls this [EtherChannel](http://en.wikipedia.org/wiki/Etherchannel "Wikipedia -- EtherChannel")). _VLAN tagging_ is what other manufacturers call a Cisco _trunk_. It makes sense if you remember that 802.1Q simply tags the frame.
