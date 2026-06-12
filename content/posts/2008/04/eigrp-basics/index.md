---
title: "EIGRP Basics"
date: 2008-04-11
tags: 
  - "eigrp"
  - "routing"
---

I realized the other day that I haven't mentioned EIGRP once. As a Cisco guy, I think I'm required to do at least one article on it, so here it goes.

Enhanced Interior Gateway Routing Protocol (EIGRP) is a Cisco-proprietary routing protocol. Routing protocols share routes, right, but "interior" is the keyword here; it's used to distribute routes on your internal network (Contrast that with BGP, which is allows you to share your routes with others). In a nutshell, each router in the EIGRP cloud tells everyone what subnets it has connected to him.  A receiving router then combines that information with everything that it already knows and passes on any new information.  Do that recursively for a while, and, eventually, every routers knows all the subnets in the network.

When you configure EIGRP, you have to tell it some stuff to get it moving.  First, you have to configure a AS number, which is very similar to the AS number is BGP.  When two routers talk EIGRP, they always include their AS numbers, so, if one router receives an update for an AS it isn't configured for, it ignores it.  Besides the AS, the only other thing you need to configure is what networks you want advertised via that EIGRP AS.  It's simple and easy, but there are thousands of combinations of commands that you can use.

Basic config time!  The scenario is that you have two routers, R0 and R1, that share a LAN segment 192.168.0.0/24 off their F0/0 interfaces.   R0 has the network 192.168.100.0/24 off F0/1, and R1 has 192.168.101.0/24 off F0/1.  You want both routers to know where every subnet on the network is, and we'll use AS 1 for simplicity.

On R0:

> router eigrp 1 network 192.168.0.0 network 192.168.100.0

Every interface to falls into these network ranges will now participate in EIGRP AS 1.  If you had another interface on another subnet, that interface would not participate.  On the flip side, if your F0/0 as 192.168.0.0/25 and F0/1 was 192.168.0.128/25, then you would only need the one _network_ line to cover both interfaces.  Notice that there's no subnet or wildcard mask in these lines.  EIGRP is very classful, so it summarizes routes to network boundaries before inserting the more-specific route from EIGRP, so keep an eye out for that to be sure it doesn't bite you.

On R1:

> router eigrp 1 network 192.168.0.0 network 192.168.101.0

Almost immediately, you should see some lines talking about new adjacencies or neighbor changes.

If you do a _show ip route_ on each router, you should see some routes from EIGRP.  Here's the output from R0.

> C    192.168.0.0/24 is directly connected, FastEthernet0/0 C    192.168.100.0/24 is directly connected, FastEthernet0/1 D    192.168.101.0/24 \[90/30720\] via 192.168.0.2, 00:00:47, FastEthernet0/0

The big ol' _D_ at the beginning of the line tells you that this is an EIGRP route, so you know it's working.

Check out [one of Cisco's pages](http://www.cisco.com/warp/public/103/1.html "Cisco.com -- Introduction to EIGRP") for more info.
