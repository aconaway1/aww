---
title: "Using CDP To Track Down Physical Connections"
date: 2008-10-31
tags: 
  - "cdp"
  - "documentation"
  - "lan"
  - "switching"
---

We have a location that's a few blocks down from the main office here, and we were reviewing the circuit size to make sure it was sized properly.  Since not one person knows what's going on and the trending graphs gave us conflicting details, one of our network dudes took me down to the site to do a physical survey to see what's going on.  Well, besides the fact that no one was there, we discovered a hodgepodge of routers and switches that were cross-connected to one another on multiple floors of the building (I really wish I could post pics to emote the effect).  It's kind of hard to figure out what's going on when you can't see both ends of the cable, so we had to abandon all hope.

What are our options, then, to see how things are uplinked and connected?  In this case, CDP is the answer.  The Cisco Discovery Protocol (CDP), if you don't know, tells you what other Cisco devices a particular Cisco device in plugged into.  So, if you have a 2811 plugged into a 2960, you can see what ports they're connect on along with some other details.  Here's an example.

> Switch1#sh cdp neighbors Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone
> 
> Device ID            Local Intrfce         Holdtme   Capability    Platform   Port ID Router1                    Gig 0/10              122             R       3640      Fas 0/1 Switch2                    Gig 0/9               141            S I      WS-C2950G-Fas 0/48

As you can see from the output, Switch1's G0/10 is plugged into Router1's F0/1 interface, and Switch1's G0/9 is plugged into Switch2's F0/48.  You can also see that Router1 is a 3640 and a router (the "R" under Capability).  Switch2 is a 2950G switch.

So, today, I'm going to start at the head end of my frazzled location and try to figure out where everything is connected.  I'll get all the CDP neighbors for that device, document it, then repeat for the next hop until I'm all the way through.  When I'm done, I should have a nice physical map.

Beware, my friends, that the "C" stands for Cisco.  It doesn't stand for Juniper or Nortel or anybody else.  The rule is that CDP only shows your Cisco devices that are connected together and won't show any other devices in the path, but there are exceptions.  Since it's broadcast-based, a lot (maybe all?) non-Cisco switches just pass along the packet to the next hop on layer 2, so you may see CDP neighbor adjacencies between switches that aren't connected to one another.  CDP will think they are, and I don't know of any way to detect that, so be careful.

Send me money Halloween candy comments if you feel inclined.
