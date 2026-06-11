---
title: "Using an Old Server as a Home Firewall"
date: 2007-08-10
tags: 
  - "linux"
  - "security"
---

You can use an old PC as a firewall at home (and at work, I guess). It's not that hard to do if you have a basic knowledge of Linux, DHCP, and IPtables, but that may be saying a lot.

Why would anyone want to do this, though? If you're like me, you like to know what's going on in the network. One of the Linksys routers you buy at Best Buy or Circuit City just doesn't let you monitor very well. You can't get very good logs off of it, so you don't really know what it's doing or complaining about. It also doesn't let you query the interfaces, so you really don't know how much bandwidth you're using.  If you have a Linux box as your router/firewall/gateway, you can get really good logs, monitor the interfaces with SNMP, and have some really great, granular control over your network.

To set up a simple network, you only need a machine with two NICs in it and a Linux distro, then follow these steps. This is a simplified procedure, but it should get you started.

1. Install Linux on the box. Make sure you have IPtables and DHCPd on it when you're done.
2. Cable one NIC to the cable modem (WAN) and the other to your switch (LAN).
3. Hard-code the IP address of the LAN interface to your favorite IP space.
4. Set up DHCPd to serve IPs to your LAN.  Remember to set your default gateway option to the IP of the LAN interface and your DNS servers to your ISP's servers.
5. Set up IPtables as your firewall. This is not a simple endeavor, but there are several links for help below.
6. Enjoy your newfound infrastructure.

This is how I started out, but my network has grown tremendously in the last year or so. I've got four NICs in my gateway to create different network segments, and my IPtables file is about 5k now with 79 rules in the system. The price you pay for being a network dude.

Another thing you may want to do is recycle the wireless router.  If everything's working in place after you implement the new box, you can turn off DHCP on your wireless router and stick it into the LAN segment.  You can still attach to it with your notebook or whatever, but you'll get your address from the server just like the wired hosts.

Since you're running Linux now, you can also look at using some other tools to help monitor everything.

- [Nagios](http://www.nagios.org/ "The Nagios site") -- for monitoring the status of the network
- [Cacti](http://cacti.net/ "The Cacti site") \-- for trending and usage graphs
- [Syslog](http://en.wikipedia.org/wiki/Syslog "Wikipedia Article") \-- for collecting log files
- [Apache](http://www.apache.org/ "The Apache site") \-- the de facto web server
- [Bind](http://en.wikipedia.org/wiki/BIND "Wikipedia Article") \-- for serving DNS locally
- [NTP](http://www.ntp.org/ "The NTP site") \-- for syncing all your machines' clocks
- [tcpdump](http://en.wikipedia.org/wiki/Tcpdump "Wikipedia Article") \-- for capturing packets coming in and out of the network

Let me know if you have any problems or need any help. ----- Some IPtables links to help you on your way:

- http://www.linuxhomenetworking.com/wiki/index.php/Quick\_HOWTO\_:\_Ch14\_:\_Linux\_Firewalls\_Using\_iptables
- http://www.sns.ias.edu/~jns/wp/iptables/
- http://www.yolinux.com/TUTORIALS/LinuxTutorialIptablesNetworkGateway.html
- http://www.faqs.org/docs/iptables/traversingoftables.html
