---
title: "Qos Priority"
date: 2008-04-08
tags: 
  - "qos"
---

We just talked about [tagging traffic](/posts/2008/04/qos-tagging/ "AConaway.com -- QoS Tagging") and [policing traffic](/posts/2008/04/qos-policing/ "AConaway.com -- QoS Policing"), but we haven't talked about prioritizing traffic. Tagging just sets a value in the header. Policing sets a "bandwidth ceiling" that can't be crossed. Prioritization guarantees a certain amount of bandwidth for a flow/app/etc. no matter what's going on.

Prioritization offers you a certain amount of bandwidth; it doesn't carve it out and hand it over. If you're using less than the priority value, you only get as much as you need and the rest of the reserved bandwidth goes into the pot for everyone to use. As priority traffic grows, though, you're given as much as you need up to the configured value. When you go over that, your extra traffic just goes into the best-effort queue with everything else (Note: Don't go over the limit with VOIP traffic. Echoes and artifacts suck). For example, if you give your VOIP traffic 70% of the bandwidth of an interface but are only using 40%, the other 30% can be used by the other apps on the line. If you're using 80%, that 10% over is competing with everything else for bandwidth.

Let's get on with it, shall we? To configure prioritization, you go through the same steps as we did with policing, but, instead of setting the policy-map for _police_, you set it up for...wait...wait..._priority_. Here's the scenario, then. You have a router that has a VOIP system behind it. That router tags all VOIP packets as AF41, so we want to give any AF41 packets 70% of the bandwidth on the interface. The tagging router is directly attached to your F0/0 network, and your users are out F0/1.

We don't use an access list this time since we're looking for DSCP values in the packets. We'll start with the _class-map_.

> class-map VOIP match dscp af41

Remember that _class-maps_ define what traffic you're interested in, so this line says that we want to do something with any packet that's tagged AF41. Let's tell the router what we want to do with it, then.

> policy-map PMAP class VOIP priority percent 70

With this configuration, any packet with a DSCP value of AF41 is given priority and is guaranteed up to 70% of the total bandwidth available. So, what is 70% of the total bandwidth available? That depends on where we apply the policy-map. If it's on a T1, that's 1.08Mbps. On our FastEthernet, that's 70Mbps.

Let's apply it to the interface to finish up.

> interface F0/1 policy-map output PMAP

Since we're worried about squashing VOIP traffic on F0/1, we apply it outbound there (towards the users). We could apply it inbound on F0/0, right? Not really. Once the packets hit the interface, it's too late to do any prioritization on them; they're already on the router in the order that the switch is delivering them to us. Prioritization really only works outbound.

So, if nothing's going on, VOIP can use up to 100Mbps, but, if the link is saturated, it's guaranteed 70% of the bandwidth or 70Mbps. Of course these numbers should be tweaked to suit your needs, but you can see how useful prioritizing is.
