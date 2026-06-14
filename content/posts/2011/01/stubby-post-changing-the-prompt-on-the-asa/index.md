---
title: "Stubby Post - Changing the Prompt on the ASA"
date: 2011-01-20
categories: 
  - "asa"
tags: 
  - "active"
  - "asa"
  - "cisco"
  - "context"
  - "domain"
  - "failover"
  - "hostname"
  - "primary"
  - "priority"
  - "prompt"
  - "secondary"
  - "standby"
  - "state"
---

RichardF commented on [an article I wrote last November](/posts/2010/11/running-commands-on-a-standby-asa-from-the-active/) and mentioned the _prompt_ command in the ASA.  I never set aside any time to research it, but I finally took the time today while waiting for a maintenance window.

This is one of those little things in life that make me happy.  Since the active ASA always has the same hostname and IP address, I find it hard to keep track of to which firewall I'm actually connected.  That "configurtions are no long in sync" message you get when you _conf t_ on the standby firewall really irks me.  With the _prompt_ command, I can see which firewall I'm on and in what state it is.

Here are the options you can use.

> firewall(config)# prompt ?  
>   
> configure mode commands/options:  
>   context   Display the context in the session prompt (multimode only)  
>   domain    Display the domain in the session prompt  
>   hostname  Display the hostname in the session prompt  
>   priority  Display the priority in the session prompt  
>   state     Display the traffic passing state in the session prompt

Note that the command is similar to the _service timestamps_ in IOS where you can stack options.  I wound up setting my prompts to "hostname priority state" so I can see that information without having to do a _show failover_.  If you run contexts, I'm sure that would be a good one to include as well.  I imagine adding "domain" may make the prompt too long for use, though.  Heh.

Send any ~~candy hearts~~ questions my way.
