---
title: "Summary Post - OSPF Network Statement Order and Matching"
date: 2014-07-10
categories: 
  - "cisco"
  - "route"
tags: 
  - "area"
  - "network"
  - "ospf"
  - "specific"
  - "statement"
---

When you configure OSPF network statements, IOS orders them most-specific to least-specific then does a top-to-bottom match of the interfaces. It doesn't matter which order you put them in, the configuration will always be ordered with the longest prefix matches first.  Lab time!

I have router R1 with these interfaces.

> ```
> R1#sh ip int brief
> Interface                  IP-Address      OK? Method Status                Protocol
> FastEthernet0/0            10.0.0.1        YES manual up                    up
> FastEthernet0/1            unassigned      YES unset  administratively down down
> Loopback100                10.0.101.1      YES manual up                    up
> Loopback200                10.2.101.1      YES manual up                    up
> ```

Let's add the OSPF configuration where 10.0.0.0/8 is in area 2 then check what OSPF thinks is happening.

> ```
> R1(config)#router ospf 1
> R1(config-router)#network 10.0.0.0 0.255.255.255 area 2
> ...
> R1#show ip ospf interface brief
> Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
> Lo100        1     2               10.0.101.1/24      1     LOOP  0/0
> Lo200        1     2               10.2.101.1/24      1     LOOP  0/0
> Fa0/0        1     2               10.0.0.1/24        10    WAIT  0/0
> ```

All the interfaces are in area 2 as expected. Now let's add 10.0.0.0/16 into area 1 to see what happens.

> ```
> R1(config-router)#network 10.0.0.0 0.0.255.255 area 1
> ...
> R1#show ip ospf interface brief
> Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
> Lo100        1     1               10.0.101.1/24      1     LOOP  0/0
> Fa0/0        1     1               10.0.0.1/24        10    WAIT  0/0
> Lo200        1     2               10.2.101.1/24      1     LOOP  0/0
> ...
> router ospf 1
>  log-adjacency-changes
>  network 10.0.0.0 0.0.255.255 area 1
>  network 10.0.0.0 0.255.255.255 area 2
> ```

Alright. Lo200 stayed in area 2, but the other two interfaces moved to area 1. You can see that the network statement for area 1 was inserted above the area 2 config even though I configured it later.

I also got this interesting log message when I configured the new network statement. I thought it was worth mentioning.

> ```
> *Mar  1 00:13:20.571: %OSPF-6-AREACHG: 10.0.0.0/16 changed from area 2 to area 1
> ```

One more to grow on?  10.0.0.0/24 goes into area 0.

> ```
> R1(config)#router ospf 1
> R1(config-router)#network 10.0.0.0 0.0.0.255 area 0
> ...
> R1#show ip ospf interface brief
> Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
> Fa0/0        1     0               10.0.0.1/24        10    WAIT  0/0
> Lo100        1     1               10.0.101.1/24      1     LOOP  0/0
> Lo200        1     2               10.2.101.1/24      1     LOOP  0/0
> ...
> R1#show run | section ospf
> router ospf 1
>  log-adjacency-changes
>  network 10.0.0.0 0.0.0.255 area 0
>  network 10.0.0.0 0.0.255.255 area 1
>  network 10.0.0.0 0.255.255.255 area 2
> ```

Yes, I got the message again when F0/0 moved to area 0.

Send any ~~link state table updates~~ questions my way.
