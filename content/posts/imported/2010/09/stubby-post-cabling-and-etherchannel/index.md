---
title: "Stubby Post - Cabling and EtherChannel"
date: 2010-09-13
categories: 
  - "cisco"
tags: 
  - "bundle"
  - "cdp"
  - "etherchannel"
---

I've done it.  You've done it.  We've all done it.  You turn up another EtherChannel bundle and realize the hard way that your interface descriptions aren't accurate.  Or you've swapped out a [piece-of-crap 3750](http://aconaway.com/2010/08/30/catalyst-3750s-bad-luck-with-a-cisco-logo/) and didn't notice that the labels on the cables were wrong.  In either case, we all know that EtherChannel bundles don't really work if the links aren't plugged into the right switches.

So, what do you to make sure that your links are cabled the way you think they are?  Personally, I don't trust any label at all - no matter if I did it or not.  At some point, someone has changed something on a switch, and that just might have been a change to where the port is question is cabled.  If I was onsite, I would hand-trace the cabling from one end to the other then do it again to make sure I didn't hose it up the first time.  The big problem with this technique is that I'm not everywhere at the same time, and the travel budget isn't very big these days.  If I can't get my hands on the cables, I relegate myself to using CDP to see what's on the other end of links when putting ports into EtherChannel bundles.

For the cases of adding a new bundle, I will configure the individual ports, cable them up, make sure they go to the same switch, then configure the channel group.  In cases where you do a swap, you already have the config applied, so it's a matter of verifying everything was cabled correctly.  A quick _show cdp neighbor_ will show you where the other ends of cables are and let you know if there's a problem before users start complaining.  A problem already exists, mind you, but you can fix it quickly if you look at CDP.  Let's hope the misconfigured bundle isn't between you and the switch, though, or you may be SOL.

Send any mislabled ports questions my way.
