---
title: "A Simple BGP Lab with Dynamips/Dynagen"
date: 2007-11-10
tags: 
  - "cisco"
  - "linux"
  - "tools"
---

I assume you take every word I say to heart and that you've been using Dynamips/Dynagen for a few days now, right? Good. That's a start, but let's break down a simple lab to make sure everyone's on the same page. I run my labs on Linux most of the time, so you'll see my commands for that platform. You're a smart one, so you can figure out what to do on Windows. :)

First of all, everyone download [the lab file](http://aconaway.com/static/labs/bgp/bgp.net "AConaway.com -- BGP Lab"). This is a very simple lab that I created to do some experimentation with BGP. I modified it a bit to save resources for the general public, though; it's a lot easier to run 2 2651XMs than 2 7206s, right? Let's go through the lines.

> autostart = False \[localhost\]

Yes, that means don't start up the routers when you fire up dynagen. The second line is the dynamips server you want to use. This will always be localhost unless you're leveraging another box to use as the emulator. That's an advanced topic that I'm not going to cover right now, though.

> \[\[2651XM\]\] image = /home/jac/labs/images/c2600-adventerprisek9-mz.124-17.img ram = 96

These lines define the parameters for any 2651Xm that we'll be using. The image file is the actual IOS image to use. I can't provide an IOS file for you, so you'll have to download one yourself and change this line to where you put the file. Guess what the "ram" line is. Wow...you're a genius if you said it was the amount of RAM to give each 2651XM. :)

> \[\[ROUTER R1\]\] s0/0 = R2 s0/0 f0/0 = LAN 1 model = 2651XM
> 
> \[\[ROUTER R2\]\] f0/0 = LAN 2 model = 2651XM

Here's the meat of the lab. We're creating 2 2651XMs, called R1 and R2, that each have s0/0 and f0/0 interfaces.

The 2651XM has 2 FastEthernets, so, when you fire up the lab, the routers will have those interfaces, but we don't care about f0/1 in the scope of this lab. We'll just ignore it for now. The "LAN" keyword in the f0/0 lines tell how you want the FastEthernets cabled up. We're trying to do BGP here, so the FastEthernet interfaces are connected to different network (R1 to LAN 1 and R2 to LAN 2).

Notice that, in the R1 configuration, we say that R1's s0/0 is connected to R2's s0/0. This lets the routers talk across the serial interfaces in the virtual world. In our lab, this is the link that we're going to run BGP over to share the paths to the f0/0 interfaces. Very simple setup.

So, let's fire this guy up. First, make sure you've started your dynamips server. I run mine in a [screen](http://en.wikipedia.org/wiki/GNU_Screen "Wikipedia -- Screen") session to get it out of the way, but it's your choice. Run "dynamips -H 7200" to get going and listening on port 7200. When that's up and running, you simply run dynagen against your lab file with a "dynagen bgp.net".

If everything is right, you'll be see the "=>" prompt. Remember that we set autostart to false, so we have to start up our routers. You can type "start R1" and "start R2" to get them going, but it can be easier to do a "start /all". You may run into problems with CPU or memory if you do that, though, so be careful.

If you do a "list" at the dynagen prompt, you should see both of the routers in a "running" state. That's good, but now what? Time to get on the console. If you're running your lab on your workstation (the box that's attached to the keyboard you're typing on), you can do a "console /all" to bring up all the consoles at once. If you're doing the lab remotely, you'll have to telnet to the right port to get a console. That info's in the last column of the "list" command.

Now comes the configuration, so get to it. Configure these guys to share their routes on the f0/0 interface via BGP. Experiment a little. Experiment a lot. You're not going to break anything, but remember to save the configuration when you're done. You can reuse the lab later.

\-----

If you're lazy, you can use the configs that I used for this lab. They're very, very simple, but they work.

- [R1](http://aconaway.com/static/labs/bgp/R1.cfg "AConaway.com -- R1 Config for BGP Lab")
- [R2](http://aconaway.com/static/labs/bgp/R2.cfg "AConaway.com -- R2 Config for BGP Lab")
