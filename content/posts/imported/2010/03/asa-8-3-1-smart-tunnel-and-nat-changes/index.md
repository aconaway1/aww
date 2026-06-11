---
title: "ASA 8.3.1 – Smart Tunnel and NAT Changes"
date: 2010-03-12
categories: 
  - "asa"
tags: 
  - "asa"
  - "cisco"
  - "firewall"
  - "security"
  - "smart-tunnel"
---

_I’ll start off with a warning.  I’ve been running 8.3.1 on my home 5505 for a few hours now.  Not only is this not really enough time for a thorough review, it’s also not the environment to test enterprise-level configurations.  There are also a lot of details missing that I just don’t know about yet, so please do some research on your own to figure out what’s going to break if you upgrade your ASA._

If you haven't heard, Cisco has released version 8.3.1 of their ASA operating system.  I'm excited about this for only one reason - Smart Tunnels with tunnel policies.

If you've never heard of Smart Tunnels, you're probably not alone.  I don't know why they're not more popular than they are, but I dig them.  A user connects to a URL, logs in, and a little applet loads on the machine that is used to proxy traffic through the ASA.  It doesn’t proxy all your traffic, though; only traffic from applications that you define are sent through the tunnel.  There is a huge problem that I can’t stand, though.  What if you need to SSH through the firewall and to your local LAN at the same time?  The smart tunnel applet doesn’t care or even know what you want to do; it tunnels all the traffic from the application.  Not good, eh?

The big change to this in 8.3.1 is the addition of tunnel policies to the smart tunnels.  According to [the release notes](http://www.cisco.com/en/US/docs/security/asa/asa83/release/notes/asarn83.html), you can now dictate which connections do and don’t go through the smart tunnel.  Now, I can configure the tunnel so that some traffic goes through the ASA to get to the production gear, but other traffic pukes out the NIC normally.  I know a lot of users who are going to like not having to log in and out all day.

_Note: I may do an article on smart tunnels once everything slows down a bit.  It’s a solid way to implement a clientless VPN that doesn’t require administrative access on the machine to run._

The big feature that everyone is talking about, though, is the change to the way NAT is done.  Back in the day (that means earlier this morning), if I wanted to configure a static NAT, I’d do something like this to create a static and a service NAT to two different boxes.

> ```
> firewall(config)#static (inside,outside) 192.0.2.1 192.168.1.100
> firewall(config)#static (inside,outside) tcp interface ssh 192.168.1.101 ssh
> ```

Now, you create an object and give that object all the attributes.  I think Cisco calls this auto-NAT.  I have no idea what the auto part means.  In our example, we would do something like this.

> ```
> firewall(config)#object network TESTHOST1
> firewall(config-network-object)#host 192.168.1.100
> firewall(config-network-object)#nat (inside,outside) static 192.0.2.1
> firewall(config)#object network TEST2
> firewall(config-network-object)#host 192.168.1.101
> firewall(config-network-object)#nat (inside,outside) static interface service tcp ssh ssh
> 
> ```

I would say that the configuration is easier to parse with your eyes if the ASA didn’t break up the configuration into two parts.  If you were to do a _show run_ and look for our configuration, you would have to look in two places.  The first part declares the object name and the host/subnet/IP range for which it’s associated.  The next part, which comes after the ACLs, declares the NAT stuff.

> ```
> object network TESTHOST1
>  host 192.168.1.100
> object network TESTHOST2
>  host 192.168.1.101
> 
> [SNIP a billion lines of ACL]
> 
> pager lines 24
> logging enable
> logging timestamp
> logging buffer-size 8192
> logging buffered informational
> logging asdm informational
> logging host inside x.x.x.x
> flow-export destination inside x.x.x.x 12345
> mtu outside 1500
> mtu guests 1500
> mtu inside 1500
> icmp unreachable rate-limit 1 burst-size 1
> icmp permit x.x.x.0 255.255.255.0 inside
> asdm image disk0:/asdm-631.bin
> asdm history enable
> arp timeout 14400
> 
> object network TESTHOST1
>  nat (inside,outside) static 192.0.2.1
> 
> 
> 
> object network TESTHOST2
>  nat (inside,outside) static interface service tcp ssh ssh
> ```

It may be simpler to configure, but it’s not simpler to figure out later.  I’d rather have single lines of _static_ statements; at least I can use regex on those efficiently.

There is a bright side to the new NAT thing, though.  Because the NAT statements are configured in the object, you can now reference the real IP of the host in ACLs instead of the NATted IP.  This will help those of us who use firewalls with 488249284 interfaces and that many NATs for each host.  If we wanted to allow access to the SSH host in the example, we would write an ACL that allows access to 192.168.1.101 instead of finding the NATted address on that interface and building the rules to that address.

Speaking of ACLs, you can actually create a global access-group.  Instead of creating an ACL with rules and an access-group to bind to an interface, you can build one single ACL and configure an access-group with the _global_ directive to basically apply that ACL to all interfaces.  A few quick tests show that you can have both interface and global access-group configured simultaneously and that interface ACLs will be executed first.  I need to do some more testing to figure out exactly how these work together.

Everyone should upgrade, right?  Nope.  I don’t ever upgrade to something cool just because it’s cool.  I also don’t like to have to buy more hardware to go up a minor revision.  Take a look at the the memory requirements for 8.3.1; every model up to the 5510 requires more than the base amount to upgrade.  I got lucky since my 5505 has 512MB in it already, but I would hate to have to justify quadrupling (!) the RAM in a 5540 just for some cool features.

Send any rotten tomatoes questions to me.

Sources:

- [8.3.1 Release Notes](http://www.cisco.com/en/US/docs/security/asa/asa83/release/notes/asarn83.html)
- [Smart Tunnels on 8.2](http://www.cisco.com/en/US/products/ps6120/products_configuration_example09186a0080affd2d.shtml)
- [NAT on 8.2](http://www.cisco.com/en/US/docs/security/asa/asa82/configuration/guide/nat_staticpat.html)
