---
title: "Loading Configs at Startup in Dynagen"
date: 2008-06-24
tags: 
  - "dynagen"
  - "tools"
---

Here's a quick one for you. In Dynagen, if you want to load a configuration when you first fire up the router instance, you can use the _cnfg_ tag in your NET file like this.

> cnfg = /home/jac/labs/cfg/R0.cfg

If you put that in your dynagen NET file under a router, the contents of that file will be loaded into the router configuration when it's brought up. This is great if you already have a configuration to use in another lab or if you want to load a basic configuration on startup. Please be warned, though; if you make changes to your router instance via the CLI and restart dyangen, the configuration changes you made will be gone.  Be sure to remove that line from the NET before you restart dynagen.
