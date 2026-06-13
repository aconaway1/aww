---
title: "OSPFv3 - The Basics"
date: 2011-02-01
categories: 
  - "cisco"
  - "route"
tags: 
  - "area"
  - "config"
  - "example"
  - "ipv6"
  - "ospf"
  - "ospfv3"
  - "route"
  - "router-id"
---

A few hours ago, the last of the IPv4 addresses were allocated by IANA.  Now's the time to learn more about IPv6!  Yesterday, I posted about [EIGRP for IPv6](http://aconaway.com/2011/01/30/eigrp-for-ipv6-the-basics/), so I think I'll continue the trend by introducing OSPFv3, which is the IPv6 implementation of OSPF.  As always, I'm using Cisco routers here.  Just as yesterday, this is just a guide to the absolutely basics; if you want to do some funky OSPF magic, you won't find it here - perhaps in time, though.

### Configuration

As with all IPv6 routing protocols, the first thing we need to do is enable IPv6 unicast routing.

> Router(config)#ipv6 unicast-routing

OSPFv3 also has the same router ID problem as EIGRP for IPv6 has, so we have to sort that out.  You can set the router ID either through a loopback interface with an IPv4 address on it or you can set it manually.  I'll just do it manually for now.  Let's use OSPF process ID 100.

> Router(config)#ipv6 router ospf 100  
> Router(config-rtr)#router-id 192.0.2.1

Just like in OSPFv2 and in EIGRP for IPv6, we add interfaces to the routing protocol instead of using _network_ statements; those don't exist in OSPFv3.  Let's assume you already have IPv6 addresses on interface f0/0 and you want that network in area 0.

> Router(config)#interface f0/0  
> Router(config-if)#ipv6 ospf 100 area 0

You can see that it's really easy to add interfaces to different areas as well.

### Checking Our Work

Just like we did yesterday, let's check to make sure the right interfaces are participating in the routing protocol.  We can do this with the _show ipv6 ospf interface brief_ command.

> ```
> Router#show ipv6 ospf interface brief
> Interface    PID   Area            Intf ID    Cost  State Nbrs F/C
> Fa0/1        100   0               5          10    BDR   1/1
> Fa0/0        100   2               4          10    DR    0/0
> ```

You can see that we've got two FastEthernet interfaces in two different OSPF areas.  You can even see the state and neighbor count in the output.

That looks good, so let's check to see if we have any neighbors.  Of course, we already saw that we have one off of f0/1 from the output above, but just humor me and run _show ipv6 ospf neighbors_.

> ```
> Router#sh ipv6 ospf neighbor
> 
> Neighbor ID     Pri   State           Dead Time   Interface ID    Interface
> 192.0.2.2         1   FULL/DR         00:00:31    5               FastEthernet0/1
> ```

That looks good to me. The other guy is a DR and is full adjacent with our router. Cool.

One last command shows us the routing table.  Can you guess what that command is without looking at the book?  Very good, class.  It's _show ipv6 route_.

> ```
> Router#sh ipv6 route
> IPv6 Routing Table - 6 entries
> ...
> C   FC00:1::/64 [0/0]
>      via ::, FastEthernet0/1
> L   FC00:1::1/128 [0/0]
>      via ::, FastEthernet0/1
> C   FC00:2::/64 [0/0]
>      via ::, FastEthernet0/0
> L   FC00:2::1/128 [0/0]
>      via ::, FastEthernet0/0
> O   FC00:3::/64 [110/20]
>      via FE80::C001:1CFF:FED0:1, FastEthernet0/1
> OI  FC00:4::/64 [110/30]
>      via FE80::C001:1CFF:FED0:1, FastEthernet0/1
> L   FF00::/8 [0/0]
>      via ::, Null0
> 
> ```

Isn't that fancy?  We seem to have both an area router (the O route) and an inter-area route (the OI route).  We are ready for the big time now!

Send any ~~tunnel broker recommendations~~ questions my way.
