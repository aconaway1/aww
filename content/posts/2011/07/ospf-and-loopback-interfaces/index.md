---
title: "OSPF and Loopback Interfaces"
date: 2011-07-31
categories: 
  - "ccie"
  - "route"
tags: 
  - "ipv6"
  - "loopback"
  - "ospf"
  - "ospfv2"
  - "ospfv3"
  - "route"
---

I was studying via Google+ Hangout the other day with [CJ](https://plus.google.com/111171425909122797357/posts) and [Rob](https://plus.google.com/108174404544807661420/about), and one of the topics that came up was that OSPFv2 advertises all loopbacks as 32-bit no matter what the configured mask is.  I rarely use loopbacks outside of a lab and had no idea it did that, so I set up a quick lab to see for myself.  Sure enough!  That's exactly what I saw.

Of course, being the inquisitive network guys that we are, we went on to discuss methods for making OSPF advertise the configured network instead of the single IP.  The guys mentioned two methods - to redistribute the connected interfaces and to manually set the OSPF network type on the loopback.  We were using IPv4 during the session, but I went back and added some IPv6 addresses and processes to compare.

**The Basics**

The whole lab consisted of R101 and R102 connected via their e0/0 interfaces; R101 also has a loopback interface as the guinea pig.  Here are the interesting lines of config on R101; I think you can figure out the configs on R102.

> ```
> interface Loopback0
>  ip address 172.16.0.1 255.255.255.0
>  ipv6 address 2001:DB8:101::1/64
>  ipv6 ospf 1 area 0
> !
> interface Ethernet0/0
>  ip address 10.0.0.101 255.255.255.0
>  ipv6 enable
>  ipv6 ospf 1 area 0
> !
> router ospf 1
>  log-adjacency-changes
>  network 10.0.0.0 0.0.0.255 area 0
>  network 172.16.0.0 0.0.0.255 area 0
> !
> ipv6 router ospf 1
>  router-id 192.0.2.101
>  log-adjacency-changes
> ```

So, what would I expect to see in the routing table on R102?  From our discussions, I would guess that 172.16.0.1/32 and 2001:db8:101::1/128 would show up.

> ```
> R102#sh ip route
> [ SNIP ]
> 172.16.0.0/32 is subnetted, 1 subnets
> O        172.16.0.1 [110/11] via 10.0.0.101, 00:00:03, Ethernet0/0
> R102#show ipv6 route
> [ SNIP ]
> O   2001:DB8:101::1/128 [110/10]
>      via FE80::A8BB:CCFF:FE00:6500, Ethernet0/0
> ```

Hey!  I'm right for once.  Of course, that's not really "right" (for definitions of the word). What if I have a service module on my router with an unnumbered IP address bound to the loopback interface. I'm thinking of something like a Unity Express (!).  You want to advertise the whole network or else you can't get to the module. Let's look at our two options to fix that.

**Redistribute Connected**

I removed the loopback interface from both OSPFv2 and OSPFv3 processes and redistributed the connected interfaces (don't forget the _subnets_ option in OSPFv2).  Here's what the routing tables on R102 look like now.

> ```
> R102#sh ip route
> [ SNIP ]
> 172.16.0.0/24 is subnetted, 1 subnets
> O E2     172.16.0.0 [110/20] via 10.0.0.101, 00:00:06, Ethernet0/0
> R102#sh ipv6 route
> [ SNIP ]
> OE2 2001:DB8:101::/64 [110/20]
>      via FE80::A8BB:CCFF:FE00:6500, Ethernet0/0
> ```

We can now see the network as an E2 route with a proper mask in both routing protocols.  I don't like this solution, though, because I have a serious obsessive-compulsive disorder that won't let me settle with having an internal route show up as external in OSPF.  Let's try the other solution and see if we have any better luck.

**OSPF Network-type**

For this part of the experiment, I removed the redistribute commands and added the looback interface back into the routing processes.   When I manually configured the loopback interface as an OSPF point-to-point network, the networks were advertised as the network without being external.  No more nervous ticks caused by my OCD!

> ```
> ! R101 config
> interface Loopback0
>  ip address 172.16.0.1 255.255.255.0
>  ip ospf network point-to-point
>  ipv6 address 2001:DB8:101::1/64
>  ipv6 ospf network point-to-point
>  ipv6 ospf 1 area 0
> end
> ```

> ```
> R102#sh ip route
> [ SNIP ]
> 172.16.0.0/24 is subnetted, 1 subnets
> O        172.16.0.0 [110/11] via 10.0.0.101, 00:01:04, Ethernet0/0
> R102#sh ipv6 route
> [ SNIP ]
> O   2001:DB8:101::/64 [110/11]
>      via FE80::A8BB:CCFF:FE00:6500, Ethernet0/0
> ```

Send any ~~type-2002 LSAs~~ questions my way.
