---
title: "Using Probes on the CSM"
date: 2008-11-06
tags: 
  - "catalyst"
  - "csm"
  - "ios"
---

There are three different ways that a CSM checks for the health of the servers -- active probes, inband health checking, and inband HTTP monitoring.  Let's talk about active probes.

Active probes (or just probes) typically send traffic to one of the RIPs of a serverfarm, do some stuff, and give a pass or fail grade.  If the probe fails a certain number of times in a row, that server is considered sick and taken out of the pool for use.  The CSM keeps checking the unhealthy until it passes a number of times in a row, at which point it is placed back in the pool for use.  Almost everything is configurable, of course, so let's look at some of those settings.

These all have their defaults, so you don't need to actually configure them, but it's important to know they're there to tweak later.  There are other parameters to the more specific types of probes as well.  You'll have to venture forth on your own to figure those out, though I'll be glad to help.

- interval:  The time between probes when the server is healthy.
- retries:  The number of consecutive failures before a healthy server is considered failed.
- failed:  The time between probes when the server is failed.
- recover:  The number of consecutive successes before a failed server is considered health.

I always said that the CSM only speaks HTTP, but it knows how to order a ham and cheese sandwich in a few other protocols, including doing some decent stuff like watching for SMTP banners, looking for ICMP reachability, or getting a response from a Tcl script.  Here's a list of the probes with some boring commentary.  Depending on IOS versions, you may have more or less available to you.

- tcp: Establishes a connection to a TCP port.  If the port is open, we pass.
- udp: Same as TCP, but in UDP.  Duh.
- icmp: Ping-a-ling.  Do I need to explain this one?
- http: Requests a URL from a webserver and looks for HTTP return codes.
- dns, smtp, telnet: These guys just attach to the service and look for a proper response header.  It doesn't do any transactions or anything but simply makes sure that DNS, SMTP, or telnet is running on the port.
- script:  These probes run a Tcl script (that you write) and look for the return code of the script.  Very powerful!
- kal-ap-tcp, kal-ap-udp:  Admittedly, I have no earthly idea what those are.  Most references I've seen to it involve the Cisco ACE, but I have no clue.  Can someone fill in for me here?

Shall we try one or two?  How about a TCP probe that makes sure your custom app is still running on TCP/8839?  Since it's a custom app, we can use a TCP probe on that port to make sure something is listening (If you need something more, you'll need to check out [script probes](http://www.cisco.com/univercd/cc/td/doc/product/lan/cat6000/mod_icn/csm/csm_4_1/icn/scriptg.htm "Cisco.com -- Configuring CSM Scripts").).

> probe MYAPP tcp description My app on TCP/8839 port 8839

Now we apply the probe to the serverfarm.

> serverfarm MYFARM probe MYAPP real 1.1.1.1 inservice real 1.1.1.2 inservice

Easy enough.  How about another?  I want to configure an HTTP probe that gets the URL /test.php and looks for the status code 200 and apply it to a serverfarm.

> probe LOOKFOR200 http request url /test.php expect status 200
> 
> serverfarm YOURFARM probe LOOKFOR200 real 2.2.2.1 inservice real 2.2.2.2 inservice

By the way, you don't want to use this in production at all.  You'll probably want to elect to look for ranges of status codes instead of a single value.  Google up HTTP return codes and you'll see why.

I will mention again, though, that the script probe is very interesting.  If you know Tcl or have done any development, check these things out.  You can do whatever you want with them instead of using the canned probes that come with your CSM.  I suggest taking a look at [Ivan Pepelnjak's page](http://blog.ioshints.info/2006/01/contact-me.html "blog.ioshints.info -- Cisco IOS Hints and Tricks") for some insight into Tcl on Cisco devices.

I think you can take it from here.  As always, comments are welcome.
