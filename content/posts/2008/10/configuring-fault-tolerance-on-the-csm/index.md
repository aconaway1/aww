---
title: "Configuring Fault Tolerance on the CSM"
date: 2008-10-10
tags: 
  - "catalyst"
  - "csm"
  - "ios"
---

Like (nearly) everything in the Cisco world, you can set up your CSM to fail over to another module when the primary dies a horrible death.  You can have two in the same chassis or even have them in separate chassis -- the process is the same no matter how you have it set up.  Either way, you have a primary and a secondary module in fault tolerance (FT) mode.

First, we'll establish a VLAN that the CSM will use to do its configuration and state syncing over.  This is just an ordinary VLAN; there's nothing special about it, really, but it should be dedicated for the CSM to use for syncing.  Let's randomly choose VLAN 83.

> vlan 83 name CSM-Sync

You will, of course, have to do this on every switch that holds a CSM, so, if you're using them in two different chassis, you'll put the same VLAN on each making sure they can see each other through a trunk.  Cisco recommends that you dedicate a trunk between the two switches for the sync VLAN in order to remove the chance of other traffic stepping on the sync packets, but I'm not convinced that's necessary.  Use your judgement on that one.

Back to it.  Next, you need to decide on a FT group ID.  This is similar to a HSRP group and lets you run multiple FT groups on the same VLAN.  The group ID needs to be in the range of 1 to 256, so, since this is the first one, let's just use 1.  Get into config mode for the CSM that you want to be the primary and do this.

> ft group 1 vlan 83

This takes you to the _config-slb-ft_ prompt.  Just like HSRP, we need to set priorities for each device and whether or not it should preempt, so let's configure.  Yes, we want to preempt, right?  Let's set the priorities to 100 and 90, too.

> priority 100 alt 90 preempt

This sets the primary CSM to priority 100 and the secondary to 90; both will preempt.

What about configuring the secondary for FT? That's easy.  Go into CSM config mode on the secondary and enter the _ft group 1 vlan 83_ command.  That's it. The two CSMs will do a little arguing and come back as the primary and secondary.  After that, all configuration is done on the primary, which is synced over to the secondary just like an ASA.  Pretty cool, eh?

When configuring things like IP addresses, though, you'll need to make provisions for the secondary with the _alt_ directive (remember that one from the priority).  I won't go into much, but you'll need it mostly when settings IPs to VLANs.  Here's an example of setting an IP address on client VLAN 100 for both the primary and secondary.

> vlan 100 client ip address 192.168.0.11 255.255.255.0 alt 192.168.0.12 255.255.255.0

Alright...one more thing.  The configurations don't sync automagically (at least not on my old version of code).  If you make a change to the primary CSM, you'll see an out-of-sync message when you look at the FT status.

> Switch#sh mod csm X ft FT group 1, vlan 83 This box is active Configuration is out-of-sync priority 100, heartbeat 1, failover 3, preemption is on alternate priority 90

If the primary goes down now and the secondary takes over, the changes you just made won't be reflected on the secondary.  You fix this with the _hw-module contentSwitchingModule X standby config-sync_ command (where X is the module slot in the chassis).  Alternatively, you can just type _hw c X s c_ as a shortcut.  It'll take a few minutes depending on your configuration, so check your logs for when it's finished.  Note that the secondary does not save the new configuration to its startup-config; you'll have to log in and save that manually (or automatically through CiscoWorks or something) to save changes there.

Let me know if you have any questions and check out [my page on getting output](http://aconaway.com/2008/06/10/getting-something-out-of-the-csm/ "AConaway.com -- Getting Something Out of the CSM") from Cisco's fine mid-tier load balancer.  :)
