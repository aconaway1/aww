---
title: "ROUTE Notes - OSPF Virtual Links and Frame Relay Stuff"
date: 2010-06-21
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "frame-relay"
  - "notes"
  - "ospf"
  - "route"
  - "virtual-link"
---

Feel free to correct.  I feel like I'm missing a big piece here, so please fill in a gap if you see one.  Thanks.  :)

**Study Questions**

- How many area 0s (zero) can you have in an OSPF implementation

Just one.

- If my company merges with another company, and we're both running OSPF, how can we get our networks routing together properly?

The easiest thing to do is to connect your two area 0s together through some physical link.  If you can, you can use virtual links to connect an ABR to another ABR to extend the zones together.

- How do you configure virtual links?

R1(config-router)#area 1 virtual-link 1.1.1.1

- That IP in the virtual link command looks like a loopback.  What's up with that?

It's the RID of the router to which you want to connect.

- Why wouldn't I just use a GRE tunnel between my two routes and put that in zone 0?

That's a good question.  I would probably do that instead of virtual links if I had the choice because it eliminates any weird problems you may see with the virtual links.  \[Someone pipe up on this one, please.\]

- What types of authentication can you do with virtual links?

None Clear text MD5

- I've configured _frame-relay map ip 1.2.3.4 101_ on my s0/0/0.1, but I can't get a neighbor to come up.  What gives?

A non-broadcast medium can't detect neighbors dynamically.  You need static neighbors, or you can add the _broadcast_ keyword to the end of your map statement.

- What is the big problem with partial mesh frame relay topologies when OSPF comes into play?

Not all routers are connected directly, so some routes won't see all the neighbors.  When OSPF routes propagate on broadcast medium, the next-hop is the router what propagated it; you'll wind up seeing routes to routers to which you don't connect.

- How do you get over the partial mesh problem?

You can statically configure frame relay maps pointing the IPs of the unconnected routers to the DLCI of a router that is connected to them (like a central hub router).

- What network types use DRs and BDRs?

Anything multiaccess, so the NBMA and BMA.

- Which network types can dynamically discover neighbors?

Broadcast and point-to-multipoint

- Why do you have to configure static neighbors on NBMA and point-to-multipoint non-broadcast links?

Since OSPF uses multicast to talk to neighbors, a router treats the packets like a broadcast.  Since these network types don't have a broadcast capability, the only way a neighbor will be established would be through static statements.

- You have a hub-and-spoke topology over a frame relay cloud.  One of your hubs sees routes for all the networks at the other other hub sites.  Is all as well as it seems?

No.  The routes to the other hub networks will have their next hop set to the frame relay IP of each hub router.  Since one hub router can't get directly to others, the router won't be able to pass traffic to those sites at all.  You'll need to statically map those IPs to the DLCI of the hub site for traffic to flow as expected.

**What Command Was That**

Which command(s)...

- ...define a virtual link using the MD5 has of the key "test"?

R1(config-router)#area 1 virtual-link 1.1.1.1 authentication message-digest R1(config-router)#area 1 virtual-link 1.1.1.1 message-digest-key 1 md5 test

- ...configure a static OSPF neighbor?

R1(config-router)#neighbor 1.2.3.4 \[cost X\] \[priority Y\]

- ...shows the status of a virtual link?

show ip ospf virtual-links show ip ospf neighbor

- ...shows the authentication type and youngest key for a virtual link?

show ip ospf virtual-links

- ...displays the network type for an interface?

show ip ospf interface
