---
title: "An Interesting Problem with Multiple DCs on a Stick"
date: 2009-03-24
tags: 
  - "client"
  - "csm"
  - "datacenter"
  - "linux"
  - "rip"
  - "route"
  - "server"
  - "vip"
  - "vlan"
  - "vserver"
---

We talked about [running multiple data centers on a stick](/posts/2008/08/running-multiple-data-centers-on-a-stick-with-the-csm/ "AConaway.com -- Running Multiple Data Centers on a Stick") back in August, which is where you have multiple logical pairs of client and server VLANs on a single CSM for different tiers or functions.  The big point of the article was that you had to do some fancy forwarding to get a server-initiated connection from one server VLAN to appear out the appropriate client VLAN.  Well, we ran into an interesting issue with the given solution.

Let's set up a scenario.  Check the diagram for an overview.  We have many pairs of client and server VLANs each with a firewall interface as the gateway into the DCOAS.  Let's just focus on just one, though -- client VLAN 101 and server VLAN 102.  In VLAN 101 is ServerA (not pictured) with an IP of 1.1.101.45; in VLAN 102 is our web farm that needs to connect to ServerA to drop off some data.  We add a static route on ServerA pointing traffic for 1.1.102.0/24 back through the CSM.

![](images/DCOAS-2.svg "Multiple Data Centers on a Stick")

When you try to connect from the web farm, though, it just times out.  Why is that?

Remember that weird forwarding vserver that we had to use to get traffic to come out of the right client VLAN?  Well, that's stabbing you in the eye right now.  When the web server initiates a connection, it sends traffic to the server VLAN IP of the CSM.  The forwarding vserver grabs the new connection and load balances it to its only RIP, which is the IP of the firewall.  What happens when any good firewall accepts traffic destined on an interface destined for a host out of the same interface?  It drops the packet, and, eventually, the server times out.

What's the fix, then?  There are a few that come to mind.  The first may be to just move ServerA to another network segment.  Another may be to change the process around a bit by having ServerA pull the data instead of it being pushed since client-initiated connections will work like a champ.

A really outrageous one would be to set up another forwarding vserver that has only ServerA as it's serverfarm.  You would then add a static route in the web servers pointing to ServerA through that VIP, which would foward it over.

On the CSM, you'd do something like this.

> ```
> serverfarm SERVERA-SF
>  no nat server
>  no nat client
>  real 1.1.101.45  <--- ServerA
>   inservice
> 
> vserver SERVERA-VS
>  virtual 1.1.102.5 any
>  vlan 102
>  serverfarm SERVERA-SF
>  inservice
> ```

On the server, you would add a static route to ServerA through 1.1.102.5. If you're using some brand of Linux, you'd do this.

> ```
> route add 1.1.101.45 gw 1.1.102.5
> ```

Don't forget the static route on ServerA.

Send any ~~[Peeps](http://www.marshmallowpeeps.com/ "MarshmallowPeeps.com -- Marshmallow Peeps")~~ questions my way.
