---
title: "SPANs on Cisco Switches"
date: 2009-03-13
tags: 
  - "cisco"
  - "monitor"
  - "span"
  - "switch"
  - "tcpdump"
---

I can't believe I haven't blogged on this yet.  SPANs are one of my favorite things in the world.

The switched port analyzer (SPAN) is a mechanism on Cisco switches that allows you to take traffic on one port and copy it to another.  It's generally used to get traffic to a sniffer or IDS for analysis, but it's a great tool to use to sample traffic from a host for troubleshooting.

Let's use a real-world example.  You've told your roommate to quit illegally downloading songs off the Internet, but you suspect he's still doing it.  Instead of sneaking into his room at night and checking his machine, you can use a SPAN to copy his traffic to another switch interface where you can use _tcpdump_ to record what's happening.

Let's say you have a 2950, and the roomie is on F0/1.  You have a Linux box plugged into F0/24 ready to capture the traffic.  Here's what you do.

> ```
> monitor session 1 source interface F0/1 both
> monitor session 1 destination interface F0/24
> ```

This will create a new monitor session (that is, a SPAN session) that copies traffic from port F0/1 in both directions to port F0/24.  Now, when you run tcpdump on your Linux box, you see all the traffic coming in and going out of your roommate's port.

That's pretty easy, right?  You can have multiple sources ports by just adding more source lines or using ranges of ports.  You can also just copy received or transmitted traffic from a source.  Check out the contextual help for a little more info.

To see what's going on, you can do a _show monitor_ or a _show monitor session 1_ (depending on the IOS version).  You'll see something like this.

> ```
> switch#sh monitor
> Session 1
> ---------
> Type              : Local Session
> Source Ports      :
> Both          : Fa0/1
> Destination Ports : Fa0/24
> Encapsulation : Native
> Ingress : Disabled
> ```

If you take a look at the destination port when the SPAN is running, you'll see it's in a state of _up/down (monitoring)_.  I think you can figure out that this means we're monitoring some traffic to this port.  Here's what you'll see if you look at the port.

> ```
> switch#sh int f0/24
> FastEthernet0/24 is up, line protocol is down (monitoring)
> ...
> ```

There are two big things to keep in mind when doing SPANs.  The first is that monitoring a port can drive CPU utilization way up (depending on the platform and traffic volume), so you may run into problems if you have a bunch of SPANs going at the same time.  Related to this is the fact that, if your switch has to decide between switching and copying traffic, it will stop copying until there's enough CPU headroom to do that safely, and you'll lose packets in the meantime.  It's a switch -- not a copier.

The second thing to keep in mind involves those little voices in your head called ethics.  What if you see a VOIP phone call from your boss to the HR department?  How about if you find someone in upper management copying a spreadsheet of people to be fired tomorrow?  How about if you find an engineer's telnet password to a key system?  These are things that you probably shouldn't see, so be careful when looking at the packets.  I would suggest you tell someone in your security when you're going to do a packet capture to make sure someone knows you're not up to no good.

Send shamrocks questions my way.
