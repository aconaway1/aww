---
title: "Using MAC Access-lists"
date: 2008-10-27
tags: 
  - "acls"
  - "catalyst"
  - "cisco"
  - "lan"
  - "switching"
---

We ran into this today, and, though I knew it existed, I never actually saw it in the wild.  I'm talking about MAC access-lists.

In the example setup, we have a DMZ off of a firewall that contains a whole mess of servers -- email, web, ftp, etc.  These should all be in the DMZ for sure, but they shouldn't talk to each other.  If a bad guy was able to own my FTP server, he would have a nice platform to use to attack my email server.  That's not cool, so we've put in MAC access-lists to help out.

MAC access-lists do just what you think they do; they put access controls on what MAC addresses can talk to what on a  particular port.  You build a list of _permit_ and _deny_ lines that you want to use to control access and apply them to a port.  Sound familiar?  Yeah...it does sound a lot like IP ACLs, doesn't it?  Here's some technical detail for those that care.

- MAC ACLs can be numbered or named.  The range for numbered ACLs is 700-799.  The naming conventions follow the same rules as an IP list.
- A port must be a in access mode before you can apply a MAC ACL.  You can't apply them to trunks.
- You can use the _any_ and _host_ directives instead of using MAC/mask combos.
- If you're feeling froggy, you can actually do a MAC/mask combo, but, since the MACs on your hosts probably aren't sequential, I don't see the point.

Let's build one.  Suppose we have a host with the MAC 1111.2222.3333 plugged into a switch on port F0/1.  It is a web server and needs access to the mail server on the same network (4444.5555.6666) and the firewall (9999.8888.7777) as a gateway.  How do we set it up so that the web server can only speak to those guys?

> mac access-list extended WEBSERVER permit host 1111.2222.3333 host 4444.5555.6666 permit host 1111.2222.3333 host 9999.8888.7777
> 
> int f0/1 mac access-group WEBSERVER in

Now that host can only speak to those two MACs on layer 2.  Pretty simple yet again.  There are some things to note, though.

First of all, this just keeps this host from talking to other MACs on the same network and does nothing to keep packets from other host from reaching our webserver.  Though the webeserver won't be able to respond, one could argue that it's best practice to apply an outbound MAC ACL that mirrors the inbound.

There's also an issue of broadcast.  Say that the webserver is now trying to send a packet to the mail server to send an alert.  One of the first things that it will do is to send a broadcast (ffff.ffff.ffff) asking for an ARP reply.  Guess what?  That MAC isn't in the ACL.  You could put static ARP entries on the boxes, but it may be easier just to allow the host to talk to the broadcast.

Don't get layer-2 security mixed up with layer-3 security.  This restricts access on (usually) a single  IP network where you don't have routers or firewalls between hosts.  Use the old-fashioned IP ACL for between networks and MAC ACLs for between hosts on a network.

Questions?  Comments?  Bribes?  Free money?  Send them all my way.
