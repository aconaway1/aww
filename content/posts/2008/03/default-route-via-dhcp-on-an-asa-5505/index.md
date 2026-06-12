---
title: "Default Route via DHCP on an ASA 5505"
date: 2008-03-22
tags: 
  - "asa"
  - "firewall"
---

I finally got my ASA 5505 up and running at the house, but I ran into a little problem -- the box wouldn't add the DHCP-provided default route into its routing table.  That one threw me for a loop since the box is made for SOHOs, but it makes sense in some corporate, lazy way.

I got an IP from the DHCPD on the 5505, but I couldn't get to the Internet.  I checked the console, and it had an IP from the provider, so I checked ACLs; those were fine.  I looked at the log and found this.

> %ASA-6-110002: Failed to locate egress interface for UDP from inside:x.x.x.x/1028 to y.y.y.y/53 

I had no clue what this really meant until I checked the routing table; there was no default route at all.  For some reason, the ASA 5505 was ignoring the default route from the DHCP server upstream.  The fix?  Assuming your outside interface is VLAN 1, just do this.

> interface Vlan1 ip address dhcp setroute

The box will go out and get the DHCP default route by itself, so no need to _shut_/_no shut_.
