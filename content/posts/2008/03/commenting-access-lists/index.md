---
title: "Commenting Access-lists"
date: 2008-03-12
tags: 
  - "acls"
  - "asa"
  - "firewall"
  - "pix"
---

There's a very-overlooked feature of access-lists -- the remark. Yes, this is very basic, but it's worth mentioning, as it has saved me anguish time and time again.

I use remarks to document each line of an ACL (on IOS, PIX, FWSM, ASA, etc.) so that when I go back later, I actually know what I did. They're simple to use, and, I promise you, you'll thank yourself for using it when the CTO asks why access to TCP/80 is open from the Internet to the development server.

Easy to use.

> access-list 101 remark This line allows access from the Internet to the development server. See ticket 1234 access-list 101 permit tcp any host 1.2.3.4 eq 80

Now, when you get asked the inevitable question, you can look at the line and know to check ticket 1234 for more information. The remark is just a string, so you can put what you want. I like to put source and destination hostnames, protocol/port, ticket number, and date/time the line was entered for reference like this.

> access-list 2001 remark \*\*\* I'net -- HTTP -> dev.example.com, Ticket 1234, 12Mar2008-0853 \*\*\*

It works with the _ip access-list_ command as well.

> ip access-list extended INBOUND remark \*\*\* I'net -- HTTP -> dev.example.com, Ticket 1234, 12Mar2008-0853 \*\*\*

It might be a good idea to use a remark to document what the ACL itself does. For example, on a firewall with 28974 interfaces, you might want to do something like this.

> access-list DMZ1\_OUT remark This ACL allows traffic out of the DMZ interface access-list DMZ1\_OUT remark \*\*\* .... access-list DMZ1\_OUT permit ...
