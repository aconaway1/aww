---
title: "A Must-Know:  TCPDump"
date: 2008-06-06
categories: 
  - "cisco"
tags: 
  - "tools"
---

If you've never used [TCPDump](http://en.wikipedia.org/wiki/Tcpdump "Wikipedia -- TCPDump") before, you're missing out on one of the best parts of being a network guy -- pointing fingers at everyone else.

TCPDump is an open-source app that copies packets on a machine's NIC to screen or to file. TCPDump is typically a Linux/Unix app; in the Windows world, TCPDump is replaced by [WinDump](http://www.winpcap.org/windump/ "Windump -- tcpdump for Windows") or [Ethereal, now known as Wireshark](http://www.wireshark.org/ "Wireshark.org -- Main Page"). It's a must-know for network dude(tte)s since it lets you capture the packets that a machine is generating. An app may be documented to work one way, but I've seen many times where the documentation is out-of-date or just wrong, and I've had to look at captures to see what it was actualy doing. I used it one time way back when a developer told me the switch was changing his HTTP POST to an HTTP GET; I captured the packets he was sending, pointed to the GET, and never answered a phone call from him ever again.

Am I angry today?

Here's a more down-to-earth example. How many times have you been asked to open an ACL for a host, but the requester didn't know the destination IP or the service port? For me, this happens as least twice a week, and I use TCPDump to figure out what the requester is trying to do. Here's a typical conversation.

> Requester: I've installed an app on my Linux server to help me zip up my pants, but it's not working. Me: Well, I've told you on at least 6 occasions that the firewall is going to block connections unless you tell us to open it up. R: Oh, yeah. I'm still used to the way we did things in 1988. Can you open that up for me? M: Can you put in a ticket like I always ask you to do? R: Sure. What do you need it to say? M: The same thing the last 6 tickets said -- source IP, protocol, port, destination IP. R: I don't know all that. I just know it tries to connect to my home IP via HTTP, but I don't know what that IP is. M: Then how are you going to zip up your pants? R: I guess I won't. Can you help me find that information?

Wow. I've got some penned up aggression, don't I?

To find out what's used to help this user zip up his pants, we can just run TCPDump (as root since you need access to the NIC) to capture some packets.

> sudo /usr/sbin/tcpdump

Hmmm...I bet you get a lot of stuff. You're looking at all the packets that are flying in and out of the box, including all sorts of stuff like DNS requests and your SSH session. This is where TCPDump _tries_ to shine, though; it's got a very powerful capture filtering system, which can get complicated at times. You can do all sorts of filtering on the capture, but the most common are the _host_ and _port_ filters, which, like all the other filters, can be strung together to make great huge chains of filters.  Since we know it's trying to connect to an  HTTP server, so we can start by showing only traffic on port 80.

> sudo /usr/sbin/tcpdump port 80

Much better. Here's some output.

> \[jac@finland ~\]$ sudo /usr/sbin/tcpdump port 80 tcpdump: verbose output suppressed, use -v or -vv for full protocol decode listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes 18:54:36.690198 IP 192.168.70.129.8736> yourmama.com.http: S 1864432324:1864432324(0) win 5840 <mss 1460,sackOK,timestamp 383978802 0,nop,wscale 3> 18:54:36.694046 IP yourmama.com.http > 192.168.70.129.8736: S 1931495854:1931495854(0) ack 1864432325 win 64240 <mss 1460> 18:54:36.694110 IP 192.168.70.129.8736> yourmama.com.http: . ack 1 win 5840 18:54:38.172982 IP 192.168.70.129.v> yourmama.com.http: P 1:16(15) ack 1 win 5840 18:54:38.173947 IP yourmama.com.http > 192.168.70.129.8736: . ack 16 win 64240

It looks like it's using the hostname yourmama.com. Now we can just look up what that IP is and generate the firewall rule for it (or, if you're like me, just give is a -_nn_ flag to not look up IPs or service ports). What else can you do with TCPDump? You can check out the man pages for how to do this stuff or just ask me nicely.

- Capture the packet headers to file for review later
- Capture the whole packet to screen or file
- Read in a packet capture from file
- Listen on a particular interface (like eth3 instead of eth0)
- Capture X number of packets
- Filter based on all sorts of stuff including IP address, port, protocol, MAC address, IPv6 multicast address, VLAN from 802.1Q packet, and all sorts of other good stuff
