---
title: "Getting Something Out of the CSM"
date: 2008-06-10
tags: 
  - "csm"
---

My buddy told me that my site is the only place on the web with documentation on the Cisco [Content Switching Module (CSM)](/posts/2007/10/getting-started-with-the-cisco-csm/ "AConaway.com -- Getting Started with the CSM"). I also noticed a few months ago that every TAC case I've opened on the CSM has been handled by the same guy. I seriously think that the only people in the world that really know about these things are me and him. Cool. I better get some more content up.

The CSM is configured and controlled by the IOS running on the 6500. Unlike the FWSM, it is not independent from the switch's operation, which is sometimes good. One good thing out of that is the fact that you can pull stats and stuff from the IOS without having to session or SSH to another module. The bad part of that, though, is that the commands wind up being long.

To start with, all _show_ and _clear_ commands start like this.

> show|clear module contentSwitchingModule <SLOT> <COMMAND>

Do you see how long that command is? And we didn't even tell it what we wanted to do yet. You can use the auto-complete, though, f you're lazy like I am.

> sho|cl mod csm <SLOT> <COMMAND>

Yes, it takes _csm_ instead of contentSwitchingModule. That's not in the contextual help. Heh.

_SLOT_ is where the module is in the chassis, so, if your CSM is in slot 8, you...can figure it out. _COMMAND_ is what you want to do, right? Yeah...I won't insult your intelligence.

What are some show commands? There's a lot of them, but here's some I use every day. My production CSMs are in slot 3, so I'll just use that for the slot.

- _show mod csm 3 arp_ : Shows you the ARP table (duh!). This is good to see if the CSM can contact the real servers or the gateways properly.
- _show mod csm 3 conns_ : Shows the current connections through the CSM. This shows you what client IP is connected to what virtual IP and on what real IP that connection lands.
- _show mod csm 3 ft_ : Fault tolerance. This shows what your FT VLAN and status is. It also shows if your secondary configs are out-of-sync with the primary.
- _show mod csm 3 reals_ : Shows all the real servers involved in all serverfarms along with the weight, state, and current number of connections. This shows you a lot of information that could be helpful in troubleshooting a problem. Look for FAILED, PROBE\_FAILED, or OUTOFSERVICE; these are bad.
- _show mod csm 3 serverfarms_ : Shows your setup and status of the serverfarms. Also great for troubleshooting.
- _show mod csm 3 vservers_ : Shows the IP, VLAN, and state of your vservers along with the current number of connections.
- _show mod csm 3 vlan_ : Shows the VLAN ID, subnet and mask, and VLAN type (server, client, FT)

Be sure to use your contextual help for more detail on these commands; most of them actually can get very specific. For example, _ft_ shows your status, but _ft detail_ shows your message counts, resets, and other good and bad things that deal with the fault tolerance.

How about something to clear?

- _clear mod csm 3 connections_ : Clears the connections through the CSM. This is probably very close to _clear conns_ on a PIX, FWSM, or ASA, so be careful not to kick everybody off.
- _clear mod csm 3 arp-cache_ : Clears ARP
- _clear mod csm 3 ft active_ : Forces the primary to fail over.
- _clear mod csm 3 counters_ : Do you have to ask?

Again, these guys need to be explored with the question mark to get the full effect.

Since I've established myself as the long authority in the world on the CSM \[_sic_\], drop a comment with any questions.
