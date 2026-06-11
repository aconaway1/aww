---
title: "Redistribution Notes - Tagging"
date: 2011-06-20
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ccie"
  - "notes"
  - "redistribution"
  - "route"
  - "tag"
  - "written"
---

- Tagging provides a way to mark common or similar routes to manipulate later.
- In redistribution scenarios with mutual redistribution on two different routers, any routes that gets redistributed from one route process to another are tagged.
    - When the other router sees those tags on the route, that route to keep from adding non-optimal routes to its routing table.
- Tags can also be used to do other manipulation such as setting higher metrics or changing ADs.

**OSPF**

> ```
> R102#show run
> ...
> router ospf 1
> log-adjacency-changes
> redistribute connected subnets route-map SETTAG
> network 192.0.2.0 0.0.0.255 area 0
> !
> route-map SETTAG permit 100
> set tag 55555
> ...
> 
> R101#sh ip route 10.0.0.2
> Routing entry for 10.0.0.0/24
> Known via "ospf 1", distance 110, metric 20
> Tag 55555, type extern 2, forward metric 10
> Last update from 192.0.2.102 on Ethernet0/0, 00:00:13 ago
> Routing Descriptor Blocks:
> * 192.0.2.102, from 192.0.2.102, 00:00:13 ago, via Ethernet0/0
> Route metric is 20, traffic share count is 1
> Route tag 55555
> ```

**EIGRP**

> ```
> R102#sh run
> ...
> router eigrp 1
> network 192.0.2.0
> redistribute connected route-map SETTAG
> no auto-summary
> !
> route-map SETTAG permit 100
> set tag 55555
> ...
> 
> R101#sh ip route 10.0.0.2
> Routing entry for 10.0.0.0/24
>   Known via "eigrp 1", distance 170, metric 409600
>   Tag 55555, type external
>   Redistributing via eigrp 1
>   Last update from 192.0.2.102 on Ethernet0/0, 00:00:14 ago
>   Routing Descriptor Blocks:
>   * 192.0.2.102, from 192.0.2.102, 00:00:14 ago, via Ethernet0/0
>       Route metric is 409600, traffic share count is 1
>       Total delay is 6000 microseconds, minimum bandwidth is 10000 Kbit
>       Reliability 255/255, minimum MTU 1500 bytes
>       Loading 1/255, Hops 1
>       Route tag 55555
> ```

\-- Corrections are encouraged.
