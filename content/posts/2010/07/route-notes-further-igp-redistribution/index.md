---
title: "ROUTE Notes - Further IGP Redistribution"
date: 2010-07-18
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "bgp"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eigrp"
  - "exam"
  - "ospf"
  - "redistribution"
  - "route"
  - "test"
---

As always, corrections are requested.

**Study Questions**

- I've got IGRP and EIGRP both configured with the same AS number.  What's special about this configuration?

If both use the same AS number, then they automatically redistribute their routes into each other without using the _redistribute_ command.

- When redistributing one IGP into another, where's a good place to filter routes?

There's no one good place, but at the router(s) that's doing the redistribution is a good start.  There's no need to send an IGP a bunch of routes it doesn't need.

- When redistributing one IGP into another, where's a good place to summarize routes?

There's no one good place, but that may be best done at the router just inside the redistributing router.  If the redistributing router only sees the summary route, that's what it will pass to the other IGP.

- What's the default metric of RIP?

That's infinity, so it's unreachable with an explicit metric.

- I've redistributed OSPF into RIP, but I don't see my subnets there.  What gives?

RIP automatically summarized routes, so look for summaries instead of specific subnets.

- How can you limit the number of routes redistributed into EIGRP or OSPF?

Use the _redistribute maximum-prefix_ _X_ directive under the routing protocol, where _X_ is the maximum number of routes.

- What are the metrics of connected routes when redistributed into EIGRP?

Those routes take the metric of the associated interface instead of using the metric you gave to the redistribution.  \[This seems fishy at best.  Can anyone help clarify, please?\]

- I have 845734928 interfaces on my router, but I only want to use 3 of them for EIGRP and only want to configure a single _network_ statement.  What's the easiest way to do that?

Set all the interfaces as passive with the _passive-interface default_ router subcommand.  Next, make all your interesting interfaces non-passive with the _no passive-interface X_ subcommand.  Now you can configure _network 0.0.0.0 255.255.255.255_ to match all the interfaces, but only the interesting interfaces will participate.

- What is the term for the rank of trustworthiness a routing protocol provides?

Administrative distance

- How can I change the AD of external EIGRP routes to 201 while keeping the default AD for internal EIGRP routes?

Router1(config-router)#distance eigrp 90 201 You have to set both, so you'll have to remember that EIGRP has an AD of 90 for internal routes by default.

- How can I change the AD of OSPF routes to 192.168.0.0/24 to 202?

Router1(config)#access-list 88 permit 192.168.0.0 0.0.0.255 Router1(config)#router ospf X Router1(config)#distance 202 0.0.0.0 255.255.255.255 88

- Is it possible to set the AD of different OSPF routes types like intra-area and interarea?

Yes.  You can give it the old _distance ospf inter-area X_ to change the AD.  It also works for intra-area and external routes.

- Is it possible to set the AD of an external OSPF route to 192.168.100.0/24 to 202 without changing the others?

I would have though you could use a route-map for that, but I can't find a proper _set_ command in a route-map.  \[A little help, please.\]
