---
title: "Dynamips and Dynagen"
date: 2007-11-02
tags: 
  - "cisco"
---

I've run across articles for these apps a thousand times, so I thought I'd get in on the action. [Dynamips](http://www.ipflow.utc.fr/index.php/Cisco_7200_Simulator "Dynamips") and [dynagen](http://www.dynagen.org/ "Dynagen") are a pair of apps that make simulating Cisco routers very easy. I use them constantly at the office (and even at home on the couch) to try out new configs and even new IOS versions.

Dynamips is the brains behind the operation. It was written to simulate Cisco 7200s for testing, but, eventually, it came to support several platforms, including 3600s, 3700s, and 2600s. You can use it to simulate a whole series of routers that are directly connected together through their interfaces, through virtual switches, or even connected to real interfaces on your box to pass traffic out through the real network. It uses real \]IOS images, so you can run whatever you can download. The problem with it is that it's very complicated to use; if you did a fully-populated 7206, your command line would be 5 lines long and not make a lot of sense.

A guy named Greg Anuzelli came along, though, and gave us dynagen. Dynagen utilized the hypervisor functionality of dynamips to make a very easy configuration interface. Now, you can configure a lab by editing an INI-like file and run it against dynagen, which shoves all the right stuff into dynamips for you. It's easy as cake.

I'll save the details for later, but make sure you check out these guys. They save me so much time and effort at work and have become an invaluable part of my work day. Yes, it's that good.

I'll be sure to share some of my lab if they turn out to be interesting.
