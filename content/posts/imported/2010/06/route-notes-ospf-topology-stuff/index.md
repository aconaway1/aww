---
title: "ROUTE Notes - OSPF Topology Stuff"
date: 2010-06-20
tags: 
  - "842-902"
  - "certification"
  - "cisco"
  - "database"
  - "ospf"
  - "route"
  - "topology"
---

Feel free to correct.

**Study Questions**

- The obvious first question involves the common LSA types and their function.  Can you list them?

Type-1 - Router - Lists each router their connected IP addresses Type-2 - Network - Lists all the transit, or multiaccess, networks Type-3 - Net Summary - Defines a  host route for interarea routes; this is from the ABR Type-4 - ASBR Summary - Defines a host route for an external (to OSPF) route; this is from an ASBR Type-5 - AS External - Lists the networks advertised into OSPF from external sources (redistribution) Type-7 - NSSA External - External routes injected into a not-so-stubby area

- What information about the OSPF area does a router's OSPF database contain?

Everything, basically.  The database includes all the routers in the area, the IPs of every OSPF-enable interface, all the networks, and the costs of each hop.  The SPF algorithm uses all this information to figure out the best path to all networks advertised.

- Define the reference bandwidth in OSPF.  What's the default value, and how do you change it?

The reference bandwidth is the bandwidth that is divided by an interface bandwidth to get a the cost for that link.  The default value is 100Mbps, which can be changed with the _auto-cost reference-bandwidth_ OSPF subcommand.

- How can you override the cost on an interface?

You can change the reference bandwidth, but that will change the costs of all interfaces.  The _ip ospf cost X_ command on an interface will do the trick.  You can also give it the old _bandwidth_ change (the one that tends to break or influence other things).

- What is the formula for calculating the cost of an interface?

reference bandwidth / interface bandwidth

- What are the five OSPF message types, and what do they do?

Hello - Establishes neighbor relationships Database Description (DBD) - Send summaries of the LSAs a router has Link State Requests (LSR) - Sent to a router to ask for more details on an LSA Link State Update (LSU) - Reply to an LSR that includes the details of the requested LSA Link State Acknowledgment (LSAck) - An acknowledgement of the DBD

- How often does a router send its full OSPF database to its neighbors?

It doesn't.  It does, however, send any self-originated LSAs (LSAs that it generated) every 30 minutes (1800 seconds).

- R1 is an ABR to area 1 with an area 0 route to a network with a cost of 100.  R2, also the same ABR setup, advertises the same route to area 1 (and, thus, R1) with a cost of 10.  Which route does R1 take?

ABRs always take an intra-area route over and interarea route, so the path with a cost of 100 will be chosen.

- You see a type-2 LSA in a router's database.  Without knowing what the details of the LSA are, list some things you can assume have happened.

Type-2 LSAs mean a transit network (multiaccess network) is turned up somewhere. This transit network has two or more OSPF routers on it. An election for DR and BDR has taken place. The DR has started acting as a pseudonode for the transit network. All other area routers have been told about that transit network. \[There are many others, I'm sure.\]

- What configuration is required to enable unequal-cost load balancing in OSPF?

This isn't EIGRP; you can't do that.

- It seems that your OSPF database has 95 equal-cost paths to the same network.  By default, how many show up in the routing table?

Four.  You can change this with the _maximum-paths_ directive under OSPF.

- Your 700 series router has been elected the DR on a transit network.  How do you make sure your 12000 series is elected instead?

On the 12000's interface, set the priority higher with the _ip ospf priority x_ command.

**What Command Was That**

What command...

...shows all the type-1 (router) LSAs that a router has seen?

show ip ospf database router

...shows all the type-2 (network) LSAs that a router has seen?

show ip ospf database network

...shows all the type-3 (network summary) LSAs that a router has seen?

show ip ospf database summary

...shows the maximum paths OSPF will send to the routing table?

show ip protocols

...shows what transit networks exist in the area?

show ip ospf database network

...shows all the routers in the area?

show ip ospf database router

...shows what router advertise a particular transit network?

show ip ospf database

...shows the DR and BDR for a transit network?

show ip ospf interface

...shows the reference bandwidth?

show ip protocols

...shows how many times the SPF algorithm has been run in an area?

show ip ospf - OR - show ip ospf statistics

...shows how many of each message type a router has sent?

show ip ospf traffic
