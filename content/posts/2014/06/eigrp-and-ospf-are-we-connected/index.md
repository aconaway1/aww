---
title: "EIGRP and OSPF - Are We Connected?"
date: 2014-06-27
categories: 
  - "ccnp"
tags: 
  - "eigrp"
  - "mask"
  - "neighbor"
  - "ospf"
  - "peer"
  - "subnet"
---

For both OSPF and EIGRP routers to become neighbors, their interface's primary IP address must be on the same subnet. That statement is true. There is a difference in the definition of "same subnet", though.

In OSPF, both routers have to be configured to be on the same subnet with the same mask or else they won't neighbor up.  When an hello packet is sent, the subnet mask is sent embedded in there.  The router does a quick look to be sure the subnets are defined the same way on both ends.  If everything doesn't match, they don't neighbor. Here's a [Wireshark](http://www.wireshark.org/) screenshot to show you the OSPF hello.  _Note: See edit below._

![OSPF-Header](images/OSPF-Header-300x238.svg)

In EIGRP,the subnet mask isn't sent in the hello packet, so that doesn't come into play.  Each router does a subnet calculation on the source address of the potential suitor, and, if that guy falls within the connected network, the peering magic happens.  Here's another Wireshark shot for you to enjoy.

![EIGRP-Header](images/EIGRP-Header-300x198.svg)

Send any ~~Wireshark certification vouchers~~ questions my way.

_Edit_:  I did some further research on Julius's comment about point-to-point links in OSPF.  It is absolutely true that point-to-point links do indeed ignore the subnet mask, and routers will become neighbors even with mismatched masks.  The same holds true for point-to-multipoint.  The other network types (non-broadcast and point-to-multipoint non-broadcast) don't support dynamic neighbor discovery, so there's nothing to test between those.  The moral of the story is that the mask is only evaluated on broadcast network types.
