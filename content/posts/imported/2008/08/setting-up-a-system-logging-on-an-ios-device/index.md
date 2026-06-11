---
title: "Setting Up System Logging on an IOS Device"
date: 2008-08-11
tags: 
  - "ios"
  - "tools"
---

I like logging on an IOS device.  I like to look at the buffer and tell you that your interface went down 30 seconds ago.  I like to look on the box and see that BGP with my Internet provider has been flapping since 02:13ET.  I like to look and see that one of the other guys has been making changes to the gear all morning.  I could go on and on.

There are lots of ways to monitor a Cisco box -- SNMP polling, SNMP traps, _show_ commands, etc. -- but there's nothing so handy as the log buffer.   A _show logging_ can provide you all sorts of information on things you do and don't care about, so it's important to know the destinations and levels when setting up logging.

There are four logging destinations.

- Console -- logs to the console ports of the device
- Monitor -- logs to any device or pseudo-device that's in monitoring mode.  The most common application is when you do a _terminal monitor_ to see output of debugs.
- Buffer -- logs to a memory device that let's you see the log messages on demand.  It has a finite size and scrolls old messages out after X bytes have been written.
- Host or Trap -- logs to an external syslog server.

What's the most important destination?  There's not one.  I personally thing the syslog host is the most important since it allows you to log messages to disk on a server somewhere.  The buffer is also important since it lets anyone with access see what's going on with the device.  Your mileage will vary depending on what you have set up.

There are eight logging levels as well.

- Debug - level 7
- Informational - level 6
- Notifications - level 5
- Warnings - level 4
- Errors - level 3
- Critical - level 2
- Alerts - level 1
- Emergencies - level 0

Wow.  There's some numbers.  What does it mean?  Every logging message comes with a level.  If my CSM loses a RIP, it generates a level 6 message (%CSM\_SLB-6-RSERVERSTATE) telling me.  If I configure the box, I get a level 5 message (%SYS-5-CONFIG\_I) saying that I've done so.  If my router is on fire in the rack, I'll get a level 0 message telling me.  Now, when you configure a destination, you have to give a logging level, and every message at that level or below will be logged.  If I set my logging buffer to 5, I'll see the configuration message and the "Oh, the humanity!" message, but not the RIP failures.  If I set it to 0, I only see the emergencies.  If I set it to 7, I see everything.

Let's do the configuration, then.  After hours or research, you've decided to use a remote syslog server at 1.2.3.4 for warnings and the buffer for informational.  Here's what you'd do.

> `logging host 1.2.3.4 logging trap warnings logging buffer information`

It's not that hard.  You can even use the number instead of the words for the logging level if you would like.  The same procedure holds true for the console and monitor mechanisms -- logging <mechanism> <level>.  Easy.

If you care, here's what I usually run.

- Console -- off.  I've seen a console rendered unusable because the console was being obliterated with syslog messages.  Not only is it an issue with being able to see what you're typing when stuff is scrolling, but some older devices wind up using 100% CPU because they're sending messages to the console.
- Monitor -- debug.  It doesn't really log anything unless you do a _term mon_ or something, and, in that case, I want to see my debugs.
- Buffer -- informational, but it depends on the device.  It lets me see all the messages except for debugs, which is probably just right for most routers and switches.  If you're switch is in a closet somewhere with users plugged directly into it, you may be flooded with up/down messages, so keep an eye out for stuff like that.
- Host or Trap -- informational.  Debug's a little too much for the corporate environment, but, depending on how much disk space you have, you may be able to handle it.

There's a lot more to syslog and log messages, so see [this nifty Cisco page](http://www.cisco.com/en/US/docs/ios/12_4/system/messages/Vol2/sm_over2.html "Cisco.com -- Cisco IOS System Messages").
