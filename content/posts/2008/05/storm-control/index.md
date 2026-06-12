---
title: "Storm Control"
date: 2008-05-15
tags: 
  - "catalyst"
  - "lan"
  - "switching"
---

We run a large number of LANs all over the country that are "controlled" by the particular business unit. We manage the gear, but, since they have the money and have to pay for anything we do, they make the final decision on what gets put in. Sometimes that gets out of hand, as you can well imagine.

A good terrible example came up a few months ago. It seems that, at some time in the past, one site needed some more LAN ports, but, instead of calling us and having us send them another switch, one of the "technical people" there brought in a hub from home. It really irks me to see a hub on the switched LAN, but we really have no control over those decisions. They plugged the hub into one of the existing drops somewhere in the building and plugged everyone in. It worked...until somebody moved one of the machines. The machine was at a desk near the hub, and the network cable, still with one end plugged into the hub, was just left lying there. A good Samaritan came by, saw that the hub was not plugged into the network (though it was through another path), and plugged it back in for us -- providing a nice second link from the hub to the switch stack in the closet. Take one switch stack, add a hub, insert a switching loop, bake at 350F for a few milliseconds, and you have a broadcast storm. If you don't know already, broadcast storms are bad and eat switch CPU like the yummy cookies we baked. In this case, several 3750s were taken completely down.

How does one prevent such from happening again? Well, the first thing to do is to get the CTO to tell everyone that they can't plug hubs into the network. That works about 0% of the time, though, so we had to find a solution that was enforceable. One of my coworkers found the traffic storm control mechanism built into Cisco switches. This mechanism allows you to set thresholds based on broadcast, multicast, and unicast traffic and take action when those are reached.

Here are the gory details. I need to mention, though, that storm-control is configured very differently across platforms and IOS versions. I would say your mileage may vary, but it's probably more accurate to say that this won't work on your switch. A 6500 is configured differently than a 4500. A 2900XL is different from a 2950. This will get you going, but you're going to have to do some research on your own to find out what works on your platform.

`interface FastEthernet 0/1 storm-control broadcast level 50 storm-control action shutdown`

What just happened? Good question to ask. If broadcast traffic on F0/1 utilizes 50% of available bandwidth, the port is shutdown. That means that if broadcast traffic takes up 50Mbps of bandwidth on this port, the port is admined down just as if you did a _shutdown_ on it.. You should probably do the same for multicast or unicast as well to make sure you don't get bitten by those. If you don't want to shut down the port, you can also use the _trap_ action to just send an SNMP trap with the port and information, but that doesn't prevent very much; the storm will probably wreak havoc before an email for the trap lands on your Crackberry.

Here's another big disclaimer. Finding a good level for a port can be very, very difficult. A linux box is going to have very different broadcast/multicast/unicast traffic than a Windows box which is different than a Mac. You may have to spend a lot of time analyzing SNMP counters to find out what a good level is. God help you if you have a hub like we did with mixed computer platforms on it.
