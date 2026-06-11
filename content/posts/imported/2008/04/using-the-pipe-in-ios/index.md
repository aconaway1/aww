---
title: "Using the Pipe in IOS"
date: 2008-04-14
tags: 
  - "cli"
  - "ios"
  - "pipe"
---

A lot of IOS commands give you a lot of information. Most of the time, though, it's way too much information, and it sure would be nice to do some grep-like stuff on the output, right? Well, just like on Linux, you can use the pipe (|) to do such. That's not a butt cheek, by the way.

The most useful function is the _include_ directive. This is the equivalent of just plain _grep_ on Linux, and will show you only lines that match a string that you give it. Say that you want to find what ports on your switch are down, but don't want to grind through all the lines of a _show ip interface brief_. If you just pipe it to the _include_ command followed by the word "down", you'll see something like this.

> Switch#show ip interface brief | include down GigabitEthernet0/4 unassigned YES unset down down GigabitEthernet0/7 unassigned YES unset down down GigabitEthernet0/17 unassigned YES unset down down GigabitEthernet0/18 unassigned YES unset down down GigabitEthernet0/19 unassigned YES unset down down GigabitEthernet0/20 unassigned YES unset down down

You can also use the _exclude_ directive, which is the same as a _grep -v_ on Linux. I hope you figured out that this gives you all lines that don't match the word, so, let's use the _exclude_ directive to found out what ports are down. How about we just ignore the lines that are up.

> Switch#show ip interface brief | exclude up Interface IP-Address OK? Method Status Protocol GigabitEthernet0/4 unassigned YES unset down down GigabitEthernet0/7 unassigned YES unset down down GigabitEthernet0/17 unassigned YES unset down down GigabitEthernet0/18 unassigned YES unset down down GigabitEthernet0/19 unassigned YES unset down down GigabitEthernet0/20 unassigned YES unset down down

Well, this won't exactly give you all the ports that are down. What if the port is up/down?

What else can you use with the pipe? What if you want to look at the configurations of all the ports or interfaces on a box but don't want to go through the config hitting the spacebar over and over. If you use the _begin_ command, you'll see the output beginning from the first match, so, using the string _interface_ will show you the config starting at the first interface.

> Switch#show running-config | begin interface interface GigabitEthernet0/1 description Server switchport access vlan 4 switchport mode access spanning-tree portfast
> 
> ...

Another good one is the _section_ command. It's usually used on the output of show running-config and shows you sections of the config that match your string. Huh? If you want to see the BGP section of the configuration, you can do something like thing just to see that part of the configuration.

> Router#show running-config | section bgp router bgp 1 neighbor 1.1.1.1 remote-as 65000 neighbor 1.1.1.1 version 4

There are a few other commands for use with the pipe, so explore on your own. You might also want to check out [regular expressions on the Cisco IOS](http://www.cisco.com/univercd/cc/td/doc/product/software/ios122/122cgcr/fdial_c/fnsprt13/dafaapre.htm "Cisco.com -- Regular Expressions") if you want to match more than just simple text.
