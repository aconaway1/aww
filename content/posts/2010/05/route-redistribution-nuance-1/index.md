---
title: "ROUTE - Redistribution Nuance #1 - Admin Distance FTW"
date: 2010-05-24
categories: 
  - "cisco"
  - "route"
tags: 
  - "643-902"
  - "certification"
  - "cisco"
  - "eigrp"
  - "ospf-static"
  - "redistribution"
  - "route"
---

I just got back from Global Knowledge's ROUTE class, and I must say that it was a great class.  John Barnes puts on quite the show and is the best instructor I've ever had.  I digress, though.

One of the topics we covered was route redistribution, so I went back to the hotel one night and fired off this network in GNS3 to study a bit.

[![](images/redist21-300x138.svg "Redistribution")](http://aconaway.com/wp-content/uploads/2010/05/redist21.png)

The object was to see how redistributing statics into OSPF and into EIGRP differ.  It was also an opportunity to see how EIGRP redistributes into OSPF (and OSPF into EIGRP, but I didn't make it that far).  To do that, I redistributed 10.10.10.0/24 from R1 into OSPF and 10.10.20.0/24 from R4 into EIGRP.  I then had R2 and R5 redistribute all EIGRP routes into OSPF.  It's a nice mix, but I saw some weirdness in the paths to 10.10.20.0/24.

Here's the simple config from R4 that shows the redistribution. I'm just shoving the static routes into EIGRP without touching the K-values.

> ```
> ip route 10.10.20.0 255.255.255.0 Null0
> <SNIP>
> router eigrp 1
> redistribute static
> network 10.0.0.4 0.0.0.0
> network 192.168.101.0
> no auto-summary
> ```

Nothing out of the ordinary going on there.  I can see the route on R3 and R2 as a "D EX".

> ```
> D EX    10.10.20.0/24 [170/281600] via 192.168.101.104, 00:29:33, Ethernet0/0
> ```

It's not the case for R5, though.  Check this out.

> ```
> O E2    10.10.20.0/24 [110/20] via 192.168.0.102, 00:34:52, Ethernet0/0
> ```

That, my friend, is an external OSPF route (Type-5 LSA for those scoring at home) going through R2.  Why the heck does that happen?  The physical path is shorter through R4, but it really comes down to administrative distance.  EIGRP has an AD of 90, but external EIGRP routes (D EX) have an AD of 170.  Since OSPF routes have an AD of 110, the OSPF route wins.   Even though you may have a much "better" route through one protocol, simply having a route in another protocol with a lower AD will be enough to win.

I found another nuance with R1, as well, but that will have to wait until next time.

Send any Android Twitter clients questions my way.

Audio commentary: ![Audio: ROUTE - Redistribution Nuance #1](images/audio-unavailable.svg)

\---

So, why didn't R2 see the route via OSPF through R5?  I experimented a bit by reloading each router to see what would happen.  When I reload R2, R5 quickly picks up the EIGRP route, but, from there, things seem a little random to me.  Sometimes, R5 picks the OSPF route through R2 again.  Sometimes, R2 picks R5 as the best path and leaves R5 to route via EIGRP.  The same happens when I reload R5; sometimes the route goes one way and sometimes it goes the other.  Can one of you higher-ups shed any light on why this happens like it does?
