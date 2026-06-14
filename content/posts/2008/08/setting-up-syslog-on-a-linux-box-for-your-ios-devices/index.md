---
title: "Setting Up Syslog on a Linux Box for Your IOS Devices"
date: 2008-08-26
tags: 
  - "tools"
---

A few articles ago, [we discussed](/posts/2008/08/setting-up-a-system-logging-on-an-ios-device/ "AConaway.com -- Setting up System Loggin on an IOS Device") getting logging up and running on your IOS box.  Part of the discussion was actually having the device log remotely to a box somewhere, but that's kind of worthless without a properly (for definitions of proper) configured syslog server.  A low-end Linux box with an appropriate amount of disk space is a really good candidate to do this for you.  I'll assume you're running some Redhat-based distro.

I won't go through the installation, but it should be easy.  Just look for the syslog packages for your distro and you should wind up with a working copy on your box.  On a Redhat distro, you'll probably just do a _yum install syslog_ to get it working.

The first thing you need to do is to configure the daemon to listen for remote machines, so open in _/etc/sysconfig/syslog_ in your favorite editor (read: vi) and change the SYSLOGD\_OPTIONS line to read this.

> SYSLOGD\_OPTIONS="-m 0 -r"

By default, if you restarted the daemon now, you'd wind up sending all your syslog messages to _/var/log/boot.log_.   That may be alright for you, but, if you have a lot of devices, you may want to log them to another file.  To do that, you need to change the local7 line at the bottom of _/etc/syslog.conf_.  Just add this and comment out the original line.

> #  Write router messages to /var/log/cisco.log local7.\*       /var/log/cisco.log

<NOTE> This is not the best way to do handles messages from IOS devices, but it'll get your started.  You'll want to look at changing the facility or further filtering the logging based on facility and severity. In several setups I've done, the devices all log to files based on function -- routers to one file, firewalls to another, switches to another, etc. -- and those files are rotated every X hours. </NOTE>

That's all the configuration you need, so let's restart the service for everything to spring into action.

> /etc/init.d/syslogd restart

In a perfect world, when your IOS devices are configured properly, you'll have a nice log of IOS messages to keep for posterity.
