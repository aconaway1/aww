---
title: "Router-on-a-Stick"
date: 2007-08-20
tags: 
  - "cisco"
---

Ever heard of a router-on-a-stick? Go ahead and laugh...everyone does. It's a funny name for a very serious topic, though. A router-on-a-stick is a network configuration that uses a single router interface as a gateway for more than one network segment. You literally take a single Ethernet interface, put it on multiple VLANs, and set up the IP address stuff.

Here's how it works: The router is plugged into a port on a switch that is configured as a trunk that carries all the important VLANs. The router is configured with Ethernet sub-interfaces (just as you do frame-relay or ATM sub-interfaces) -- one for each VLAN. Piece of cake.

Using a router-on-a-stick gives you a few things. First, it can save you a lot of cash since you don't need an expensive layer-3 switch or additional routers for all the VLANs. It also centralizes the network into a single point, so every network is directly attached, and no routing protocols are really needed. Since it segments your network, you can also apply inbound or outbound ACLs to control traffic. Savings, ease of managment, and security? Wow!

Setting your network up this way may set you up for disaster, though. You have two single-points-of-failure in the router and switch. If either one goes down, you're whole network is gone. If I were to implement a setup like this, I would have two switches with at least 2 trunks between them. I would also have two routers that are each configured as a router-on-a-stick and run [HSRP](http://en.wikipedia.org/wiki/Hot_Standby_Router_Protocol "Wikipedia Article") or something similar for redundancy and availability.

Here's a terrible diagram to show what we're doing. In our example, we have two works stations connected to a switch on VLANs 2 and 3.

![Router-on-a-stick Diagram](images/ROAS.svg "Router-on-a-stick Diagram")

How about some sample configurations to bring it all together? Of course, I'm assuming you're using a Cisco router and Cisco switch. I can't speak for any other platforms. I'm also assuming that you are doing to use 802.1q trunks.

Configure the switch for a trunk.

> interface FastEthernet0/1 description TRUNK switchport mode trunk switchport trunk allowed vlan 2-3

Configure the router sub-interfaces for each network.  I picked F0/0.2 and F0/0.3 because we're using VLANs 2 and 3, but it doesn't matter what the sub-interface names are at all.  It's just logical to use the same sub-interface number as the VLAN ID.

> interface FastEthernet 0/0 description The Stick ! interface FastEthernet 0/0.2 description Leg into VLAN2 encapsulation dot1q 2 ip address 10.0.2.1 255.255.255.0 ! interface Fastethernet 0/0.3 description Leg into VLAN3 encapsulation dot1q 3 ip address 10.0.3.1 255.255.255.0

Pretty easy stuff. Let me know if you have any questions.

---

Let me make a note here. When looking around for a good page to reference for this article, I read a lot of pages that said that this is a definite CCNA or CCDA test topic. I've taken the CCNA twice now (I passed both -- the first one expired) and didn' t see anything like this on either.
