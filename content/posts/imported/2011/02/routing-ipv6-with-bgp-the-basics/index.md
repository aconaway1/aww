---
title: "Routing IPv6 with BGP - The Basics"
date: 2011-02-10
categories: 
  - "cisco"
  - "route"
tags: 
  - "address-family"
  - "as"
  - "bgp"
  - "ipv6"
  - "mpbgp"
  - "multiprotocol"
  - "neighbor"
  - "remote-as"
  - "route"
  - "router-id"
  - "unicast"
---

Are you sensing a theme lately?  Since we covered the basics of the main IGPs (I'm an enterprise guy, so no IS-IS comments, please.), I thought I'd try to describe the basics of advertising IPv6 routes over BGP.  Yet again, we're not going to do any route manipulation or change any of the 948284928 BGP attributes.  We're just trying to get routes exchanged.

### Configuration

There's no new version of BGP for IPv6 here.  It's the standard BGP version 4 that we've all been using for years, but we're going to take advantage of the multiprotocol support (MPBGP, [RFC 2858](http://tools.ietf.org/html/rfc2858) [RFC 4760](http://tools.ietf.org/html/rfc4760)).  We'll get to the differences in a second, but the first thing to do is to set up the BGP process as normal.  

Just as with the IGPs covered so far, the router ID needs to be set somehow.  Let's just manually set it for now.  We'll be routing on behalf of AS 65001.

> Router(config)#router bgp 65001  
> Router(config-router)#bgp router-id 192.0.2.1

Since we're not using that archaic IPv4 any more, we need to disable IPv4 unicast, which is enabled by default when you configure MPBGP.

> Router(config-router)#no bgp default ipv4-unicast

All is good so far?  Now comes the part that's different from your standard BGPv4 deployment with your ISP.  To enable the protocol for IPv6, we need to use the _address-family_ directive.  This tells BGP which of the several protocols that you want to use in MPBGP.  We'll use IPv6, obviously; the others are well beyond our scope.

> Router(config-router)#address-family ipv6

This will take your to the address family config mode which is where you can configure your neighbors and network statements just like you did in more traditional configurations of BGP.  A difference here is that you have to activate the neighbor before the address family is enabled for it.  Luckily, that's so easy I won't even explain it; I'm confident you can figure out which command does this.  Heh.  Let's peer with the router at fc00::2 using AS 65002 and advertise fc00:1::/64 to it, shall we?

> Router(config-router-af)#neighbor fc00::2 remote-as 65002  
> Router(config-router-af)#neighbor fc00::2 activate  
> Router(config-router-af)#network fc00:1::/64

Piece of cake, right?

I've got to note that this is just one way to configure the IPv6 address family under MPBGP; there are other orders of command entry that can be used to get to the same config.  That's actually evident if you look at the config after your'e done.  

> ```
> router bgp 65001
>  bgp router-id 192.0.2.1
>  no bgp default ipv4-unicast
>  bgp log-neighbor-changes
>  neighbor FC00::2 remote-as 65002
>  !
>  address-family ipv6
>   neighbor FC00::2 activate
>   network FC00:1::/64
>  exit-address-family
> ```

You'll see that config items don't land in the order and place that you configured them.  The end results are the same.

### Checking Our Work

First, let's see if we have a neighbor adjacency.  We're all used to running _show ip bgp summary_, but we're not running IPv4.  The equivalent is _show bgp ipv6 unicast summary_.  The whole unicast/multicast thing is beyond both the scope of this article and of my comfortable knowledge.  :)

> ```
> Router#show bgp ipv6 unicast summary
> BGP router identifier 192.0.2.1, local AS number 65001
> BGP table version is 3, main routing table version 3
> 2 network entries using 304 bytes of memory
> 2 path entries using 152 bytes of memory
> 3/2 BGP path/bestpath attribute entries using 372 bytes of memory
> 1 BGP AS-PATH entries using 24 bytes of memory
> 0 BGP route-map cache entries using 0 bytes of memory
> 0 BGP filter-list cache entries using 0 bytes of memory
> Bitfield cache entries: current 1 (at peak 1) using 32 bytes of memory
> BGP using 884 total bytes of memory
> BGP activity 2/0 prefixes, 2/0 paths, scan interval 60 secs
> 
> Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
> FC00::2         4 65002      15      15        3    0    0 00:10:29        1
> Router#
> ```

We can see the neighbor is up and that we have a single prefix from that guy.  Let's check out exactly which route that is with the _show bgp ipv6 unicast neighbor fc00::2 routes_ command.  That was a mouthful, eh?

> ```
> Router#show bgp ipv6 unicast neighbors fc00::2 routes
> BGP table version is 3, local router ID is 192.0.2.1
> Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
>               r RIB-failure, S Stale
> Origin codes: i - IGP, e - EGP, ? - incomplete
> 
>    Network          Next Hop            Metric LocPrf Weight Path
> *> FC00:2::/64      FC00::2                  0             0 65002 i
> 
> Total number of prefixes 1
> Router#
> ```

It looks like we have a route to fc00:2::/64 through that neighbor.  That's a good thing since we're trying to exchange routes.  The next question is whether we are sending any routes to the neighbor.  Run _show bgp ipv6 unicast neighbor fc00::2 advertised-routes_ (even more of a mouthful!) to find out.

> ```
> Router#show bgp ipv6 unicast neighbors fc00::2 advertised-routes
> BGP table version is 3, local router ID is 192.0.2.1
> Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
>               r RIB-failure, S Stale
> Origin codes: i - IGP, e - EGP, ? - incomplete
> 
>    Network          Next Hop            Metric LocPrf Weight Path
> *> FC00:1::/64      ::                       0         32768 i
> 
> Total number of prefixes 1
> Router#
> ```

That looks right.  We're advertising the route to fc00:1::/64 just as we configured in our neighbor statement above.  We're on a roll!

Finally, let's check the routing table to make sure that route from the neighbor landed there.  Run _show ipv6 route_ like we've done with the IGPs, and you should see prefix as a "B" like in the old days of IPv4.

> ```
> Router#show ipv6 route
> IPv6 Routing Table - 6 entries
> Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
>        U - Per-user Static route, M - MIPv6
>        I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
>        O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
>        ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
>        D - EIGRP, EX - EIGRP external
> C   FC00::/64 [0/0]
>      via ::, FastEthernet0/0
> L   FC00::1/128 [0/0]
>      via ::, FastEthernet0/0
> C   FC00:1::/64 [0/0]
>      via ::, FastEthernet0/1
> L   FC00:1::1/128 [0/0]
>      via ::, FastEthernet0/1
> B   FC00:2::/64 [20/0]
>      via FE80::C001:1FF:FE18:0, FastEthernet0/0
> L   FF00::/8 [0/0]
>      via ::, Null0
> Router#
> ```

Well, there it is.  The only thing you may not have see is the concept of address families, but there's not much to that at all.  

Send any as-path prepends questions to me.
