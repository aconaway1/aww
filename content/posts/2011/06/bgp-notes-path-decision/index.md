---
title: "BGP Notes - Path Decision"
date: 2011-06-09
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "bgp"
  - "ccie"
  - "decision"
  - "notes"
  - "path"
  - "written"
---

This is required blogging...and reading for that matter.  A good chunk of this is taken from my CCNP posts from last year.  Corrections, please.

---

### How does a BGP router decide which BGP route is the best?

**Next-hop** : Does the router have a route to the next-hop?

**Weight** : This is a numeric value where bigger is better.  Weight is not passed onto other peers and is a Cisco proprietary feature.

**LOCAL\_PREF** : This is a numeric value where bigger is better.  All iBGP peers pass this value around amongst themselves.

**Local** : Is the next hop me (0.0.0.0)?

**AS\_PATH length** : This is the number of AS hops to the destination.  If you don't know this one by now, then you missed something big.

: This is the number of AS hops to the destination.  If you don't know this one by now, then you missed something big.

**ORIGIN** : Did this route come from a _netowork_ statement in an IGP (I), from  EGP (E, which shouldn't exist any more), or somewhere else (?) like a redistributed route?  _I_ is better than _E_ is better than _?_.

**MED** : The Multi Exit Discriminator can be used by one AS to influence routes to that AS.  The smaller the better.

**Neighbor type** : eBGP are better than iBGP routes.

**IGP metric** : Prefer the next-hop address that’s closest via an IGP like OSPF or EIGRP (or RIP, Ivan).

**Route age** : Prefer the oldest (and, thusly, the most stable) route.

**Lowest BGP neighbor router ID** : Do I have to explain that one?

**Lowest BGP neighbor IP** : You know what this is, right?
