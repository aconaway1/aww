---
title: "The Cisco Network Hierarchical Model"
date: 2008-02-06
tags: 
  - "access"
  - "ccnp"
  - "certification"
  - "cisco"
  - "core"
  - "distribution"
---

I got my CCNP certification library the other day to finally get myself another cert, so I've been doing some reading of late. The thing I hate about certs is that, even if you have all the experience in the world, there's always a whole mess of academic stuff that no one really knows or cares about. One of those things is the Cisco Network Hierarchical Model. This model is purely academic and comes with the caveat that you may or may not want to need to use this model in your situation. In other words, here's what we recommend, but do what you want to make your network run properly.

This model tells us to configure our network in three layers -- the access, distribution, and core layers.

- The access layer is where hosts are connected to the network. This includes your closet switches for your users and any other switches where your servers connect up. This layer is OSI layer-2 only and includes physical segments and VLANs. When I think of this layer, I immediately think of a Catalyst 2950 or 3550.
- The distribution layer aggregates the access layers into a central layer-3 device (a router or L3 switch) for distribution between access devices or up to the core. This is where you lock down access with ACLs. When I think of the distribution layer, I think of a 3750 or 4500.
- The core layer combines your distribution layers at layers 3 and 4 and simply ships data from distribution layer to distribution layer. There's no access control so that everything is as speedy as possibly. I think of 6500s or 7600 at this layer.

Did you notice that this seems to be LAN-based? You're not imagining things. This model is for deploying a campus network where every host is in the same building or very close, so Ethernet dominates connectivity. You could apply other technologies, such as OC3s or DS0s on the core, I imagine, but there's no mention of WANs at all in the model description.

Speaking of WANs, where's my Internet access? Where's my HTTP server farm? Where's my firewall? Where's my management system? Those questions are left answered in this model. This is where the caveat comes into play. You have to be able to place those devices in the network in the most efficient and effective place. Your Internet access will probably be in the core. The server farms, by definition, are just hosts, so you would create another access layer for those. Since you need to firewall those servers off, you'll put their access layer under their own distribution layer with the firewall at the top for access control. Management is just another access layer, but the distribution layer where that lives isn't quite as clear.

Like I said, it's an academic model, so there's no definitive answers for anything, but it has a lot of information in there that you may or may not have considered.
