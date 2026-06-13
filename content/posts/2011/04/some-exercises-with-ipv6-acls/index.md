---
title: "Some Exercises with IPv6 ACLs"
date: 2011-04-15
categories: 
  - "ipv6"
tags: 
  - "access"
  - "access-list"
  - "filter"
  - "interface"
  - "ipv6"
  - "list"
  - "matches"
  - "sequence"
  - "traffic"
  - "traffic-filter"
---

ACLs in IPv6 aren't that different from what you're used to dealing with in the IPv4 world.  You create a list of denies and permits for use with some other structure like filtering, PBR, and all sorts of other stuff.  Let's take a look at building an ACL and filtering traffic with it.

For those playing at home, here's the setup I used to generate the configs and get the output.  Execute some click action for the whole thing.

[![](images/screenshot-150x93.svg "IPv6 ACLs")](http://aconaway.com/wp-content/uploads/2011/04/screenshot.png)

The first thing you need to know is that all IPv6 ACLs are extended and named.  There's no concept of numbering and using standard list types that include the destination only.  This is a good thing in my opinion, and I've been doing that in my IPv4 ACLs for many years now.  This allows (forces?) you to use descriptive names and very specific entries.  Sometimes my entries are too specific, but that's usually because people don't include all the requirements.  I digress.

Creating an IPv6 ACL is so similar to the way you do it IPV4 that I don't even want to mention it.  I'll just give an example that we'll use in a second.  As usual, we're using IOS on Cisco devices.

> ```
> ipv6 access-list TRANSIT-ACL
>  permit tcp host 2001:DB8:0:1::2 host 2001:DB8::1 eq telnet
>  deny ipv6 any any log
> ```

No numbers or ACL types means the config is a lot simpler and cleaner.  You can see that TRANSIT-ACL is allowing telnet from 2001:db8:0:1::2 to 2001:db8::1 and denying everything else.  Of course, it's also logging the denies to  syslog so we know what's going on.  This shouldn't be foreign to you at all.  Note:  This is a lab, and we're just allowing telnet as a demonstration.  You should always yell at people who try to use telnet and show them how to use SSH.  Just sayin'.

On a tangent, I just realized that I actually typed _deny any any log_ in the last line, and the router took it to mean all IPv6.  Cool.

At some point, you'll want to see what kind of matches you're getting on the ACL.  You can do a _show ipv6 access-list_ or just a _show access-list_ to see them.  Of course, if you have any IPv4 ACLs configured, those will be included in the latter, bu the output of each is the same in relation to IPv6.

> ```
> R2#show access-lists
> IPv6 access list TRANSIT-ACL
>     permit tcp host 2001:DB8:0:1::2 host 2001:DB8::1 eq telnet (24 matches) sequence 10
>     deny ipv6 any any log (19 matches) sequence 20
> ```

Here you can see the entries of each ACL and see that this ACL has already been applied somewhere since it has hits.  The obvious difference between the output here and that from an IPv4 ACL is the sequence number.  In the IPv4 world, the sequence will come before the function (10 permit tcp ...).  Here, it comes afterwards.  I'm not yet sure if this is better or not.  I'll reserve judgement when I get some more experience with it.

If you've done ACLs a lot, you can probably tell that this ACL was meant for filtering traffic on an interface.  Let's apply it to F0/1 to do such.

> ```
> interface FastEthernet0/1
>  no ip address
>  ipv6 address 2001:DB8:0:1::1/64
>  ipv6 traffic-filter TRANSIT-ACL in
> ```

Make note that we use the _traffic-filter_ directive on the interface along with the ACL name and the direction.  Simple stuff.

There is a problem here, though.  If you remember your extensive IPv6 training, you know that we no longer have the concept of ARP to map layer-3 addresses to layer-2 addresses.  To find layer-2 neighbors, IPv6 devices use neighbor discovery (ND), which itself uses ICMPv6, to look for connected devices.  Since ICMPv6 is a layer-3 protocol like IP, when you apply this ACL as indicated, you'll not find any new neighbors on F0/1.  If a new router has a route to 2001:db8:0:1::1, there's no way to discover the layer-2 address, and I'll let you guess how that works out.  Not very well.  The fix is just to allow ICMPv6 into the interface; the details of that will run away very quickly, so I'll save it for later.

When one device sends ND packets, it uses it's link local address as the source and the multicast address of FF01::1 (the all routers group) as the destination.  You can see that in the log from before we fixed our neighbor problem.

> ```
> *Mar  1 01:10:07.735: %IPV6-6-ACCESSLOGDP: list TRANSIT-ACL/20 denied icmpv6 FE80::C002:15FF:FE58:0 -> FF02::1 (134/0), 2 packets
> R2#
> *Mar  1 01:15:07.739: %IPV6-6-ACCESSLOGDP: list TRANSIT-ACL/20 denied icmpv6 FE80::C002:15FF:FE58:0 -> FF02::1 (134/0), 2 packets
> R2#
> *Mar  1 01:21:07.735: %IPV6-6-ACCESSLOGDP: list TRANSIT-ACL/20 denied icmpv6 FE80::C002:15FF:FE58:0 -> FF02::1 (134/0), 1 packet
> ```

This look pretty standard, but  you can see that the message content includes the ACL name followed by the sequence number.  Now you can see exactly which entry is denying the traffic instead of having to go through the whole 8482482-line ACL to see what happened.  I'm digging that a lot.

Make sure you check out [Packetlife's post on IPv6 ACLs](http://packetlife.net/blog/2010/jun/30/ipv6-access-lists-acl-ios/) as well.  As always, there's good stuff going on there.

Send any Cadbury Creme Eggs questions to me.
