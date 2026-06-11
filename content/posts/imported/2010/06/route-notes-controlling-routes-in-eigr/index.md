---
title: "ROUTE Notes - Controlling Routes in EIGRP"
date: 2010-06-17
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eigrp"
  - "filtering"
  - "route"
  - "summarization"
---

Corrections welcome.

**Study Questions**

- Why would you ever want to summarize routes?

Summarizing routes minimizes the routes advertised to the network.  For example, instead of advertising 192.168.0.0/24, 192.168.1.0/24…192.168.n.0/24, a router can advertise a single route to 192.168.0.0/16.  Keeping routing tables small saves hardware resources, minimizes convergence times, helps avoid route flapping, and makes the routing table easier to read for humans.

- When will an EIGRP router auto-summarize a route?

If a router has interfaces that that are in different classes of network (Class A, B, C), then that router will auto-summarize those routes up to the classful boundary.  For example, if you have a 10.0.0.1/24 and a 192.168.100.1/30, the router will advertise 10.0.0.0/8 and 192.168.100.0/24.

- You have two routers advertising the same summarized route.  How do you make one preferred over the other?

Adjust the delay or bandwidth so one is favored.

- What is suboptimal forwarding in regards to summarization?

A summary route could be advertised from a router that's not in the optimal path to the destination.  If that route is chosen by a downstream router, traffic is passed to that router instead of a more optimal path through another router.

- How do you avoid suboptimal forwarding in regards to summarization?

Disable summarization.  Advertising real networks will result in the optimal path being calculated.

- How do you manually summarize the route 192.168.100.0/22?

R1(config-if)#ip summary-address eigrp 1 192.168.100.0 255.255.252.0

- When will a summarized route stop being advertised by a router?

When the router no longer has any routes that fall inside the summary route, the summary is removed.  That is, if a router is advertising 192.168.100.0/22, the route will be removed if the router no longer has ANY routes that are in the 192.168.10\[0123\].0 networks.

- What’s the biggest route a router can summarize?

The default route.

- What are two ways to advertise a default route in EIGRP?

Advertise a static default route through _redistribute static_ Summarize _0/0_ out of an interface

- How do you keep one part of the network from having a route to another part of the network?

This is done with route filtering.

- When configuring route filtering in EIGRP, what's the big keyword?

_distribute-list_

- What are the three techniques for filtering routes?

ACLs Prefix lists Route-maps

- Do you mean to tell me that route filtering also uses ACLs and route-maps?

Yes.  Welcome to Cisco Systems.

- How do you use an ACL to filter out the route 192.168.0.0/24?

> ```
> access-list 1 deny 192.168.0.0 0.0.0.255
> access-list 1 permit any
> router eigrp 1
>  distribute-list 1 out
> ```

- How do you use a prefix list to filter out the route 192.168.0.0/24?

> ```
> ip prefix-list PL1 deny 192.168.0.0/24
> ip preffix-list PL1 permit 0.0.0.0/0 le 32
> router eigrp 1
>  distribute-list prefix PL1 out
> ```

- How do you use a route-map to filter out the route 192.168.0.0/24?

> ```
> access-list 1 permit 192.168.0.0 0.0.0.255
> route-map RM1 deny 10
>  match ip address 1
> route-map RM1 permit 999
> router eigrp 1
>  distribute-list route-map RM1 out
> ```

\-OR-

> ```
> ip prefix-list PL1 permit 192.168.0.0/24
> route-map RM1 deny 10
>  match ip address prefix-list PL1
> route-map RM1 permit 999
> router eigrp 1
>  distribute-list route-map RM1 out
> ```

**What Command Was That?**

What command…

- …shows you the prefix lists configured?

show ip prefix-list

- …shows the summary route being advertised for a particular network?

show ip route 192.168.0.0 255.255.0.0 longer-prefixes

- …shows what networks are being summarizes on a router?

show ip protocols

- …shows what route a router considers to be a default candidate?

show ip route (look for the \*)

- …shows the default network?

show ip route
