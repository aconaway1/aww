---
title: "Lessons Learned from a Bad Day"
date: 2010-11-11
categories: 
  - "misc"
tags: 
  - "bad"
  - "day"
  - "moral"
  - "outage"
  - "story"
  - "switch"
  - "vtp"
---

I had a really, really bad day this past Tuesday.  I mean, a really bad day.  I guess I should have seen it coming since the last #stabbytuesday was uneventful.  Here's what said cosmos had in for me and the lessons I took away.  Most of these are things we've all lived before, but, for various reasons, I got blindsided.  I expected more from myself.<!--more-->

First of all, I drove the 2 1/2 hours from my home office to the headquarters to get some work done.  We have a large migration going on this weekend, and I didn't want the guys there to have to do all the hands-on work.  I planned to get some switches installed and do some cabling so the systems guys could just plug in when the servers landed in the data center.  None of that got done thanks to the rest of the karmic retribution.

> Moral of the story:  Give yourself some additional time to do projects just in case something happens.

Over the past weekend and into the week, we ran into an issue with a 48-port GE module on a 6513.  It kept reloading, and a TAC engineer figured out that we were seeing a bug that would cause the module to reload every 12 hours (don't ask me why it was showing up after years in the same configuration).  Each time it went down, we'd lose around half of our servers.  Bear in mind that we have a pair of 6513s and that each server is cabled to each switch for redundancy.  We shouldn't have seen _any_ servers go down; they should have simply flipped over to the other NIC and kept trucking.  As you probably figured out, the bonding/teaming configurations on some of them was either wrong or missing, so the $100k+ switch for redundancy was just a big paperweight.

> Moral of the story:  Having redundant network gear is worthless if the servers can't handle a lost link.

When we got back from lunch, the module had rebooted again, and we lost all those servers and services.  This time, however, the module didn't come back.  We reloaded the module via software but we couldn't make any headway.  It would come up with a status of "Other" and then reboot again.  We ran down to the data center to reseat it, but that didn't help.  It absolutely refused to come up, so we called our TAC engineer and had him generate an RMA for us.

> Moral of the story:  Modules die all the time.  You have to have a plan to deal with it.

We tried one more time to reset the module in software but we lost our SSH connection to the 6500.  Nothing but solid red lights on the supervisor.  The console didn't respond at all, so we wound up pulling power to reboot the whole switch.  It came back up, allowed us to log into the console, and then crashed.  It did this over and over until we physically removed the bad module.  This wasn't exactly an easy feat.  The switch isn't fully populated, but all the 48-port cards were right on top of one another.  This made the cabling very, very dense and hard to maneuver through.  The cables were neat and tidy, mind you, but it's hard to keep one bundle above the card without snagging it, and there is always the one cable that has to be different and not follow the rest of the structured cabling.  Of course, that one winds up cutting right across the module you need to get out.

> Moral of the story:  Your switch may be modular, but each modules needs the supervisor.  Break the sup, and nothing works.  
> Moral #2:  If the cabling weighs more than you do, you may want to spread them out a bit.

We finally got the 6500 more-or-less stable, but there's are 48 ports missing.  Of course, all the servers are still down  The servers aren't configured to use their other NICs, and the RMA is 4 hours out; what do we do now?  I said we have to wait, but upper management declared an emergency and said that it needed to be back up as soon as possible.  Remember the switches [Online Blackjack](http://www.sbs-alamerger.org/) I was going to install for the migration?  Yeah...those are installed as replacements now.  There goes those plans, eh?

> Moral of the story:  If you can't wait until the RMA gets there, you better have spare parts on hand.

The replacement switches are actually a pair of 3750s stacked together.  As you know from my previous rants on them, I absolutely hate the 3750s.  These were the only ones we had, though, so we configured them and went into the data center to install.  That's when we noticed that there was no appropriate power in that rack.  The 6513s had power, but it was an L21-30 (or something similar; I didn't look at the plugs) which doesn't exactly help when you have standard connectors.  We found a power outlet in the floor a few feet down the way and wound up putting in a strip to get power into the rack.

Here's our Senior Network Engineer doing his part to help.  The pic pretty much sums up the whole day.  Upside down.  Cold.  Parts and cables all over the place.  He looks unconscious.

[![](images/power-crawler-300x224.jpg "Crawling for Power")](http://aconaway.com/wp-content/uploads/2010/11/power-crawler.jpg)

There was another problem, though.  There was no room to mount the new switches.  There was only room for the patch panels and the 6513 with a single rack unit of space at the bottom.  Luckily, we were able to finagle the switches into the bottom of the rack using that single U and the extra space on the floor.  Once powered up, we go the cabled plugged in and checked the systems that we could while our boss walked around to get status from the different departments.  Everything was finally fine.

Not really.  While we were dong our checks, someone sent an email saying that one of the major systems was down.  We just had a major outage, and all hands were in the data center helping where they could.  Do you really think anyone checked their email?  After about an hour and a half, we finally saw the problem reports and went back at it to figure out what was wrong.

> Moral of the story:  Use a proper trouble reporting and escalation system.  Email is not it.  
>  Moral #2:  Don't be so inflexible in your infrastructure planning that a rack can only serve one function.  That includes power, cabling, and rack arrangement.

I totally take credit for this one.  It turns out that VTP had slapped us in the face.  I configured the switches and didn't even check the VTP configuration in the rush to get all the servers back kup.  There were other contributing factors, though, that were beyond me.  First of all, the 6513 that we trunked up to had a null VTP domain in transparent mode, but the other 6513 had a domain in server mode.  When I plugged in my 3750 stack (also in server mode), down went the VLANs.  If you know how VTP works, you know that the domains have to match in order to give it the opportunity to take down the network.  It turns out that the 3750s and the other 6513 (the healthy one) were configured with same VTP domain.  It's not our standard practice to use the same VTP domains everywhere, but, for some reason the other 6513 was configured with the VTP domain of another location. 

> Moral of the story:  Go through your configuration checklist no matter what.  If I would have taken 5 more minutes in configuration, we would have saved over an hour of downtime.  
> Moral #2:  Just because two devices are supposed to be configured the same doesn't mean that they are.  Always check.  
>  Moral #3:  Be consistent in your configurations.  Most configurations should be predictable.

Finally, after about 6 hours or so, everything was up and running.  The replacement module arrived, but management said that we couldn't take another outage to do the replacement.  I can understand that they don't want any more downtime and wasted time, so I have no problem with that.  There's an old adage, though, that says that nothing is temporary.  I'm not expecting to get those 3750s out of that rack for quite a while.

Oh, yeah.  The 2 1/2 hour drive back home sucked.

Send any stiff drinks questions my way.
