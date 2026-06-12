---
title: "Stubby Post - Path Cost of EtherChannels"
date: 2010-04-27
categories: 
  - "ccnp"
  - "switch"
tags: 
  - "645-813"
  - "cost"
  - "etherchannel"
  - "path"
  - "spanning-tree"
  - "stp"
  - "switch"
---

I was doing some STP labs tonight and found something that caught me off guard a bit.  I had been meddling with some EtherChannels between a pair of 3750s earlier today, and I forgot to reset the configs before starting on the STP stuff.  One my secondary root switch, I ran a _show spanning-tree vlan 1_ to see what status the ports were in, and I noticed the root path cost.

> ```
> VLAN0001
>   Spanning tree enabled protocol ieee
>   Root ID    Priority    24577
>              Address     001b.d4fa.bb00
>              Cost        12
> ```

This switch is directly connected to the root bridge via a pair of EtherChanneled FastEthernets, so I just assumed I'd get a cost of 19.  I surely didn't expect a cost of 12.  I added a third interface to the channel-group and wound up with this.

> ```
> VLAN0001
>   Spanning tree enabled protocol ieee
>   Root ID    Priority    24577
>              Address     001b.d4fa.bb00
>              Cost        9
> ```

Obviously there's some internal math going on with the EtherChannel and STP.  Guess what happens when I add a fourth link?

> ```
> VLAN0001
>   Spanning tree enabled protocol ieee
>   Root ID    Priority    24577
>              Address     001b.d4fa.bb00
>              Cost        8
> ```

It's interesting to see how the path cost changes in a way to seems disproportionate to the bandwidth.

Send any new math formulae comments this way.
