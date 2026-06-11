---
title: "Redistribution Notes - AD Manipulation"
date: 2011-06-22
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ad"
  - "ccie"
  - "manipulation"
  - "notes"
  - "redistribution"
  - "written"
---

- Manipulating administrative distance (AD) is another way to help with a mutual redistribution scenario.
- EIGRPs has different ADs for internal and external (redistributed) routes
- OSPF and RIP have the same AD no matter where the route orginated.
- This means that routes redistributed into OSPF may be used instead of a local RIP route.
    - AD 110 (OSPF) beats 120 (RIP) every time.
- The _distance_ subcommand allows you to change the AD on specific routes from specific neighbors.
- This example changes the AD of the route to 10.0.0.0/16 advertised from 1.1.1.1 to 121.
    - This will make this router prefer a RIP route to the same destination.

> ```
> ip access-list standard RIP-ROUTES
>  permit 10.0.0.0 0.255.255.0
> !
> router ospf 1
>  distance 121 1.1.1.1 0.0.0.0 RIP-ROUTES
> ```

– Corrections are encouraged.
