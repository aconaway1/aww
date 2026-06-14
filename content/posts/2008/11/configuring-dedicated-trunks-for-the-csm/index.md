---
title: "Configuring Dedicated Trunks for the CSM"
date: 2008-11-24
tags: 
  - "catalyst"
  - "cisco"
  - "csm"
  - "ios"
  - "lan"
  - "switching"
  - "trunking"
  - "vlans"
---

Did you catch the article on [setting up fault tolerance on the CSM](/posts/2008/10/configuring-fault-tolerance-on-the-csm/ "AConaway.com -- Configuring Fault Tolerance on the CSM")?  In that article, I mentioned that Cisco recommends a dedicated trunk for the FT VLAN if you have two HA CSMs in two chassis.  Discuss amongst yourselves while I drone on.

Why should you set up a dedicated trunk for this stuff?  The most obvious reason is to be sure that normal traffic doesn't step on the syncing traffic.  Since we're syncing state information as well as configuration, the frames need to arrive in a timely manner.  Any errors could potentially disrupt the FT process, which is bad.  You surely don't want the primary to fail only to find out that the standby doesn't have the complete or current config.

Another reason is to keep the syncing traffic from stepping on normal traffic.  The CSM is a pretty robust box and can handle a pretty good chunk of data.  If you had a 100Mbps trunk between your chassis, there is the potential for the link to get flooded if the CSM ever starts sending some real data.  All things being equal, though, your trunks are probably sized properly for your network, and the addition of the syncing traffic probably won't affect much.

Let's review our configuration from the other article.

> ```
> vlan 83
>  name CSM-Sync
> !
> module csm 3
>  ft group 1 vlan 83
>   priority 100 alt 90
>   preempt
> ```

This snippet creates VLAN 83 and tells the CSM to use it for syncing, but how do we dedicate a trunk for that VLAN?  We use the _switchport trunk allowed vlan_ directive.  We'll assume that G1/1 on your primary switch is connected to G1/1 on your standby.

> ```
> interface GigabitEthernet1/1
>  description CSM Syncing
>  switchport
>  switchport trunk encapsulation dot1q
>  switchport trunk allowed vlan 83
>  switchport mode trunk
> ```

This sets G1/1 up to only allow VLAN 83 across it.  If you do a _show int G1/1 trunk_, you'll see that this VLAN is the only one allowed, the only one active, and the only one one forwarding on that link.  Of course, you'll need to do the same on the other side to keep traffic flow sane, but it's fairly easy.

What if G1/1 goes down, though?  You'd lose sync, so you probably want to look at a solution for that little problem.  You could put in multiple links and let Spanning Tree do the work.  You could even turn those links into an EtherChannel for redundancy and throughput.  If you have more than two chassis, you could full mesh them with trunks dedicated to VLAN 83.  There are a number of ways around the problem.  Be creative.

Be sure to send turkey questions my way.
