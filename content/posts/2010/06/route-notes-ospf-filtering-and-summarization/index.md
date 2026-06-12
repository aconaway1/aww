---
title: "ROUTE Notes - OSPF Filtering and Summarization"
date: 2010-06-20
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "area"
  - "ccnp"
  - "certification"
  - "cisco"
  - "ospf"
  - "route"
  - "stub"
  - "summarization"
---

Feel free to correct all this stuff.  Additions are also welcome.

**Study Questions**

- How do I keep an area route from reaching a router in that area?

You don’t.  That defeats the whole purpose of having the topology database on every router.  If you filtered one route from a router, there’s no way that SPF could calculate routes correctly.

- Fine, then.  Where do I filter routes?

You filter routes on an ABR or ASBR.  Since routers only have the whole topology for their area, it’s safe to filter routes from another area or from a redistributed routing protocol.  On a more technical note, you’re filtering type-3 LSAs on an ABR and type-5 LSAs on an ASBR.

- Show me an example of keeping the area 1’s route of 192.168.0.0/24 from hitting area 0.

R1(config)#ip prefix-list PL1 deny 192.168.0.0/24 R1(config)#ip prefix-list PL1 permit 0.0.0.0/0 le 32 R1(config)#router ospf 1 R1(config-router)#area 0 filter-list prefix PL1 in

- How about keeping a router from even learning about that same route from area 1?

R1(config)#router ospf 1 R1(config-router)#area 1 filter-list prefix PL1 out

- You know that that seems a little backwards, don’t you?

You have to think of filtering in terms of the area instead of in terms of the router.  You’re filtering into the area or out of the area…not into or out of the router.

- How do you keep the OSPF route to 192.168.0.0/24 from being submitted to the routing table?

I’ll use the same prefix list above. R1(config)#router ospf 1 R1(config-router)#distribute-list prefix PL1 in

- Isn’t that almost the same syntax to filter EIGRP routes?

Almost.

- How do I send area 1 the summary route of 192.168.0.0/16 from area 0?  That would be a type-3 LSA.

On the ABR:  R1(config-router)#area 1 range 192.168.0.0 255.255.0.0

How do I do the same thing for external routes (type-5 LSAs)?

On the ASBR:  R1(config-router)#summary-address 192.168.0.0 255.255.0.0

- If you see “totally” in the stub area description, what does that mean?

Someone at Cisco is a surfer.  It also means that there are no type-3 LSAs in that area.

- Is the term “stubby” an insult?

No.  It’s a term for an OSPF area that has certain types of LSAs filtered.  Summary routes are usually involved.  This is not filtering that we discussed above, though.  This is keeping all instances of an LSA type from entering an area.

- What the heck is a type-7 LSA?

If an NSSA has an external route it needs to flood, it uses a type-7 instead of a type-5.  This allows a router in a NSSA to advertise external routes without being bombarded by type-5s from other areas.

- What are the four types of stubby areas?  What LSA types do they filter?  What LSA types do they allow?

Stub – filters type-5s – allows type-3s Totally stubby – filters type-3s and type-5s NSSA – Filters type-5s – allows type-3s and type-7s Totally NSSA – Filters type-3s and type-5s – allows type-7s

- What area can never be a stubby?

Area 0, of course.

- If area 1 is a stub, what LSA types will area 0 see from it?

Type-3s.  The routes from area 1 are still advertised into area 0 as normal.

- How about if area 1 is a totally NSSA?

Type-3s and type-5s.  The routes from area 1 are still advertised into area 0 as normal, and the type-7s would be translated to type-5s.  \[Someone check me on this one.\]

- Where do you configure an area to be a stub?

On all the routers in the area.  The same goes for NSSA.

- Where do you configure an area to be a totally stubby?

The totally stubby part is configured on the ABR.  The other routers in the area should be configured as stub.  The same goes for totally NSSA.

- What route always shows up in a stubby or totally stubby area unless someone has done something weird?

0.0.0.0/0

- Speaking of the default route, how do you manually summarize the default route in OSPF?

You can use the _area 1 range 0.0.0.0 0.0.0.0_. You can also use the _default-information originate_ command in OSPF.

- What would you see on the internal routers if you had an ABSR that only had full BGP tables from your ISP configured with _default-information originate_?

You would see nothing.  You need to have a default route somewhere for the router to advertise into OSPF.  Since BGP full routes don’t contain a default, it won’t advertise.

**What Command Was That**

What command…

- …shows what type of stubby area an area is configured to be?

show ip ospf
