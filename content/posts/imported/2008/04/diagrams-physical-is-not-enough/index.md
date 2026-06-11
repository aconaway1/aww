---
title: "Diagrams -- Physical Is Not Enough!"
date: 2008-04-24
tags: 
  - "diagrams"
  - "documentation"
---

In my billion years in the industry, when I've asked for network diagrams, I've inevitably received a physical diagram -- a diagram that shows where stuff is plugged in. This is fine and dandy and has lots of information, but that's not really enough these days. In the times of Arthur, when every piece of network gear did a single thing, you only needed to know where things were plugged in. In the modern era, devices do more -- a switch can route and house wireless, an ASA can terminate VPNs and be a switch -- so you need more than just where the cables run.

Logical diagrams show layer-3 (the IP networks) and how those are interconnected. From that, the diagram inherits the data paths as well -- how does the packet get from network A to network B and back. You can't see that with physical diagrams in a lot of cases.

[Here's a physical diagram](http://aconaway.com/static/PhysicalDiagramExample.png) of a single Internet router and a 6509 with an FWSM. It literally shows a router and a switch. How many IP networks do I have? How many firewall interfaces do I have? How many layer-3 interfaces on the 6509 am I using? [This logical diagram](http://aconaway.com/static/LogicalDiagramExample.png), however, shows the same network from layer-3. A big difference, eh? Didn't know I had 8 networks, did you?

Don't go replacing all your physical diagrams with logical ones, though. You still have to know where things are plugged in, so keep the physical...just add a logical as well.

Tip of the day: Use Visio tabs for logical and physical diagrams on the same document.
