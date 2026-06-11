---
title: "NAT on a PIX/ASA"
date: 2008-03-13
tags: 
  - "acl"
  - "asa"
  - "firewall"
  - "nat"
  - "pix"
---

NATting sucks and can be confusing. I'm sure everyone agrees to that, but you have to use it at some times. In a PIX/ASA, it's easy to configure a simple setup, but can be super-complicated in larger networks. In a simple lab, we have set up an ASA with inside and outside interfaces, with the inside as your internal and outside as the Internet.

The NAT setup here is easy.

> nat (inside) 1 0.0.0.0 0.0.0.0 global (outside) 1 interface

This NATs everyone on the inside (0.0.0.0 with a mask of 0.0.0.0, or 0/0) to the IP of the outside interface (overload in the IOS world). The _nat_ command says who gets NATted; the _global_ command says what they get NATted to. Notice the number "1" in both commands; this is the NAT group and allows you to have some flexibility in your NAT strategy. In essence, if you match a _nat_ line with a "1" in it, you'll be matched to a "1" on the _global_ list.

What if you add a DMZ interface and don't want to NAT when your inside network talks to it? That, my friend, is a little more complicated. We'll assume your internal network is 10.0.0.0/24 and your DMZ is 192.168.0.0/24.

> access-list NONAT permit ip 10.0.0.0 255.255.255.0 192.168.0.0 255.255.255.0
> 
> nat (inside) 0 access-list NONAT nat (inside) 1 0.0.0.0 0.0.0.0 nat (dmz) 1 0.0.0.0 0.0.0.0
> 
> global (outside) 1 interface

That was painful, but what did it do? That's a very good question.

We have multiple _nat_ lines on the inside, so the firewall starts at the top and works its way down (there are exceptions). The first _nat_ line has a group of 0, which is very special. If you match group 0, you are not NATted at all, and your connection is passed as-is with no changes. In our second example, you match if the ACL matches, so, if you're going from the inside network to the DMZ, you won't be NATted. If your connection didn't match this line (like you're downloading porn from the Internet), the firewall goes to the next line, which says to NAT everyone to group 1 just as we did in the first example.

Another twist here is the "nat (dmz) 1 0.0.0.0 0.0.0.0" line. This says that anything from the DMZ is NATted to group 1 just as the inside traffic is.

So, if the inside network connects to the DMZ, it doesn't get NATted. If the inside goes to the Internet, it gets NATted to the outside IP of the firewall. If the DMZ connects to the Internet, it gets NATted to the outside IP as well, but what if the DMZ connects to the inside? That's another story. :)
