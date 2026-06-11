---
title: "VTP and You"
date: 2008-04-16
tags: 
  - "lan"
  - "switching"
  - "vlan"
  - "vtp"
---

[VLAN Trunk Protocol (VTP)](http://www.cisco.com/warp/public/473/21.html "Cisco.com -- Understanding VLAN Trunk Protocol") is a little gem on Cisco switches that allows you configure VLANs in one place and have them appear on all of your switches. This is great for large enterprises with 8457839 switches all trunked together because who wants to configure the new VLAN for that one-off application on all 8457839 switches?

VTP works by having designated VTP _servers_ (not real servers like your Linux box, but a switch) tell the rest of the switches in the network with what VLANs they should be configured. All the designated VTP _clients_ say "OK" and configure themselves with those VLANs. When you take a VLAN out of the server, all the clients take it out; when you add a new VLAN, all the clients add it as well. The server and client designation is known as the VTP _mode_, and there's one more to mention. When a switch is in VTP _transparent_ mode, he will see VTP from the servers but will ignore them and pass them on to the next switch as if nothing ever happened.

To configure VTP, you have to define the VTP _domain_, which is a name for a group of switches that exchange VTP information. In general, in an enterprise, all your switches will probably be in the same VTP domain, but there are situations where you have multiple VTP domains on a LAN (like production and test networks). Next, you set the mode on the switch -- server, client, transparent. I would suggest you configure a _password_ as well to at least appear as though you're practicing good security practices (I'm not sure it's actually secure or not since every switch has the same password and the hash is sent in every packet...somebody enlighten me, please).

Get on your configuring gloves while I give you [some more detail](http://www.xpresslearn.com/cisco/vtp-facts "XpressLearn.com -- VTP Facts"). VTP only works over 802.1q or ISL trunks. Also, VTP packets are Ethernet frames with the VTP stuff shoved into the data section. A switch can only be in one VTP domain at a time and can only be in one mode at a time. You can have more than one VTP server for a domain if you'd like (via a revisioning mechnism where the latest update always wins).

Here's your scenario. You have SwitchA's port G1/1 plugged into SwitchB's G2/1. You want SwitchA to be the VTP server and SwitchB to be the client. Let's get the config in place.

> SwitchA(config)#interface G1/1 SwitchA(config-if)#switchport trunk encapsulation dot1q SwitchA(config-if)#switchport mode trunk SwitchA(config-if)#exit SwitchA(config)#vtp mode server SwitchA(config)#vtp domain VTP-DOMAIN SwitchA(config)#vtp password VTP.PASS
> 
> SwitchB(config)#interface G2/1 SwitchB(config-if)#switchport trunk encapsulation dot1q SwitchB(config-if)#switchport mode trunk SwitchB(config-if)#exit SwitchB(config)#vtp mode client SwitchB(config)#vtp domain VTP-DOMAIN SwitchB(config)#vtp password VTP.PASS

Both switches are now in VTP domain "VTP-DOMAIN" and share the password "VTP.PASS". When you add a VLAN to SwitchA, it automatically gets added to SwitchB. When you add a VLAN to SwitchB, it actually errors out and says something like "Dude, I'm a VTP client. You can't update me."

At work, we have a large number of ports but a small number of switches. We also make very few changes to VLANs, so the work needed to maintain the VLANs is very low. Because of that, we actually do use transparent mode on all the switches. I'm also a bit of a purist, and configuring VLANs is something I like to do by hand; in my opinion, some things, especially mechanisms that can take whole networks down, are better left to the human. That's just me, though.
