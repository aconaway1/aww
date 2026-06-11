---
title: "Using SSH to Run Commands on a Router or Switch"
date: 2009-04-30
tags: 
  - "cisco"
  - "cli"
  - "csm"
  - "grep"
  - "privilege"
  - "router"
  - "ssh"
  - "switch"
---

SSH is more than just a shell.  You can copy files from and to a server or piece of network gear with it.  You can use it to tunnel traffic.  Possibly my favorite, though, is to use SSH to run a command on a remote box without interacting with a shell.

One of my biggest pet peeves with IOS (or pretty much any Cisco OS) is the lack of complex filtering.  Let's say I want to look at all the downed ports and interfaces on modules 3 and 6 of my 6509.  I can't easily do that with command from the IOS, but, on my Linux box, I can use multiple _grep_ commands to get exactly what I want really easily.  Let's work through the example, shall we?

To start with, let's just do a _show ip int brief_ without getting a shell on the switch.

> ```
> ssh my.switch.com "show ip int brief"
> ```

When you run this and give your password, you see the output we've all learned to love, and, now that you've got it in STDOUT on your Linux box, you can start filtering. Now, let's use _grep_ to find the downed ports and interfaces on modules 3 and 6.

> ```
> ssh my.switch.com "show ip int brief" | grep down | grep Ethernet[36]
> ```

How about downed ports and interfaces on modules 3 and 6 that not administratively down?

> ```
> ssh my.switch.com "show ip int brief" | grep down | grep Ethernet[36] | grep -v admin
> ```

I'll stop there, but it can go on and on.  Read up on regular expression and/or grep if you don't know what we're doing here.

What's really happening is that we're taking the output of the command "ssh ...." and piping it (with |) to the command _grep_.  We can send it to whatever command we want, though, so don't be shy.  I've actually written several scripts that take output of commands like _show int description_ on a router to generate some reports.  When I want to run one of those, I do something like this.

> ```
> ssh my.switch.com "show int desc" | parseOutput.pl
> ```

There's always a gotcha or two to watch for, isn't there?  I've found a couple.

First, your command runs at your privilege level, so, if your user is priv 1, you're not going to be able to do a _show run_ or _reload_.  You could just ignore security for a bit and set your privilege to 15, but I don't recommend doing anything like that.  Before you say it, you'll probably have a hard time with enabling as well.  You can only run one command at a time, so you would just enable yourself and get kicked off.  Not very helpful.

Another problem I see is the lack of public/private key pair support on Cisco devices.  On a Linux box, you can copy your keys around, and those are presented in lieu of a password.  Since (most) Cisco devices don't have home directories, there's no place to drop the keys, and we're left with just using passwords.  Support for this would be nice, but the security problems associated with keep SSH keys and user home directories are probably too much to even think about.

What else?  Oh, yeah.  The PIX/FWSM/ASA family supports SSH, but it acts differently from the IOS guys.  When you run a command through SSH, you actually get an interactive shell with the command already on the CLI for you. This is probably by design; the only thing you can really do from a non-priv prompt is to _enable_.

Anyway, send any grilling tips questions my way.
