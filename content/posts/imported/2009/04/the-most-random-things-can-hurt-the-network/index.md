---
title: "The Most Random Things Can Hurt The Network"
date: 2009-04-16
tags: 
  - "bandwidth"
  - "camera"
  - "network"
  - "paper"
  - "utilization"
---

This is a great one that I have to share.

A couple of coworkers walk in today and ask for some help on an issue.  It seems that a business unit was having latency problems with a web app, and, after research by the product team and sysadmins, nothing wrong could be found.  Lots of sites use the product, and only this one was having issues.  Also, the site was having no problems getting to other web sites and apps like Yahoo! or Google.

I informed the guys that their Internet access went over a T1 to an ISP, but, since the application was housed at our place, access to it was actually over the WAN circuit.  The guys told me that the unit had just called to complain about it again, so we checked out their WAN circuit.  Eureka!  The circuit was at 98% utilization.  There's your cause.  The app is slow because the circuit is full.  Looking back through history, we see weeks of high utilization that explains why the users are having issues with the app.

Of course, the next logical step is to find out what's talking so much.  I pulled up our netflow box to see what conversations were the big talkers.  I found a single IP at the site talking to a single IP at our home office via HTTP and HTTPs that accounted for 40% or so of the total bandwidth usage in the last 24 hours.  And the previous 24 hours.  And the previous 24 hours.  Hmmmm.

I didn't recognize the IPs, so I asked the systems guys what they were.  They had no clue, and neither IP was reserved in our IP management system.  Quite strange.  I ran NMap against both boxes; that fine app told me that they were both printers.  Printers?  That makes no sense.  I noticed that HTTP was open (we saw that in netflow, too), so we pointed a browser at the one at the home office to see what turns up.  It's the security system.  That makes even less sense.  Pointing a browser at the other IP, we find that it's a security camera that's looking at some server racks.  Great!  Somebody's streaming video over the WAN.

We march upstairs to talk to the security guys.  They're not streaming directly but pull up their app and notice that the camera in question has been sending 10 seconds of video every few minutes for the past 6 weeks.  The cameras only send video if their detect movement in the room, so what's going on?  You can expect several movement events in a "computer room" as they call it, but people go home at night, and you shouldn't see movies coming across for weeks at a time.  We pull up a few clips to see what the camera saw.

We see people going in.  We see people going out.  We see the rack doors open.  We see the usual stuff, but most of the videos are nothing.  Ten seconds of no change.  Finally, though, we see it.  Something stupid.  Someone had taped a piece of paper to one of the racks, and that guy was moving ever so slightly.  I couldn't even really detect the paper itself moving; I could only see the text on the page move just a tad.  The camera saw it, though, and was doing its job and recording back to the home office.  A piece of paper took up 40% of the WAN circuit?  It's always something new, eh?

I really hope they get our message to take down the paper.
