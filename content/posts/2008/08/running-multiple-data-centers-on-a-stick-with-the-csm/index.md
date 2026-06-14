---
title: "Running Multiple Data Centers on a Stick with the CSM"
date: 2008-08-12
tags: 
  - "csm"
---

That's an awesome title, eh?  I've mentioned a [router-on-a-stick](/posts/2007/08/router-on-a-stick/ "AConaway.com -- Router on a Stick") before but not a data-center-on-a-stick (DCOAS).  This is one of those Cisco terms I ran across a while ago and is a group of servers sort of sticking out on their own behind a load balancer and/or firewall.  Connections to and from the server group go through a single spoke -- kinda like stubby routing.  Here's a pretty picture.

![Data Center on a Stick](images/DCOAS-1.svg "Data Center on a Stick")

To configure this type of setup on the CSM, assuming you're running in router mode, you just define a server and client VLAN pair along with an alias IP on the server VLAN.  The servers on that VLAN point to the alias as their gateway for return traffic, and the CSM handles the VIP/RIP conversion.

What if you have more than one server/client VLAN pair, though?

![Data Center on a Stick](images/DCOAS-2.svg "Data Center on a Stick")

You set up your VLANs and your server VLAN alias for both sets and all is well, right?  Not really.  The CSM isn't a router and doesn't do a very good job at routing.  If you were to send traffic from "Other Stuff" to a VIP on VLAN101, everything works great.  If, however, one of those servers initiates a connection, the traffic will come out of the client VLAN with the lowest VLAN ID.  If you have VLANs 1 and 2 for the top pair, traffic from **all** servers will come out VLAN 1.  Very big problem if you have a firewall that's expecting traffic on another interface.

How do you get around it, then?  You have to trick the CSM by generating new catch-all vservers on each server VLAN to "load balance" the gateway for each VLAN.  Let's look at the configs.

Here are the serverfarms.

> serverfarm VLAN1-SF no nat server no nat client real 1.1.1.1 inservice
> 
> serverfarm VLAN101-SF no nat server no nat client real 1.1.101.1 inservice

And the vservers.

> vserver VLAN1-VS virtual 0.0.0.0 0.0.0.0 any vlan 2 serverfarm VLAN1-SF inservice
> 
> vserver VLAN101-VS virtual 0.0.0.0 0.0.0.0 any vlan 102 serverfarm VLAN101-SF inservice

When a server makes a new connection to a database or whatnot, it sends the traffic to the alias you already configured on the server VLAN.  When the CSM receives that traffic through the alias, this new vserver takes over since it's set for any protocol and port on any IP.  This vserver uses a serverfarm that contains the gateway for the appropriate client VLAN -- in our case, the IP of the firewall -- so now every connection from the server will exit an appropriate VLAN instead of out the lowest ID.

Confusing?  Yes.  Should be fixed?  Yes.  Is already fixed?  I have no idea, but it's not as of 12.2 and some change.
