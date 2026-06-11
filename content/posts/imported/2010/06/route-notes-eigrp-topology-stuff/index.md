---
title: "ROUTE Notes - EIGRP Topology Stuff"
date: 2010-06-17
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "842-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eigrp"
  - "route"
---

**Study Questions**

- How do you keep EIGRP from killing your WAN?

You can use the _ip bandwidth-percent eigrp AS X_ command to limit the amount of bandwidth that EIGRP uses to update neighbors.

- How does EIGRP calculate how much bandwidth it can use for each frame relay PVC?

By default, EIGRP takes 50% of the (sub)interface's configured bandwidth (with the _bandwidth_ command) to use for updates on NBMA (non-broadcast mutliaccess) networks like frame relay.  This value is divided equally among all the PVC configured on that interface.

- Why should you use delay instead of bandwidth to manipulate EIGRP?

There are other mechanisms, like QoS, that use bandwidth, so changing that value would affect those mechanisms.  Only EIGRP uses delay.

- What's the difference between the feasible distance (FD) and the reported (advertised) distance (RD)?

Feasible distance is the EIGRP metric value after the router has added it's own information like bandwidth and delay to the formula.  The reported distance is what a router calculates before it has added it's own values.  Essentially, the FD of one router is the RD of the next.

- What is an offset list?

An offset list is a way to artificially increment the FD  and RD of a route or set of routes.

- You can add the _load_ k-value into the metric calculation in EIGRP, but it's not generally a good idea.  Why?

The load is constantly changing as traffic changes on an interface.  This would cause a constant stream of updates as traffic flows change.

- How often does a router send its full EIGRP topology table?

When new neighbors come up, the neighbors exchange their full tables, but, from that point forward, only updates are sent.

- When we talk about bandwidth in EIGRP, what are we actually talking about?

The bandwidth is actually the bandwidth of the slowest link between a router and the destination network.  This is what's used in the calculations.

- Assuming we're using k1 and k3, what is the formula for calculating the metric?

metric = 256 \* ( 10^7 / bandwidth \[in kbps\] + cumulative delay )

- You've decided to use k2 in your metric calculations, so you add that to a router.  What happens to all the neighbors?

The neighbors all drop and start generating a "K-value mismatch" error.

- What is a successor?  Feasible successor?

A successor is the EIGRP route for a particular network entry with the lowest metric.  This is the route that EIGRP submits to the routing table for inclusion.  A feasible successor (FS) is another EIGRP whose RD is lower than the successor's FD; feasible successors can be used as an alternate path to a network if the successor goes away somehow.

- Where would you run into split horizon issues with EIGRP?

Split horizon says that you don't advertise a route over the interface on which it was received.  If you have a multipoint WAN link of some kind, routes from one spoke won't be passed to another spoke through the hub.

- In what unit is the _delay_ directive?

Tens of microseconds (10 \* usec).  That means that _delay 1000_ is 10,000 usec, or 10 ms.

- How does EIGRP do unequal cost path load balancing?

You can set the _maximum-paths_ value under EIGRP to set the maximum number of equal paths that can be used.  You also set the _variance_ command there with a multiplier integer.  The variance is multiplied by the successor's FD, and any feasible successor whose metric is less than this new number is considered equal cost.

- What is an EIGRP stub router?

A stub router only receives routes via EIGRP and does not send them to other EIGRP neighbors.  Since all the other routers know a router is a stub, they won't send query messages to the stub router if they're looking for a route.  This will cut down on time waiting in active state.

- What is "stuck in active"?

If a successor for a network becomes unavailable and there are no FSes, a router will query each of its neighbors for a new routes to that network.  If that router does not have a route, it will then ask its own neighbors, etc.  In the meantime, the original router is still waiting for everyone to answer (that is, the route is in the active state) and will wait patiently until that happens.  This could take a long time and result in a several-second outage to the queried network.

- By default, which types of routes are sent to EIGRP neighbors from a stub router?

Connected and summary

**What Command Was That?**

What command...

- ...disables split horizon on an interface?

R1(config-if)#no ip split-horizon eigrp 1

- ...sets the delay of an interface to 10ms?

R1(config-if)#delay 1000  (remember the tens of usec unit)

- ...restricts the amount of bandwidth that EIGRP uses to 20% of the total bandwidth on an interface?

R1(config-if)#ip bandwidth-percent eigrp 1 20

- ...apply an offset list to interface F0/0?

R1(config-router)#offset-list ACL in OFFSET F0/0

- ...show the metrics of all the EIGRP routes a router has recieved for a network?

show ip eigrp topology 192.168.0.0/24

- ...shows the successors and feasible successors for a network?

show ip eigrp topology 192.168.0.0/24

- ...shows if a neighbor is a stub?

show ip eigrp neighbor detail

- ...shows the _maximum-paths_ and _variance_ values?

show ip protocols
