---
title: "NBAR and HTTP Data Conversations"
date: 2010-03-08
categories: 
  - "ccnp"
  - "ont"
tags: 
  - "ccnp"
  - "cisco"
  - "class"
  - "map"
  - "nbar"
  - "nmap"
  - "ont"
  - "policy"
  - "qos"
---

I’m still working on the ONT test and doing labs, so I marked up a lab for me to work.  I’m using the same setup as I did last time.  The two routers are 3640s running 12.4(25b).

[![nbar-classmap1](images/nbarclassmap1_thumb.svg "nbar-classmap1")](http://aconaway.com/wp-content/uploads/2010/03/nbarclassmap1.png)

Part of the lab was to identify HTTP traffic coming into F0/0 and mark it as CS3.  That’s pretty easy, right?  Of course, the lab I made up was a little more complicated, but the point comes clear with a simpler example.

> ```
> class-map match-all HTTP
>  match protocol http
> !
> policy-map PM-F0/0-IN
>  class HTTP
>   set dscp cs3
> !
> interface FastEthernet0/0
>  service-policy input PM-F0/0-IN
> ```

I fired off a small script on TestHost1 to repeatedly do NMap scans on TCP/80 of TestHost2 to generate some traffic. 

> ```
> root@bt:~#while ( true ) do nmap -sT -p 80 -v -n 172.16.2.101; done
> ```

I let that run for a while and checked out the service policy on F0/0; there were absolutely no matches on that class.

> ```
> R1#sh policy-map int f0/0 input class HTTP
>  FastEthernet0/0
> 
>   Service-policy input: PM-F0/0-IN
> 
>     Class-map: HTTP (match-all)
>       0 packets, 0 bytes
>       5 minute offered rate 0 bps, drop rate 0 bps
>       Match: protocol http
>       QoS Set
>         dscp cs3
>           Packets marked 0
> ```

I thought that was strange, so I kept the script running and captured the traffic coming out of S1/0.  Looking at the packets in Wireshark showed that none of the HTTP packets were showing up as being marked as CS3; they’re all set to the default DSCP value.

[![HTTP-nomark](images/HTTPnomark_thumb.svg "HTTP-nomark")](http://aconaway.com/wp-content/uploads/2010/03/HTTPnomark.png)No matter how many NMap scans cycled through, the class never incremented a bit.  I let it run for two full minutes and generated a few hundred HTTP packets, but there was still nothing.

On a whim, I enabled NBAR protocol discovery on F0/0 to see if that would shed any light on my mess.  Guess what I found.  That’s right; there were no HTTP matches to NBAR, either.  That makes sense since the class-map I defined uses the NBAR protocol.  Alright, so it seems that it’s NBAR that doesn’t see the packets as HTTP, but why?

Looking through the packet captures again, I noticed that there was no real data in the streams.  I saw the 3-way TCP handshake (a.k.a., my signature wrestling move) and then a RST,ACK.  I only told NMap to check if the port was open, so I changed the while loop a bit and enabled version detection with the “-sV” flag.  This time when I can the script, NMap was actually getting the HTTP banner.  It was much traffic, but it was an actual HTTP conversation, so I checked NBAR again.  Success!  The same for the class, too. 

For craps and smiles, I created a class-map that matched SIP, added it to the same policy-map, and set NMap after TCP/5060 without version detection.  Without having a real SIP conversation, the class counters incremented as long as I was sending packets.  That would seem unexpected until you realize that NBAR has some advanced knowledge of HTTP; you can actually match on URLs, hostnames, and MIME-types.  I guess that means it also know when a real HTTP conversation is taking place.

To finish out the testing, I added an ACL to the router that matched any-any-eq-80.  I made the class-map into a match-any and added the ACL.  Since the ACL just matches the destination port and doesn’t care what the content is, every packet sent matched the class as expected.  I remember reading several places and seeing a couple videos that said that you can use NBAR matching and ACLs interchangeably.  That may not really be true when it comes to HTTP.

Send any ~~Cisco learning credits~~ questions my way.
