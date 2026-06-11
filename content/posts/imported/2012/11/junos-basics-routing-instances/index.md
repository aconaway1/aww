---
title: "Junos Basics - Routing Instances"
date: 2012-11-01
categories: 
  - "junos"
tags: 
  - "instance"
  - "juniper"
  - "junos"
  - "route"
  - "routing"
  - "table"
  - "vrf"
---

Here's one that I use every day at work. We have multiple customers coming into the same router, and, as luck would have it, they all use 192.168.1.0/24 (OK...not really but it might happen). That means we have to separate them into their own routing instance, or virtual router, so pass traffic to their firewall.  Think VRF lite on a Cisco router.  Let's conflagrate.

First, we configure the instance as a _virtual-router_.

> ```
> set routing-instances CUST1 instance-type virtual-router
> ```

There are a handful of instance types, and, to tell the truth, I've never cared to really look into them all.  Let's use the good ol' "beyond the scope of this document" excuse on that one so I look a little more prepared.

In practice, the _virtual-router_ type creates a new routing table to isolate traffic on the same router.  It's pretty worthless to just create it and not do anything with it, so let's take some of our interfaces and shove them into the new routing instance.

> ```
> set routing-instances CUST1 interface ge-0/0/0.100
> set routing-instances CUST1 interface ge-0/0/0.150
> set routing-instances CUST1 interface vlan.200
> ```

Not hard.  So, let's add some static routes and some OSPF config to make it even more functional.  With the base routing table, you just configure those under _routing-options_ and _protocols_.  It's the same here, but you just shove that config under the routing instance tree.  Something like this.

> ```
> set routing-instances CUST1 routing-options static route 192.168.0.0/16 \
>                                                      next-hop 10.1.100.1
> set routing-instances CUST1 protocols ospf export REDIST-INTO-OSPF
> set routing-instances CUST1 protocols ospf area 0.0.0.0 interface ge-0/0/0.100
> set routing-instances CUST1 protocols ospf area 0.0.0.0 interface vlan.200
> set routing-instances CUST1 protocols ospf area 0.0.0.150 interface ge-0/0/0.150
> ```

Now we have a new routing instance with 3 interfaces in it along with a static routes and OSPF.  Great.  Let's see what the routing table looks like now. A _show route_ does that job.

> ```
> inet.0: 6 destinations, 6 routes (3 active, 0 holddown, 3 hidden)
> + = Active Route, - = Last Active, * = Both
> ...SNIP...
> CUST1.inet.0: 15 destinations, 16 routes (16 active, 0 holddown, 0 hidden)
> + = Active Route, - = Last Active, * = Both
> 
> 192.168.1.0/24  *[OSPF/150] 1w5d 14:49:47, metric 0, tag 0
>                     > to 10.1.100.1 via ge-0/0/0.100
> ...SNIP...
> ```

Now the CUST1 table shows up.  Looks like we already have an OSPF route, too.  That turned out better than I thought.

With routing instances, you'll have to look at adding _instance_ or _routing-instance_ to your show commands to limit output to just a single instance.  For example, _show ospf neighbor_ _instance X_ and _show interfaces terse_ _routing-instance X_.  Contextual help for the win!

_NOTE:  I'm going to leave it at that, but you may have to add more to this config to make it work.  For example, on the SRX platform in flow-based processing mode (the default), you'll have to create security zones for each interface along with appropriate policies and host-inbound-traffic.  This is twice in one post that I'm claiming this is beyond the scope of this document.  :)_

Send any Halloween candy questions to me.
