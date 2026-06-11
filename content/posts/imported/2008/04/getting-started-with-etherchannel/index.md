---
title: "Getting Started with EtherChannel"
date: 2008-04-18
tags: 
  - "catalyst"
  - "lan"
  - "switching"
---

In my professional life at some point, I came across someone who had a stack of Catalyst 2950 switches all trunked together with their Internet routers connected to the top of the stack. This was all well and good until they kept adding hosts to the "middle" of the stack, then they had all sorts of latency and packet loss.

The old adage of your chain only being as strong as your weakest length holds true in this case. Here, the weakest link is actually the most-congested trunk, though. Let's step through to see. A 2950 is a 10/100 switch, so a single trunk can handle 100Mbps of traffic. We have 10 of these guys, Switch1 to Switch10, all trunked to the one above and below. If a server in the center of the stack on Switch5 is sending a lot of data to the Internet routers on Switch1, the trunks off of Switch5 will start to get saturated. Switch4 has a few hosts doing the same thing, so traffic from both Switch4 and Switch5 heads towards Switch1, further filling the trunks. Same for Switch3. Same for Switch2. Next thing you know, there's 184Mbps or so trying to go across a 100Mbps link.

I fixed the problem using [EtherChannel](http://www.cisco.com/en/US/tech/tk389/tk213/technologies_tech_note09186a0080094714.shtml "Cisco.com -- Understanding EtherChannel"). EtherChannel, sometimes called trunking (not Cisco trunks...don't get confused) or bonding, takes up to 8 interfaces of a switch and binds them logically as one _Port-channel_ interface. The switch then load-balances (not really, but can be pretty close) the traffic across all the links, so, in essence, if you have 2 100Mbps ports in an EtherChannel, you get 200Mbps of bandwidth. If you put in 8 ports, you'll get 800Mbps. Or, at least, close to it.

There are, as usual, stipulations for using it.

- All ports on the EtherChannel must terminate to the same device on both ends. You can't have one port go to one switch and another to another switch.
- The ports must use identical media. You can't have a copper port and a fiber port in the same group.
- The ports must have the same capabilities. You can't put a 100Mbps port and a 1Gbps port in the same group.
- The ports must be configured identically or it won't work. This is actually pretty easy to maintain since configuring the Port-channel actually sets the config on all participating ports.
- Both ends of the ports must be EtherChannel. You can't run it on one switch but not the other.

Enough of that. Let's configure. The scenario is that you have SwitchA and you want to bind F0/1 and F0/2 into an EtherChannel. Of course, you want to carry traffic from all VLANs, so let's make it a trunk.

> int F0/1 channel-group 1
> 
> int F0/2 channel-group 1
> 
> int Port-channel 1 speed 100 duplex full switchport trunk encapsulation dot1q switchport mode trunk

Easy as pie. Do the same thing on the other end and you have a 200Mbps, bonded trunk.
