---
title: "ROUTE Notes - Controlling BGP"
date: 2010-07-06
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "bgp"
  - "ccnp"
  - "cisco"
  - "path"
---

Corrections, please.  I skipped a bunch of BGP intro stuff to get to the juicy center.  I'll see if I can come back later and finish the other parts for posterity.

**Study Notes**

- Is BGP route selection a controversial subject?

Yes.  If you ask 1000 network guys the best way to influence BGP, you'll probably get 1000 different answers.

- At what position in the PA list of a BGP update do you find the weight attribute?

You don't.  Weight is a Cisco-proprietary thing.

- List the attributes of a BGP route that a Cisco router evaluates in order of operation with a short description.

Next-hop : Is the next hop IP reachable? Weight : A numeric value where bigger is better; this is of local significance and is not passed to any BGP peers LOCAL\_PREF : A numeric value where bigger is better; this is shared within an AS Local : Is the next hop me (0.0.0.0)? AS\_PATH length : The number of AS hops to the destination; the closer the better ORIGIN : Did this route come from an IGP (I), an EGP (E), or somewhere else(?)?  I over E over ? MED : Multi Exit Discriminator; can be used by one AS to influence routes to that AS; smaller is better Neighbor type : eBGP are better than iBGP routes IGP metric : Prefer the next-hop address that's closest via an IGP like OSPF or EIGRP (or RIP, Ivan) Route age : Prefer the oldest (and thus the most stable) route Lowest BGP neighbor router ID : Do I have to explain that one? Lowest BGP neighbor IP : You know what this is, right?

- Alright, what's the mnemonic?

N WLLA OMNI

- Which attributes can be used to influence your path out to another AS?

Weight LOCAL\_PREF AS\_PATH

- Which attributes can be used to influence another AS's path to you?

MED AS\_PATH

- When you look at the output of _show ip bgp_, which column lists the MED?

The _Metric_ column.

- If there are two entries for a network in the output of _show ip bgp_, in what order are they listed?

They are listed from youngest to oldest.  You can infer the comparative age by looking at the order in which they appear.  See "route age" in the attribute list.

- If I set the weight of a prefix with a route-map in the BGP neighbor config, but then set the weight of the neighbor, what shows up in the BGP table?

The neighbor weight trumps the prefix weight, so all routes from that neighbor will be weighted the same.

- What is different about weight compared to the other attributes?

Weight is actually not a BGP path attribute (PA).  When a route is received from a BGP peer, the weight is set and stored locally; it is not an attribute carried in the routing update like AS\_PATH or MED.

- If you receive the same route from both an eBGP and iBGP peer, what will the local preference be for each route assuming you haven't changed the explicitly?

The eBGP route's local preference will be _null_, and the iBGP route's will be 100.

- What is _maximum-paths_ in BGP land?

That's the maxiumum number of routes that BGP will submit to the RTM if routes are still tied by the IGP metric step of the tie breaker process.

- I applied a route map to a BGP neighbor to change the AS path, but now all the routes are gone except for the influenced routes; what happened?

BGP always tries to filter routes if you use a route map, so you probably just forgot your explicit _permit_ at the end of the route map.

- What super-common mechanism is typically used to change BGP attributes like MED or AS path?

_route-maps_ rock!

- What does ">" mean in the output of _show ip bgp_?

That means the path indicated is the best path for the prefix.

**What Command Was That**

What command...

- ...shows the weights for all BGP routes?

show ip bgp

- ...shows the local preference for all BGP routes?

show ip bgp

- ...shows the AS path for all BGP routes?

show ip bgp

- ...show the MED for all BGP routes?

show ip bgp \[ look under the Metric column \]

- ...shows all the prefixes that a router knows via BGP?

show ip bgp \[ is there a theme here? \]

- ...shows the MED for a specific BGP route in the routing table without using _show ip bgp_?

show ip route x.x.x.x \[ and look for the metric of the route; the number after the AD \]

- ...shows why a BGP route wasn't inserted into the routing table?

show ip bgp rib-failures
