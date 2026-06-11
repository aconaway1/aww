---
title: "BGP Notes - Path Attribute Categories"
date: 2011-06-08
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "attribute"
  - "bgp"
  - "ccie"
  - "notes"
  - "pa"
  - "path"
---

Make my corrections!  Please!

**Well-known mandatory** : These PAs must be recognized by all BGP routers and passed along to other peers.

**Well-known discretionary** : These PAs do not need to be in every _update_, but they must be recognized by all BGP routers.

**Optional transitive** : These PAs don't have to be recognized but they must be passed along to other BGP peers if they are present in an update.

**Optional notransitive** : These PAs neither have to be recognized nor passed along.
