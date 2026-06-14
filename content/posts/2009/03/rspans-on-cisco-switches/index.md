---
title: "RSPANs on Cisco Switches"
date: 2009-03-18
tags: 
  - "cisco"
  - "remote"
  - "rspan"
  - "span"
  - "switch"
  - "vlan"
---

We [discussed SPANs](/posts/2009/03/spans-on-cisco-switches/ "AConaway.com -- SPANs on Cisco Switches") earlier, but let's talk about RSPANs for a bit.

Can anyone guess what the "R" means?  You guessed it -- "Remote".  An RSPAN is a way to get traffic from a SPAN source on one switch to a SPAN destination on another switch that's connected via a trunk.

The basic premise is that a special VLAN is created on all the switches and allowed to traverse the trunks.  You then set up a SPAN session that copies your traffic to this special VLAN.  This VLAN then gets the traffic to the other switches through some voodoo magic to be used as source for a SPAN on another switch.

Let's work through the steps.  In our example, we want to copy traffic from G2/18 on SwitchA to G3/38 on SwitchB.

First, on both switches, we need to create the new RSPAN VLAN.  We'll assume you've already got it set up to allow this VLAN over your trunks.

> ```
> vlan 2000
>  name RSPAN
>  remote-span
> ```

Notice the nice keyword _remote-span_.  This designates the VLAN to be used in an RSPAN.  Easy so far.

Now, let's create the session to copy traffic **to** the RSPAN.  The source port is G2/18, and the destination is the RSPAN VLAN.

> ```
> switchA(config)# monitor session 1 source interface Gi 2/18
> switchA(config)# monitor session 1 destination remote vlan 2000
> ```

Now the traffic is being copied to the RSPAN, so let's copy that traffic **from** the RSPAN to the sniffer.  In this case, the source is the RSPAN, and the destination is the sniffer's port.  Let's use session 8 to avoid confusion.

> ```
> switchB(config)# monitor session 8 source remote vlan 2000
> switchB(config)# monitor session 8 destination interface Gi 3/38
> ```

There are always things to look out for, aren't there?  The first that comes to mind is the fact that you're copying traffic from a port onto one or more trunks.  If the port is sending enough traffic and your trunk is close to capacity, you may wind up crushing the trunk link.  That would suck.

If you have a fully-meshed switch environment, you'll see the additional traffic across all your trunks if you're set up that way.  If you have four trunks that transport all VLANs, you may have four copies of the data coming out of the switch.  Let's say the box being monitored is compromised and sending out 600Mbps of data.  That means that every switch will have to deal with that much traffic.  This sounds to me like a CPU/memory issue waiting to happen.

Don't expect RSPANs to work on your 2950 like this.  On the lower-end switches, you have to use a reflector port to copy the traffic to the RSPAN.  I don't get into that here, but Google is your friend.

Send any ~~Cadbury Creme Eggs~~ questions my way.
