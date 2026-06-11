---
title: "ROUTE Notes - Implementing IPv6 in an IPv4 Network"
date: 2010-07-04
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "ipv4"
  - "ipv6"
---

**Study Questions**

- Your boss says that ever host in the network needs to be converted over to IPv6 by the end of the day.  Which of multipoint tunnels, point-to-point tunnels, or native IPv6 would be the most appropriate to use to help with that conversion?

Native IPv6

- The engineering department wants to permanently use IPv6 on their test boxes in two offices.  Which of multipoint tunnels, point-to-point tunnels, or native IPv6 would be the most appropriate to use?

Point-to-point tunnels

- A handful of departments want to use IPv6 for testing but have no schedule.  Which of multipoint tunnels, point-to-point tunnels, or native IPv6 would be the most appropriate to use?

Multipoint tunnels

- You've implemented 6to4 tunnels and are turning up another router that should participate.  What do you need to configure on the other routers to support the new one?

Nothing.  The use of 6to4 tunnels requires a strict addressing scheme that is used to determine tunnel endpoints dynamically.

- You've implemented ISATAP tunnels and are turning up another router that should participate.  What do you need to configure on the other routers to support the new one?

You need to add static routes pointing the new prefix across the IPv6 address of the new router's tunnel interface.

- How does a router using 6to4 tunnels determine tunnel endpoints?

The second and third quartet of the destination address are used to figure out what the IPv4 tunnel endpoint is.  For example, a host on 2002:a01:a01::/64 sits behind the tunnel endpoint at 10.1.10.1.

- Don't you need to have some sort of IPv6 routing enabled to use 6to4 tunnels?

Since 6to4 tunnels use the reserved prefix of 2002::/16, all the routers just have to point that prefix out the tunnel interface.  Since this covers all the networks that 6to4 uses,  no other routes are necessary.

- How does a router using ISATAP tunnels determine tunnel endpoints?

The last two quartets (7 and 8) of the endpoint's tunnel interface are used to determine the IPv4 tunnel endpoint.  For example, a router with an IPv6 address of 2000::a04:b0b has an IPv4 tunnel endpoint of 10.4.11.11.

- What are the different types of IPv6 tunnels, and what are their tunnel modes?

Manual IPv6 point-to-point - tunnel mode ipv6ip GRE point-to-point - tunnel mode gre ip 6to4 multipoint - tunnel mode ipv6ip 6to4 ISATAP multipoint - tunnel mode ipv6ip isatap

- Which tunnels types support OSPFv3?

Manual IPv6 and GRE

- How are routes learned on ISATAP tunnels?

Routes aren't really learned.  ISATAP requires a static route pointing prefixes towards a tunnel address on a distance router.

- How are the local link addresses determined on an ISATAP tunnel?

The local link address, like all local link addresses, starts with _fe80_ and ends with the IPv4 address of the tunnel source as the last two quartets; quartets 2 through 6 are all zeroes.

- What is required to be configured when using any of the tunnel types?

_ipv6 unicast-routing_

**What Command Was That?**

What command...

- ...shows the status of an IPv6 tunnel?

show ipv6 interface tunnel X show ipv6 interface brief show interfaces tunnel X

- ...shows the routes involved with an IPv6 tunnel?

show ipv6 route

- ...pings a distant host over an IPv6 tunnel terminated on the router?

ping ipv6 _distantaddress_ source _localaddress_
