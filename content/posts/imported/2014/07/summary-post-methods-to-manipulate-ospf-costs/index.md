---
title: "Summary Post - Methods to Manipulate OSPF Costs"
date: 2014-07-11
categories: 
  - "cisco"
---

There are three ways to manipulate the interface cost in OSPF.  One is very direct, one changes the presentation of the interface, and the other changes the calculations for every interface.

**Set the cost of the interface directly** - Just give it the number you want.  Easy.  This is the number OSPF will use in the SPF calculations without doing any math on the interface.

> ```
> R1(config-if)#ip ospf cost 8482
> ```

**Set the bandwidth of the interface** - The formula that OSPF uses to calculate interface cost is pretty easy to remember - (reference bandwidth) / (interface bandwidth).  Changing the interface bandwidth will obviously change the result of the calculation.  The same caveat for EIGRP route manipulation holds true here; if you change the bandwidth of the interface, you may affect other things like QoS...or EIGRP, now that I mention it.

> ```
> R1#sh ip ospf inter brief
> Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
> Fa0/0        1     0               192.0.2.1/24       10    DR    0/0
> R1#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> R1(config)#int f0/0
> R1(config-if)#bandwidth 10
> R1(config-if)#do show ip ospf interf brief
> Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
> Fa0/0        1     0               192.0.2.1/24       10000 DR    0/0
> R1(config-if)#
> ```

**Set the reference bandwidth** - This changes the numerator of the calculation.

> ```
> R1(config-if)#router ospf 1
> R1(config-router)#auto-cost reference-bandwidth 74362
> % OSPF: Reference bandwidth is changed.
>         Please ensure reference bandwidth is consistent across all routers.
> R1(config-router)#do show ip ospf interface brief
> Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
> Fa0/0        1     0               192.0.2.1/24       7436  DR    0/0
> ```

Notice the nice message about making the reference bandwidth consistent.  If you were to only set the reference bandwidth on this one routers, then each router would calculate the path costs differently.  That will lead to undesirable results, I imagine.

Send any SPF calculation overhead questions to me.
