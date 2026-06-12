---
title: "ASA and Proxy ARP"
date: 2009-09-11
tags: 
  - "arp"
  - "asa"
  - "cisco"
  - "fwsm"
  - "nat"
  - "pix"
  - "proxy"
  - "syn"
---

Wow.  A new entry.  Everyone sit down before you pass out.

I've got a real-world example for you today.  We have an ASA 5540 installed at a business unit with interfaces in multiple networks, including one containing the production servers and another containing the accounting servers.  The production network sits on a 7600 that's not ours, so, to avoid IP conflicts, we are statically NATting connections into that network.  The 7600 has with many, many VLANs, and, since the firewall production servers are on different VLANs, there's an interface VLAN between us.  Sounds pretty straightforward, but it just wasn't working when we try to connect between the interfaces.

When we tried to connect from the accounting servers to the production gear, the firewall saw the SYN, built the outbound connection, sent the packet on, and waited.  Nothing back.  SYN timeout.  The vendor on the production side checked the routing.  Fine.  Checked the ACLs.  None installed.  When the (other) vendor ran TCPDump on the production servers, they saw the SYN landing and the SYN-ACK leaving, but it never got to the ASA.  We even looked at the inline IDS and still didn't see the SYS-ACK hitting the firewall.  It was simply not getting passed on.

I got tired of walking people through stuff over the phone, so I drove up there to see what I could find.  When I checked the ARP table on the 7600, I noticed that the statically NATted IP we were serving was conveniently _incomplete_.  For those who don't know, that means that the 7600 was ARPing for the address, but nothing was answering for it.  Obviously, our ASA should be answering, right?  To make the situation a little more dire, I did a _debug arp_ (or something close) on the firewall and generated an ARP request; the firewall saw the request but just ignored it.  Ugh!

If you couldn't tell by the title, it turns out that the solution was to enable proxy ARP.  It's off by default for good reason, but here's how to enable it.

> ```
> no sysopt noproxyarp PRODUCTIONINTERFACE
> ```

Enabling proxy ARP, however, could be a security issue.  Any time you use the word "proxy", there is a potential to spoof addresses, and, in this case, an attacker could (potentially) use the firewall to discover hosts that are on the other side of it.  That wouldn't be good.

A more-secure solution is to use static ARP entries.  In our case, we added a static ARP entry on the 7600 that points our NATted IP to the MAC address of the firewall.  Now, when you ping the IP, the 7600 doesn't ARP; it already has the MAC in the ARP table, so it just sends the packet on.  Since we only have one static translation in this case, it's no big deal, but, if we had a whole class-C of addresses to NAT, there would be a management problem.

A part of me wants to do the simple thing and enable proxy ARP, but the vast majority of article, blogs, forums, lists, etc., that I've ready say to turn it off for security and efficiency purposes.  The more I think about it, though, the more Iwonder why proxy ARP needs to be enabled to make staic NATs work.  I looked back at an old PIX running 6.x,  and proxy ARP is on by default.  The same holds true for an FWSM running 2.x.  I'm going to have to ask Cisco what's up with that.

Send any misconfigured subnet masks questions my way.
