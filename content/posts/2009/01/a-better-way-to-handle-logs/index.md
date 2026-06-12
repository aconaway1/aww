---
title: "A Better (?) Way to Handle Logs"
date: 2009-01-19
tags: 
  - "logging"
  - "syslog"
  - "tools"
---

Happy new year, all.  I'm finally over my hangover from the party and ready to blog.

Everywhere I go, I always wind up in a debate about how to alert on log messages as they come in.  I was at the grocery store yesterday, and the cashier told me that she had a list of log messages that she watched for, and, if she saw one of them, she sent an email.  I asked her what she would do if she got a log message that she had never seen before, and she said that she would have to find it first, then research the message and put in an alert for the next time it showed up.

This is probably a decent method for most setups, but what if that one message was that someone was DOSing your Internet router or that your firewalls were all at 100% CPU?  My preferred logging method (and others') is to send an alert on every message and, if I don't really care about it, to filter it out for the next time.  Now, when I see a message that hasn't come in before, I know that something has happened instead of finding the message after all my routers have died.

The big disadvantage to using this method, though, is the noise you'll get at first.  If there are no filters on the messages, you'll see denies from your firewalls, port up/down from your switches, and a whole bunch of other messages that you get 4849278 times a day.  If your network is large enough, you may get so many that you fill up your syslog server.  That sucks, but it's it's the price you pay for being able to know when something unknown is happening.  As the alerts DB matures, you usually only see stuff that doesn't come up very often -- like when a power supply dies or someone is attacking your web server.

Just some food for thought.
