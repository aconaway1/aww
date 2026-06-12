---
title: "Port Forwarding on the ASA/FWSM/PIX"
date: 2008-05-27
tags: 
  - "asa"
  - "firewall"
  - "fwsm"
  - "nat"
  - "pix"
---

Here's a simple one since I haven't updated in a while. I have my ASA 5505 at home and want to forward TCP/80 traffic to my public IP to my webserver at 10.10.10.10. There are two steps here -- forward the port and open the ACL.

To forward the port, I would use the _static_ directive, but there are two ways to do that. I can either set up a one-to-one NAT or a port redirection. In the one-to-one NAT, you have a outside address that's mapped directly to an inside address, and any traffic to that IP is passed to the inside host (if it passes ACLS, of course). One of the limitation, though, of using this setup is that you can't use that IP as your PAT address, and, since I only have one IP, no other inside hosts would have a outside address to which to be NATted. The other method -- port redirection -- is a much better solution. In this setup, I actually forward a protocol/port on a outside address to a protocol/port on an inside address. Since there are other ports available on that outside address, the address is still available for other hosts to use as a NAT address.

In an enterprise, I would probably use an address out of my pool for the port forwarding, but, since I only have one address at home, I've got another decision to make. I can configure the _static_ statement with an IP address or I can use the reserved word _interface_ to indicate the IP that is on an interface. This is a great feature, actually, since my outside IP could potentially change without notice. I'm going to use that feature, too.

`static (inside,outside) tcp interface 80 10.10.10.10 80`

This is pretty simple, but I'll explain.  The ASA will take any request that comes in on TCP/80 (HTTP) on its outside interface's IP and forward it to TCP/80 of 10.10.10.10.  If my webserver ran on TCP/81 on my box, I could just change the last 80 to 81 to make it work.

The port is redirecting, but I still need to open the ACL.  When that's done, everything should work as expected.
