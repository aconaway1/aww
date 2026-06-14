---
title: "EIGRP for IPv6 - The Basics"
date: 2011-01-31
categories: 
  - "route"
tags: 
  - "config"
  - "eigrp"
  - "example"
  - "ipv6"
---

I'm not going to [go all out](http://packetlife.net/blog/2010/dec/13/blog-examples-going-ipv6-next-year/) like Jeremy over at Packetlife.net has, but I'm going to start to discuss a few IPv6 topics.  In time (like [in September when APNIC runs out of IPv4 addresses](http://www.potaroo.net/tools/ipv4/)), I'm sure I'll ramp up the IPv6 talk, but let's start easy and get EIGRP for IPv6 up and running.  

### Configuration

There are quite a few differences between EIGRP for IPv6 (yes, that's an official name) and the IPv4 version.  First of all, all IPv6 routing is disabled by default on a Cisco router, so, if you're doing any routing in IPv6, you'll want to enable it or risk smashing your head into the desk trying to figure out what's going on.

> Router(config)#ipv6 unicast-routing

Next, let's get to configuring EIGRP for IPv6.  By default, IPv6 routing protocols (all of them?) are EIGRP for IPv6 is shut down like Ethernet interfaces, so we'll have to enable it first.  

> Router(config)#ipv6 router eigrp 100  
> Router(config-rtr)#no shutdown

There's also the issue of the router ID.  In IPv4, EIGRP has [an method to figure out its router ID](/posts/2010/06/route-notes-eigrp-neighbor-relationships/), and EIGRP for IPv6 uses that same method.  The problem is that the router ID is still a 32-bit number, but there aren't any 32-bit address on the router if you're pure IPv6.  A dilemma, eh?  There are two way to get around this, though.  First, you can set a loopback interface with an IPv4 address so that EIGRP will have an address to use.

> Router(config)#interface lo0  
> Router(config-if)#ip address 192.0.2.1 255.255.255.255

You can also statically assign a router ID to EIGRP for IPv6.

> Router(config)#ipv6 router eigrp 100  
> Router(config-rtr)#router-id 192.0.2.1

Either method gets the same result.  Of course, you should be careful that all routers have a unique ID.

So now we need to add some _network_ statements, right?  Actually, there are no _network_ statements in EIGRP for IPv6.  The interfaces themselves are where you configure the networks to be included in the routing protocol.  It's kinda like the way you can [use the interfaces to configure OSPFv2.](http://www.cisco.com/en/US/docs/ios/12_0s/feature/guide/ospfarea.html)

> Router(config)#interface f0/0  
> Router(config-if)#ipv6 eigrp 100

Of course, we're assuming you already have an IPv6 address on f0/0.

### Checking our Work

Let's check to see if everything is working the way we think it should be.  First of all, let's make sure all our interfaces are participating as expected with the _show ipv6 eigrp interface_ command.

> ```
> Router#sh ipv6 eigrp interfaces
> IPv6-EIGRP interfaces for process 100
> 
>                         Xmit Queue   Mean   Pacing Time   Multicast    Pending
> Interface        Peers  Un/Reliable  SRTT   Un/Reliable   Flow Timer   Routes
> Fa0/0              0        0/0         0       0/1            0           0
> Fa0/1              1        0/0        23       0/2           50           0
> ```

This output looks a lot like the IPv6 version, and we can see both f0/0 and 0/1 are participating.  That looks right, so let's check for EIGRP neighbors with the _show ipv6 eigrp neighbor_.  I've got another router off of f0/1, so we should see a neighbor adjacency.

> ```
> Router#sh ipv6 eigrp neighbors
> IPv6-EIGRP neighbors for process 100
> H   Address                 Interface       Hold Uptime   SRTT   RTO  Q  Seq
>                                             (sec)         (ms)       Cnt Num
> 0   Link-local address:     Fa0/1             13 00:02:40   23   200  0  11
>     FE80::C001:14FF:FEB0:1
> ```

What the heck is FE80::C001:14FF:FEB0:1??!?!?!  That's not a network we configured!  That's actually the link-local address of the other router off of f0/1.  Perhaps I'll discuss IPv6 addressing some day (when I have a firmer grasp on it), but, for now, I'll just say that it's a special address for hosts to talk to one another on a local network.

Finally, let's check for routes.

> ```
> R1#show ipv6 route eigrp
> IPv6 Routing Table - 6 entries
> ...
> D   2002::/64 [90/307200]
>      via FE80::C001:14FF:FEB0:1, FastEthernet0/1
> ```

Just like in IPv4, EIGRP for IPv6 routes show up with the route code of "D".  It's looks like we have one route to the 2002::/64 network.  Everything seems to be working!

There are obviously a lot of more features and functions to EIGRP for IPv6, but this should get you started in your studies.  I'm sure I'll expound as time and my CCIE studies progress.

Send any ~~6to4 tunnels~~ questions my way.
