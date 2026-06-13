---
title: "BCMSN Notes - EtherChannel Distribution"
date: 2009-06-23
tags: 
  - "bcmsn"
  - "calculation"
  - "ccnp"
  - "cisco"
  - "distribution"
  - "etherchannel"
  - "xor"
---

EtherChannel lets you aggregate links into one logical connection, but the distribution of traffic is not uniform.  It does not use per-packet load-balancing or the like to determine what interface in the bundle to use.  Instead, it uses a XOR function on packet information to generate a hash that is used to determine what interface to use.

By default, the switch will use both the source and destination IP addresses to generate the hash, but there are lots of others.

- **src-ip**:  Just the source IP
- **dst-ip**:  Just the destination IP
- **src-dst-ip**:  Both the source and destination IPs
- **src-mac**:  Just the source MAC
- **dst-mac**:  Just the destination MAC
- **src-dst-mac**:  The source and destination MACs
- **src-port**:  Just the source TCP/UDP port
- **dst-port**:  Just the destination TCP/UPD port
- **src-dst-port**:  The source and destination TCP/UDP ports

You change the method by giving the _port-channel load-balance method_ directive in global config mode.  Notice that this is a system command, so any change affects all channel groups.

To generate the hash, the switch takes the binary representation of the address(es) and does a XOR (that is, are they different? yes/no) on the last few bits of each to get a value.  If you have 8 interfaces, it uses the last 3 bits (2^3 = 8).  If you have 4 interfaces, it uses 2 bits.  Two interfaces means a single bit.

What we wind up with is an index of the interface to use.  If you do a _show etherchannel detail_ on your switch, you'll see each interface is assigned an index that starts with 0.

Let's go through an example.  You have a switch that has F0/1(index 0) and F0/2 (index 1) in Po10.  You also have left the load-balancing method to the default of **src-dst-ip**.  The switch needs to forward a packet that is sourced from 10.0.0.1 and destined to 10.0.0.2 over Po10.  Let's step through it.

> 10.0.0.1 = 00001010 00000000 00000000 00000001 10.0.0.2 = 00001010 00000000 00000000 00000010

Since we have two interfaces in the channel group, we look at the last bit of each address and XOR them to get an index of 1 (1 XOR 0 = 1).  That's F0/2, so that interface will be used to send the frame over.

If we add f0/3 and f0/4 to the channel group, we would calculate a XOR on the last 2 bits of each address, which would give us 11, which is 3 in binary.  The interface with an index of 3 (probably f0/4) would get the traffic.

What if I'm switching IPX packets?  Any non-IP packet will default to using the MAC address.  Can someone answer exactly what method it will be used?

What if we then add f0/5?  That's a good question, but I'm not exactly sure how the switch handles a number of interfaces not a power of 2.  Can someone help on this, too?

Send any ~~obvious corrections and~~ questions my way.
