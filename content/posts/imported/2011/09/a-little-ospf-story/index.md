---
title: "A Little OSPF Story"
date: 2011-09-12
categories: 
  - "misc"
tags: 
  - "area"
  - "ospf"
---

Here's a story from last week with little of no teaching value.

I got a call from one of our business units looking for some routing help.  We don't usually care about their production networks, but they were seeing some funky traceroutes, so I agreed to try and help them out.

They sent over two fresh traceroutes from a host on a 7600.  In one of them, the trace went to the 7600 and then on down the line as expected.  In the other, the trace showed the 7600, another router's far interface IP (that is, an interface not facing the 7600), then the 7600's interface facing that router.  Every few minutes, the path was switch between the two.  The dude told me that they were an OSPF shop, so I asked him to send me the standard _show ip route_ and _show ip ospf database_ commands so I could see what's going on.  The word "unexpected" comes to mind when trying to describe what I found.  So do other words that aren't very appropriate.

The 7600, the main router at the main campus, was in OSPF area 50.  The router that showed up in the trace was also in area 50.  The same was true for every other router at that location, so I figured that area 0 was at another location.  Nope.  All routers at all locations (probably around 20 total) were all in area 50, and area 0 was nowhere to be found.  I always thought you _could_ run a single non-backbone OSPF area, but I never understood why you would actually choose to do so.  If you want one area, that's fine, but why not make it area 0?

That single area was working so I didn't ask too many questions and looked again at the outputs they sent over.  I chuckled a bit when I noticed that the routes to the target network were showing up as an OSPF type-2 external.  I got a copy of the config at the far network and, lo and behold, I found that there is a single _network_ statement for the transit network back to the main campus along with _redistribute connected subnets_.  For some reason, instead of actually advertising networks natively in OSPF, all the networks with hosts on them were being redistributed.  I wasn't there to redesign their network, so I just sighed out loud and kept looking.

I got a copy of the OSPF config for the main campus's 7600 to see if would show why the traceroute was weirding out on them.  Here's the part where I actually laughed out loud on the phone.  Right in the middle of the config, I see "area 50 nssa".  Yes, this single non-backbone area with no real costs being advertised was configured as a not-so-stubby area.  Not only did they go out of their way to make it a non-backbone area but they also wanted it as a stub area.  Since they had all the other networks redistributing into the area, they had to make it NSSA.  It's a week later, and I still roll my eyes.

How did this happen?  When this business unit was being turned up, they actually outsourced the initial build to a company who will not be named here.  They're the ones who put in this creative OSPF configuration that I'm putting in my hall of shame (if I had one).  They're also the ones who caused the reported problem.  After a few more hours of looking around, our guys discovered that the other company put in a new VPN endpoint configured with the IP of the SVI of the 7600.  IP conflicts aren't good, eh?  Once that was changed, everything returned to normal.

A fun few hours indeed.  At least it was entertaining.
