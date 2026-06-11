---
title: "Resetting Sections of the Config"
date: 2008-03-18
---

I was configuring a switch the other day and realized I had configured a trunk on the wrong port. God, I hate that. Instead of dumping the configuration for the port and doing a "no" on each line, I used the _default_ command.

> Switch(config)#default interface G0/1

This resets the configuration on interface G0/1 to how it was when Cisco shipped it to you. Much better than killing all the lines of the configuration one-at-a-time, eh?

_Default_ can be used for all sorts of stuff, too. You can reset the configuration for CDP, AAA, NTP...pretty much anything. It can come in handy if you want to reset just one part of the config without touching everything else you've configured on the box.
