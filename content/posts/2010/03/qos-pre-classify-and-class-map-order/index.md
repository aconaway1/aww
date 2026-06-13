---
title: "QoS Pre-classify and Class-map Order"
date: 2010-03-06
categories: 
  - "ont"
tags: 
  - "cisco"
  - "class"
  - "gre"
  - "ont"
  - "policy"
  - "pre-classify"
  - "qos"
  - "tunnel"
---

I’m still studying for the ONT test, so I did some labs tonight.  One of them was to demonstrate the **qos pre-classify** command for tunnel interfaces.  When you have a packet sent over a GRE tunnel, the ToS field gets copied to the GRE packet, but there’s no way to see the original packet’s higher-level headers on the way out the interface.  This can be a problem if your service policy needs to see protocol, port, IPs, etc.  The fix for that is to enable qos pre-classify on the tunnel interface and cyrpto map; doing so will provide a copy of the original packet to the physical interface to classify the packet thoroughly.

Here’s the lab.  I was testing from TestHost1 to TestHost2 and configuring R1 to do the pre-classification.  Both R1 and R2 are 3640s running IOS 12.4(25b) and have a GRE tunnel between them.

[![qospre1](images/qospre1_thumb1.svg "qospre1")](http://aconaway.com/wp-content/uploads/2010/03/qospre11.png)

I created a policy map that simply acknowledges the existence of ICMP packets; the router doesn’t do anything except match them in a class-map and smile at them on the way through.  Here’s the snippet.

> ```
> class-map ICMP
>  match protocol icmp
> !
> policy-map PM-S1/0-OUT
>  class ICMP
> !
> interface S1/0
>  service-policy output PM-S1/0-OUT
> !
> interface tunnel 0
>  qos pre-classify
> ```

Not much going on there.  We match ICMP using NBAR’s built-in protocols and do absolutely nothing.  I did a few pings and noticed that there were no matches to the ICMP class and that all the packets were classified as class-default .  I thought that the pre-classify wasn’t working, so I cursed for a while after making no progress at all.  I had no idea what was wrong.

> ```
> R1#sh policy-map int s1/0
>  Serial1/0
> 
>   Service-policy output: PM-S1/0-OUT
> 
>     Class-map: ICMP (match-any)
>       0 packets, 0 bytes
>       5 minute offered rate 0 bps
>       Match: protocol icmp
>         0 packets, 0 bytes
>         5 minute rate 0 bps
> 
>     Class-map: class-default (match-any)
>       467 packets, 39832 bytes
>       5 minute offered rate 0 bps, drop rate 0 bps
>       Match: any
> ```

_I need to stop here so people don’t get confused.  The configuration that you see is correct; the problem was actually with the NBAR protocols in the class-map.  As Jeremy Stretch notes at the bottom of_ [_this article_](http://www.packetlife.net/blog/2009/jun/17/qos-pre-classification/)_, there’s some issue with matching NBAR protocols.  I later used an extended ACL to match ICMP which worked.  The same is true for the SSH stuff later.  Back to the show._

Here’s what I wound up with after cursing a lot and making random configuration changes to get the blasted thing to work.  Notice the order of the classes.

> ```
> class-map match-any TUNNEL
>  match protocol gre
> !
> policy-map PM-S1/0-OUT
>  class TUNNEL
>  class ICMP
> ```

I know that order is going to be important, but these are matching two totally different things, so it shouldn’t matter, right?  I checked out the policy-map again and saw that every packet was matching the TUNNEL class-map, and none were matching the ICMP class-map.

> ```
> R1#sh policy-map int s1/0
>  Serial1/0
> 
>   Service-policy output: PM-S1/0-OUT
> 
>     Class-map: TUNNEL (match-any)
>       441 packets, 49392 bytes
>       5 minute offered rate 2000 bps
>       Match: protocol gre
>         441 packets, 49392 bytes
>         5 minute rate 2000 bps
> 
>     Class-map: ICMP (match-any)
>       0 packets, 0 bytes
>       5 minute offered rate 0 bps
>       Match: access-group name ICMP
>         0 packets, 0 bytes
>         5 minute rate 0 bps
> 
>     Class-map: class-default (match-any)
>       467 packets, 39832 bytes
>       5 minute offered rate 0 bps, drop rate 0 bps
>       Match: any
> ```

I finally went downstairs, talked it over with my wife who is my [rubber duck](http://etherealmind.com/network-dictionary-rubber-duck-debugging/), and finally figured it wouldn’t hurt to change the order of the classes.  Once I put ICMP before TUNNEL, it started matching.  I thought that was odd, so I left ICMP and added an SSH class and put it after the TUNNEL class.  I saw the ICMP match and the tunnel match, but I didn’t see a single match on SSH even though I was SSHed through (not to) the router. 

> ```
> R1#sh policy-map int s1/0
>  Serial1/0
> 
>   Service-policy output: PM-S1/0-OUT
> 
>     Class-map: ICMP (match-any)
>       252 packets, 28224 bytes
>       5 minute offered rate 0 bps
>       Match: access-group name ICMP
>         252 packets, 28224 bytes
>         5 minute rate 0 bps
> 
>     Class-map: TUNNEL (match-any)
>       5 packets, 440 bytes
>       5 minute offered rate 0 bps
>       Match: protocol gre
>         5 packets, 440 bytes
>         5 minute rate 0 bps
> 
>     Class-map: SSH (match-any)
>       0 packets, 0 bytes
>       5 minute offered rate 0 bps
>       Match: access-group name SSH
>         0 packets, 0 bytes
>         5 minute rate 0 bps
> 
>     Class-map: class-default (match-any)
>       547 packets, 46588 bytes
>       5 minute offered rate 0 bps, drop rate 0 bps
>       Match: any
> ```

When I moved SSH above TUNNEL, it started incrementing as it should.  The best that I can tell is that both the original packet and the GRE packet are being evaluated when pre-classification is enabled.  Since all the packets in the lab are going over a GRE tunnel, GRE will always be matched if you assess before everything else.

For the record, I did this lab twice – once with the GRE tunnel encrypted and once without encryption.  The results of the pre-classification were the same in both attempts; GRE matches every time.

Send any ~~ROUTE class vouchers~~ questions my way.
