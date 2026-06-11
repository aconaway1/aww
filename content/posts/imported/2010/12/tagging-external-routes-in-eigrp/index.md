---
title: "Tagging External Routes in EIGRP"
date: 2010-12-03
categories: 
  - "route"
tags: 
  - "bgp"
  - "egp"
  - "eigrp"
  - "external"
  - "igp"
  - "ospf"
  - "redistribution"
  - "route"
  - "routing"
  - "tag"
---

EIGRP allows you to tag external routes.  That is, any route redistributed into EIGRP can be tagged with a numeric descriptor from 0 to 4294967295.<!--more-->  These tags are carried throughout the EIGRP AS, so, with some planning and documentation, you can look at any route on any router and get an idea of what it's trying to do, where it came from, etc.  Also, tagging routes is a common way to make sure you're not redistributing the same routes over and over if you have multiple mutual redistribution points.

The config is quite easy and involves my favorite config item in all of Ciscodom - route-maps!  You create a route-map that sets the tag value and apply it to the redistribution.  There are a few ways to skin this cat, but I'll use an outbound distribute-list here.  Here's the config save the basics for getting EIGRP going.

> ```
> route-map TAGIT permit 100
>  set tag 2000
> !
> router eigrp 1
>  redistribute static
>  distribute-list route-map TAGIT out
> !
> ip route 172.16.0.0 255.255.255.0 Null0
> ```

If you do a _show ip route_ on one of the other routers [pokie machine games online](http://adaptfunrun.org/), you can see the tag that has been applied.  Check out the last line of the output.

> R1#sh ip route 172.16.0.1  
> Routing entry for 172.16.0.0/24  
>   Known via "eigrp 1", distance 170, metric 28160  
>   Tag 2000, type external  
>   Redistributing via eigrp 1  
>   Last update from 192.168.0.2 on FastEthernet0/0, 00:00:09 ago  
>   Routing Descriptor Blocks:  
>   \* 192.168.0.2, from 192.168.0.2, 00:00:09 ago, via FastEthernet0/0  
>       Route metric is 28160, traffic share count is 1  
>       Total delay is 100 microseconds, minimum bandwidth is 100000 Kbit  
>       Reliability 255/255, minimum MTU 1500 bytes  
>       Loading 1/255, Hops 1  
>       Route tag 2000

Remember that you can only tag routes external to the AS.  That means you can't have a router tag all the internal routes (which may be a cool thing to be able to do).  You can, however, tag any route that is redistributed - including another EIGRP AS. 

Tags are also used in OSPF and BGP.  Our MPLS provider actually tags the routes they distribute to us with their BGP AS number.

Send any infinite-hop routes questions my way.
