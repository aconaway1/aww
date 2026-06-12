---
title: "PPP Notes - LFI"
date: 2011-06-23
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ccie"
  - "fragmentation"
  - "interleaving"
  - "lfi"
  - "link"
  - "multilink"
  - "ppp"
  - "written"
---

- Link Fragmentation and Interleaving
- A QoS tool to prevent smaller, higher-priority packets from waiting on larger packets to transmit
    - For example, VoIP packets and FTP packets
- Fragments the larger packets and interleaves them with the smaller packets
- Only available in PPP with Multilink
    - Can be a multilink bundle with a single link in it
- Common to use with LLQ to interleave the delay-sensitive packets
- _fragment-delay_ allows you to change the fragment size
    - In milliseconds
    - size = _fragment-delay_ \* bandwidth of interface

> ```
> R1(config)#interface Multilink 1
> R1(config-if)#bandwidth 512
> R1(config-if)#ppp multilink interleave
> R1(config-if)#ppp multilink delay 10
> ```

\-- Corrections, please.
