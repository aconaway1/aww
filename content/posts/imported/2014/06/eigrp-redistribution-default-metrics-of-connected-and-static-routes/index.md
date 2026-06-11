---
title: "EIGRP Redistribution - Default Metrics of Connected and Static Routes"
date: 2014-06-19
categories: 
  - "ccnp"
  - "route"
tags: 
  - "connected"
  - "eigrp"
  - "redistribute"
  - "redistribution"
  - "route"
  - "static"
  - "topology"
---

I wanted to do some analysis of the EIGRP topology table last night, so I fired up a small lab. I was especially interested in how external routes appear there and compare to internal entries. Like all good scientific endeavors, the whole thing got derailed when I made a realization.

Here's the lab I set up. You can ignore the IPv6 info for this exercise.

[![eigrp1](images/eigrp1-300x198.png)](http://aconaway.com/wp-content/uploads/2014/06/eigrp1.png)

It's a simple little thing.  All the networks you see are included in EIGRP 100 for simplicity.  I limited the network statements to 192.0.2.0/24 to keep my options open. I went ahead and added Loopback100 on R3 with an address of 3.3.3.3/32 and added a _redistribute_ _connected_ with a route-map to get the route out in the wild.  Here's what I had.

> ```
> R3#show run | section eigrp
> router eigrp 100
>  redistribute connected route-map RM-REDIST-CONN
>  network 192.0.2.0
>  no auto-summary
> !
> route-map RM-REDIST-CONN permit 100
>  match interface Loopback100
> ```

Alright. All looks good there, so I checked the topology table on R1 and saw it in there as an external and everything.

> ```
> R1#sh ip eigrp topology 3.3.3.3/32
> IP-EIGRP (AS 100): Topology entry for 3.3.3.3/32
>   State is Passive, Query origin flag is 1, 1 Successor(s), FD is 409600
>   Routing Descriptor Blocks:
>   192.0.2.66 (FastEthernet0/1), from 192.0.2.66, Send flag is 0x0
>       Composite metric is (409600/128256), Route is External
>       Vector metric:
>         Minimum bandwidth is 10000 Kbit
>         Total delay is 6000 microseconds
>         Reliability is 255/255
>         Load is 1/255
>         Minimum MTU is 1500
>         Hop count is 1
>       External data:
>         Originating router is 3.3.3.3
>         AS number of route is 0
>         External protocol is Connected, external metric is 0
>         Administrator tag is 0 (0x00000000)
> ```

I got to thinking, though, that it probably shouldn't exist in the topology table on R1. One of the things we all learn about redistribution is that there is no mapping of metrics between protocols and that EIGRP is the pickiest of them all. At least that's how it echoes in my head.  So, where did these K values come from for this calculation? Usually you set them with a _default-metric_ or a _route-map_, but I'm not setting them anywhere.  I ventured further into the rabbit hole.

How about a static route?  How does a redistributed static show up in the topology table?  I added a static for 13.13.13.13/32 to interface Null 0, added _redistribute static_, and took a look.  Yep.  There it is with different K values.

> ```
> R1#sh ip eigrp topology 13.13.13.13/32
> IP-EIGRP (AS 100): Topology entry for 13.13.13.13/32
>   State is Passive, Query origin flag is 1, 1 Successor(s), FD is 281600
>   Routing Descriptor Blocks:
>   192.0.2.66 (FastEthernet0/1), from 192.0.2.66, Send flag is 0x0
>       Composite metric is (281600/256), Route is External
>       Vector metric:
>         Minimum bandwidth is 10000 Kbit
>         Total delay is 1000 microseconds
>         Reliability is 0/255
>         Load is 1/255
>         Minimum MTU is 1500
>         Hop count is 1
>       External data:
>         Originating router is 3.3.3.3
>         AS number of route is 0
>         External protocol is Static, external metric is 0
>         Administrator tag is 0 (0x00000000)
> ```

Where were they coming from!??!??!?!  I had some suspicions, and the leading candidate had to do with the fact that each route deals with an interface.  I posted a discussion on [Twitter](https://twitter.com/aconaway/status/479064824190468096) and the [Cisco Learning Network](https://learningnetwork.cisco.com/message/407420) to get some ideas, and [Lonnie](https://learningnetwork.cisco.com/people/lobarnes) showed that the values in the topology table come from the route's outgoing interface details.  That's exactly what was happening!  The connected was taking the characteristics of the loopback interface, and the static was taking the characteristics of the Null 0 interface.  Those were passed on to R1, who did the standard EIGRP thing of incrementing delay and comparing minimum bandwidths.

> ```
> R3#sh ip eigrp topology 3.3.3.3/32
> IP-EIGRP (AS 100): Topology entry for 3.3.3.3/32
>   State is Passive, Query origin flag is 1, 1 Successor(s), FD is 128256
>   Routing Descriptor Blocks:
>   0.0.0.0, from Rconnected, Send flag is 0x0
>       Composite metric is (128256/0), Route is External
>       Vector metric:
>         Minimum bandwidth is 10000000 Kbit
>         Total delay is 5000 microseconds
>         Reliability is 255/255
>         Load is 1/255
>         Minimum MTU is 1514
> ...
> R3#sh int lo100
> Loopback100 is up, line protocol is up
>   Hardware is Loopback
>   Internet address is 3.3.3.3/32
>   MTU 1514 bytes, BW 8000000 Kbit/sec, DLY 5000 usec,
>      reliability 255/255, txload 1/255, rxload 1/255
> ...
> R3#sh ip eigrp topology 13.13.13.13/32
> IP-EIGRP (AS 100): Topology entry for 13.13.13.13/32
>   State is Passive, Query origin flag is 1, 1 Successor(s), FD is 256
>   Routing Descriptor Blocks:
>   0.0.0.0, from Rstatic, Send flag is 0x0
>       Composite metric is (256/0), Route is External
>       Vector metric:
>         Minimum bandwidth is 10000000 Kbit
>         Total delay is 0 microseconds
>         Reliability is 0/255
>         Load is 0/255
>         Minimum MTU is 1500
>         Hop count is 0
> ...
> R3#sh int null0
> Null0 is up, line protocol is up
>   Hardware is Unknown
>   MTU 1500 bytes, BW 10000000 Kbit/sec, DLY 0 usec,
>      reliability 0/255, txload 0/255, rxload 0/255
> ```

_Note : There's a difference in BW between the Lo100 characteristics and the EIGRP topology table. I really can't find a reason for this discrepancy. Input welcome as always._

Let's go deeper still!  [Pete Lumbis had a similar suggestion](https://twitter.com/aconaway/status/479064824190468096) on Twitter.  He said that static routes pointing to an interface that's included in EIGRP (_i.e._, covered by a network statement) will have the K values for that interface automatically applied.  I added a route on R3 pointing to 23.23.23.23/32 through 192.0.2.200.  The next hop didn't exist in the network, but it did resolve to an interface participating in EIGRP.  Sure enough, it popped right into the topology table on R1.  Looks like Pete was right, too. Going deeper still, I removed the 192.0.2.192/26 network from EIGRP and checked topology again.  Since this interface included the next hop for the route to 23.23.23.23/32, I expected it to not be redistributed.  Wrong!  It's in there with the same metrics as before.

"Too many words, Aaron. What's the juicy details we need to remember?"  I guess it's that EIGRP will try to apply K values when it can, and the outgoing interface is one way to get it.  If you redistribute OSPF without a default metric, will it work?  Probably not, but, if the next hop is out of an interface that EIGRP already knows about, it probably will. \*insert "lab it out yourself" and "I can't do it all" comments\*

Send any study time wasted on pointless labs questions to me.
