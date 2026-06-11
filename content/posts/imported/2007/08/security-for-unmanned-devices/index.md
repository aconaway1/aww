---
title: "Security for Unmanned Devices"
date: 2007-08-23
tags: 
  - "security"
---

I was talking to a coworker the other day about setting up his home network more securely.  "No problem," I said, and we started listing devices on his network to see what we needed to do.  I was pretty surprised that he had so many things on his network.  I mean, I was quite amazed.  He had all sorts of stuff -- from gaming consoles to guest machines to special-purpose Linux boxes to sewing machines.  A sewing machine?  Yes, a sewing machine.

We beat out a pretty good function list for his to segment everything out, but his huge list of stuff got me thinking about the things that are network-ready nowadays.  You can get refrigerators, exercise bikes, and pet feeders.  Who knows how to secure a refrigerator?  I would think that nobody really does, so what's the best thing you can do to protect yourself from your refrigerator?  Good question.

If you step back and take another look at it, you could definitely consider those hosts to be untrusted.  You may or may not have any management on them, and they go about doing their thing without your intervention.  What else is an untrusted host?  How about your buddies who bring their laptops over?  The same thing applies to them, too, so why not?

Segmentation is a really good start with security, especially if you don't know exactly what a box does, so let's start there.  If you have a bunch of stuff that's not traditionally a network device, you should first think about making a network segment for those guys.  If you have a Linux box as a firewall/router, you can simply add a NIC to it and voila!  New network segment for untrusted devices.  Lock that puppy down and you should be good.  In my setup at home, that segment has its own SSID for guests to connect to and contains my Wii.  The only thing that hosts have access to is HTTP and HTTPS and all access to my other networks is denied completely.  This protects my machine from them and doesn't let them go running amok.

\----

My usual note:  You should probably have separate segments for untrusted devices, trusted workstations like laptops and PCs, and servers such as file servers.  If you want to be really anal about stuff, you should get a managed switch that supports private VLANs so hosts on the same network can't get to each other.  That sounds like another article to me, though.
