---
title: "Stubby Post - show ip protocols"
date: 2010-06-10
tags: 
  - "642-902"
  - "bgp"
  - "certification"
  - "eigrp"
  - "ip"
  - "ospf"
  - "protocols"
  - "route"
  - "show"
  - "test"
---

I've seen and used the command before, but I've never really seen any use of the _show ip protocols_ command until tonight while reading up for my ROUTE test.  There's a lot of good information in the output, and, from the way the book is reading, this is a great candidate for use in a lab question.

To check it out a bit, I set up a small network with four routers connected only to a single Ethernet segment.  I set up one router to run EIGRP, OSPF, and BGP to each one of the other routers just so I could see the output for the different routing protocols.  Here's what puked out after struggling with GNS for a few minutes.

> ```
> R1#sh ip protocols
> Routing Protocol is "eigrp 1"
>   Outgoing update filter list for all interfaces is not set
>   Incoming update filter list for all interfaces is not set
>   Default networks flagged in outgoing updates
>   Default networks accepted from incoming updates
>   EIGRP metric weight K1=1, K2=0, K3=1, K4=0, K5=0
>   EIGRP maximum hopcount 100
>   EIGRP maximum metric variance 1
>   Redistributing: eigrp 1
>   EIGRP NSF-aware route hold timer is 240s
>   Automatic network summarization is in effect
>   Maximum path: 4
>   Routing for Networks:
>     192.168.0.0
>   Routing Information Sources:
>     Gateway         Distance      Last Update
>   Distance: internal 90 external 170
> 
> Routing Protocol is "ospf 1"
>   Outgoing update filter list for all interfaces is not set
>   Incoming update filter list for all interfaces is not set
>   Router ID 192.168.0.101
>   Number of areas in this router is 1. 1 normal 0 stub 0 nssa
>   Maximum path: 4
>   Routing for Networks:
>     192.168.0.0 0.0.0.255 area 0
>  Reference bandwidth unit is 100 mbps
>   Routing Information Sources:
>     Gateway         Distance      Last Update
>   Distance: (default is 110)
> 
> Routing Protocol is "bgp 65001"
>   Outgoing update filter list for all interfaces is not set
>   Incoming update filter list for all interfaces is not set
>   IGP synchronization is disabled
>   Automatic route summarization is disabled
>   Neighbor(s):
>     Address          FiltIn FiltOut DistIn DistOut Weight RouteMap
>     192.168.0.104
>   Maximum path: 1
>   Routing Information Sources:
>     Gateway         Distance      Last Update
>   Distance: external 20 internal 200 local 200
> ```

The EIGRP section shows some important details, including what k-values are used, networks configured, and administrative distance (AD) of the various route types (internal and external).  The OSPF section shows the router ID, number of areas on the router, and number of area types (normal, stub, NSSA), as well as the networks configured and the AD.  The section regarding BGP shows summarization status, neighbors (along with any filter lists, distribution lists, local weights, and route-maps if they were configured), and the ADs again.

That's good stuff to know.  I'll have to put that command in usual repertoire.
