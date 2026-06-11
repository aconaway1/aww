---
title: "DHCP on the ASA 5505"
date: 2008-04-03
tags: 
  - "asa"
  - "dhcp"
  - "firewall"
  - "lan"
---

Let's keep going with [our example setup on the ASA 5505](http://aconaway.com/2008/04/01/setting-up-vlans-on-an-asa-5505/ "AConaway.com -- Setting Up VLANs on an ASA 5505") and set up DHCP on this guy. You can set it up to either forward (relay) DHCP requests to a DHCP server somewhere or have it be the DHCP server. Let's do it.

To set up DHCP forwarding, you have to configure where the DHCP server is and then enable the relaying on the proper interfaces. Let's say we have a DHCP server on the inside interface at 192.168.14.11 and we want it to serve IPs to the _guests_ network. Setting up the DHCP server is beyond the scope here, so you'll have to look elsewhere on how to set that up.

> dhcprelay server 192.168.14.11 inside dhcprelay enable guests

Another piece of cake, right?

Setting up the 5505 to be the DHCP server requires a few more lines, but, again, it's easy. In the simplest setup, you only have to define your DHCP scopes and enable it on an interface. Let's set up a DHCP scope for the inside interface of 192.168.14.101 - 120.

> dhcpd address 192.168.14.101-120 inside dhcpd enable inside

You probably want to serve a DNS server to the DHCP clients as well. You have two options -- you can provide your own DNS server or have the 5505 serve the DNS servers it got from the upstream provider (like your cable modem provider) via DHCP. To serve out your DNS server at 192.168.14.12, just do this.

> dhcpd dns 192.168.14.12

Serving the same DNS servers that the firewall got from the provider via DHCP is a little weird. Who puts underscores in commands? Assuming your outside interface is toward your ISP, just do this.

> dhcpd auto\_config outside

There's the basics, but you can do all sorts of stuff with it. Change the lease time. Set the default search domain. Set a WINS server. Notice one thing, though; there's no way to configure a default gateway. The ASA 5505 (and the rest of the 5500 series) only serve their own IPs as the default gateway, so be aware.
