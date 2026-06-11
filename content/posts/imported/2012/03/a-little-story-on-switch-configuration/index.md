---
title: "A Little Story on Switch Configuration"
date: 2012-03-27
categories: 
  - "cisco"
tags: 
  - "configuration"
  - "story"
  - "switch"
---

Here's another story from the late night.  I've changed the details to protect the innocent, but you'll get the idea.

I think most of you know that I started a new job late last year, and I've spent my waking hours getting caught up on how the new company works, how everything fits together, and all that jazz.  One of the big reasons that I (and a number of others) were brought in was to fix the biggest problem; the company doesn't have a real central control over customer-facing technologies.  There's a group that does central IT for the company (Exchange, SharePoint, Oracle apps, etc.), but there are dozens and dozens of applications out there.  That means there are dozens of "network teams" around the world doing their own thing.

One of those groups gave me a call the other day for some help.  Their stack of old 2950s was having some issues, and they needed my help to figure out what was going on.  Among the symptoms were flapping interfaces on the firewall and - the best of all - every command was greeted with an memory error.  Want to see the config?  Too bad.  Want to see the memory utilization?  Too bad.  How about configure the thing?  Too bad.  The only command that I could actually get to work was a _show version_, but that's pretty pointless when trying to troubleshoot issues.

So, what did I find?  Nothing that could help with the problem, but plenty of stuff to fix.  Bascially, the switch has VLAN 1, it's layer-3 interface, and a single username configured.  Nothing else. The configuration items that I consider to be basic just weren't there thanks to the group's network guy being a jack of all trades and master of none.  Does putting every host on VLAN1 work?  Sure it does.  Would you just turn on your switch and not configure anything?  I hope not.  Does someone who does networking part-time thing it's a problem?  Obviously not.

So, what was missing?  For starters, there was no syslog server configured (or even existed on the network at all).  That's a problem since the only way that I could see the logs was to reboot the switch and try again.  What did the logs say when I finally rebooted the guy?  Nothing since the buffer is empty, but the logs for the boot messages started with "1h3m" by the time I got back to it.  That means the _service timestamps_ commands for logging were missing.  That lead me to ask what the time was on the box.  Did you guess it was March 1, 1993?  Yeah - no NTP server set, either.  Without these basic configuration items, the odds of doing any troubleshooting are just about zero.  Actually, they are zero in this case.  The basics were missing, and now we have no idea what the real cause of our problems was.

I found a whole mess of other issues, too.  The second switch was connected over an access port.  No password encryption service.  Both switches were unconfigured VTP servers.  Not a single interface description.  My OCD definitely kicked in that day.

I guess I'll have to work for my paycheck this week.
