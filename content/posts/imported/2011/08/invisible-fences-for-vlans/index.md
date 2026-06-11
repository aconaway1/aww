---
title: "Invisible fences for VLANs"
date: 2011-08-09
categories: 
  - "cisco"
---

_This week we have a guest post from CJ Infantino. He is currently writes on [convergingontheedge.com](http://convergingontheedge.com). You can find him hanging out on Google Plus as [CJ Infantino](https://plus.google.com/111171425909122797357/about) or follow him [@cjinfantino](https://twitter.com/#!/cjinfantino) on twitter._

\-------------------------------------

The other day I was adding VLANs to the the allowed list on the core routers at work. It was then a question came to mind, “Does the VLAN allowed list filter ingress or egress traffic?”.

Now, because all good engineers would configure the allowed list on both ends – as Aaron would say – in the grand scheme of things this really doesn't matter, but being the inquisitive guy that I am, I wanted to know.

So I searched, and searched and google'd and could not find the answer. At that point there was only one thing left to do – lab it up!

<!--more-->**The Setup:**

 

|PC1| ------ \[SW1\] --- _trunk_ --- \[SW2\] ------ |PC2|

 

**The Test:**

 

We will setup a trunk between SW1 and SW2. PC1 and PC2 will connect to SW1 and SW2 respectively. Each PC will connect into an access port configured for VLAN 10.

 

Output for SW1:

 

> SW1#show run int f0/3
> 
> interface FastEthernet0/3 switchport access vlan 10 switchport mode access

Output for SW2:

 

> S2#sho run int f0/7
> 
> interface FastEthernet0/7
> 
> switchport access vlan 10
> 
> switchport mode access

 

Next, we configure SW1's trunk port with VLAN10 in the allowed list, and SW2's trunk for ONLY VLAN20. So VLAN10 will not be allowed on the trunk.

 

Output for SW1:

 

> SW1#sho run
> 
> interface FastEthernet0/1
> 
> switchport trunk encapsulation dot1q
> 
> switchport trunk allowed vlan 10
> 
> switchport mode trunk

 

Output for SW2:

 

> SW2: sho run
> 
> interface FastEthernet0/1
> 
> switchport trunk encapsulation dot1q
> 
> switchport trunk allowed vlan 20
> 
> switchport mode trunk
> 
> end

 

And to verify the trunks are setup properly:

 

> SW1#sho int f0/1 trunk
> 
>  
> 
> Port Mode Encapsulation Status Native vlan
> 
> Fa0/1 on 802.1q trunking 1
> 
>  
> 
> Port Vlans allowed on trunk
> 
> Fa0/1 10
> 
>  
> 
> Port Vlans allowed and active in management domain
> 
> Fa0/1 10
> 
>  
> 
> Port Vlans in spanning tree forwarding state and not pruned
> 
> Fa0/1 10
> 
>  
> 
> \----
> 
>  
> 
> SW2#sho int f0/1 trunk
> 
>  
> 
> Port Mode Encapsulation Status Native vlan
> 
> Fa0/1 on 802.1q trunking 1
> 
>  
> 
> Port Vlans allowed on trunk
> 
> Fa0/1 20
> 
>  
> 
> Port Vlans allowed and active in management domain
> 
> Fa0/1 20
> 
>  
> 
> Port Vlans in spanning tree forwarding state and not pruned
> 
> Fa0/1 20

 

Now, if we ping from PC1 to PC2, it will pass over the trunk port and if we see the icmp echo hit PC2's interface we can safely say it only filters on egress. We know too, that the ping will ultimately fail even if SW2 allows the packet on ingress, it has to then filter on egress so the reply would never reach PC1.

 

Lets run a ping and test it out.

 

> PC1% ping 192.168.1.2
> 
> PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
> 
> From 192.168.1.1 icmp\_seq=22 Destination Host Unreachable

 

It failed and we also never saw the packet hit the interface on PC2. So this tells us that the command filters both ingress and egress frames. Just to verify we will add VLAN10 to the allowed list on SW2 and see if the ping goes through.

 

> SW2#sho run
> 
> interface FastEthernet0/1
> 
> switchport trunk encapsulation dot1q
> 
> switchport trunk allowed vlan 10,20
> 
> switchport mode trunk
> 
>  
> 
> \----
> 
>  
> 
> SW2#sho int f0/1 trunk
> 
>  
> 
> Port Mode Encapsulation Status Native vlan
> 
> Fa0/1 on 802.1q trunking 1
> 
>  
> 
> Port Vlans allowed on trunk
> 
> Fa0/1 10, 20
> 
>  
> 
> Port Vlans allowed and active in management domain
> 
> Fa0/1 10, 20
> 
>  
> 
> Port Vlans in spanning tree forwarding state and not pruned
> 
> Fa0/1 10, 20

 

Next we will ping from PC1 to PC2, let's take a look.

 

> PC1% ping 192.168.1.2
> 
> PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
> 
> 64 bytes from 192.168.1.2: icmp\_req=1 ttl=64 time=9.66 ms
> 
> 64 bytes from 192.168.1.2: icmp\_req=2 ttl=64 time=0.414 ms

 

So, there you have it. PC1 can now ping PC2.

 

So next time you're configuring your VLAN allowed list remember, it filters in both directions! And most importantly, I will sleep better now that that mystery has been solved.

 

Did you know this already? No? Am I the only one who was wondering this?

 

CJ
