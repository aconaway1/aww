---
title: "Fallback IPtables"
date: 2007-08-11
tags: 
  - "security"
---

The hardest part of messing with firewall configs is knowing what is going to lock you out of the firewall itself.  It doesn't to me very often, but I've been doing firewalls for 10 years now.  I was thinking about my own IPtables implementation at home and realized that I do most of my tweaking remotely.  If I were to fat-finger something, I'd have to get on the console, and everything would be down until then.  I don't need a lot of uptime at my house, but I really can't stand downtime, but I digress.

Since I use IPtables at home, I took a look at some of the inner workings and found the "save" option.  This writes your running configuration to a file _/etc/sysconfig/iptables_.  Since IPtables doesn't write this file automatically, you can use this mechanism to keep a known-good copy on disk.  So how?  You can use this command to restore the good config.

> iptables-restore < /etc/sysconfig/iptables

If you stick this command in root's cron to run every 15 minutes, you can make any changes you want, and, if you hose it up, the known good config will be restored in a few minutes.  When you make changes and everything seems fine, you just do a "/etc/init.d/iptables save", and your new config is saved off for later.

By the way, I use "iptables-restore" to make any changes.  I have a file that I make changes to and then use the syntax above to apply it.  The command actually flushes all the tables and puts the config in the file back, so you don't have to remove any rules from memory if they conflict with new rules.  Remember that order is important when configuring IPtables rules.
