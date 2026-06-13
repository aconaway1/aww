---
title: "Junos - Logical Tunnel Interfaces with Virtual Routers"
date: 2013-03-02
categories: 
  - "junos"
tags: 
  - "interface"
  - "junos"
  - "logical"
  - "router"
  - "tunnel"
  - "virtual"
  - "vr"
---

There are a few ways to leak routes in and out of virtual routers in Junos. On the list is a cool feature called the logical tunnel interface.

So, what am I talking about?  One way to separate traffic on a router is to use virtual routers (VRs) so that you wind up with multiple routing tables on the same router.  This separate traffic, but you will usually (read: always) have a demand to get traffic from one VR to another.  There are a few different way to do that (see rib-group, instance-import, next-table, et al.), but one really cool way to do it is through logical tunnel interfaces.

The logical tunnel (lt-0/0/0) interface is a special little guy that allows you to connect its units to each other.  The result is similar to connecting an Ethernet cable from one physical interface to another. With a little configuration, these guys provide a point-to-point interface that you can include in your routing setup.  Let's look at an example.

> ```
> set interfaces lt-0/0/0 unit 100 encapsulation ethernet
> set interfaces lt-0/0/0 unit 100 peer-unit 200
> set interfaces lt-0/0/0 unit 100 family inet address 192.168.0.100/24
> 
> set interfaces lt-0/0/0 unit 200 encapsulation ethernet
> set interfaces lt-0/0/0 unit 200 peer-unit 100
> set interfaces lt-0/0/0 unit 200 family inet address 192.168.0.200/24
> ```

The encapsulation lines are pretty straightforward. In this case, I want the link to appear as an Ethernet interface, but you can choose frame-relay, vlan, bridging, and others.

The peer unit lines tell what unit is connected to what other unit. Each lt-0/0/0 unit is a point-to-point link, so you have to tell the router what's on the other end of the link (sorry...no multiaccess here). In this case, I want unit 100 to connect to unit 200, so I configure both units with the appropriate peer unit.

Of course, we can all figure out that we're using IPv4 on these new units as well. In this case, I've put both interfaces on the 192.168.0.0/24 network (how original!).

Now that we have our interfaces configured, we need to put these interfaces into the correct VR. Don't forget the security zone, too, if you're running in flow mode. That's beyond the scope here, though.

> ```
> set routing-instances VR100 instance-type virtual-router
> set routing-instances VR100 interface lt-0/0/0.100
> set routing-instances VR100 interface lo0.100
> 
> set routing-instances VR200 instance-type virtual-router
> set routing-instances VR200 interface lt-0/0/0.200
> set routing-instances VR200 interface lo0.200
> ```

I put lo0.100 and lo0.200 in there to have something to advertise. You'll see that in a second.

Now, let's see if we can ping across.

> ```
> root@TestSRX# run ping 192.168.0.100 routing-instance VR200
> PING 192.168.0.100 (192.168.0.100): 56 data bytes
> 64 bytes from 192.168.0.100: icmp_seq=0 ttl=64 time=1.879 ms
> 64 bytes from 192.168.0.100: icmp_seq=1 ttl=64 time=3.480 ms
> <SNIP>
> ```

Woot! It works. Now we can treat these new interaces as if they are regular ole Ethernet. Since I'm not ready to try and blog about IS-IS, let's just use the standard OSPF. I'm not going to go through the steps to configure OSPF, but here's the routing table after all the interfaces are included.

> ```
> root@TestSRX# run show route                  
> 
> VR100.inet.0: 5 destinations, 5 routes (5 active, 0 holddown, 0 hidden)
> + = Active Route, - = Last Active, * = Both
> 
> 10.0.0.100/32      *[Direct/0] 00:41:33
>                     > via lo0.100
> 10.0.0.200/32      *[OSPF/10] 00:00:28, metric 1
>                     > to 192.168.0.200 via lt-0/0/0.100
> 192.168.0.0/24     *[Direct/0] 00:38:21
>                     > via lt-0/0/0.100
> 192.168.0.100/32   *[Local/0] 00:38:21
>                       Local via lt-0/0/0.100
> 224.0.0.5/32       *[OSPF/10] 00:01:18, metric 1
>                       MultiRecv
> 
> VR200.inet.0: 5 destinations, 5 routes (5 active, 0 holddown, 0 hidden)
> + = Active Route, - = Last Active, * = Both
> 
> 10.0.0.100/32      *[OSPF/10] 00:00:28, metric 1
>                     > to 192.168.0.100 via lt-0/0/0.200
> 10.0.0.200/32      *[Direct/0] 00:41:33
>                     > via lo0.200
> 192.168.0.0/24     *[Direct/0] 00:38:21
>                     > via lt-0/0/0.200  
> 192.168.0.200/32   *[Local/0] 00:38:21
>                       Local via lt-0/0/0.200
> 224.0.0.5/32       *[OSPF/10] 00:01:18, metric 1
>                       MultiRecv
> ```

Look! OSPF routes! Sweet. Just to keep my OCD at bay, let's ping from the loopback of one VR to the loopback of the other.

> ```
> root@TestSRX# ping source 10.0.0.100 routing-instance VR100 10.0.0.200
> PING 10.0.0.200 (10.0.0.200): 56 data bytes
> 64 bytes from 10.0.0.200: icmp_seq=0 ttl=64 time=1.463 ms
> 64 bytes from 10.0.0.200: icmp_seq=1 ttl=64 time=1.443 ms
> <SNIP>
> ```

Well, look at that.  It works again.

Let's look back at the topic we're discussing, though.  If we use OSPF between the VRs, we need to make sure our routing design allows us to filter routes between the VRs; the risk is that you may wind up having all the routes from each VR advertised to the other.  Kind of defeats the purpose, eh?  Running BGP between the VRs might be an option that allows you to control what routes go in and out.  Statics might be the answer, as well.  As long as you can filter the advertisements, you wind up with a pretty elegant solution for sharing routes between VRs.

Send any ~~Marshmallow Peeps~~ questions my way.
