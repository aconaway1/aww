---
title: "Stubby Post - UplinkFast"
date: 2010-04-28
categories: 
  - "ccnp"
  - "switch"
tags: 
  - "645-813"
  - "ccnp"
  - "cisco"
  - "spanning-tree"
  - "stp"
  - "switch"
  - "uplinkfast"
---

I've got a few switches daisy chained together with single links and have enabled UplinkFast on them.  This switch is not the root bridge; F0/24 is the root port and F0/23 is a blocked alternate port. I've got _debug spanning-tree uplinkfast_ on to help out.

> ```
> SW3#sh span | incl 0/2[34]
> Fa0/23           Altn BLK 3019      128.23   P2p
> Fa0/24           Root FWD 3019      128.24   P2p
> ```

Now let's unplug F0/24 and see what happens.

> ```
> 19:05:05: STP FAST: UPLINKFAST: make_forwarding on VLAN0001 FastEthernet0/23 roo
> t port id new: 128.23 prev: 128.24
> 
> 19:05:05: %SPANTREE_FAST-7-PORT_FWD_UPLINK: VLAN0001 FastEthernet0/23 moved to Forwarding (UplinkFast).
> 19:05:05: STP: UFAST: removing prev root port Fa0/24 VLAN0001 port-id 8018
> SW3#
> 19:05:06: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to down
> SW3#
> 19:05:07: %LINK-3-UPDOWN: Interface FastEthernet0/24, changed state to down
> ```

Before the switch even reports that F0/24 is down, F0/23 is brought into the forwarding state. Now let's plug F0/24 back in.

> ```
> 19:07:16: %LINK-3-UPDOWN: Interface FastEthernet0/24, changed state to up
> SW3#
> 19:07:17: STP FAST: make_forwarding: via UPLINKFAST: NOT: port FastEthernet0/23
> VLAN0001 is: uplink enabled new root FastEthernet0/23 (me)prev root exists(8018/) cur state forwarding role uplink
> 19:07:17: STP FAST: make_forwarding: via UPLINKFAST: NOT: port FastEthernet0/24
> VLAN0001 is: uplink enabled new root FastEthernet0/23 (not me)prev root exists(8018/) cur state blocking role looped
> 19:07:18: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to up
> SW3#
> 19:07:18: STP FAST: make_forwarding: via UPLINKFAST: NOT: port FastEthernet0/23
> VLAN0001 is: uplink enabled new root FastEthernet0/23 (me)prev root exists(8018/) cur state forwarding role uplink
> SW3#sh span | incl 0/2[34]
> Fa0/23           Root FWD 3019      128.23   P2p
> Fa0/24           Altn BLK 3019      128.24   P2p
> ```

Notice that the port comes back up, but it isn't returned as the root port immediately. It should be, though, right? The original STP convergence said that it was the closest to the root bridge, so it makes sense that it should be the root port again, right? Since the port just came up, STP still has to make sure there's no loop, so it has to step through all the states like any good port does. If we wait a few more seconds, we see this.

> ```
> 19:07:53: STP FAST: UPLINKFAST: make_forwarding on VLAN0001 FastEthernet0/24 root port id new: 128.24 prev: 128.23
> 
> 19:07:53: %SPANTREE_FAST-7-PORT_FWD_UPLINK: VLAN0001 FastEthernet0/24 moved to Forwarding (UplinkFast).
> 
> SW3#sh span | incl 0/2[34]
> Fa0/23           Altn BLK 3019      128.23   P2p
> Fa0/24           Root FWD 3019      128.24   P2p
> ```

Now we're back to where we were originally. The moral of the story is that UplinkFast already knew the status of both ports, so it could quickly move the blocked port to fowarding when the port failed. Traditional STP would have to send a TCN message to the root bridge, which would then forward them out with the rest of the switches so they can reconverge. UplinkFast skips the whole reconverging thing.

Send any questions my way.
