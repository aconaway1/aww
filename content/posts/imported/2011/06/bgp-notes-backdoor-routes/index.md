---
title: "BGP Notes - Backdoor Routes"
date: 2011-06-11
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "backdoor"
  - "bgp"
  - "notes"
  - "routes"
---

- The fact that eBGP has an AD of 20 can be a problem.
    - You may have a very short path via EIGRP (or OSPF or RIP or whatever other IGP), but the longer eBGP path will be preferred.
- For God's sake, do not lower the AD of EIGRP!  Havoc will ensue.
- Using backdoor routes causes eBGP routes to have an AD of 200.
    - Allows the shorter-path IGP routes to be added to the routing table.

> ```
> router bgp 123
>  network 1.1.1.0 backdoor
> ```

\-----

Comment with corrections, please
