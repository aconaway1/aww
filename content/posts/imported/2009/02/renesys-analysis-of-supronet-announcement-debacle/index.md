---
title: "Renesys Analysis of SuproNet Announcement Debacle"
date: 2009-02-18
tags: 
  - "bgp"
  - "bgp-internet"
  - "routing"
---

Earl Zmijewski of [Renesys](http://www.renesys.com/ "Renesys.com -- Renesys Corporation") has [an analysis of the SuproNet incident](http://www.renesys.com/blog/2009/02/the-flap-heard-around-the-worl.shtml "Renesys.com -- Reckless Driving on the Internet") that took down a good bit of the Internet on Monday.  From the [blog](http://www.renesys.com/blog/ "Renesys.com -- Renesys Blog"):

> This single Czech provider announcing a single prefix caused a huge increase in the global rate of updates, peaking at 107,780 updates _per-second_. This peak occurred at 16:30:54 UTC, less than 8 minutes after the first announcement.
> 
> At Renesys, we call a prefix _impacted_ in a given hour if either suffers an outage or has a non-trivial amount of instability. In the hour before this event, there were 1215 impacted prefixes globally out of a total of 271,175. During the event, that number surged to 12,920 or 4.8% of all prefixes on earth. One announcement from one provider and we have a 10-fold increase in planetary routing instability for an hour. North America suffered the most, increasing from 0.35% to 4.76%, while South America suffered the least, increasing from 0.52% to 1.75%.

It's an interesting read and shows another example of just how vulnerable the Internet is as a whole.
