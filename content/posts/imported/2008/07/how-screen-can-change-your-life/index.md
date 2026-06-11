---
title: "How Screen Can Change Your Life"
date: 2008-07-10
tags: 
  - "tools"
---

Alright, that's an exaggeration, but [screen](http://en.wikipedia.org/wiki/GNU_Screen "Wikipedia -- Screen") is pretty freaking cool.  It's an app that's (usually) run under Linux that lets you run commands then detach from that session and reattach later.  It doesn't seem like much, but a few examples can show what it does for me.

I have a backup script at home that takes a target file, tars up everything listed in there, zips up the new file, and puts it on an external drive.  It's very simple but takes about 3 hours to run.  I run it manually, so, in normal circumstances, I have to SSH in to my box and keep that window open for 3 hours while the backup runs.  With screen, I can open a new shell, run the script, and detach from it while everything gets backed up.

To do this, I log into my box and simply type _screen_.   This takes me to a new shell that's no different than the one I got when I first logged into the box, and, from here, I run my backup script and watch it dump output like it's going out of style.  When I see it's running as expected, I do a _Ctrl-A, D_ to detach from the session and return to my original shell.  From there, I can do my other business or just log off.  When I want to check status, I log into the box again, type _screen -r_ to reattach, and I'm back at my backup session.

How about something more network-dude(tte)-based?  In the past, we've had issues with our VPN kicking us off at random times while we're trying to do some maintenance.  This sucked pretty badly for us when we were doing log archive searches or running custom reporting scripts that may each take several minutes to run  -- when we got kicked off, we lost everything we had.  Since we weren't the guys doing the VPN at the time, we wound up using screen to help alleviate some of those problems.  We would VPN in and connect to one of the Linux management servers.   From there, we would open a new screen session and do our work.  When the inevitable boot came around, we could just reattach to the screen session to find our stuff still running.  That saves a whole mess of frustration when something happens at 03:00.

What else?  I've mentioned in past articles that I use screen to run dynagen labs -- I have a shell for dynamips, one for dynagen, and one for each console that all run in the same screen session.  I can use my function keys to add new shells, navigate among them, and detach when I'm done. I editing my _.screenrc_ file on my lab box so that I get the same setup just by typing _screen_. I stole most of this off the Intrawebs, but here's my .screenrc file.  It sets up the function keys for navigation and opens (and labels) the multiple sessions for my labs.

> bindkey -k k7 detach bindkey -k k8 kill bindkey -k k9 screen bindkey -k k; title bindkey -k F1 prev bindkey -k F2 next termcapinfo xterm ti@:te@ term vt100 multiuser on shell -$SHELL screen -t dyanmips 0 screen -t dynagen 1 screen -t R0 2 screen -t R1 3 select 0

Check the man pages or ask me for more details.
