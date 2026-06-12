---
title: "ROUTE Notes - Routing IPv6"
date: 2010-06-30
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eigrp"
  - "ipv6"
  - "ospf"
  - "redistribution"
  - "rip"
---

**Study Questions**

- Why would anyone develop a version of RIP that supports IPv6?

I have no idea.  Boredom, maybe.  Whatever the case, it works just like RIPv2, which is pretty scary.

- In EIGRP for IPv4, there are several requirements for two routers to neighbor up.  Which of those is not true for EIGRP for IPv6?

The two routers don't need to be in the same subnet.  The concept of the link local address takes care of that need since neighbors always share a common medium like an Ethernet segment or a serial link.

- I configured EIGRP for IPv6 on my completely IPv6 router, but it's not working.  Nothing happens.  What's going on?

For one, you have to do a _no shutdown_ as an EIGRP subcommand; by default, EIGRP for IPv6 is in a shutdown state.  Another reason could be that a router ID hasn't been set; EIGRP for IPv6 still uses the IPv4 addresses to establish a router ID, so you may have to set one manually.

- I tried to configure EIGRP for IPv6 with the _network_ statements, but it's not taking the command.  What gives?

You actually configure EIGRP for IPv6 (and RIPng and OSPFv3) the "new way" by using the interfaces.  Try doing a _ipv6 eigrp X_ as an interface subcommand.

- When redistributing one IPv6 IGP into another, what kinds of routes will and won't be redistributed?

Only routes discovered via the original IGP will be redistributed.  Connected routes, even the ones configured in the original IGP, won't be redistributed.  Link local addresses and local routes will also NOT be redistributed.

- Show me a simple RIPng config.

R1(config)#ipv6 router rip PROC-NAME R1(config-rtr)#int f0/0 R1(config-if)#ipv6 rip PROC-NAME enable

- Show me a simple EIGRP for IPv6 config.

R1(config)#ipv6 router eigrp 8 R1(config-rtr)#router-id 1.1.1.1 R1(config-rtr)#no shutdown R1(config-rtr)#int f0/0 R1(config-if)#ipv6 eigrp 8

- Show me a simple OSPFv3 config.

R1(config)#ipv6 router ospf 4 R1(config-rtr)#router-id 1.1.1.1 R1(config-rtr)#int f0/0 R1(config-if)#ipv6 ospf 4 area 0

- How do you include connected routes when redistributing one IGP into another in IPv6?

Use the _include-connected_ directive in the redistribution command.

- In EIGRP for IPv6, what address shows up as the next hop in the routing table?

The link local address of the advertising router.

**What Command Was That**

What command is used to...

- ...show all the IPv6 routes?

show ipv6 route

- ...shows the status of OSPFv3 neighbors?

show ipv6 ospf neighbor

- ...shows the status of RIPng neighbors?

There is none; RIPng doesn't have neighbors.

- ...shows a route to a specific prefix?

show ipv6 route prefix::/length
