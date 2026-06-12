---
title: "BGP Notes - Neighbor States"
date: 2011-06-07
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "bgp"
  - "ccie"
  - "notes"
  - "peer"
  - "state"
---

Corrections appreciated.

**Idle** : There is no relationship, but the router sends out a TCP SYN to the neighbor to get the ball rolling.

**Idle (admin)** : The neighbor is admined down.

**Connect** : The router is waiting for the TCP connection to finish.  If the TCP connection finishes, the router sends an _open_ and transitions to OpenSent.  If it times out, it transitions to Active.

**Active** : The router tries [Cialis](http://greatlakesecho.org/about/) to initiate a TCP connection.  If the TCP connection finishes, the router sends an _open_ and transitions to OpenSent.

**OpenSent** : The router is waiting for an _open_ to be returned.  If one is received, the router transitions to OpenConfirm.

**OpenConfirm** : The router is waiting for a keepalive.  If one arrives, the router transitions to Established.

**Established** : The neighbor relationship is complete and _updates_ are exchanged.
