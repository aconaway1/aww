---
title: "Setting Up VLANs on an ASA 5505"
date: 2008-04-01
tags: 
  - "asa"
  - "firewall"
  - "switching"
  - "trunking"
  - "vlan"
---

I've had my ASA 5505 in place at home on my Comcast cable for a few weeks now, and, let me tell you, this thing rocks. I did, however, have a few problems finding a clear answer on how I could set up my VLANs. It turns out that the base license on the ASA 5505 comes with a few restrictions with regards to VLANning -- in particular the number of VLANs and the number of trunks.

When you have the base license and the ASA is in routed mode (you have IPs on interfaces), you can have three VLANs configured. One of them, however, has to be configured to not forward to one of the other VLANs. I had to go over that a few times before I got what the doc was saying. Basically, you have two VLANs that are fully functional and one that can only initiate traffic to one of the others. At home, I consolidated my network down to three VLAN -- outside (I'net), inside (Users), and a DMZ (Guests). The inside interface can initiate connections to both outside and the DMZ, but the DMZ can only talk to the outside VLAN. This is probably not a very big deal to an average user, but I'm a network guy and will add networks just to say I've got one more subnet than you do. :)

I also had some confusion over the number of trunks available on this guy. My Aironet 1231 is set up to tag multiple bridged VLANs to the Ethernet so that I could have multiple SSIDs on it with each in their own VLAN. I did this by making the switch port on the 2950 into a tagged trunk. With the base license on the 5505, you don't get any trunks. I didn't find any docs that said you couldn't, but it's pretty obvious from the "show version" info.

> VLAN Trunk Ports : 0

This really puts a damper on my AP setup. I had to reconfigure it to just use a single, untagged bridged interface with a single SSID. If I wanted to implement the Aironet 1231 the way I had it beforehand, I would have to upgrade my license on the 5505. I'm not paying any more money for this thing, so I'll have to get one of my lab APs in place for guests. Thank God for eBay.

So, how do you configure this thing? First, let decide on our VLANs -- say VLANs 11, 12, and 13. The configuration is just like any PIX 7.X

> interface Vlan11 description OUTSIDE nameif outside security-level 0 ip address dhcp setroute ! interface Vlan12 description GUESTS no forward interface Vlan13 nameif guests security-level 10 ip address 192.168.13.1 255.255.255.0 ! interface Vlan13 description USER nameif inside security-level 100 ip address 192.168.14.1 255.255.255.0

The interface VLANs are what handles the IP addresses. We'll put specific ports in the VLANs in a minute, so hold tight. The configuration is pretty self-explanatory with VLAN11 being the outside (I'net), VLAN 12 being the DMZ (guests), and VLAN13 being the inside (users).  There is, however, the one line that reads _no forward interface Vlan13_. This is the line  that makes dictates which VLAN is a DMZ with respect to the base license and that this VLAN (VLAN12) can't initiate connections to VLAN13. You also might have noticed the line _ip address dhcp setroute_. You can read [one of the earlier articles](http://aconaway.com/2008/03/22/default-route-via-dhcp-on-an-asa-5505/ "AConaway.com -- Default Route via DHCP on an ASA 5505") on that guy.

The last thing you do is to put ports in VLANs. The ports on the 5505 are labeled Ethernet0/0 - 7, and you treat them just like an IOS switch with the _switchport access vlan X_ command. At home, you would plug your cable modem (or whatever) into one of the interfaces (say, Ethernet0/0) and put that port into VLAN11.

> interface Ethernet0/0 switchport access vlan 11

Plug all your stuff in, put the ports in the right VLAN, and enjoy.
