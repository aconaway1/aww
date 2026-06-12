---
title: "HSRP vs. GLBP"
date: 2008-03-18
tags: 
  - "glbp"
  - "routing"
---

HSRP (Hot Standby Router Protocol) is a Cisco-proprietary method for supplying a highly-available gateway for hosts to use. GLBP (Gateway Load Balancing Protocol) does the same thing. So, what's the difference?

HSRP works on layer 3 and provides a standby IP address for hosts on that network to use as their gateway (or other routers to use as a next-hop for a route). Two or more routers are configured with the standby IP on a broadcast interface (usually an Ethernet of some kind), and a passive election is held to determine the active router. This router answers ARP requests for the standby IP with a virtual MAC address, so every host that sends packets to the standby IP winds up sending it to the active router. If the active router dies, another election is held, and a new king is crowned who answers for the virtual MAC; the hosts never know anything happened.

GLBP is a little different and runs on layer 2. Instead of one router taking all the traffic all the time, GLBP provides a mechanism to load-balance the standby IP. I'm sure you figured that out by the name, though. When configured, GLBP provides a standby IP just as HSRP does, but it also provides multiple virtual MAC addresses. When a host on the connected network sends an ARP request, one of the routers answers with the virtual MAC address. The next time a host ARPs, a different router answers with a different virtual MAC address. After all is said and done in our perfect world, you have an equal number of hosts sending traffic to each router doing GLBP via the virtual MACs (this never pans out due to the way machines ARP). If a router dies, one of the other participating routers takes over for that virtual MAC, and the host is none-the-wiser.

If you're having problems deciding on which one to use, it really all boils down to how many hosts you have on that particular network that you want to be HA. For example, if I had a network that only had two routers as the gateway and a single firewall, I'd use HSRP; there will only be the one firewall ARPing and sending packets the standby IP any traffic so you wind up only using one anyway. If you have a network with a thousand hosts on it (say a web farm), then go with GLBP to balance the traffic across your routers.
