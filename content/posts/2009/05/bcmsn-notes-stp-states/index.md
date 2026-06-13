---
title: "BCMSN Notes -- STP States"
date: 2009-05-22
tags: 
  - "bcmsn"
  - "blocking"
  - "ccnp"
  - "cisco"
  - "forwarding"
  - "learning"
  - "listening"
  - "spanning-tree"
  - "state"
  - "stp"
  - "switch"
---

I've decided to take on the CCNP certification, so I'm going to wind up with a few posts will be more my own notes than anything.  :)

A switch port on a 2960 comes up with a default configuration on VLAN 1.  What happens from the perspective of spanning-tree?

- First, the port comes up on **blocking** mode.  This is to make sure that loops aren't created without first listening to the network to see what's going on.
- Next, if the port may be a root or designated port, the port is moved to the **listening** state.  In this state, the port can send and receives BPDUs only.  It can't send traffic, but it can discover the other switches participating in STP.
- After the forwarding delay, the port goes into the **learning** state.   In this state, the port can send and receive BPDUs as in listening, but it can now receive traffic.  It can't yet send any.
- After the forwarding delay again, the port goes into the **forwarding** state.  The port can now send and receive data.

If the port is configured with _spanning-tree portfast_, the mode goes from **blocking** directly to **forwarding** without going through these steps.  Obviously you don't want a switch plugged into a port configured for portfast since you may wind up with a loop.

Here's the _debug spanning-tree events_ output from one of my labs.  F0/3 is configured for portfast.  I _shut_/_no shut_ it to see what happens.

> ```
> *Mar  8 18:09:51.163: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to down
> sw01#
> *Mar  8 18:09:51.747: set portid: VLAN0007 Fa0/3: new port id 8003
> *Mar  8 18:09:51.747: STP: VLAN0007 Fa0/3 ->jump to forwarding from blocking
> sw01#
> *Mar  8 18:09:53.739: %LINK-3-UPDOWN: Interface FastEthernet0/3, changed state to up
> *Mar  8 18:09:54.739: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to up
> ```

Notice the "jump to forwarding from blocking".

Here's the same output when the port is not in portfast mode.  Notice the timestamps.  It takes about 30 seconds (2 x default foward delay) to go from blocking to listening to learning to forwarding.

> ```
> *Mar  8 18:13:05.313: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to down
> sw01#
> *Mar  8 18:13:06.013: set portid: VLAN0007 Fa0/3: new port id 8003
> *Mar  8 18:13:06.013: STP: VLAN0007 Fa0/3 -> listening
> sw01#
> *Mar  8 18:13:06.381: %LINK-3-UPDOWN: Interface FastEthernet0/3, changed state to up
> *Mar  8 18:13:07.381: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to up
> sw01#
> *Mar  8 18:13:21.013: STP: VLAN0007 Fa0/3 -> learning
> sw01#
> *Mar  8 18:13:36.013: STP: VLAN0007 Fa0/3 -> forwarding
> ```

Send any ~~obvious corrections and~~ questions my way.
