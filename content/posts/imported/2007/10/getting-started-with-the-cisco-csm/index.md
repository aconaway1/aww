---
title: "Getting Started with the Cisco CSM"
date: 2007-10-03
tags: 
  - "cisco"
  - "csm"
---

Cisco's Content Switching Module (CSM) is an application accelerator. Or is it an application networking service module? I hate those fancy buzzwords -- it's a load balancer. It's a module for the 6500 series switches that lets you load balance services in any VLAN and can also be set up for high-availability. I could go on for a while about the features, but let's keep it simple for now. A short tutorial, if you will.

Configurations to the CSM are made in the IOS itself. It's a little different than other modules where you have to session over or SSH to the module. The only trick is that you go into CSM mode like this.

> Switch#conf t Switch#(conf)module csm 3 Switch#(conf-csm)

So, what's first? The first thing is setting up the VLANs. I'll assume you've already got the VLANs set up on the switch part (like you always do to get everything talking). I'm also assuming that you want to use routed processing mode (don't ask).

The VLANs have to be configured as either server or client -- the servers themselves sit on the server VLAN, and the virtual IP addresses sit on the client VLAN (this is where the client comes in to access the service). Let's look at the client VLAN first. You obviously need to have the VLAN ID. You also need to configure the IP address for that VLAN to put a layer-3 address and a gateway, which is what the CSM uses to send return packets back to the client from the server. It'll make sense if you think about it for a bit.

> Switch#(conf-csm) vlan 100 client Switch#(conf-csm-vlan-client) description My first client VLAN Switch#(conf-csm-vlan-client) ip address 10.0.0.1 255.255.255.0 Switch#(conf-csm-vlan-client) gateway 10.0.0.254

Ok, then. Let's do the server VLAN. You need the VLAN ID and the IP address the same as you did for the client VLAN. The difference here is that you don't configure a gateway. Whereas the IP address on the client VLAN is just to have a leg into that network, the IP on the server VLAN is generally used as the default gateway for the server on that network. By the way, you don't have to have the servers on that VLAN directly; the servers simply have to be accessed from that VLAN, so you can put servers a few hops away.

> Switch#(conf-csm) vlan 200 server Switch#(conf-csm-vlan-server) description My first server VLAN Switch#(conf-csm-vlan-server) ip address 10.0.1.1 255.255.255.0

No problems so far, eh? I hope not because here's the complicated part. Now we're going to configure a new load-balanced IP that uses a couple of web servers to server pages. Let's start with configuring the serverfarm, which is a group of servers that serve the same function.

> Switch#(conf-csm) serverfarm WEBFARM Switch#(conf-csm-sfarm) real 10.0.1.11 Switch#(conf-csm-real) inservice Switch#(conf-csm-real) real 10.0.1.12 Switch#(conf-csm-real) inservice

I think you can figure out what that config does. Yes, the "inservice" is required. The reals are often referred to as real IPs, or RIPs.

Now we need to configure the vserver, which is an object that includes the virtual IP (VIP), protocol, port, serverfarm, VLAN, etc. This is the main guy and, if you do some more advanced stuff, you'll have a vserver config longer than the iPhone line. Look first, then I'll tell you what we're doing.

> Switch#(conf-csm) vserver VIRTSERVER Switch#(conf-csm-vserver) virtual 10.0.0.11 tcp 80 Switch#(conf-csm-vserver) serverfarm WEBFARM Switch#(conf-csm-vserver) vlan 100 Switch#(conf-csm-vserver) inservice

It's pretty easy to see what we're doing. We're creating a VIP of 10.0.0.11 that serves traffic sent to TCP/80. When traffic is sent to this VIP on that port, the CSM will round-robin load-balance using serverfarm WEBFARM. We also have told the CSM to only answer requests to this VIP if they come from VLAN 1 (If you don't include the VLAN line, the CSM will answer on all VLANs that it's configured to use. In my opinion, this is a design problem and it's fixed in the ACE.).

Let's do a quick packet trace to be sure we're all clear.

You open your browser and ask for the page at http://10.0.0.11. Your packets go through the network and eventually land on VLAN 1. The CSM knows that it needs to answer for it, so it accepts the packet. Since there is an existing vserver on TCP/80, it send the packet to its SLB (server load balancing) algorithms. Through the SLB magic, the CSM picks what server in the serverfarm to use and changes the destination address of the packet to the RIP and passes it on.

On the way back, the RIP sends the packets back to the server VLAN address. The CSM knows that we're accessing the VIP, so it changes the source address to the VIP and passes it on to the gateway for VLAN 1. The packet eventually lands back on your machine, and you never knew anything happened to the packet along the way.

That's the basics. We went through and configured the VLANs and a simple vserver. The CSM can do dozens of more things, but that's for another time.
