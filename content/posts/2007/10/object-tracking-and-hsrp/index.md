---
title: "Object Tracking and HSRP"
date: 2007-10-19
tags: 
  - "cisco"
  - "hsrp"
---

We've done some tracking with HSRP in other articles, but there are lots and lots of ways to use object tracking on an HSRP device. In our example network, we tracked the interface, and, if it went down, we decremented the standby priority. What if just the line protocol goes down? How about if the BGP peer on the other end stops sending you routes? If you don't know that object tracking is the answer, you didn't read the title.

In doing any type of object tracking, the first thing you is...wait for it...create the object. Let's do the line protocol object first.

> track 100 interface S0/0 line-protocol

This creates an object with the object number of 100 that tracks the line protocol of interface S0/0. Now what? If we look back to the [HSRP setup we have](/posts/2007/08/running-hsrp-for-availability/ "AConaway.com -- Running HSRP for Availability") two routers with HSRP running on each of the FastEthernets. If we add an interface S0/0 for Internet access (or corporate access or POS access or access to your toilet), we probably want to track the line protocol of those interfaces to make sure the interface is still healthy. Here's the new configuration on the FastEthernet interfaces.

> interface FastEthernet 0/0 ip address 10.1.1.11 255.255.255.0 standby 75 ip 10.1.1.1 standby 75 priority 100 standby 75 preempt standby 75 track 100 decrement 55

Now, when the line protocol of S0/0 goes down, the priority of standby group 75 goes down by 55. Sweet. How about if S0/0 is to an Internet circuit, and the BGP peer stops providing routes? It's just as easy to set up.

First, you need to find a route in your BGP table that's going to be stable. I like Google or Yahoo, but it doesn't really matter. Let's say the route you want is 1.2.0.0/20, so let's build the object.

> track 101 ip route ip route 1.2.0.0/20 reachability

We put this in the config, and we end up with this.

> interface FastEthernet 0/0 ip address 10.1.1.11 255.255.255.0 standby 75 ip 10.1.1.1 standby 75 priority 100 standby 75 preempt standby 75 track 101 decrement 55

If your router doesn't have the exact route in object 101, the priority of standby group 75 goes down by 55. Notice I said exact -- if you have a bigger or smaller route, it won't match. You knew that, though. And, yes, you can have more than one track statement in each standby group, so you can track the route and the line protocol at the same time if you want. Good stuff.

If you implement HSRP anywhere, you should probably do tracking of some kind. Check out Cisco's page on [Enhanced Object Tracking](http://www.cisco.com/univercd/cc/td/doc/product/software/ios122/122newft/122t/122t15/fthsrptk.htm "Cisco.com -- Enhanced Object Tracking") for a list of the tracking objects you can use.
