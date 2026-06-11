---
title: "Basic Logging on an IOS Device"
date: 2008-03-03
---

I've been looking around at some lists and forums for technical help on Cisco gear, and one thing keeps coming up -- people new to \[tag\]Cisco\[/tag\] devices don't know how to look at logs. The \[tag\]logs\[/tag\] are your friends and a great tool. You can use them to see what your router is doing, what's going wrong, and even when something happened.

Get on your router and do a "show logging". You're looking at the log buffer where the router logs \[tag\]events\[/tag\]. If you don't have anything in there, you need to run a "logging buffered informational" and "logging on" from config mode. This will turn on some logging at a basic level, and you should see some stuff going on. Keep doing a "show logging" and you'll see the buffer start to fill up.

If the buffer gets too big, the router will start cycling out old logs, so, if you have a lot going on, a message may cycle out, so keep that in mind. You can always set the log buffer to be bigger; by default, it's 4K (4096 bytes), but you can change that with the command "logging buffered <SIZE>" from config mode. The size will be in bytes, so don't make it too big or you'll kill your router (Google up "syslog" to get started on having your router log remotely.).

Since logging is on now, we can take a look at a sample line to see what kind of information you get.

> %SEC-6-IPACCESSLOGDP:list 2011 denied icmp x.x.x.x -> y.y.y.y

In this sample, we have a line telling us that ACL 2011 has denied an ICMP packet from x.x.x.x to y.y.y.y. That's probably something we want to note, since Larry User just called and said he can't ping his development server. Since Larry's IP is x.x.x.x, you know that you have to change ACL 2011 if you want Larry to be able to ping his server.

How about this one?

> %SEC-6-IPACCESSLOGP: list 183 denied tcp a.a.a.a(51325) -> b.b.b.b(22)

Patty just called to complain she can't reach a web server via SSH. We know that SSH runs on TCP/22, that the server has an IP of b.b.b.b, and that Patty's workstation has an IP of a.a.a.a, so we can see that ACL 183 is blocking her from her box. Now you can tell her that she's being blocked and that, in order to grant access, she has to go though a bunch of red tape. We don't want anyone except the systems guys on the box anyway. :)

Here's a non-ACL example.

> %LINK-3-UPDOWN : Interface Serial0/1, changed state to down

It looks like your serial interface went down. I bet some people can't get to some things, and the phone is already lighting up.

\[tag\]Logging\[/tag\] is your friend. If there's something not quite right, do a "show logging" first. I promise it'll help solve a mess of problems for you.
