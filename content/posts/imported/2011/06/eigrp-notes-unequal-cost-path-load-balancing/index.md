---
title: "EIGRP Notes - Unequal Cost Path Load Balancing"
date: 2011-06-06
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "balancing"
  - "ccie"
  - "cost"
  - "eigrp"
  - "notes"
  - "path"
  - "unequal"
  - "variance"
---

Per the standard rules, please correct anything that's wrong.

One of EIGRP's big features is the ability to use unequal cost paths for load balancing.  This is done with the _variance_ command.

**variance** : A multiplier used to calculate which feasible successors can be used as active routes.  The router takes integer and multiplies it by the successor's feasible distance, and any FS with a an FD less than this new number gets submitted to the routing table manager.

> ```
> R1(config-router)#variance ?
>  <1-128>  Metric variance multiplier
> ```

**maximum-paths** : The maximum number of routes to submit to the route table manager.

> ```
> R1(config-router)#maximum-paths ?
>   <1-32>  Number of paths
> ```

**traffic-share balanced** : The router sends traffic based on a weighted values of the metric so that the lower metrics get more traffic.

> ```
> R1(config-router)#traffic-share balanced
> ```

**traffic-share min** : Only use the lowest metric route.  This seems kind of pointless to me.

> ```
> R1(config-router)#traffic-share min
> ```

**traffic-share min across-interfaces** : Use the lowest metric route but use routes out different interfaces if they exist.

> ```
> R1(config-router)#traffic-share min across-interfaces
> ```
