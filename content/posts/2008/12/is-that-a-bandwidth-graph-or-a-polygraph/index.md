---
title: "Is That a Bandwidth Graph or a Polygraph?"
date: 2008-12-23
tags: 
  - "snmp"
  - "tools"
---

I thought I'd throw an easy one out before taking off for the holiday.  Merry Christmas, Hannukah, Kwanzaa, Saturnia, etc., to all.

A few years ago, I was looking through some Cacti graphs of gigabit trunks between 6500s and noticed an abrupt change in traffic.  The graphs were nice and smooth at around 135Mpbs until, seemingly randomly, they just started going wild.  It seriously looked like a lie detector from the movies; I saw spikes up to 140Mbps in one sample and 2Mpbs the next sample for days and days.  I looked around to see if anything weird was going on somewhere on the network, but I didn't find anything.

I manually went to the trunk ports and sampled the output of _show interface_ over the course of a day or so.  Nothing strange.  Everything was moving up and down about 10% during the day, but there were no huge jumps and drops like the graphs told me. I asked our monitoring guy for a little help, and we sat down and found...nothing.  On a whim, we started looking through the templates that we could use on an interface and saw the 64-bit counters, which triggered a little binary math in both of our heads.

> 2^32 = 4,294,967,296 120Mbps \* 60 sec/min \* 5 min/sample = 3,600,000,000 bits/sample

That's awful close, isn't it?  What if traffic goes up to 150Mbps.

> 150Mbps \* 60 sec/min \* 5 min/sample = 4,500,000,000 bits/sample

That's bigger than a 32-bit counter!  If a trunk was pushing 150Mbps at any time, Cacti would not be able to detect that a counter had been flipped multiple times between samples!

Cacti (and any or most other SNMP tools) polls an interface, it gets the total number of bits that have been sent or received since these counters were reset (usually at boot).  When it polls again in 5 minutes, it get the new number and subtracts the old number, and, voila, the total number of bits transferred in the last 5 minutes.  If the new number is smaller than the first, then Cacti assumes that the counter flipped and adds the second number to the difference between the first number and 2^32 to get the value.  If, however, the interface is spewing out 150Mpbs of data, the counters may flip around once and then still be higher than the original number.  If that the case, Cacti only sees a small number of bits difference and show you a sample rate of 2Mpbs.  What if you're pushing 300Mbps on the trunk?  It may flip twice and still land higher than the first sample for a rate of 2Mbps.  Ack!

The fix?  Query the proper OID for 64-bit counters.  It shows the same data, but reports it in much larger numbers.  Calculating 2^64 gives you 18,446,744,073,709,551,616.  That's 18.4Ebps.  Exabits.  Wow.  I can't even imagine that much traffic.  I'm sure I'll be dead and gone by the time network reach those speeds in the wild.

All modern network gear has the capability to use 64-bit counters, so use them where you can.  Since it's jut another OID, using 64-bit counters doesn't add any more CPU to the gear or the monitoring box.  Some packages like Cacti come bundled with support for "the big boy" counters, but you may have to do a little research and find the right OID to query. Google is your friend.  Let me know if you have problems, and I'll try to help.

Do it now, by the way; you don't want to have to explain those flaky graphs to the boss.  The concept of Exabits may be a little much for him to understand.
