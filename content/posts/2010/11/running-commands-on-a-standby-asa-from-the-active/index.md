---
title: "Running Commands on a Standby ASA from the Active"
date: 2010-11-22
categories: 
  - "asa"
tags: 
  - "asa"
  - "config"
  - "exec"
  - "failover"
---

I was exploring commands on the ASA a while back and discovered that you can run commands on the standby unit from the active.<!--more-->  It's a bit weird, though, since you actually run the commands from config mode.

As an example, if you want to do a _show interface OUTSIDE_ on the standby unit to see what the status is, you would do this.[](http://www.konradscollision.com/)

> ```
> firewall(config)#failover exec standby show interface OUTSIDE
> Interface Ethernet0/0 "OUTSIDE", is up, line protocol is up
>  Hardware is i82546GB rev03, BW 1000 Mbps, DLY 10 usec
> <SNIP>
> ```

Pretty handy when you want to know information about the other firewall without having to log into that sucker (and getting confused by the same prompts and reloading the wrong mate).

Send any ~~misplaced commands~~ questions my way.
