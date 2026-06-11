---
title: "ROUTE Notes - Even More IGP Redistribution"
date: 2010-07-17
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "cisco"
  - "eigrp"
  - "ospf"
  - "redistribution"
  - "route"
  - "test"
---

I didn't do so well on IGP redistribution the last time out, so here's some more stuff to study.  As always, feel free to correct.

**Study Questions**

- What three things are needed to be able to redistribute one routing protocol into another?

1\. One or more links into each routing protocol 2. A proper, working config for each protocol 3. The addition of the _redistribute_ command to one or more of the protocols

- You just configured OSPF to redistribute EIGRP routes, but EIGRP, with the _network_ statement of _network 0.0.0.0 0.0.0.0_, is configured with a passive interface.  Does this interface's connected network get redistributed?

Yes, it does.  Even if it's not participating in routing, it's still an interface that EIGRP is configured to use, so it goes along for a ride on the redistribution train.

- Name three ways to set the metric of redistributed routes in EIGRP.

1.  _default-metric ..._ 2.  _redistribute X metric ..._ 3.  _redistribute X route-map ..._

- How can I set the metric for all OSPF routes redistributed into EIGRP?

Use the _redistribute ospf X metric_ command.

- You are redistributing OSPF into EIGRP and want to set the metric of one particular route to another set of metric values (BW, delay, etc.).  How do you do that?

Use a route-map to match the single route and to set the new values.

- Routes from what routing protocol need a metric set when redistributing into EIGRP?  Routes from what protocols don't?

Routes from another EIGRP instance have their metrics copied over; all others need to have it set.

- What's the default metric of a BGP route when redistributed into OSPF?  EIGRP?

BGP has a metric of 1 in OSPF.  There is not default metric in EIGRP without some configuration.

- You left out the _subnet_ keyword when redistributing EIGRP into OSPF.  What is the result?

Only classful routes will be redistributed and only if EIGRP has a classful route to redistribute.

- You left out the _subnet_ keyword when redistributing OSPF into EIGRP .  What is the result?

There is no _subnet_ keyword for redistribution under EIGRP.

- Routes from what routing protocol need a metric set when redistributing into OSPF?  Routes from what protocols don't?

OSPF set metrics automatically.  If the route came from another OSPF process, the metric is copied over.  If the route came from BGP, the metric is set to 1; if it came from any other routing protocol, the metric is set to 20.

- What are three ways to manipulate the metric of redistributed routes in OSPF?

1.  _default-metric ..._ 2.  _redistribute X metric ..._ 3.  _redistribute X route-map ..._

- My ASBR is advertising static routes into area 0, but I'm not seeing any type-5 LSAs in area 1.  What's gives?

Assuming everything else is configured correctly and no filtering is done, area 1 is probably a stub area of some kind.

- My ASBR is advertising static routes into area 1, but I'm not seeing any type-5 LSAs in that area.  What gives?

Area 1 is probably an NSSA or totally NSSA area, so any external routes are flooded as type-7s - note type-5s.

- If I look at the OSPF database on my router, I see that a whole bunch of type-5 LSAs advertised from the router with the ID of 1.1.1.1.  What does that say about that router?

Among other things, that router is an ASBR and is redistributing external routes into that area.

- I see several routes in the OSPF database with a cost of 20.  What metric type are those routes?

More likely than not, they are type-2 routes (O E2).

- I have two type-5 LSAs for the same network through two different ABRs; both are of the type-2 metric.  How does the router decide which one to use?

Since both routes are E2, they will have a metric of 20 (unless manipulated somehow), so looking at the intra-area cost results in a tie.  The router will then look at the type-4 LSAs which contain the cost from the ABR to the ASBR.  Since each ABR floods these type-4s, the router knows which ABR is closer to the ASBR advertising the route.  The lower metric in the type-4 LSAs wins.

- I have two type-5 LSAs for the same network through two different ABRs; both are of the type-1 metric.  How does the router decide which one to use?

Since both routes are E1, the costs to the ABR are first compared since they may be different.  If tied, the type-4 LSA's cost to the ASBR is compared.  If still tied, the external (type-5 LSAs) cost is compared.

- I have two type-5 LSAs for the same network through two different ABRs; one is type-1 and the other is type-2 ?  How does the router decide which one to use?

E1 routes are always preferred over E2 routes.

- Why can't you redistribute static routes into a stubby network?  How can you make it work?

Stub networks do not flood type-5 LSAs, so the routes cannot be advertised into the area.  You can change it to a regular area to make it work.  You can also make it an NSSA or totally NSSA area.

- How do OSPF routes that come from type-7 LSAs appear in the routing table?

They appear as "O N1" or "O N2" depending on the metric type.
