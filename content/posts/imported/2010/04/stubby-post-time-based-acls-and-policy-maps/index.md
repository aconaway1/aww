---
title: "Stubby Post - Time-based ACLs and Policy-maps"
date: 2010-04-28
categories: 
  - "cisco"
tags: 
  - "acl"
  - "class"
  - "map"
  - "policy"
  - "qos"
  - "service"
  - "time"
---

Certain divisions of the company tend to shoot themselves in the foot by kicking off large file transfers during business hours, so I had a thought that maybe we could use time-based ACLs to do some QoSing for those guys. I fired up GNS3 with a 3600 running 12.4(25b) with some virtual PCs on it's Ethernet interfaces.

> ```
> time-range BUSINESSHOURS
>  periodic daily 8:00 to 17:00
> !
> ip access-list extended PINGS
>  permit icmp any any time-range BUSINESSHOURS
> !
> class-map match-all PINGS
>  match access-group name PINGS
> !
> policy-map PM-F0/0-OUT
>  class PINGS
> ```

First, I set the router's time to outside of the time range and sent some pings over.

> ```
> R1#sh clock
> 00:01:13.107 UTC Wed Apr 28 2010
> R1#sh access-lists
> Extended IP access list PINGS
>     10 permit icmp any any time-range BUSINESSHOURS (inactive)
> R1#sh policy-map int f0/0
>  FastEthernet0/0
> 
>   Service-policy output: PM-F0/0-OUT
> 
>     Class-map: PINGS (match-all)
>       0 packets, 0 bytes
>       5 minute offered rate 0 bps
>       Match: access-group name PINGS
> 
>     Class-map: class-default (match-any)
>       11 packets, 1140 bytes
>       5 minute offered rate 0 bps, drop rate 0 bps
>       Match: any
> ```

Alright, that's expected. Now let's set the clock to within the time range and repeat.

> ```
> R1#sh clock
> 13:00:12.887 UTC Wed Apr 28 2010
> R1#sh access-lists
> Extended IP access list PINGS
>     10 permit icmp any any time-range BUSINESSHOURS (active) (10 matches)
> R1#sh policy-map int f0/0
>  FastEthernet0/0
> 
>   Service-policy output: PM-F0/0-OUT
> 
>     Class-map: PINGS (match-all)
>       10 packets, 980 bytes
>       5 minute offered rate 0 bps
>       Match: access-group name PINGS
> 
>     Class-map: class-default (match-any)
>       20 packets, 1970 bytes
>       5 minute offered rate 0 bps, drop rate 0 bps
>       Match: any
> ```

How about that?  Time-based ACLs seems to work with policy-maps.  I didn't know that.
