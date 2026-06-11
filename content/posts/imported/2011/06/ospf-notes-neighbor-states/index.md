---
title: "OSPF Notes - Neighbor States"
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

My prediction about covering network types was wrong.  I'm going to puke out some information about neighbor states for now.  As is always the case, corrections are welcome.

**Down** : No hellos have been received from this router.

**Attempt** : This state only applies to manually-configured neighbors on an NBMA network.  In this state, a router has sent unicast hellos to the neighbor but has not received any back from it.

**Init** : The router has received hellos, but none of the hellos have the router's RID included as a known neighbor.

**2way** : The router has received hellos with its RID included.  This means the other router has received the hellos from this router, so they now have 2-way communication going.  The DR and BRD is elected at the end of this stage.

**ExStart**: When a router grows up and starts to have feelings for other routers, it enters the ExStart state to have further relations with a neighbor.  This is where the master/slave relationship and the initial sequence numbers are established.

**Exchange** : Once we know who wears the pants in this relationship, the master sends over a DBD with it's LSAs listed.  In response, the slave does the same so that both routers have all the LSA headers they both know.

**Loading** : This is where the LSRs and LSUs flow.  If a router need the full LSA from the neighbor, it sends an LSR, and the neighbor should send an LSU in response.

**Full** : After the LSR/LSU exchange, the routers should both be in sync, so they each send an LSAck to the other to confirm.

As a bonus, here's [a nifty little animation showing neighbor states](http://www.visualland.net/view.php?cid=915&lang=en).
