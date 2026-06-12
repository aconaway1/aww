---
title: "OSPF Notes - Network Types"
date: 2011-06-04
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ccie"
  - "network"
  - "ospf"
  - "type"
---

Corrections are always welcome.

**Broadcast** : Think an Ethernet segement

> DR/BDR? : Yes Default hello interval : 10 sec Neighbor config required? : No

**Point-to-point** : Physical point-to-point links, frame-relay point-to-point subifs

> DR/BDR? : No Default hello interval : 10 sec Neighbor config required? : No

**Nonbroadcast Multiaccess** : Frame-relay multipoint or physical

> DR/BDR? : Yes Default hello interval : 30 sec Neighbor config required? : Yes

**Point-to-multipoint** : Partial mesh networks like a frame-relay hub-and-spoke configuration

> DR/BDR? : No Default hello interval : 30 sec Neighbor config required? : No

**Point-to-multipoint nonbroadcast** : Partial mesh networks like a frame-relay hub-and-spoke configuration but without the ability to broadcast NOTE:  I'm  having trouble finding an example of this that doesn't include changing routing preferences between two unequal paths.

> DR/BDR? : No Default hello interval : 30 sec Neighbor config required? : Yes
