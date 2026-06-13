---
title: "Catalyst 3750s - Bad Luck with a Cisco Logo"
date: 2010-08-31
categories: 
  - "cisco"
tags: 
  - "3750"
  - "cisco"
  - "switch"
---

Last week, [@fletcherjoyce](http://twitter.com/fletcherjoyce) posted [an article on his blog](http://reloadin10.wordpress.com/2010/08/28/catalyst-3750-are-they-really-that-bad/) about his positive experiences with Cisco's 3750 switches.  If you follow my complaints [tweets](http://twitter.com/aconaway), you know that I've had quite the opposite experience with them.  I would never pick on anyone, but I had to throw in my 2 cents.

I'm guessing here, but we have about 50 3750 stacks in the enterprise.  Most of them are pairs, you wind up with roughly 120 switches.  Since we've done about 20 replacements over the last 5 years, that means we have a 17% failure rate.  That's pretty horrible, isn't it?

For the most part and with few (if any) exception, we use the 3750s as aggregation points for our access switches.  We don't do QoS on them.  We don't do any access control on them.  We don't even do routing on them.  They're simply used to connect all the access switches in the closet to the core, so they're not doing anything funky or burdensome.  The CPU and memory are always well within normal operating parameters.  They just fail and fail repeatedly.

The flies started dropping in closets at our corporate headquarters a few years ago.  It was the middle of summer, and the temperatures kept rising to over 90F (32C) until the we lost 3 switches in 3 weeks.  If you could stand to make it into the closet, you could feel that the sheet metal of the switches was hot enough to make you pull your hand back!  When the facilities team added more cooling, the temperatures dropped to around 82F there (28C), but we continued losing switches.  I figured the newly-failed switches were feeling the effects of the earlier heat wave and were just getting around to giving up the ghost.  Surely the heat was the culprit.

A few months after our headquarters meltdown, a tech for a satellite office called and asked if we could help with some latency issues.  He showed me the switch stacks throughout the building, and I noticed that only one of the 10 switches actually had a label.  The tech said that he never got around to relabeling them after they were replaced.  Some, he said, had been replaced multiple times.  The closets were running about 76F (24C), so heat didn't seem to be the problem at this location.  The closets were clean as a whistle, and everything in the racks was on building UPS.  I couldn't find a pattern at all.  _For the record, all their latency issues were related to two unrelated 3750s.  Two RMAs later, and their problems were gone._

I've been trying to find patterns for the failures, but I can't think of any.  If it's heat, humidity, power, dust, etc., then why are we not replacing 2950s as well?  There are 4-10 of them for every 3750s stack we have.  We're replacing them, but it's a rate of less than 1%.  If it is environment, then the 2950s are English hooligans compared to the 3750s being French aristocracy.  Maybe it's sabotage.  I still don't know after years of watching RMA after RMA come in.

I have noticed one pattern, though.  The only deployments of 3750s that have never had a problem are in data centers.  They seem to love any room that has an ambient temperature of 62F (16C) with less than 40% humidity and large volumes of air flow.  If only we could install micro-data centers in all our closets, then I would be a happy network dude.

Send any ~~wooden shoes~~ questions my way.

Edit:  I went back and checked our TAC cases to see what switches we actually replaced.  It turns out that we've done 19 replacements, and they've all been 3750G-12S-S switches.
