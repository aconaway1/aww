---
title: "Stubby Post - What's an IDB?"
date: 2010-09-03
categories: 
  - "cisco"
tags: 
  - "block"
  - "cisco"
  - "descriptor"
  - "idb"
  - "interface"
  - "limit"
  - "router"
  - "switch"
---

I [posed the philosophical question](http://twitter.com/aconaway/status/22554005934) on Twitter the other day asking if single trunk links should be in an EtherChannel bundle just in case you need to expand later.  I didn't really expect an answer, but the ever-verbose [@WannabeCCIE](http://twitter.com/WannabeCCIE) pointed out (in not so many words) that you should watch your IDBs.  What is that?

That's an [interface descriptor block](http://www.cisco.com/en/US/products/sw/iosswrel/ps1835/products_tech_note09186a0080094322.shtml).  I admit that I'm not intimately familiar with them, bu they're data structs in IOS used to keep track of the interfaces on that device.  They come in two flavors - hardware and software.  HWIDBs usually represent a physical interface but they also represent tunnels, SVIs, PortChannels, subinterfaces, and any other virtual interface that you can configure.  The SWIDBs represent the layer-2 encapsulation of each HWIDB, so you'll see entries talking about Ethernet, HDLC, PPP, etc.  That means that every interface you have on a router consumes two IDBs (there are always exceptions).  That's important because each platform and IOS version combination has a limit to the number IDBs that device supports.

If you check out [one of Cisco's pages on IDBs](http://www.cisco.com/en/US/products/sw/iosswrel/ps1835/products_tech_note09186a0080094322.shtml#idb_limits), you'll see a pretty table showing the limits.  The 3640 running 12.4(25b) that I run in my GNS3 lab has a limit of 800 IDBs.  That means that I can have 400 interfaces configured at most.  That little 800 series router running 12.1T that you still have running at the VP's house has an IDB limit of 300 or 150 interfaces.  The 7200 in the data center running 12.3 can handle 20,000 IDBS or 10,000 interfaces!

If you guessed that you can see your IDBs by typing _show idb_, then you guessed right.  That will show you the IDB limit, how many are being used, a summary table, and a list of all the IDBs with their details.  Remember that there may be more interfaces on your device that just physical.  You may have an SVI, loopback interface, or even a null or two.  These all count towards the limit.

Before you get freaked out and start checking the IDB limits on all your devices, take a breath.  I've never run into the IDB limit on any device and I've never heard of anyone who has.  I'm sure someone has, but I don't remember hearing about any.  Think about it for a second.  If I took my 3640 and filled it with 4 NM-16ESWs, I'd only have 128 IDBs used (16 ports \* 4 modules \* 2 IDBs for each port).  Don't forget the null interface and VLAN 1 SVI by default (VLANs take 1; VLAN SVIs take 2 each).  That brings the count to 133.  Let's add 100 more VLANs and SVIs on this guy.  Now we're up to 433.  How about we put each interface into a channel group of its own.  That adds another 128, which is 561.  Only 239 more to go.

Unless you're doing something out of the ordinary, I don't think the IDB limit will be a problem.  Of course, that depends on your definition of "ordinary".

Send any sort indexes questions my way.
