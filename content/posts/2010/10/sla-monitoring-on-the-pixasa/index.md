---
title: "SLA Monitoring on the PIX/ASA"
date: 2010-10-15
categories: 
  - "asa"
tags: 
  - "asa"
  - "icmp"
  - "ip-sla"
  - "monitor"
  - "ping"
  - "reliable"
  - "route"
  - "routing"
  - "sla"
  - "static"
---

We're working on an data center design for a customer, and they've dropped in two ISP links - each with it's own managed router and public IP space off one of the Ethernet interfaces.  The idea is that they want to use the Internet links in an active-passive setup without getting their own IP addresses to avoid running BGP with the ISPs.  To top it off, the headend of their control is an ASA cluster, so we wind up with two interface on the Internet to treat with a local security level.  Oh, the joys of doing network design.

Your first thought is probably to use the old fashioned floating static route where you have a weighted route that takes over if the primary route is withdrawn from the routing table.  That only works if the next-hop of that route is no longer available...like when serial interface goes down and the next-hop isn't directly connected any more.  This is Ethernet, though, so there's no way for the firewall doesn't know or doesn't care if a host on the network isn't there any more.  This config has another problem, too.  What about a scenario where the ISP's router is up, but it's interfaces are down?  How about if there are routing issues farther upstream?  You surely don't want to send traffic to a provider's router is the provider is having issues, right?  

If you've ever tried to do something similar on an IOS router, then you've probably done IP SLA.  An ASA has the same functionality, but it's just called SLA monitoring.  You wind up with a config that is a very similar to IP SLA stuff on IOS routers, actually.  I wrote [a terrible blog post](/posts/2008/04/reliable-static-routing/) about it a few years back, and several other bloggers talk about it as well, but the idea is that you have a process, called an SLA monitor on the ASA, that monitors an IP address by pinging it.  You then create a track object that watches the monitor's status.  This track object is applied to a static route, and, if the SLA monitor fails, the route is removed from the routing table.  We've all done something like this with HSRP tracking, so this shouldn't be totally foreign.

Let's take a look at the test network that I've used to simulate the setup at the customer site.

[![](images/asa-ip-sla1-258x300.svg "ASA IP SLA")](http://aconaway.com/wp-content/uploads/2010/10/asa-ip-sla1.png)

The test is to have INSIDE1 communicate with TARGET.  Each ISP knows where TARGET is through a huge EIGRP AS, but we want to detect any routing problems on ISP1.  If we find a problem, we want [Volume Pills](http://photoshopturk.org/) to roll over to ISP2 on the BACKUP interface.  What do we monitor, though?  We can monitor the IP of the ISP's router at the data center, but we'd miss any issues upstream.  Let's monitor the IP of the second router on ISP1, which is 10.0.0.2.  In the real world, we'd fine a host somewhere deep on the Intertubes that we think won't go down very often.  In our test, 10.0.0.2 is the closest thing we can find to that.

Let's create a beautiful symphony of ICMP generation.  First, we create the SLA monitor.

> sla monitor 1  
>  type echo protocol ipIcmpEcho 10.0.0.2 interface OUTSIDE  
> !  
> sla monitor schedule 1 life forever start-time now

I think you can see that we are creating an ICMP echo process that will ping 10.0.0.2 on the OUTSIDE interface.  The third line is what controls the start and stop of the process; in this case, we start now and don't ever finish thanks to the word _forever_.  We can't use the SLA monitor directly on our routes, so let's create a track object.

> track 100 rtr 1 reachability

Now we have track object 100 that looks to SLA monitor 1 for reachability.  We apply this to the route just like we do on IOS.  We'll go ahead and add the weighted route as well.

> route OUTSIDE 0.0.0.0 0.0.0.0 192.0.2.1 1 track 100  
> route BACKUP 0.0.0.0 0.0.0.0 192.0.2.129 240

Now the default will go through 192.0.2.1 until 10.0.0.2 is unreachable.  If that happens, the route is removed from the routing table, and the weighted default route will take over.  That's all you need.  Of course, I would create another track object for ISP2 so you can at least get a syslog message or SNMP trap if a problem happens over there, but you can probably get away with just the one.

If you've ever done IP SLA on a router, you would call me on the fact that there's some stuff missing.  If you don't force the ICMP packets to ISP1's router, the state of the SLA monitor will keep flopping; you flip to ISP2, the SLA check is healthy again, you flip back, the SLA check dies again...ad nauseum.  That's not the case for the ASA, actually.  Even though the default route has rolled over to the backup, the monitoring process continues to send requests to the old gateway.

Sometime I like it when my gear knows what I'm trying to do; this is one of those times.

Send any ~~stray ICMP packets~~ questions my way.

Audio Commentary  
![Audio: SLA Monitoring on the PIX-ASA](images/audio-unavailable.svg)
