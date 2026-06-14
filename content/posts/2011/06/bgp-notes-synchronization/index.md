---
title: "BGP Notes - Synchronization"
date: 2011-06-11
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "bgp"
  - "ccie"
  - "notes"
  - "synchronization"
  - "written"
---

- With synchronization on, route must be synchronized to an IGP in order for that routes to be able to be voted 'best" by BGP.
    - That means the exact route must already be in the routing table via an IGP.
    - Static routes don't count.
    - This is traditionally accomplished by redistributing BGP routes into an IGP.
    - With today's Internet prefix count over 350k, this may not be such a good idea in some situations.
    - Synchronization is off by default.
- Synchronization prevents black hole routes from being advertised via iBGP.
    - Unless every router is participating in iBGP, there's no guarantee that any one router will have a route to NEXT\_HOP.
- Synchronization also prevents a router from advertising the black hole to an eBGP neighbor.
    - You don't want to tell the world you have a path to a prefix when you really have a !N.
- Synchronization can be safely disabled with the use of [route reflectors](/posts/2011/06/bgp-notes-route-reflectors/) or [confederations](/posts/2011/06/bgp-notes-confederations/).

---

These are just some notes I've been taking.  Comment with corrections, please.
