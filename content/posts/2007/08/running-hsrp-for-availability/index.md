---
title: "Running HSRP for Availability"
date: 2007-08-22
tags: 
  - "cisco"
  - "hsrp"
---

In [the article describing a router-on-a-stick](http://aconaway.com/2007/08/20/router-on-a-stick/ "aconaway.com -- Router-on-a-stick"), I mentioned that I would use two routers that run HSRP for availability, so I figured that I would write up a short post on what it is and how it works.

HSRP (Hot Standby Router Protocol) is a Cisco-proprietary protocol for establishing two or more layer-3 devices as a fault-tolerant gateway. Please note that it is not a _routing_ protocol like OSPF or BGP. HSRP provides availability and fault-tolerance...it does not advertise routes. I actually found several Google results that said it was a routing protocol. Those were on the first page of the results, so be careful when searching! Webopedia.com is terrible.

I'm sure you would like to know how it works, so let's walk through the process. Each router (we'll just assume its a router, but you can run HSPR on any Cisco layer-3 device) is configured with a _standby group_, _priority_, and _standby address_. Each advertises its configuration to the others, and, after everyone knows what the other routers' settings are, each looks at the list of priorities and figures out which one is the highest. If a router thinks that it has the highest priority, it becomes the _active router_ and will start answering for the standby address. If a router doesn't think it has the highest priority, it becomes the _standby router_ and just chills. Every few seconds, everyone sends _hello packets_ to let everyone know that they're still alive, and, if the active router doesn't answer in a certain amount of time, another internal election occurs, and the router with the highest priority becomes the new active router. This whole process takes less than 10 seconds and is automatic. As long as at least one router is configured for the standby group, the standby ip is available.

That was awfully technical, so let's look at an example. Here's another terrible diagram to show what I'm talking about. I can't afford Visio. :( Anyway, both routers have their FastEthernet0/0 on the same network, and we want to configure them as HSRP pairs.

![HSRP Diagram](images/HSRP.svg "HSRP Diagram")

Let's do the configuring. We'll use standby group 75 for our configuration. It's just a number so you can use multiple HSPR configurations on the same interface, so it doesn't really matter. Router 1 and Router 2 have IP addresses of 10.1.1.11 and 10.1.1.12, respectively. We'll use 10.1.1.1 as the standby IP. We'll also say that the priority of Router 1 should be higher just so we can get an example going.

Router 1

> interface FastEthernet 0/0 ip address 10.1.1.11 255.255.255.0 standby 75 ip 10.1.1.1 standby 75 priority 100 standby 75 preempt

Router 2

> interface FastEthernet 0/0 ip address 10.1.1.12 255.255.255.0 standby 75 ip 10.1.1.1 standby 75 priority 50 standby 75 preempt

The only thing I haven't noted yet is the _preempt_ command. This tells the router that it can take over the standby IP if its priority says so. Everything else is pretty straightforward and should work like a champ. If you're using a router-on-a-stick setup, you configure the sub-interfaces instead of the physical interfaces (like F0/0.1 instead of F0/0).

Have fun and let me know if you have any questions.

A note as usual: These are just the basics of HSRP. It can do all sorts of stuff like [interface tracking](http://www.cisco.com/warp/public/619/hsrpguidetoc.html#intracking "HSRP interface tracking"), [object tracking](http://www.cisco.com/univercd/cc/td/doc/product/software/ios122/122newft/122t/122t15/fthsrptk.htm#wp1146585 "HSRP object tracking"), [load sharing](http://www.cisco.com/warp/public/619/7.html "HSRP load sharing") (it's a workaround, really), and [authentication](http://www.cisco.com/en/US/tech/tk648/tk362/technologies_tech_note09186a0080094a91.shtml#authentication "HSRP authentication").
