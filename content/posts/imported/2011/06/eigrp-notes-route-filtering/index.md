---
title: "EIGRP Notes - Route Filtering"
date: 2011-06-07
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ccie"
  - "eigrp"
  - "filtering"
  - "notes"
  - "route"
---

As always, correction are encouraged.

You can configure an EIGRP router to filter routes from being advertised or from being accepted.

Objective:  Filter out the route to 10.0.254.1/32 from being advertised to the rest of the network via EIGRP.

> ```
> ip prefix-list PRE1 deny 10.0.254.1/32
> ip prefix-list PRE1 permit 0.0.0.0/0 le 32
> !
> router eigrp 1
>  distribute-list prefix PRE1 out
> 
> -- OR --
> 
> ip access-list standard ACL1
>  deny 10.0.254.1 0.0.0.255
>  permit any
> !
> router eigrp 1
>  distribute-list ACL1 out
> ```
