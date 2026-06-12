---
title: "Mixed-platform LANs and Spanning Tree"
date: 2007-08-10
tags: 
  - "cisco"
  - "nortel"
  - "stp"
  - "switch"
---

We just an HP C-class blade chassis which included two GbE2c network modules.  These modules are Nortel switches running AlteonOS that connect the blades to the rest of your network.  When I turned these guys up the other day, every VLAN stopped working, so I ran down to the data center and unplugged the uplink.  I called HP and soon found out that the GbE2c doesn't play nice with Cisco switches out-of-the-box.  Since we have a Cisco network (not now, I guess), we can into some problems.

By default, Cisco runs Per-VLAN Spanning Tree (PVST).  That means that every VLAN on a Cisco switch has its own Spanning Tree (STP) instance.  The Nortels, however, run a single STP instance for all VLANs, so, when I turned up the uplinks, the single STP started talking to all of the PVSTs on the Cisco switches.  The result was not good.  Every VLAN converged over and over simultaneously, totally locking up the network.

When we got that fixed and the uplinks were working, I found another problem -- every switch in the network decided that the Nortels were the root bridges.  While this is not a show-stopper, it can prove to be a problem since the root bridge is used by STP to decide where the center of the network is.  It turns out that the GbE2c has a default priority of 32768 (which is the 802.1d standard) and advertise themselves as that.  Cisco switches actually add the VLAN ID to the priority on the PVST, so the Nortels always wind up with the lowest priority.  These things are absolute pieces of crap, so that's not a good thing.  I had to take an outage and reduce the priority of two of our core Cisco switches to make everything right.

Moral of the story:  Be careful when you have a mixed-platform LAN environment since competing problems don't always (read: never) play nice.
