---
title: "Configuring GLBP"
date: 2008-03-22
tags: 
  - "glbp"
  - "lan"
---

Believe it or not, I got a request for an article on how to configure GLBP. I'm as shocked as you are, so here it goes.

[The Gateway Load Balancing Protocol (GLBP)](http://cisco.com/en/US/docs/ios/12_2t/12_2t15/feature/guide/ft_glbp.html "Cisco.com -- GLBP") is another Cisco-proprietary protocol for providing highly-available gateways on a network...but there's a twist. GLBP, as you can figure out from the name, load-balances the traffic going through the participating routers. With [HSRP](/tags/hsrp/ "AConaway.com -- HSRP") and VRRP, one host is the active peer and handles all the traffic until it dies, then another peer takes over. With GLBP, all the routers accept traffic.

The key is the virtual MAC address. When you configure a router to use GLBP, it discovers all the other routers configured for GLBP, and an election is held. The winner is called the Active Virtual Gateway (AVG) and assigns virtual MAC addresses to all the members in the group (called Active Virtual Forwarders or AVFs). When a host on the network ARPs for the virtual IP, the AVG answers the request with one of the virtual MAC addresses of the AVFs. The next ARP request gets another virtual MAC, etc. Do this a few times, and the hosts are the network are splitting their traffic among all the AVFs.

Config time!

```
GLBP0(config)#interface f0/0
GLBP0(config-if)#ip address 192.168.0.10 255.255.255.0
GLBP0(config-if)#glbp 0 ip 192.168.0.1

GLBP1(config)#interface f0/0
GLBP1(config-if)#ip address 192.168.0.11 255.255.255.0
GLBP1(config-if)#glbp 0 ip 192.168.0.1

GLBP2(config)#interface f0/0
GLBP2(config-if)#ip address 192.168.0.12 255.255.255.0
GLBP2(config-if)#glbp 0 ip 192.168.0.1
```

Very simple. This sets the IP on f0/0 of three routers and enables GLBP group 0 for the IP 192.168.0.1. The group number, 0 in this case, is the same as in HSRP; you can have more than one instance of GLBP on an interface, so you have to tell it what settings go with what group.

After a few seconds of yelling at each other, the routers will have decided who the active and backup AVGs are, who the AVFs are, and what the virtual MACs for the AVFs are. After convergence, you can run the _show glbp brief_ command to see what the status is.

```
GLBP0#sh glbp brief
Interface   Grp  Fwd Pri State    Address         Active router   Standby router
Fa0/0       0    - 100 Listen   192.168.0.1     192.168.0.12    192.168.0.11
Fa0/0       0    1   - Listen   0007.b400.0001  192.168.0.12    -
Fa0/0       0    2   - Active   0007.b400.0002  local           -
Fa0/0       0    3   - Listen   0007.b400.0003  192.168.0.11    -
```

In our example, the IP 192.168.0.12 is the AVG (GLBP2) with 192.168.0.11 being the backup AVG (GLBP1). You can also see that three virtual MACs have been assigned -- 0007.b400.001 - 3.

Those are the basics, but there are a few more things worth mentioning that you should look at on your own.

- By default, the load-balancing method is round robin, but you can set the GLBP balancing method to weighted, which uses configured weights on each router to determine who's next in line for ARP replies. Use the _load-balancing_ and _weighting_ directives.
- You can set priorities for each router to better control which one becomes the AVG and backup AVG with the _priority_ directive.
- You can have GLBP track objects [just as you do with HSRP](/posts/2007/10/object-tracking-and-hsrp/ "AConaway.com -- Object Tracking and HSRP"). Use the _weighting track_ configuration to do so.
- You can put passwords on the GLBP group to protect yourself from random routers trying to participate and hose things up. Look at _authentication_.
- By default, a higher priority router won't overthrow a lower one to become the AVG. You can turn this feature on with the _preempt_ directive.
