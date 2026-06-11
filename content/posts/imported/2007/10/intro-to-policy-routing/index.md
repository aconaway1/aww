---
title: "Intro to Policy Routing"
date: 2007-10-13
tags: 
  - "cisco"
---

I like \[tag\]layer-3\[/tag\] \[tag\]switch\[/tag\]es. They give some great flexibility and bang-for-the buck, but most people overlook one issue with these things that can cause security problems. Most people configure the \[tag\]VLAN\[/tag\]s, put an IP on the VLAN interfaces, and put it in production, but the packets don't actually flow the way they think they do.

Let's check an example. Here's what the proverbial you had in mind when you plugged your web server, management server, and firewall into your 3750.

![AConaway.com Layer-3 Diagram](images/11511_1192242797510.png "AConaway.com Layer-3 Diagram")

This works, but most of the time, I don't think they really want your web servers to pass traffic directly to your management servers. Yes, your web server can send packets to your management server all day long without the firewall even knowing what's going on. And so can anyone who compromises your web server.

Your web server has a default route pointing to interface VLAN101 on your switch. Any packet is sent that way, and the switch accepts the packet and does a route lookup for it. You've got a static route on the switch for the management network pointing to the firewall, so it should go there, right? Wrong. The switch actually ignores your static route since it has a better one -- a connected route via interface VLAN201. Remember that both routers on the diagram are the same device! [Connected routes take precedence over static routes](http://www.cisco.com/warp/public/105/21.html "Cisco.com -- Route Selection in Cisco Routers"), so packets always go directly to the management server. \[tag\]Policy\[/tag\] \[tag\]routing\[/tag\] is the only way that you can override the routing table.

So, what do we do? We first make an ACL that matches the traffic we want to policy. We then create a route-map that matches the ACL and sets the next-hop address. Finally, we apply the route-map to the properly interface. Let's say that, like in the diagram, your web server has an IP of 192.168.0.51/24, and the management server has an IP of 192.168.1.88/24. We want to send traffic from 192.168.0.0/24 to 192.168.1.0/24 through the firewall (172.16.0.84/24).

First, let's build an \[tag\]access list\[/tag\]. Anything that matches the \[tag\]ACL\[/tag\] (that is, anything that is permitted) will be considered interesting to us.

> access-list 101 permit ip 192.168.0.0 0.0.0.255 192.168.1.0 0.0.0.255

Easy. Now let's build the \[tag\]route-map\[/tag\]. We want every packet that matches ACL 101 to be sent to 172.16.0.84, so let's try this:

> route-map WEB-to-MGMT permit 10 match ip address 101 set ip next-hop 172.16.0.84
> 
> route-map WEB-to-MGMT permit 20 set interface Null 0

What did that do? Nothing so far, but it will do something in a minute. The priority 10 set says that any packet that matches ACL 101 goes to 172.16.0.84 -- the firewall. Sweet. The priority 20 set says that everything else is dropped. One last step -- let's apply the policy to the interface.

> interface VLAN101 ip policy route-map WEB-to-MGMT

Now we're cooking. When a packet lands on interface VLAN101 that matches ACL101, it's sent to the firewall instead of being directly routed to the management network.   The policy is only applied to packets that come _into_ the interface, so packets that go out won't be affected.  Make sure you do it to the traffic coming back the other way.

Let me know if you have any problems. Policy routing can get tricky sometimes.
