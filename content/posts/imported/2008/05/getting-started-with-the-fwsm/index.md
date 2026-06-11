---
title: "Getting Started with the FWSM"
date: 2008-05-01
tags: 
  - "firewall"
  - "fwsm"
---

Have I talked about the Cisco Firewall Services Module (FWSM) before? It's a firewall on a module for the 6500 and is based on the PIX firewall. The term "based on" is important here, since it does a lot of stuff the PIX does but everything. It obviously does connection inspection and filtering, but it does not do any VPN stuff. It's not a license thing; it just won't do it. If you want to do VPNs on the 6500, you have to get the IPSec VPN Service Module.  The VPN thing isn't true, actually.  I believe version 3.1 and higher has support for VPNs.

Anyway, we won't be going over any configs this time, but we can get some in the future. I just wanted to go over some stuff that you may need to know to get an FWSM working.

The FWSM does not have any physical ports on it. It uses the backplane of the switch to get a foot in the networks that you need it to. What does that give you? Well, it's ultrafast on the 256Gbps backplane very fast with a 5Gbps uplink to the backplane, which is potentially a lot better than the 1Gbps on a physical interface. It also lets you have more interfaces than you could ever have on a firewall appliance (I think you can have 128 interfaces...someone correct me). I don't know any firewall appliance that has more than 20 or so interfaces. It also is good for your cabling guy since there are no cables. I love it since I'm the cabling guy, too.

The FWSM can be set up with several [security contexts](http://www.cisco.com/en/US/docs/security/fwsm/fwsm22/configuration/guide/context.html). That means that you can chop up the resources of the module into "virtual firewalls" that operate separately from each other. This is pretty cool, actually, since you can, say, make a firewall for each of your hosting customers so that configurations on one don't affect the others. You can also set the thing up in single context mode so it acts just like a single firewall. You'll have to do some research to figure out what you want to do, and there are a lot of possibilities.

You can also set each context up in [either routed or transparent mode](http://www.cisco.com/en/US/docs/security/fwsm/fwsm22/configuration/guide/fwmode.html). Routed mode is the "traditional" way to set up a firewall, where each interface is a different IP network with packets being routed through the FWSM. Transparent mode is where the FWSM has two interfaces that bridge the same IP network. I've always used routed mode everywhere, but you may have some use for transparent mode.

You also have to do [some stuff](http://www.cisco.com/en/US/docs/security/fwsm/fwsm22/configuration/guide/switch.html) on the 6500 to get the FWSM working. You make a vlan-group (a list of VLANs) to present to the FWSM and then assign that to the module. If you have more than one FWSM, you can have multiple vlan-groups; different FWSMs can control access in and out of different networks. Remember the context thing? If you have an FWSM in multiple contexts, you present the module with all the VLANs on all the contexts and split them up when you get to the context config part.

No configs. This is weird, but you'll need to get all this information straightened out before you can get an FWSM configured properly.
