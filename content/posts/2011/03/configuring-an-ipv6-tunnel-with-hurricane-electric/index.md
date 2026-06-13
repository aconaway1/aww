---
title: "Configuring an IPv6 Tunnel with Hurricane Electric"
date: 2011-03-31
categories: 
  - "cisco"
  - "ipv6"
tags: 
  - "broker"
  - "dns"
  - "eui-64"
  - "ipv6"
  - "ipv6ip"
  - "manual"
  - "tunnel"
---

[![](images/Hurricane-Earl_noaa-300x195-150x150.svg "Hurricane Earl_noaa-300x195")](http://aconaway.com/wp-content/uploads/2011/03/Hurricane-Earl_noaa-300x195.jpg)My ISP at home is great.  I have infinite bandwidth because they have no idea how to do any rate limiting.  Heck, they're not even skilled enough to know that I have several public IP addresses from their DHCP server.  That means, though, that they're not ready for IPv6.  They've ignored my emails and support tickets asking about their deployment strategy, so I gave up and looked at turning up a tunnel with a broker.  I chose [Hurricane Electric](http://www.tunnelbroker.net/) for no particular reason; they were just the first ones I found.  The setup was super-easy and works flawlessly.

When you add a new tunnel to your account, you are given a 64-bit IPv6 network to use at your local site and you have the option of asking for a 48-bit network as well.  I'm not planning on having more than one IPv6 subnet right now and the number of hosts don't quite reach 1.84467441 × 1019, so I opted to stick with the provided network.  HE also provides an IOS configlet for your end of the tunnel.  Here's the config I'm using sans the default route out Tunnel0.

> ```
> interface Tunnel0
>  description To Hurricane Electric IPv6
>  no ip address
>  ipv6 address 2001:470:1F0E:446::2/64
>  ipv6 traffic-filter ACL-TUN0 in
>  ipv6 inspect INSP-OUT out
>  tunnel source FastEthernet0/1
>  tunnel destination 216.218.224.42
>  tunnel mode ipv6ip
> end
> ```

We're talking IOS here.  I have an ASA 5505 on the head of my network, and, though it supports IPv6 routing (and filtering), it doesn't support the manual tunnel used to connect to HE.  I ended up picking an 1841 off of eBay to run parallel to my firewall.  There are other ways to connect the tunnel, though, and HE provides configurations for lots of platforms like Windows and Linux hosts; I'm a network guy, though...why not just install more network gear?

Don't get caught up in the warm glow of IPv6, though.  This is the open Internet just like when your grandmother plugs the new Macbook Pro you got her directly into the cable modem.  You will need to put in some filtering and inspection to protect yourself at the edge.  Though beyond scope today, take a look at the lines for _ipv6 inspect_ and _ipv6 traffic-filter_ for a starting point.

Send any native IPv6 support questions my way.
