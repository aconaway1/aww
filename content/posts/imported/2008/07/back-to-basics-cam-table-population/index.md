---
title: "Back to Basics -- CAM Table Population"
date: 2008-07-14
tags: 
  - "catalyst"
  - "lan"
  - "switching"
---

At the office, we reprovision servers like it's going out of style.  It happens so often that my cabling documentation rarely matches what's actually out in field, which is a pretty big problem when you're trying to find to what switch port a server is connected.  I finally relegated myself to asking for the MAC address of the server, having the admin ping something, and then tracing it down through the CAM table entries of the switches.  It works, but the guys really don't know how a switch populates its CAM table, so they always say "Why can't you just look on the switch?  I shouldn't have to ping anything."  Here's one just for the aspiring system admin.

The Content Addressable Memory (CAM) table on a switch keeps track of MAC addresses and on what port they appear, along with some other stuff like age.  When a device that's plugged into a particular port sends a frame to the switch, the switch makes note of the source MAC and the port and checks the CAM table.  If it's a new MAC, it adds an entry in the CAM table; if it's an existing on a different port, it removes the old entry and adds a new one; if it's an old MAC on the same port, it updates the age.  By default, Cisco switches keep CAM entries for 300 seconds, so they don't stay there forever.

What about the destination MAC?  Good question.  That's a pretty important field when sending a packet, but, when generating a CAM entry, the destination MAC is ignored.  If  a host talks, a switch knows exactly from where the frame came, but there's no way to know exactly where it should go without the destination first speaking up.

Let's set up an example.  You have a Cisco 2950 switch that you've just powered on with nothing plugged into it except the console cable. If you do a _show mac-address-table_, you'll see the CAM table -- a table of MAC addresses that the switch knows; you would think that it would be empty since nothing's plugge din, but the switch has its own MACs, so it always knows those guys.  There's not much to see here yet, though, since we haven't hooked anything up to the switch yet.

> ```
> Switch#sh mac-address-table
> Mac Address Table
> -------------------------------------------
> Vlan    Mac Address       Type        Ports
> ---- ----------- -------- -----
>  All    000a.f43b.ddc0    STATIC      CPU
>  All    0100.0ccc.cccc    STATIC      CPU
>  All    0100.0ccc.cccd    STATIC      CPU
>  All    0100.0cdd.dddd    STATIC      CPU
> Switch#
> ```

Next, let's plug a Linux desktop up to it.  Once that box has booted, what should you see in the CAM table?  If you guessed the MAC of the Linux box, you may be right; it all depends on if the server sent a frame or not.  There's lots of things that run on a Linux box that could send frames on startup -- DHCP requests, multicast services, network-based storage -- so, more than likely, a frame did get sent.  The only way to know it to take a gander.

> ```
> Switch#sh mac-address-table
> Mac Address Table
> -------------------------------------------
> Vlan    Mac Address       Type        Ports
> ----    -----------       --------    -----
> ...
>    1    001c.0cbb.ada2    DYNAMIC     Fa0/1
> Switch#
> ```

Ah...it worked. That's good, but it's boring with only a single device. Let's plug in a simply-configured and fully-booted Cisco router and see what happens. More than likely the router won't speak until spoken to, so the CAM table won't update, and the switch won't know where to send the frame, right? Yes, but the frame still gets sent. If the switch doesn't know where the destination MAC lives (i.e., it's not in the CAM table), then it floods the frame out every port except the one on which it was received.

When I first learned that this is how it worked, I immediately wondered why LANs weren't flooded out constantly. I didn't think long enough to realize that the host being sent the packet will actually respond within several milliseconds (hopefully), so the CAM table will then have an entry for that guy. In reality, it's even simpler than that. Since most of the world runs TCP/IP, we have this wonderful thing called ARP. When a host needs to talk to another host on the same network segment (IP subnet), it checks its ARP table, and, if it doesn't know a MAC for that IP, it will actually ask what MAC it should use via a broadcast message. In a well-behaved network, the mystery host will answer with a "Here I am!" type message, which causes the switch to generate a CAM entry. In a "perfect world", you should only have a few floods on a switch per day/week/month/year/decade.

Here's a couple items of note.

- A trunk interface will have a whole bunch of MACs listed as attached to that port.  This is quite normal, so don't freak out.
- If someone plugs a switch or hub into one of your ports, you will see multiple CAM entries for the same port.  This is a good way to see who brought in their Linksys hub from home.
- If a host hasn't sent a frame in more than 5 minutes, it disappears from the CAM table, so the whole discovery process starts over again.
- There's a limit to the size of a CAM table, so it's possible to fill it up and then every new destination gets [flooded](http://en.wikipedia.org/wiki/MAC_flooding "Wikipedia -- MAC Flooding").  Wow, I can see your packets.

Have fun.  Be safe.  Practice safe computing.  Lock down your network.
