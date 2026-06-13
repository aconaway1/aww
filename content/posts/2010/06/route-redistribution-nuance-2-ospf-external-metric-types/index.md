---
title: "ROUTE - Redistribution Nuance #2 - OSPF External Metric Types"
date: 2010-06-06
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "842-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eigrp"
  - "ospf"
  - "redistribution"
  - "route"
---

[Last time](http://aconaway.com/2010/05/24/route-redistribution-nuance-1/), we talked about a nifty little lab I set up for redistribution and how the OSPF ASBRs acted a little differently than I expected.  This time, let's look at how changing external OSPF routes to a metric-type of 1 (E1) affects the routing tables.

Here's the network again.

[![](images/redist21-300x138.svg "Redistribution")](http://aconaway.com/wp-content/uploads/2010/05/redist21.png)

The static routes are being redistributed into their respective IGPs, and EIGRP is being redistributed into OSPF.  Let's look at the routing table on R1.

> ```
> Gateway of last resort is not set
> 
>      10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
> O       10.0.0.2/32 [110/11] via 192.168.0.102, 00:06:53, Ethernet0/0
> O E2    10.0.0.3/32 [110/20] via 192.168.0.105, 00:06:53, Ethernet0/0
>                     [110/20] via 192.168.0.102, 00:06:53, Ethernet0/0
> S       10.10.10.0/24 is directly connected, Null0
> C       10.0.0.1/32 is directly connected, Loopback0
> O E2    10.0.0.4/32 [110/20] via 192.168.0.105, 00:06:53, Ethernet0/0
>                     [110/20] via 192.168.0.102, 00:06:53, Ethernet0/0
> O       10.0.0.5/32 [110/11] via 192.168.0.105, 00:06:53, Ethernet0/0
> O E2    10.10.20.0/24 [110/20] via 192.168.0.105, 00:06:03, Ethernet0/0
> C    192.168.0.0/24 is directly connected, Ethernet0/0
> O E2 192.168.101.0/24 [110/20] via 192.168.0.105, 00:06:53, Ethernet0/0
>                       [110/20] via 192.168.0.102, 00:06:53, Ethernet0/0
> ```

Notice that there are two routes to each of the networks discovered from EIGRP (the loopbacks of 10.0.0.3/32 and 10.0.0.4/32 as well as 192.168.101.0/24).  There is nothing strange here; OSPF simply sees the exit paths through the ASBRs.  How about if we change the metric-type on the routes from R2 and see what happens?

I know of at least two ways you can do it.  First, you can set the metric-type in the redistribute command on the ASBR's OSPF process.

> ```
> redistribute eigrp 1 subnets metric-type 1
> ```

You can also use a route-map to set the metric-type and apply that to the redistribute command.

> ```
> route-map TEST permit 10
>  set metric-type type-1
> !
> redistribute eigrp 1 route-map TEST subnets
> ```

Either way does the same thing.  Now let's check the route table on R1 again.

> ```
> Gateway of last resort is not set
> 
> 10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
> O       10.0.0.2/32 [110/11] via 192.168.0.102, 00:18:30, Ethernet0/0
> O E1    10.0.0.3/32 [110/30] via 192.168.0.102, 00:02:11, Ethernet0/0
> S       10.10.10.0/24 is directly connected, Null0
> C       10.0.0.1/32 is directly connected, Loopback0
> O E1    10.0.0.4/32 [110/30] via 192.168.0.102, 00:02:11, Ethernet0/0
> O       10.0.0.5/32 [110/11] via 192.168.0.105, 00:18:30, Ethernet0/0
> O E2    10.10.20.0/24 [110/20] via 192.168.0.105, 00:17:40, Ethernet0/0
> C    192.168.0.0/24 is directly connected, Ethernet0/0
> O E1 192.168.101.0/24 [110/30] via 192.168.0.102, 00:02:11, Ethernet0/0
> ```

Only one route this time, and it's the E1 route from R2.  It seems that E1 routes are more preferred than E2 routes.  Let's look at the OSPF database for 192.168.101.0/24 on R1 to see if we can figure that out.

> ```
> R1#sh ip ospf database external 192.168.101.0
> 
> OSPF Router with ID (10.0.0.1) (Process ID 1)
> 
> Type-5 AS External Link States
> 
> Routing Bit Set on this LSA
> LS age: 467
> Options: (No TOS-capability, DC)
> LS Type: AS External Link
> Link State ID: 192.168.101.0 (External Network Number )
> Advertising Router: 10.0.0.2
> LS Seq Number: 80000004
> Checksum: 0xEA58
> Length: 36
> Network Mask: /24
> Metric Type: 1 (Comparable directly to link state metric)
> TOS: 0
> Metric: 20
> Forward Address: 0.0.0.0
> External Route Tag: 0
> 
> Routing Bit Set on this LSA
> LS age: 1497
> Options: (No TOS-capability, DC)
> LS Type: AS External Link
> Link State ID: 192.168.101.0 (External Network Number )
> Advertising Router: 10.0.0.5
> LS Seq Number: 80000001
> Checksum: 0x6260
> Length: 36
> Network Mask: /24
> Metric Type: 2 (Larger than any link state path)
> TOS: 0
> Metric: 20
> Forward Address: 0.0.0.0
> External Route Tag: 0
> ```

You can see that everything is the same except for the metric-type field, which is exactly what we expect.  [By definition](http://www.cisco.com/en/US/tech/tk365/technologies_white_paper09186a0080094e9e.shtml#t33), if an external OSPF route is E1, the internal OSPF cost is added to the total cost of the route.  This is reflected in the "Comparable directly to link state metric" text next to the Metric Type value.  In contrast, an E2 route does not have the cost incremented; the cost is simply passed down the line as "Larger than any link state path".  This means that E1 routes are considered more accurate and should be more preferred than E2 routes.

Just another complexity of OSPF.  Thanks to [@matthewnorwood](http://twitter.com/matthewnorwood), [@jameskazin](http://twitter.com/jameskazin), [@steve](http://twitter.com/steve), [@wannabeccie](http://twitter.com/WannabeCCIE), [@ciscotophat](http://twitter.com/ciscotophat), and [@lbsources](http://twitter.com/LBSources) for the insight into the route differences.

Send any ~~twitter updates~~ questions my way.

Audio Commentary ![Audio: ROUTE - Redistribution Nuance #2](images/audio-unavailable.svg)
