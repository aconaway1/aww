---
title: "ACLs and HSRP, BGP, OSPF, VRRP, GLBP..."
date: 2008-06-12
tags: 
  - "acls"
  - "bgp"
  - "bootps"
  - "dhcp"
  - "dhcpd"
  - "eigrp"
  - "glbp"
  - "hsrp"
  - "ospf"
  - "rip"
  - "vrrp"
  - "vrrp-e"
---

Here's a handy list of ACL entries to allow your devices to speak routing protocols, availability protocols, and some other stuff. We'll assume you have ACL 101 applied to your Ethernet inbound; your Ethernet has an IP of 192.168.0.1.

- BGP : Runs on TCP/179 between the neighbors

`access-list 101 permit tcp any host 192.168.0.1 eq 179`

- EIGRP : Runs on its own protocol number from the source interface IP to the multicast address of 224.0.0.10

`access-list 101 permit eigrp any host 224.0.0.10`

- OSPF : Runs on its own protocol number from the source interface IP to the multicast address of 224.0.0.5; also talks to 224.0.0.6 for DR/BDR routers

`access-list 101 permit ospf any host 224.0.0.5 access-list 101 permit ospf any host 224.0.0.6`

- HSRP : Runs on UDP/1985 from the source interface IP to the multicast address of 224.0.0.2. I've seen in the past that it runs on UDP/1985, but I didn't find any evidence of that in a quick Google for it. Can someone verify?

`access-list 101 permit udp any host 224.0.0.2 eq 1985`

- HSRP version 2 : Runs on UDP/1985 from the source interface IP to the multicast address of 224.0.0.102.

`access-list 101 permit udp any host 224.0.0.2 eq 1985`

- RIP : Runs on UDP/520 from the source interface IP to the multicast address of 224.0.0.9

`access-list 101 permit udp any host 224.0.0.9 eq 520`

- VRRP : Runs on its own protocol number from the source interface IP to the multicast address of 224.0.0.18

`access-list 101 permit 112 any host 224.0.0.18`

- VRRP-E : This is a Foundary thing according to readers, and runs on UDP/8888 from the source interface IP to the multicast address of 224.0.0.2

`access-list 101 permit 112 any host 224.0.0.2 eq 8888`

- GLBP : Runs on UDP from the source interface IP to the multicast address of 224.0.0.102

`access-list 101 permit udp any host 224.0.0.102`

- DHCPD (or bootps) : Runs on UDP/67 from 0.0.0.0 (since the client doesn't have an address yet) to 255.255.255.255 (the broadcast).

`access-list 101 permit udp any host 255.255.255.255 eq 67` If anyone else has one to add, do so in the comments.
