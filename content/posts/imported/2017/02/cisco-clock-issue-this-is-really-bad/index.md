---
title: "Cisco Clock Issue - This Is Really Bad"
date: 2017-02-05
categories: 
  - "cisco"
tags: 
  - "advisory"
  - "cisco"
  - "clock"
  - "component"
  - "fault"
---

Check out [this advisory](https://www.cisco.com/c/en/us/support/web/clock-signal.html#~overview) from Cisco that came out a couple days ago.  You need to read it and act on it _immediately_!  I'll summarize for you : Thanks to a faulty clock signal component, certain Cisco devices will stop functioning after about 18 months and become really expensive bricks!  Reading through it, you'll see phrases like "we expect product failures" and "is not recoverable."  Seriously, what the hell? This really warms the heart.

The fault affects a couple Meraki devices, the Nexus 9504, and some models of the ISR 4000s - the ISR4331, ISR4321, and ISR4351.  The 4000s are part of Cisco's flagship branch routers, and I know several people (including myself!) who have some of the affected units deployed in production.  Some unnamed people on Twitter tell me that they have 50 and even 120 of these guys deployed in the field.  That's a lot of faulty clocks.

The fix is to open a TAC case and get a new device.  Cisco is using the word "platform" when talking about replacement, meaning that they'll send you a naked device.  If you have cards or memory upgrades or a particular software license, you'll need to make arrangements to get that moved over to the replacement device.  We're talking unracking, undressing, re-dressing, re-racking, upgrading, licensing, and configuring.  This is a complicated procure that is going to cost companies a lot of time and money.  I imagine companies all over the world are going to wind up flying their engineers all over creation to do hardware swaps before the 18 months have elapsed.  You can't wait until "the next time you fly out there".  You have to do something now (I refuse to say it's a race against the clock.).  I'm sure you have nothing better to do.

Cisco says that the faulty clock signal component is used by other companies as well, though I haven't heard of any other companies publishing advisories.  Cisco is also not revealing the name of the supplier, so there's no way to know what other products are affected.  I'm not trying to make your ulcers flare up, but it entirely possible that you may have other stuff in your network just stop working.  We're all waiting to see what happens next.

Send any Alka-Seltzer questions to me.
