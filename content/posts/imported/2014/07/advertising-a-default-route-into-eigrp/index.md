---
title: "Advertising a Default Route Into EIGRP"
date: 2014-07-06
categories: 
  - "ccnp"
  - "route"
tags: 
  - "default"
  - "eigrp"
  - "route"
---

Let's get an IPv4 default route into EIGRP.  There are a few methods to do it.  I hate most of them, though.  I think it will be obvious which one I like.

Here's the lab I have set up to test everything.  I want R4 to generate the default in each case.

[![topology](images/topology-300x176.png)](http://aconaway.com/wp-content/uploads/2014/07/topology.png)

**Default Network** - Candidate default.  I don't think I've ever used that all my years in networking, but here's how to use it in EIGRP for a default route.  You basically say "If you don't know where to send a packet, send it to where network _X_ lives."  We're going to set the 192.168.1.0/24 as the default network, so, in our case X = 192.168.1.0. R4 will tag that route as a default candidate when it advertises it to the rest of the network.  The config is easy but requires a classful (yes, classful) network to be configured as the default.

> ```
> R4 config:
> R4(config)#ip default-network 192.168.1.0
> !
> R1 routes:
> R1#sh ip route
> ...
>      4.0.0.0/24 is subnetted, 1 subnets
> D       4.4.4.0 [90/435200] via 192.0.2.3, 00:08:33, FastEthernet0/0
>                 [90/435200] via 192.0.2.2, 00:08:33, FastEthernet0/0
>      10.0.0.0/24 is subnetted, 2 subnets
> D       10.0.0.0 [90/409600] via 192.0.2.2, 01:01:08, FastEthernet0/0
> D       10.0.1.0 [90/307200] via 192.0.2.2, 01:01:16, FastEthernet0/0
> D*   192.168.1.0/24 [90/435200] via 192.0.2.3, 00:00:04, FastEthernet0/0
>                     [90/435200] via 192.0.2.2, 00:00:04, FastEthernet0/0
>      192.0.2.0/26 is subnetted, 2 subnets
> D       192.0.2.64 [90/307200] via 192.0.2.3, 01:01:15, FastEthernet0/0
> C       192.0.2.0 is directly connected, FastEthernet0/0
> ```

So, where's the route?  See that asterisk?  That's all you get.  But it works.

> ```
> R1#traceroute 100.100.100.100
> 
> Type escape sequence to abort.
> Tracing the route to 100.100.100.100
> 
>   1 192.0.2.3 64 msec
>     192.0.2.2 24 msec
>     192.0.2.3 8 msec
>   2 10.0.1.4 28 msec
>     192.0.2.66 40 msec
>     10.0.1.4 36 msec
>   3 192.0.2.66 !H
>     10.0.1.4 !H
>     192.0.2.66 !H
> R1#
> ```

The trace is not very clean thanks to having multiple routes to 192.168.1.0/24 in the routing table.  There's one through R2 and another through R3.

Yes, I'm a terribly negative person, so let's blast this method.  Where do you actually see the default route?  Where do you even see the gateway of last resort?  Dave's not here, man.  Let's hope your tier 1 support guys know what that asterisk means.

**Summary** - The default route is the biggest summary route you'll ever see, so why not have a route generate a summary address of 0.0.0.0/0?  That will work.  Summaries are configured on the interface, so the default will only be advertised out of that particular interface.

> ```
> R4 config:
> interface FastEthernet0/0
>  ip address 10.0.1.4 255.255.255.0
>  ip summary-address eigrp 100 0.0.0.0 0.0.0.0 5
> !
> R1 routes:
> R1#sh ip route | incl 0.0.0.0/0|last
> Gateway of last resort is 192.0.2.2 to network 0.0.0.0
> D*   0.0.0.0/0 [90/435200] via 192.0.2.2, 00:23:18, FastEthernet0/0
> ```

That works, but I don't think this is the best method.  The configuration, as-is, isn't highly-available.  If you lose the F0/0 interface on R4, then you don't have a default route any more.  The same if R2 catches on fire.  The fix for that, obviously, is to configure the same on F0/1 towards R3.  I absolutely abhor having to configure the same thing in two places.  I would never remember to change both if I had to make a config change.  I'll pass on this method.

**Network 0.0.0.0** - Yeah.  This one hurts.  You set a default route on R4 and then add _network 0.0.0.0_ to EIGRP.  That part is fine.

> ```
> R4 config:
> R4(config)#ip route 0.0.0.0 0.0.0.0 lo100
> R4(config)#router eigrp 100
> R4(config-router)#network 0.0.0.0
> !
> R1 routes:
> R1#sh ip route
> ...
> D*   0.0.0.0/0 [90/435200] via 192.0.2.3, 00:06:58, FastEthernet0/0
>                [90/435200] via 192.0.2.2, 00:06:58, FastEthernet0/0
> ```

Notice that the default route is an interface?  Yeah, that part sucks.  You have to use an interface instead of an IP address.  Welcome to a world without multiaccess networks.

**Redistributing Static** - This isn't very fancy, but it works.  You just redistribute your static routes, and a default route shows up on the network.  This is assuming, of course, that we have a static default on R4, right?

> ```
> R4 config:
> R4(config)#ip route 0.0.0.0 0.0.0.0 4.4.4.9
> !
> R4(config)#router eigrp 100
> R4(config-router)#redistribute static metric 10000 10 255 1 1500
> !
> R1 routes:
> R1#sh ip route
> ...
> D*EX 0.0.0.0/0 [170/309760] via 192.0.2.3, 00:00:05, FastEthernet0/0
>                [170/309760] via 192.0.2.2, 00:00:05, FastEthernet0/0
> ```

This works fine in my book.  It advertises the default out of interfaces like any other route, so it shows up on multiple paths.  It also is very obvious which is the default network.  I would, however, probably put a route map on the redistribution.  That way you can treat your default route differently than the rest of your statics.  That's up to you, though.

How about redistributing a default route from BGP instead of a static?  Sure.  From OSPF?  Sure.  Why not?

One thing to note is how the default routes appear in each method.  Using the _default network_ method doesn't actually show a route.  The _network_ and _summary_ methods show up as native EIGRP routes, and the _redistribute_ method, as expected, shows up as external.  This may be important when meeting certain design goals....or taking an exam.

Send any questions marks questions my way.
