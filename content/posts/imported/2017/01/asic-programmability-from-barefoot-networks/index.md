---
title: "ASIC Programmability from Barefoot Networks"
date: 2017-01-30
categories: 
  - "barefoot"
  - "switch"
tags: 
  - "asic"
  - "barefoot"
  - "networks"
  - "switch"
  - "tofino"
---

_Full disclosure : I was lucky to be among a group of networking influencers invited to Silicon Valley to visit some networking companies and see what they were offering to the market.  I was flown out and given accommodations at the expense of Gestalt IT - the company that organized the event.  I was given some swag by each company, but I was never paid to write a positive review on the product.  Heck, I'm not even expected to write at all._

Think about the fastest switch in your network and why it's so fast.  Traditionally, it's because the manufacturer has developed a very efficient ASIC that does switching very well (give me some leeway here and forget about routing, encapsulation, etc.), but it really can't do anything else.  Want a new switching feature?  Well, your switch can't do that if the ASIC doesn't support it.  No big deal - the manufacturer just needs to make a new ASIC that supports it, right?  This sounds simple, but, generally, this is a many-years process and requires a hardware update on your end.  This is not a good solution in a world where new features and technologies show up all the time.

[Barefoot Networks](https://www.barefootnetworks.com/) is focused on changing that paradigm.  They have developed a new ASIC called the Tofino that is fully programmable; it can be made to do whatever you want it to do within the limits set by hardware.  When it come off the manufacturing line, it doesn't do anything; you tell it exactly what you want it to do through an SDK.  You want to do Ethernet switching?  You tell the chip you want to do switching.  Routing?  No problem.  L4 load balancing?  It can do that.  This can all be done on the fly with no interruption in traffic to most changes.  Even if you have to rewrite everything the ASIC is doing, the whole system only takes 50ms to reboot and get back to work.  That's some high-end flexibility right there.

There's also a measure of reliability and security here.  Since we only load which features we want, we don't have to worry about the effects of other protocols.  I remember many years ago, there were a billion (I counted!) vulnerabilities in the Cisco ASA related to SIP.  I wasn't running SIP, but some of those vulnerabilities still forced me to disabled services or updated software.  It was fun explaining to management that this feature, though not enabled, was forcing us to take a maintenance for an upgrade.  Now, if we don't have those modules loaded, we don't have to worry about them.  That means our gear stays up longer and we've limited the area of effect on our switches.

Let's not get caught up in the ability to support the latest-and-great technologies at the push of some code.  Think outside the box here.  How about you update one of the less-often-used TCP header fields with a timestamp so you can track a frame across your network?  Better yet, how about inserting a custom header in the frame with that information and then rip it out once it leaves your network?  What about inserting queuing times at each hop?  If we put all this together, we can now track packets across our network, see where the bottlenecks were, and even see what flows have been sharing queues with your packets across the network.  This is some very powerful stuff that will let network dudes and dudettes actually show metrics and statistics for a flow on demand.  We haven't had that ability until now.

Notice that I've said "fast" and "efficient" when I talk about ASICs, but we don't normally use those words when we talk about programmable things.  Most programmable things are fast _enough_ or efficient _enough_, but they can't hang with specialized hardware, right?  Hardware is catching up with ASIC speeds, so that is becoming less and less true; Tofino, though, is shattering that statement completely.  This ASIC pushes 6.5 Tbps.  Let me say that again.  It pushes 6.5 Tbps. This makes it the fastest switching silicon in the world.  65 X 100 Gbps ports.  Good gravy, Bill, that's some horsepower!  All that horsepower without any additional electrical power or cost.  This chip keeps looking better and better.

Now that I've talked these ASICs up, let's put some realism into the discussion.  I'm not a developer and I'm not going to be adding new features to my ASICs myself.  I'm going to rely on my switch OS vendor to push that functionality to the ASIC when I get a software upgrade.  That implies that I've got some sort of whitebox or cloud vendor providing the OS for my switches.  That, in turn, implies that I'm not one of the traditional juggernaut switches, but that's another discussion for later.

There's a bigger chunk of reality to talk about in the fact that Barefoot is going head to head with monster - Broadcom.  Broadcom has quite a large market share in network switching ASICs (I saw 90% somewhere but can't find it again), so Barefoot will need some help and some luck to build momentum.  They're off to a good start, though, as their ASIC is currently available from [Edge-Core](http://www.edge-core.com/) as the Wedge 100BF-32X and 100BF-65X (AS7616-32X and AS7616-65X, respectively).  They will also be in the [WNC](http://www.wnc.com.tw/index.php) OSW 1800 and 6500, which will be available in Q1 and Q2, respectively.

I highly recommend taking a look at the NFD14 videos at Barefoot.  Those are some high-end guys who are all the smartest in the room.  It's worth you time to see what they have to say.  Those videos are available [here](http://techfieldday.com/appearance/barefoot-networks-presents-at-networking-field-day-14/).

Send any whitebox switches questions to me.
