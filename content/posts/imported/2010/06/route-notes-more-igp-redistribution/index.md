---
title: "ROUTE Notes - More IGP Redistribution"
date: 2010-06-23
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
  - "igp"
  - "ospf"
  - "redistribution"
---

As always, feel free to correct.

**Study Notes**

- When a router redistributes from one routing protocol to another, where does the router get the list of routes to redistribute?

From the routing table.  Only IGP A's routes (not topology or successors) are redistributed into IGP B's domain.

- What are two methods of filtering redistributed routes?

Use a _route-map_ in the _redistribute_ line or a _distribute-list_.

- Of the two methods for filtering, which one has more options?

The route-map method has more options.  You can match on all sorts of stuff, including an ACL or interface, and filter based on that.

- How does using _distribute-lists_ differ between OSPF and EIGRP?

In EIGRP, _distribute-lists_ are used to keep a route from being propagated.  In OSPF, they're used to keep routes from reaching the routing table.  The effect is basically the same, but the cause is very different.

- How do I redistribute an EIGRP into OSPF as an E1?

You can set that that in the _redistribute_ command.  You can also match a _route-map_ and set the metric-type there.

- What is a big pitfall of having two routes mutually redistribute the same two IGPs?

A router could redistribute IGP A's routes into IGP B where the second router redistributes them back into IGP A.  Potentially, either router could choose very long routes to get to a destination based on the different ADs and metrics of the IGPs.

- How can I keep this domain loop from happening?

Set the metrics of the redistributed routes so that the originating IGP has the preferred path Set the AD on the redistributed routes so that the  originating IGP has the preferred path Manually filter routes so one IGP isn't presented with its own routes Use _route-tags_ to mark redistributed routes to filter or manipulate later

- How do you change the metrics of the routes?

You can use the _redistribute_ command to set the metrics.  You can also use route-maps to match routes or tags and set the metric.

- How do I change the AD of the routes?

You can use the _distance_ subcommand to set the AD on the whole domain or from a specific originating (or redistributing) router.

- How do I change the AD for route from 1.1.1.1 to 201?

R1(config-router)#distance 201 1.1.1.1 0.0.0.0

- How do I set a route-tag?

Use a route-map to match the routes you want to tag, and use the _set tag_ directive.

- How does using EIGRP as one of my IGPs help me with mutual redistribution on multiple routers?

EIGRP actually has two ADs - 90 for internal and 170 for external routes.  If a route is redistributed into EIGRP, it will have an AD of 170, so it will be less preferred than interal EIGRP, OSPF, or RIP routes.  Unless you're using internal BGP or custom ADs, this will keep a looping route out of the routing table and, thus, from being redistributed.

- How does using OSPF as one of my IGPs help me with mutual redistribution on multiple routers?

You may be able to use the metric-types to do some filtering, but the cool AD thing is for EIGRP only.  Since external OSPF routes have an AD of 110 just like internal routes, you can't rely on AD to keep the looping routes out like you can with EIGRP.

- How does using RIP as one of my IGPs help me with mutual redistribution on multiple routers?

RIP never helped anyone with anything.  Just convert your RIP routers to EIGRP and be much happier.

- What's a quick way to have OSPF set all external routes to an AD of 201?

R1(config-router)#distance ospf external 201

- What happens if I have OSPF redistributing into EIGRP redistributing into RIP (all mutually)?

You may wind up with a VERY long path without some intervention.  You'll need to do some filtering on the redistribution to keep routes short.

- Can't I just use the _redistribute_ command to set metrics coming in and out of all the IGPs to keep the looping routes out?

Yes, you can.  If you have a small network, that shouldn't be a problem.  If you have a couple thousand routes, though, I'm sure people have better things to do than manage metrics.

- In what order do you configure the metrics when redistributing into EIGRP?

Bandwidth, delay, reliability, load, and MTU

- What's weird about an extended ACL when using them with route filtering?

When matching routes, an extended ACL will use the ACL's source field as the network of the route and the destination field as the subnet mask.  For example, _access-list 101 permit ip host 172.16.0.0 host 255.255.255.0_ matches 172.16.0.0/24.

- What can't _route-maps_ do other clean the dishes?

They're used in many, many places on a Cisco router.  I imagine there's a macro somewhere that will clean the dishes, though.

**What Command Was That**

What command...

- ...shows the metric of the route you just redistributed into EIGRP?

show ip eigrp topology

- ...shows the metric of the route you just redistributed into OSPF?

show ip ospf database external

- ...shows the tag of a route?

show ip route

- ...shows the admin distances you've messed up?

show ip protocols

- ...shows the admin distance of a particular route?

show ip route x.x.x.x y.y.y.y
