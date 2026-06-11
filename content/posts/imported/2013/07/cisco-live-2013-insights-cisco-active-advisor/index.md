---
title: "Cisco Live 2013 Insights - Cisco Active Advisor"
date: 2013-07-02
categories: 
  - "cisco"
tags: 
  - "active"
  - "advisor"
  - "caa"
  - "cisco"
  - "inventory"
  - "live"
  - "management"
---

Yes, I went to Cisco Live and survived.  It was the social event of the year, but the main focus is learning about the cool, new stuff.  One of the booths I visited was a demonstration of [Cisco Active Advisor](http://www.cisco.com/en/US/products/ps13221/index.html).

This is a cloud-based (BINGO!) application that keeps an eye on the lifecycles of your IOS devices.  Using the web interface, you can scan a range of IP addresses from your machine and have your gear automatically added to the service.  Once in there, you can see, among other things, the warranty and support contract information for your device.  If your contracts is about to expire, it'll let you know via email.   It also tracks any vulnerabilities that may apply and emails you if any are detected.  This beats trusting your reseller to send you renewals or watching an RSS feed for PSIRTs and field notices.

The best part of CAA, though, is the price.  It's FREE!  Definitely the cheapest thing in the world with a Cisco logo on it.

Like anything that's new and anything that's free, there are some issues.  The first is that it only supports IOS devices.  There's no support for your ASA or your Nexus switches.  Another problem is the fact that it runs on Java.  We all absolutely love Java, don't we?  Ick.  The biggest problem to me, though, is the fact that you can't manually add devices; you have to use the scanner or Cisco Network Assistant.  This will be problematic for networks where you use jump hosts to access certain networks with  no direct access.  It's also not an inventory system; it doesn't keep track of everything.  You won't get a report of IP addresses or interfaces here.  That's for your spreadsheet or real tool.

CAA has some nice functionality, but I would like to see some more features.  Interfaces and networks would be good.  Maybe some configuration management (but I'm not putting my config online like that).  I'm still going to use it to track support contracts.

Send any bullshit bingo cards questions to me.
