---
title: "Stubby Post - Set DF to 0 with a Route-map"
date: 2010-08-20
categories: 
  - "cisco"
tags: 
  - "bit"
  - "df"
  - "policy"
  - "route-map"
  - "set"
---

We ran into an issue the other day where an application was setting the DF bit in IP packets to 1.  We thought it may be causing problems, so we looked at setting up a route-map to set the DF bit to 0.  It turned out to be a different application problem, but it was a good exercise in looking at what you can do with route-maps and policies.

I set up a lab in GNS3 to replicate and do some captures.  It's a really simple setup.  R1 connected to R2 connected to R3.

[![](images/setdf0-topology-300x41.png "Set DF 0 - Topology")](http://aconaway.com/wp-content/uploads/2010/08/setdf0-topology.png)

Beyond the routing, here's the config we put on R2.  You configure it just like PBR, but, instead of setting the next hop or interface, you just set the DF bit to 0.

> ```
> route-map E0/0-IN permit 10
>  set ip df 0
> !
> interface Ethernet0/0
>  description To R1
>  ip address 10.0.0.2 255.255.255.0
>  ip policy route-map E0/0-IN
> ```

Really simple.  We've all done this many times, but I've never cared enough about the DF bit to have to set it on a router.  That's usually what the applications does, and, to tell the truth, I imagine that setting it only shows up in 0.009% of networks.

To test the new policy, I did a packet capture between R1 and R2 and another between R2 and R3.  R1-R2 shows the DF bit set to 1 on a ping I did from R1 to R3.  For those that care, I did a _ping 10.0.1.3 repeat 100 df-bit_ to generate this traffic.

[![](images/setdf0-1-300x178.png "Set DF 0 - 1")](http://aconaway.com/wp-content/uploads/2010/08/setdf0-1.png)

The capture from R2-R3 shows the DF bit on the same packet set to 0.

[![](images/setdf0-0-300x178.png "Set DF 0 - 0")](http://aconaway.com/wp-content/uploads/2010/08/setdf0-0.png)
