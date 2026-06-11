---
title: "Junos Basics - OSPF"
date: 2012-02-01
categories: 
  - "junos"
tags: 
  - "junos"
  - "ospf"
  - "security"
  - "srx"
  - "zone"
---

Oh, my.  Another Junos post.  Somebody stop me before I get my JNCIA!

This isn't hard stuff at all.  I'm sure there are a couple of cool tricks I don't know yet, but let's try anyway.  I"m working on an SRX240 here running 11.1 and some change.

Let's put interfaces ge-0/0/0.0 and lo0.0 in OSPF area 0. If you know the Junos configuration hierarchy, this will be very easy to you. Even if you don't, you can stare at the config for a little bit and see what we're doing.

> ```
> set protocols ospf area 0.0.0.0 interface ge-0/0/0.0
> set protocols ospf area 0.0.0.0 interface lo0.0
> ```

This is the only OSPF configuration you need, but guess what?  It won't work.  Since a Junos device is also a firewall, it will drop OSPF packets as they come into the interface; you have to declare that you do indeed want to accept OSPF packets.  You do this by creating a security zone, putting the right interfaces in the right zone, and then enabling OSPF on that zone.

We'll create a zone called INSIDE for our purposes here.  Note that there are about billion more steps (I counted) to fully configure your security zones, but that's way beyond our scope here.

> ```
> set security zones security-zone INSIDE
>      interfaces ge-0/0/0.0
> set security zones security-zone INSIDE
>      interfaces lo0.0
> set security zones security-zone INSIDE
>      host-inbound-traffic protocols ospf
> ```

You can also allow OSPF on specific interfaces like this. These commands will also put those interfaces in the right security zone.

> ```
> set security zones security-zone INSIDE
>      interfaces ge-0/0/0.0 host-inbound-traffic protocols ospf
> set security zones security-zone INSIDE
>      interfaces lo0.0 host-inbound-traffic protocols ospf
> ```

I'm not sure if you need to do this to lo0.0, but it won't hurt.

Now you can see your OSPF neighbors come up and start exchanging routing information.  That is, of course, assuming you did everything else right.

Send any blog deadlines questions my way.
