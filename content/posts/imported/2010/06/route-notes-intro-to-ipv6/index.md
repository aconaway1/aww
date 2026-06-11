---
title: "ROUTE Notes - Intro to IPv6"
date: 2010-06-29
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "intro"
  - "ipv6"
---

**Study Notes**

- Exactly how big is an IPv6 address?

It's 128 bits long.

- This shouldn't be on the test, but how many unique addresses is that?

That's 2^128 or a "3" with 38 zeros after it.  That's also 2^95 addresses for each person on earth.

- Surely we're not writing in binary, are we?

No way.  IPv6 uses 32 hex characters.  Each character is 4 bits, so we wind up with 128 bits of data.

- Surely all 32 characters aren't just written out together in one continuous string, are they?

No again.  They're written in groups of 4 separated by colons.  For example, 2000:1234:0184:AB33:0000:0000:1084:0001 is a valid IPv6 address.

- That's still a lot of characters; tell me that there's a shortcut to writing those out.

There are two shortcuts, actually.  First, you can omit leading zeros in an octet (4 hex digits).  You can also replace a single run of octets that are all zeros with a double colon (::).  If we took our example above and shortened it, we would wind up with 2000:1234:184:AB33::1084:1.  Notice that some octets are less than 4 characters longs and that the two octets of zeros are replaced wih the double colon.

- If an implementation plan says that you should statically configure all the IPv6 addresses on a bunch of routers, what should you do?

You should send the plan back for revision since there are two methods to statically configure an IPv6 address - static and static with EUI-64.

- What the heck is EUI-64?

EUI-64 is a IEEE standard for deriving a unique ID from a MAC address.  It splits the MAC address in half, shoves a "FFFE" in the middle (since MACs are 48-bits long and we need 16 more bits to make 64), and toggles the 7th bit in the whole string.

- What are you talking about?

Example time!  Your MAC is 0000.3333.1938.  First, we split the MAC in half and shove in the "FFFE" to give us 0000:03FF:FE33:1938.  Next, we toggle the 7th bit in the string to give us 0200:03FF:FE33:1938.

- What are the two methods for dynamically assigning an IP address?

Stateful DHCP and Stateless autoconfig.

- What's the difference between stateful and stateless DHCP?

Stateful DHCP functions similarly to the IPv4 version where a DHCP server gives a host and IP address, mask, etc., and keeps a record of the lease.  Stateless DHCP simply tells a host what DNS servers to use without recording the transaction.

- How do I calculate the network and broadcast addresses in IPv6?

You don't!  There's no such thing as either.

- That's cool, but how do I address groups of addresses?

IPv6 makes use of multicasting to do that.  All multicast addresses are in the prefix FF/8, and can serve many purposes.  For example, all hosts on a subnet respond to FF02::1, and all routers respond to FF02::2.

- Your all-knowing Network Architect wants all the IPv6 addresses of the routers to be the lowest in a prefix like you do with IPv4.  How can you assign the addresses like that?

You can statically configure the whole 128-bit address.  You can also set the MAC address to 0000.0000.0001 (or whatever) and use the static with EUI-64 method.

- What's the better of the two methods?

There is never a better method to use.  You have to figure out what you want to do and which method would be best suited for your situation.

- What risks do you take with changing a MAC address?

If you change two or more routers to the same MAC address, you will have all sorts of problems from IPv6 conflicts down to CAM table flapping.  Be careful!

- What are the different types of IPv6 address and what do they do?

Global unicast - a globally-unique address that can be used throughout the world Unique local - a site-unique address that can be used throughout your organization (like RFC1918) Link local - an address that is only addressable on a single segment/subnet/prefix

- For what is the link local address used?

Every host has a link local address that is used to communicate on a link (or subnet).  This address always begins with FE80/10 and uses the EUI-64 technique to generate the full address.

- How does an IPv6 host (like your laptop) get its default gateway?

The host sends a router soliciation (RS) ICMP message sourced from it's link-local address.  Each router on the subnet then responds with a router advertisement (RA) that says it's available for use as a gateway.

- What must you configure on your router to allow it to respond to RS messages?

You only need to enable IPv6 and have a global unicast IPv6 address configured.

- My ARP table is empty; why is that?

IPv6 doesn't use ARP like IPv4 does.  Instead, IPv6 uses neighbor discovery to populate the layer3-to-layer2 mappings.  The process is similar to the router discovery, except that the host suse neighbor soliciation (NS) and neighbor advertisement (NA) packets.

- How does an IPv6 host detect duplicate addresses?

When a host comes up, it calculates an address called the solicited node multicast address, which is a special multicast group in the prefix FF02::1:FF00:0/104.  The last 24 bits of the address are taken off the end of an IPv6 address configured on the device to finish the address.  Each address on the device gets one, so you may have a lot of solicited node multicast addresses.  To the point, when the box is bringing up the interface(s), a NS is sent to the solicited node multicast address (which basically is hosts with somewhat similar IPv6 address configured), and, if a NA is received with the same address that the device has configured, there's a duplicate IP!¹

- Why doesn't a host just use FF00::1 to detect duplicates?

This is just an efficiency thing.  There's no need to ask every node on the segment what address they have when you can just ask a small subset of nodes that have similar addresses.

**What Command Was That**

What command is used to...

- ...show all the IPv6 routes on a router?

show ipv6 route

- ...show a brief summary of the IPv6 interfaces?

show ipv6 interface brief

- ...show all the multicast groups of which a router is a member?

show ipv6 interface X

- ...shows all the neighbors that have been discovered?

show ipv6 neighbor

- ...shows all the routers that have been discovered?

show ipv6 routers

1  Thanks to [Jochen](http://twitter.com/verbosemode) for clearing that up for me!
