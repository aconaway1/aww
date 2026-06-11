---
title: "OSPF Notes - LSA Types"
date: 2011-06-02
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ccie"
  - "cisco"
  - "dbd"
  - "hello"
  - "lsa"
  - "lsack"
  - "lsr"
  - "lsu"
  - "message"
  - "ospf"
  - "type"
  - "written"
---

Yes, it is inevitable that I cover these.  I'm sure network types will be next.  Per my usual request, please correct my stupidity.

**Type 1 - Router** : This LSA type lists all the routers by RID as well as the networks to which that router connects.

**Type 2 - Network** : These LSAs represent broadcast network where more than one OSPF router may live.  Think Ethernet or multipoint segment.  These LSAs are flooded by the DR for that segment.

**Type 3 - Net Summary** : An area border routers take the type 1s and 2s from one area and floods them as type 3s into another, so all of these LSAs are from other areas.  No topology information is included in these LSAs; it's basically an advertisement from the ABR saying the route is through him.

**Type 4 - ASBR Summary** : These LSAs make sure that all routers in all areas have a path to an ASBR that's flooding type 5 LSAs.  Those routes in the area with the ASBR won't see these.

**Type 5 - AS External [Online Casino](http://normproject.org/)** : These are flooded by an autonomous system boundary router and are routes redistributed into OSPF from another routing process like EIGRP or BGP.  Since these routes come from a different source, there's no way to discover the topology past the ASBR, so we just have to trust the rumor that the network exists that way.  E1 routes gets the OSPF path cost added as it crosses the network, while E2 routes (the default) have a static cost.

**Type 6 - Group Membership** :  This is for Multicast OSPF and not supported by IOS

**Type 7 - NSSA External** : An ABSR in an NSSA floods routes to the external routing process through type 7 LSAs.  ABRs will translate these to type 5s when flooding other areas.

**Type 8 - External Attributes** : Back in the day, OSPFv2 had plans to overthrow iBGP.  Type 8 LSAs would have been used to carry BGP attributes while the routes themselves would be of type 5.  Type 8s aren't supported in IOS.

**Type 9,10,11 - Opaque** :  Something...something...traffic engineering...blah, blah, blah.
