---
title: "Junos Basics - Configuring BGP"
date: 2012-08-01
categories: 
  - "junos"
tags: 
  - "bgp"
  - "ebgp"
  - "ibgp"
  - "juniper"
  - "junos"
---

I'm stuck deep in Junos these days.  I mean deep.  I have an F5 load balancer and an ASA 5520; the rest of my stuff is Juniper.  That means I have some learning to do.

Here's one of the basics in Junos - configuring BGP.  I guess I've always said that BGP is BGP.  How much different can it  be from IOS?  Well, the end result is the same, but it's different enough to have to look up how to do it.  :)  The first difference is the fact that all BGP configuration is done with groups just like peer groups in IOS.  You can act like you're configuring neighbors, but there's no way around using groups.  After going back and forth, I just settled with an group for eBGP neighbors and another for iBGP neighbors.  If settings are different, I just set them in the neighbor.  Here's an example of that.

> ```
> routing-options {
>     autonomous-system 65001;
> }
> protocols {
>     bgp {
>         group EBGP {
>             type external;
>             peer-as 65021;
>             neighbor 192.0.2.1;
>         }
>         group IBGP {
>             type internal;
>             neighbor 192.0.2.100;
>         }
>     }
> }
> ```

You noticed that your own ASN isn't configured in the BGP section, didn't you?  It's actually configured in the _routing-options_ configuration.  Also notice the _type_ directive there.  For some reason (can someone speak to why?), you declare a group as either _internal_ or _external_ neighbors_._  If the type is external, you obviously have to declare the peer's ASN.

This configuration won't do very much.  Actually, it pretty darn pointless.  All it does is peer up with the two neighbors and accept their routes.  We're not sending them anything or doing anything funky with their routes as they come in.  To do something cool, you'll need to look at seemingly endless configuration items.  Those are beyond scope here, though.

Did we configure BGP correctly?  Let's find out.

> ```
> root@ROUTER> show bgp summary
> Groups: 2 Peers: 2 Down peers: 0
> Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
> inet.0            494478     431927          0          0          0          0
> Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
> 192.0.2.1             65021 3819628      58226       0       3     1w2d21h 401542/415727/415727/0 0/0/0/0
> 192.0.2.100           65001 3554056    3457157       0       1      2w4d6h 30385/78751/78751/0  0/0/0/0
> ```

That's horrible output, but you can see that we have two neighbors.  You can also see their ASNs, how many routes we're getting from them, how many we're dampening, etc.  One cool thing to notice is the routing table that is being used.  We're not running [routing instances](http://www.juniper.net/techpubs/software/junos/junos94/swconfig-routing/routing-instances-configuration-guidelines.html) on this router, so we only see "inet.0" in the list.  That's the base routing table.  If we did indeed have BGP neighbors on a configured routing instance, you'd see it listed here as well.  One more thing to notice - the 431k active paths.  That's a lot of routes!

How do I know what I'm sending to my BGP neighbors?  Like I said, you're sending nothing here.  The default behavior of BGP in Junos is to not send anything; you'll have to configure a _policy-statement_ if you want to actually advertise something.  If you put in a little more config (again, beyond scope here), you can see something like this.  A single route for 199.199.199.0/24 coming from our external peer.  Success!

> ```
> root@ROUTER> show route advertising-protocol bgp 192.0.2.1
> 
> inet.0: 431634 destinations, 494173 routes (431634 active, 0 holddown, 0 hidden)
>   Prefix                  Nexthop              MED     Lclpref    AS path
> * 199.199.199.0/24         Self                                    I
> ```

That's good enough for now.  We'll have to fill in the gaps over time.

Send any canoe rental vouchers questions to me.
