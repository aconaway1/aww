---
title: "VLANs on Linux"
date: 2009-02-19
tags: 
  - "dot1q"
  - "lan"
  - "linux"
  - "switch"
  - "trunk"
  - "vlan"
---

My home network has a Linux box running IPTables as it's center point, and, since there are four networks, it has 4 NICs and 4 cables into the switch.  I kept running into problems with the NICs (they would reorder depending on what flavor of Linux was installed), so I wanted to consolidate the NICs down to 2 -- one for the Internet link and one for the LAN segments with [802.1q tagging](http://en.wikipedia.org/wiki/IEEE_802.1Q "Wikipedia.com -- IEEE 802.1q").

Disclosure:  I have only labbed this stuff out, and it seems to work, but I have yet to implement it.  Use at your own risk in the wild.

Configuring VLAN tagging on Linux is pretty simple, actually.  One way to do it is to use the _vconfig_ command to add and remove VLANs from interfaces.  As a demonstration, say you want to run VLANs 20 and 30 on eth0.  You would just do something like this.  Note that the interface you mention here has to be in an _UP_ state, so do an _ifconfig eth0 up_ if you need to get it into a good state.

> vconfig add eth0 20 vconfig add eth0 30

Now, whenever eth0 comes up, you'll have the interfaces eth0.20 and eth0.30.  You can give them IP addresses through the command line with _ifconfig_.

> ifconfig eth0.20 192.168.20.1 netmask 255.255.255.0 up ifconfig eth0.30 192.168.30.1 netmask 255.255.255.0 up

I didn't expect them to do so, but the IP addresses actually stay across network restarts; as long on the physical interface comes up, the VLANs come up with IPs and everything.  Speaking of network restarts, the "downfall" with using vconfig is that VLAN interfaces don't show as coming up or going down during network restarts;  I don't like that at all.

Another way to configure the VLANs is through the old-fashioned _network-scripts_ directory.  Copy your interface config (_ifcfg-eth0_) to the same name but with the VLAN extension (_ifcfg-eth0.20_) and edit it.  Change the device field appropriately along with the IP address subnet mask info.  For the final touch, at the end of the file, add this line.

> VLAN=yes

Personally, this is the way I would do it.  It lets you change configurations through the configuration files just like physical interfaces instead of trusting the configuration that resides out in the ether that is the Linux kernel.  Also, when you restart network, the interface itself actually goes up and down, so you can see what's going on with it.  If you need some help with this, check out [Redhat's manual](http://www.redhat.com/docs/manuals/linux/RHL-8.0-Manual/ref-guide/s1-networkscripts-interfaces.html "Redhat.com -- Interface Configuration Files") on it.  Let me know if you're still having problems with it.

Remember to set the box's switch port to a matching 802.1q [trunk](/tags/trunking/ "AConaway.com -- Trunking").  You've seen that before, but here's a refresher, assuming the Linux box is plugged into f0/1 on the switch.

> int f0/1 switchport switchport trunk encapsulation dot1q switchport mode trunk

To check the status of your VLANs, look in _/proc/net/vlan_.  You'll see the _config_ file, which lists all your VLANs.  You'll also see a device file (like eth0.20) with the statistics for that VLAN device (interface).

Let's talk security, though.  First of all, I could argue that a Linux box shouldn't be participating in any trunking at all.  There will be exceptions, but, in my experience, a Linux box (read: server) should only be on one network at a time and shouldn't straddle networks.  Do you really trust the Linux guys to keep their boxes from doing bad things on more than one network? I don't. Heh.

If, however, you need to use VLANs on a Linux box, you'll need to make sure you have only the proper VLANs running across this port (like we did with the [CSM VLAN](/posts/2008/11/configuring-dedicated-trunks-for-the-csm/ "AConaway.com -- Configuring Dedicated Trunks for the CSM")).  If a box were to be compromised, the bad guy could simply start adding VLANs to the server and suddenly get around your routers and firewalls.  Awesome, right?  Make sure you put in the _switchport trunked allowed vlan x_ directive so the server only has access to those VLANs.

As always, send me any four-leaf clovers questions you have.

P.S.:  For the record, since I haven't tried this in the field yet, I can't tell you how well it works with IPTables, but, from what I've been reading, it should work fine.  Good luck.

For the record, I've got this working at my house connected to a Cisco Catalyst 2950 trunk port.  I'm happy to report that it works like a champ with IPTables and everything.
