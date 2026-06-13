---
title: "A Simple Firewall Upgrade - A True Story"
date: 2013-06-20
categories: 
  - "asa"
  - "cisco"
tags: 
  - "cisco-asa-conversion-upgrade-5555x-5520"
---

I just got through a big weekend.  We upgraded our main production firewall, but the process had a few twists.

The old firewalls, a pair of ASA 5520s, were running at about 80% CPU during the day.  That’s high enough that even I cringe when I saw the utilization in ASDM.  It was obviously time to upgrade to something with more beef, but we also wanted something that will last for years.  After looking around and getting some quotes (that made me jump back in my seat), we finally decided to go with a pair of 5555Xs.  These guys give about 10 times the throughput of the 5520 with about 8 times the memory.  Seems to match the requirements.  Now for the complications we had to work through.

The old firewalls were running 8.2 and some change, and, as we all know, there is a huge jump in config from 8.2 to 8.3.  I won’t go into the details of the differences, but I dreaded having to go over that hump.  We thought that maybe we could just do a platform move as-is, but the 5555Xs actually don’t run anything below 8.6; that settled that.  Since the jump over 8.2 is the one to worry about, we decided to go with the latest-and-greatest 9.1(2) code.  We’ve already got a pair of ASAs running 9.1(1), so it’s not like it’s something foreign to us.

We wanted to have a nice safe bosom to snuggle up to if something went wrong, so we decided to leave the old firewalls in place just as they were.  We talked about upgrading to 8.4 in one window then move to the new 5555Xs later, but we didn’t want to take two outages.  The plan was to install the 5555Xs, swing over to them, then move back when if something went wrong.  That means we’re left with taking an old config, converting it over, putting it on the new platform, and swapping them over.  A big ordeal for one window, so we had to get right into the preparation.

After trying to figure out how to actually do the conversion, we figured that we just had to load the config up and let the firewall do its thing.  I’m surely glad, though, that we are all paranoid and wanted to do the conversion well before the window.  The first time was at about 3pm on a Monday.  When I got back into the office the next day, it was still running.  I figured out where in the config it was and did some math to see how much longer it would take.  Carry the 1…plus 4…divide by pi…12 days.  Yes, 12 days.  Let me say that again.  It was going to take 12 days!  One more time…12 days!  What was happening?

Two of the big changes from 8.3+ were hurting us.  The first is that the NAT syntax is different; a big chunk of the NAT commands had to be converted to object-based NATs.  The conversion process had to find each _static_ and convert it over to objects.  We didn’t have that many NATs (I think I counted 53), so that wasn’t what was causing the problem.  The big culprit was the conversion of the ACLs.  In 8.2 you use the NAT address (the inside global?) in the ACL.  In 8.3+, you use the real address (inside local?), so each ACE has to be expanded and changed to their real IP address.  All sources.  All destinations.  All lines.  All ACLs.  That’s usually no big deal, but the company I’m working for has a set of whitelists that are pretty big.  That’s not true.  They’re f&$^#\*ing huge.  The main ACL expands out to 521,000 ACEs.  Yes, over half of a million lines that each need to be converted.  Guess how long that takes.  I would say about 12 days.

The fix was easy.  We just had to find out what the huge ACEs were, take them out, and convert them by hand.  So, how do you do that?  Looking at the config doesn’t show which lines are any bigger than the others.  Doing a _show access-list_ only shows how many elements are in the entire ACL and not the individual lines.  I wound up writing a terribly-constructed Perl script that goes through the output of _show access-list WHATEVER_ and telling you how many elements are in each line. *(The Perl script referenced here is no longer available.)*

The output was kind of scary, actually. We were able to see several lines above 100k elements and several more in the hundreds.  We picked a target size of 300 elements and checked out all the lines longer than that.  Some luck landed on our laps this time; most of the nested object-groups that made the lines so big were actually customer IP addresses and the rest were our own real IPs.  Of all the big hitter lines, only one referred to a NAT address, so we only had to convert one single object-group to reals.  Phew!

Since the new firewalls are much beefier than our old ones, we decided to go with an EtherChannel so that the links wouldn’t be any sort of bottleneck.  If we changed the interface config after the conversion, there were issues.  Anything that references the _nameifs_ would be removed from the config as soon as I blew away the original interface.  Makes sense, but that means NATs, access groups, failover monitors, AAA servers, etc., would up being unassociated or even removed.  The fix there was to change it before the conversion.  Pretty easy.

Another struggle over, so we tried the conversion again.  We loaded up the config, copied it to start, and rebooted with our fingers crossed.  Thirty-nine minutes later, the config was running on 8.6.  I would say that qualifies as a decent improvement, don’t you?  After putting the big hitters back in (along with a few minor changes), we saved the config and rebooted to 9.1(2) without incident.  Woot!

It was quite a few weeks discovering issues, fixing them, then waiting an hour to see what happened.  The end result, though, was a brand new pair of 5555Xs running 9.1(2) that run at 18% CPU during peak.  I feel better already.

Send any Cisco Live meetup schedules questions to me.
