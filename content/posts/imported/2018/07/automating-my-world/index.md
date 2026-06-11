---
title: "Automating My World"
date: 2018-07-23
categories: 
  - "ansible"
  - "automation"
  - "python"
  - "rundeck"
tags: 
  - "ansible"
  - "automation"
  - "python"
  - "rundeck"
---

I've told this story 984828934 time in the past year, but bear with me.  We got a new director-type last year, and he has challenged all of us to do things differently.  As in everything.  Anything that we're doing today should be done differently by next year.  This isn't saying that we're doing things wrong.  This is just a challenge mix things up, integrate new tools, and get rid of the noise.  Our group has responded big-time, and we're now doing most of our day-to-day tasks with a tool of some kind.  A couple weeks ago, I realized that I did a whole day's work without logging directly into any gear -- everything was through a tool.  It was a proud moment for me and the group.

To kick off this new adventure, we're starting with writing all our own stuff in-house; we're obviously not talking about a full, commercial orchestration deployment here.  We've talking about taking care of the menial tasks that we are way too expensive to be doing.  Simple tasks.  Common tasks.  Repeatable tasks.  All game.  What's the MAC address of that host?  Need a new host added to an existing object-group in the firewall?  Adding a new VPN tunnel to a customer?  All easily scripted out.

As a group, we got together an decided on a few standard tools to use.  Don't read too much into that, though -- we didn't involve a full RFP process.  It's more of a handshake agreement, but it allows us to have a common base of skills so we can help each other out on the way.

Let's talk about those tools for a bit.

**Python** - For a basic language, we've decided to use Python.  This is a no-brainer since Python is easy to use, is very useful, has lots of functionality.  Just look around the automation world for 10 seconds and you'll see it in wide use.  An item of interest : Netmiko

**Ansible** - For doing a set of tasks across a big list of hosts, Ansible meets our needs.  It's got a bunch (maybe all) of Python on the backend, and there are all sorts of modules already available.  This took just about as much thought as selecting Python since it's one of the most popular automation tools out there.  An item of interest : Ansible Vault

**Rundeck** - This is a web-interface tool for running scripts either on-demand or on a schedule. We use it mostly to enable other groups to do tasks without our input since it has a decent way to control execution and inputs.

With these tools in place, the team and I are in great shape to change the way we're doing things.  Is this the end?  God, no.  This is very much the beginning while we get acquainted with automating ourselves out of our jobs.

This seems like a new line of blog posts, doesn't it?  Yep.

Send any syntax errors questions to me.
