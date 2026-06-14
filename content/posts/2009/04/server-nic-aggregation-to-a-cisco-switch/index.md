---
title: "Server NIC Aggregation to a Cisco Switch"
date: 2009-04-14
tags: 
  - "bonding"
  - "cisco"
  - "etherchannel"
  - "ethernet"
  - "switch"
  - "teaming"
---

Have you even noticed that your new servers all have 2 NICs on the board?  At least all of them that I've seen in the last 3 years have.  A lot of server admin actually use them in a NIC teaming scenario where both NICs are used as one logical device -- much the same as Etherchannel on a switch.  This provides some fault tolerance and availability in case of failure, which is good idea in most cases.

There are a few different ways to configure teaming on the box (usually called [bonding](http://linux-ip.net/html/ether-bonding.html "Linux-ip.net -- Link Aggregation and High Availability with Bonding") in Linux), and each has its own advantages and disadvantages.  The network dude(tte) may have to do some things on the switch side for some of them to work, though.  If you're want to run in [link aggregation mode (mode 4)](http://www.linuxhorizon.ro/bonding.html "LinuxHorizon.ro -- Bonding (Port Trunking)"), for example, the switch ports need to be in the same channel group to work appropriately.

Let's look at mode 4 a little closer to see what we need to do.  The scenario is that you have _eth0_ plugged into F0/15 of a 2950 and _eth1_ is in F0/16.  You've seen the [configuration for channelling between switches](/posts/2008/04/getting-started-with-etherchannel/ "AConaway.com -- Getting Started with EtherChannel") before, so you know the basics.  Put the ports in the same _channel-group_ and configure the proper _Port-channel_ interface to do the work.  In this case, we're just configuring the ports to house a host instead of being trunks.

> ```
> int F0/15
>  channel-group 1
> 
> int F0/16
>  channel-group 1
> 
> int Port-channel 1
>  speed 100
>  duplex full
>  switchport
>  switchport mode access
> ```

I detect at least one problem with our setup, though.  Both NICs are plugged into the same switch; what happens when the switch goes down?  The server goes away.  Logic should tell you, then, to put the NICs in different switches to fix that, but you can't do Ethernchannel on two different switches.   The ports have to be in the same device for the aggregation to work.  What's the fix?

You can look at getting a nice chassis switch and putting each NIC in different modules.  Modern IOS versions allow etherchanneling across modules, so, if one module fails, you still have that other.  That would do it, but I'm sure you don't have the money for a 4500 in the budget, right?

Another solution is to use a couple [3760s](http://www.cisco.com/en/US/products/hw/switches/ps5023/ "Cisco.com -- Cisco Catalyst 3750 Series Switches - Products & Services - Cisco Systems") which, when connected using the [StackWise](http://en.wikipedia.org/wiki/Cisco_StackWise "Wikipedia.com -- Cisco StackWise") cable, are one logical device.  That gives you two separate switches that you can configure with the same channel group.  An upgrade to this solution is to use a pair of [6500s with VSS 1440 modules](http://www.cisco.com/en/US/products/ps9336/ "Cisco.com -- Cisco Catalyst 6500 Virtual Switching System 1440 - Products & Services - Cisco Systems") in them so that you have a stack of 6500s!  I'm sure that's not expensive at all, though.

Send any ~~white shoes~~ questions my way.
