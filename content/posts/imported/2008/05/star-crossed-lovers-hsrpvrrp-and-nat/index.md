---
title: "Star-crossed Lovers:  HSRP/VRRP and NAT"
date: 2008-05-08
tags: 
  - "nat"
---

I was doing an HSRP lab the other day, and a project from the past popped into my head. A customer had a host on a network that was separated from the rest of the network by a 1700 with a couple of FEs. They wanted that host to be NATted to a local address so that they didn't have to do any routing, which makes sense, I guess. This is just your standard 1-to-1 NAT, so we plunked down a quick config.

The setup had two networks with 192.168.0.0/24 on the customer's local network and 192.168.1.0/24 on the "DMZ" (Yes, I know it's not really a DMZ). We want to NAT 192.168.1.100 to 192.168.0.100.

`interface FastEthernet0/0 ip address 192.168.0.10 255.255.255.0 ip nat inside ! interface FastEthernet0/1 ip address 192.168.1.10 255.255.255.0 ip nat outside ! ip nat inside source static 192.168.1.100 192.168.0.100`

This works great, but then the customer asked for some redundancy. He wanted a second router inline that could take over in case of failure on the original. "Piece of cake," I thought and went about setting up the HSRP stuff on both the original and new second routers.

`interface FastEthernet0/0 standby ip 192.168.0.1 standby preempt standby name MYHSRP`

But what about that NAT thing? If the primary router goes down, the NAT goes down with it. If I configure the secondary router for the NAT, we get an IP conflict. I looked around for a while and found a feature introduced in IOS version 12.2(4) that allows a NAT to follow an HSRP or VRRP active member. If a router is the active member of an HSRP or VRRP cluster, the NAT is with that box; if it fails over, the standby IP and the NAT move. Isn't that cool? The only caveat is that it keys off the name/description of the HSRP/VRRP cluster instead of the ID, so you have to have that configured as we do above.

`ip nat inside source static 192.168.1.100 192.168.0.100 redundancy MYHSRP`

Let's test to be sure it works. On the active member (a router called NAT0), we can look at the ARP table and see that this router is speaking for the NAT address of 192.168.0.100 as well as the HSRP IP of 192.168.0.1. The secondary member (NAT1) only has ARP entries for its interface IPs. Looks good so far.

`NAT0#show standby brief Interface Grp Prio P State Active Standby Virtual IP Fa0/0 0 100 P Active local 192.168.0.11 192.168.0.1 NAT0#show arp Protocol Address Age (min) Hardware Addr Type Interface Internet 192.168.0.100 - c800.12bc.0000 ARPA FastEthernet0/0 Internet 192.168.0.10 - c800.12bc.0000 ARPA FastEthernet0/0 Internet 192.168.1.10 - c800.12bc.0001 ARPA FastEthernet0/1 Internet 192.168.0.1 - 0000.0c07.ac00 ARPA FastEthernet0/0 ... NAT1#show standby brief Interface Grp Prio P State Active Standby Virtual IP Fa0/0 0 100 P Standby 192.168.0.10 local 192.168.0.1 NAT1#show arp Protocol Address Age (min) Hardware Addr Type Interface Internet 192.168.1.11 - c801.12bc.0001 ARPA FastEthernet0/1 Internet 192.168.0.11 - c801.12bc.0000 ARPA FastEthernet0/0`

When I shut down F0/0 on NAT0, the HSRP and NAT both roll over to NAT1.

`AT0(config)#int f0/0 NAT0(config-if)#shut *Mar 1 00:30:25.248: %HSRP-5-STATECHANGE: FastEthernet0/0 Grp 0 state Active -> Init *Mar 1 00:30:27.263: %LINK-5-CHANGED: Interface FastEthernet0/0, changed state to administratively down *Mar 1 00:30:28.265: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to down NAT0# NAT0#sh stand b Interface Grp Prio P State Active Standby Virtual IP Fa0/0 0 100 P Init unknown unknown 192.168.0.1 NAT0#sh arp Protocol Address Age (min) Hardware Addr Type Interface Internet 192.168.1.10 - c800.12bc.0001 ARPA FastEthernet0/1 ... NAT1#sh stand b Interface Grp Prio P State Active Standby Virtual IP Fa0/0 0 100 P Active local unknown 192.168.0.1 NAT1#sh arp Protocol Address Age (min) Hardware Addr Type Interface Internet 192.168.0.100 - c801.12bc.0000 ARPA FastEthernet0/0 Internet 192.168.1.11 - c801.12bc.0001 ARPA FastEthernet0/1 Internet 192.168.0.11 - c801.12bc.0000 ARPA FastEthernet0/0 Internet 192.168.0.1 - 0000.0c07.ac00 ARPA FastEthernet0/0`

Now we have redundancy and NAT on the same cluster. Sweet. I also did the same lab for VRRP, but I'll spare you the innards. When you configure VRRP, you can give it a _description_, which is the same as the _name_ in HSRP. You use that string as the redundancy name to have a NAT move with VRRP. Yes, you can do both an HSRP- and VRRP-based NAT at the same time.

\*I labbed this out on a Dyanmips/Dynagen instance of two 2651XM routers running 12.4(19) Advanced Enterprise. Like all the configs here, your mileage may vary if you have different versions or feature sets on your routers.

Edit:  I wanted to make a not on GLBP and NAT.  Since all the nodes in a GLBP cluster actually route traffic and are routed to, you can't use this type of NAT solution with it.  I don't know what the best practice would be, but I imagine it would involve running HSRP on a few routers as we talked about above.
