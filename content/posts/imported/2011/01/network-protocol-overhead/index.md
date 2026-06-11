---
title: "Network Protocol Overhead"
date: 2011-01-10
categories: 
  - "misc"
tags: 
  - "ah"
  - "esp"
  - "ethernet"
  - "frame-relay"
  - "gre"
  - "header"
  - "ip"
  - "ipsec"
  - "l2tp"
  - "mpls"
  - "overhead"
  - "packet"
  - "ppp"
  - "protocol"
  - "rtp"
  - "tcp"
  - "udp"
---

Here are some packet overhead numbers for a few popular protocols to help with doing bandwidth requirement calculations.  This may be another add-as-we-go post, so please comment with additions or corrections.

> Ethernet : 20 bytes  
> Frame Relay : 4 - 6 bytes  
> PPP : 6 bytes  
> MLPPP: 10 bytes  
> MPLS : 4 bytes

> IP : 20 bytes

> TCP : 20+ bytes  
> UDP : 8 bytes  
> GRE:  4 - 20+ bytes
> 
> IPSec : 50 - 57 bytes  
> ESP : 20+ bytes  
> AH : 16+ bytes  
> L2TP : 24 bytes  
> RTP : 12 bytes
> 
> Bonus:  A voice packet is always 40 bytes + data link since it will always (?) use RTP + UDP + IP.

**Sources  
**

[CCNA Voice Official Exam Certification Guide (640-460 IIUC)](http://www.ciscopress.com/bookstore/product.asp?isbn=1587202077)

[Protocol Overhead  
](http://sd.wareonearth.com/~phil/net/overhead/)

[Generic Routing Encapsulation  
](http://en.wikipedia.org/wiki/Generic_Routing_Encapsulation)

[IPSec  
](http://en.wikipedia.org/wiki/IPsec)

[IP Authentication Header  
](http://tools.ietf.org/html/rfc4302#page-4)

[Encapsulating Security Payload  
](http://www.networksorcery.com/enp/protocol/esp.htm)
