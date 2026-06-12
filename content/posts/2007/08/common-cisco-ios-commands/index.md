---
title: "Common Cisco IOS Commands"
date: 2007-08-17
tags: 
  - "cisco"
  - "cli"
---

Here's a list of IOS commands that I use all the time that aren't a part of the basics. I obviously use more than just these, and you do, too, but I hope there's at least one eye-opener in there.

**_show env all_**: Shows the environment status, including fan, power supplies, etc. Good for making sure the environment is alright. **_show history_**: Shows your command history since you logged onto the device. Good for remembering what command you put into get those stats the boss needs. Configuration changes don't show up here. **_show inventory_**: Shows a nice list of what the device has hardware-wise. It's good for a router with a bunch of modules or a switch with a bunch of cards. **_show interface trunk_**: Shows all the trunks on a switch along with pruning information. Good for making sure all VLANs are propagating around the network. **_show interface capabilities_**: Shows what the interface is capable of doing -- not just what's its configured to do. **_show interface counters_**: Shows byte and packet information for every interface. Good for quickly showing statistics without having to look at all the _show interface_ garbage. **_show mac-address-table_**: Shows the CAM table on a switch. Good for tracking down where a host is plugged into. **_show tcp brief_**: Shows all TCP connections associated with the device like SSH sessions or BGP. **_show users_**: Shows who's logged onto the device. Good for finding a line to clear to kick everyone off the box.
