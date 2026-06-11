---
title: "ROUTE Notes - OSPF Neighbor Relationships"
date: 2010-06-18
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "843-902"
  - "certification"
  - "cisco"
  - "neighbor"
  - "ospf"
  - "route"
---

Feel free to correct.

**Study Questions**

- What are the definitions of the hello and dead intervals?

The hello intervals is how often a router sends hello messages.  The dead interval is how long to wait before considering a neighbor dead from lack of hello messages; this is 4x the hello interval by default.

- How do you keep OSPF from trying to detect neighbors on an interface?

Don’t configure a _network_ statement for that interface Make that interface passive

- What type of routers connect to multiple areas?

Area border router (ABR)

- What fields in the hello packet need to match in order for a router to neighbor with another via OSPF?

Subnet Area Hello and dead intervals IP MTU Authentication method Authentication key

- What’s another way to put an interface into an OSPF area other than the old _network_ statement?

R1(config-if)#ip ospf 1 area 0

- Don’t the process IDs have to match?

No.

- How is the router ID calculated?

The router ID is discovered just as EIGRP does it.  First, it looks for a _route-id_ command.  If one does not exist, the highest IP of on a up/up loopback interface is used.  If one does not exist, the highest IP of the rest of the up/up interfaces is used.

- What is the protocol, source, and destination in an OSPF hello packet?

OSPF used protocol 89 (not TCP or UDP) sourced from the interface sending the packet to 224.0.0.5.

- What happens after neighbors are brought up successfully?

Topology databases are exchanged.  Each router sends all the information it has about the area to its new neighbor.

- What would happen if the MTUs of two potential neighbors was different?

The routers will become neighbors, but the topology exchange will not occur successfully.  \[I think I’m missing a piece here.  This seems very prescriptive in a situation where I would expect more chaos.\]

- What types of authentication can be configured with OSPF?

None (type 0) Cleartext (type 1) MD5 hash (type 2)

- How do you configure a router to use MD5 authentication with OSPF?

R1(config-router)#area 0 authentication message-digest

- That didn’t work.  What are we missing?

You have to set the key, dummy.  Do that on the interface.

R1(config-if)#ip ospf message-digest-key 1 md5 KEY

- What is a DR?  How about a BDR?

A designated router (DR) is a router that has been elected to advertise all the router for a multi-access segment (like a LAN segment).  Every time a change occurs in the network, the router that detected it will notify the DR, who will then relay the information to the other routers on that segment.  The backup designated router (BDR) takes over that functionality if the DR fails.

- What are the default hello intervals for broadcast, point-to-point, NBMA, and point-to-multipoint interfaces?

Broadcast:  10 sec P-P:  10 sec NBMA:  30 P-M:  30

- How do you change the default hello timer?

R1(config-if)#ip ospf hello-interval X

- What types of segments use DRs and BDRs?

Only broadcast and NBMA segments use DRs and BDRs.

- What are some important states of an OSPF neighbor?  \[Yes, there are others.\]

INIT:   A router has received a hello from another router, but its own hello has not been acknowledged. 2WAY:  A router has received a hello and a acknowledgement from another router EXSTART:  The DRs and BDRs are elected, and exchanges are beginning with them FULL:  The router has successfully exchanged topology data with the neighbor

**What Command Was That?**

What command…

- …shows all the OSPF neighbors?

show ip ospf neighbor

- …shows the number of OSPF neighbors on an interface?

show ip ospf interface - OR - show ip ospf interface brief

- …shows the DR and BDR on an interface?

show ip ospf interface

- …shows the authentication method used?

show ip ospf interface

- …shows the hello and dead intervals configured on the router?

show ip ospf interface

- …shows the OSPF network type for an interface?

show ip ospf interface

- ...shows the state of a neighbor?

show ip ospf neighbor - OR - show ip ospf neighbor detail
