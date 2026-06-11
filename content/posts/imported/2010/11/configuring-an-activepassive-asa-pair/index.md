---
title: "Configuring an Active/Passive ASA Pair"
date: 2010-11-20
categories: 
  - "asa"
tags: 
  - "asa"
  - "cisco"
  - "failover"
  - "pix"
---

A buddy asked for some help on configuring a pair of ASAs in active/passive mode, and, by pure coincidence, my newest project is to set up the same.  I've done it many time, but it's one of those things that you don't really do every day (unless you're a VAR or something).  These things always get covered in rust very quickly in my head, but, once I get one or two details back to the surface, it all comes flooding back. I better take the time to jot down the details.  <!--more-->In this case, I'm working on 5510s running 8.2(1).

Assuming both firewalls have the same hardware and software (and a valid license for failover), it's not hard to configure active/passive pairs.  First of all, you have to declare what role each unit will take - primary or secondary.  This is just a label and gives neither pair one advantage over the other.  It's definitely not a priority like in HSRP, so don't think of it like that.  It's mostly to know which firewall you're using since configs on both firewalls will be the same (including the hostname and IP) after you're done.

> firewall(config)#failover lan unit ( primary | secondary )

This step is the only one that's different between the primary and secondary firewalls.  The rest apply to both units.

The next step is to configure an interface called the failover link that is used to synchronize the config.  The logical choice for a config (at least in my mind) is the Management0/0 interface, but the recommendation is that you use an interface that has the same capacity as the production interfaces.  If you're running on GigabitEthernet interfaces, though, you may want to avoid M0/0 since it's FastEthernet.  Since this is a box full of FEs, it doesn't matter which one we use, so let's use Ethernet 0/3 to mix it up a bit.  To configure this interface for failover, you need bind a physical interface to a logical name and then give that name an IP address.  Of course, let's not forget to admin up the interface.  We'll call our interface "FOVER".

> firewall(config)#failover lan interface FOVER Ethernet0/3 firewall(config)#failover interface ip FOVER 10.10.10.1 255.255.255.252 standby 10.10.10.2 firewall(config)#interface Ethernet0/3 firewall(config-if)#no shutdown

Notice the _standby_ directive when you configure the IP addresses.  The same syntax is used to configure IP addresses on all interface, but that's beyond the scope here.  Let's move on.

The next step is to configure an interface used to synchronize firewall state information.  This is called the stateful failover link and allows the firewalls to sync their state tables like xlate, conn, etc.  This allows the firewalls to fail without losing any active connection.  You can use a dedicated interface for this as well, but it's very common to use the failover link we already configured.

> firewall(config)#failover link FOVER

Yes, that's using the _link_ command just to make this all confusing.  I'm not really sure why it's called that.  I would think a name like "state" would be a little better.

Finally, you enable failover with a very complicated command.

> firewall(config)#failover

That's it.  If everything is configured correctly, you'll see something like this roll across the console and in your logs in a few seconds.

> Detected an Active mate Beginning configuration replication from mate.

To check the status, run a _show failover_.

> firewall# sh failover Failover On Failover unit Primary Failover LAN Interface: FOVER Ethernet0/3 (up) Unit Poll frequency 200 milliseconds, holdtime 800 milliseconds Interface Poll frequency 500 milliseconds, holdtime 25 seconds Interface Policy 1 Monitored Interfaces 3 of 250 maximum failover replication http Version: Ours 8.2(1), Mate 8.2(1) Last Failover at: 13:33:10 UTC Nov 18 2010 This host: Primary - Active Active time: 89785 (sec) slot 0: ASA5510 hw/sw rev (2.0/8.2(1)) status (Up Sys) Interface OUTSIDE (1.2.3.4): Normal Interface DMZ (10.1.2.1): Normal Interface INSIDE (10.1.1.1): Normal slot 1: empty Other host: Secondary - Standby Ready Active time: 0 (sec) slot 0: ASA5510 hw/sw rev (2.0/8.2(1)) status (Up Sys) Interface OUTSIDE (1.2.3.5): Normal Interface DMZ (10.1.2.2): Normal Interface INSIDE (10.1.1.2): Normal slot 1: empty <SNIP>

The output shows a couple things.  First, you can see that failover is enabled and that we're on the primary unit.  We also see the last failover and how long each unit has been active.

Send any expensive PIX failover cables questions my way.

Audio commentary

\[audio:http://aconaway.com/wp-content/uploads/2010/11/Configuring-an-Active-Passive-ASA-Pair.mp3|titles=Configuring an Active-Passive ASA Pair\]
