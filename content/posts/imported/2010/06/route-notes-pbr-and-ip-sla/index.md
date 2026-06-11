---
title: "ROUTE Notes - PBR and IP SLA"
date: 2010-06-24
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "ip"
  - "pbr"
  - "policy"
  - "route"
  - "routing"
  - "sla"
---

Feel free to correct.

**Study Questions**

- What's the most primitive way to get traffic destined to a single host to use a different path than your dynamic IGP dictates?

Use a static route.

- What's the most primitive way to get traffic sourced from a single host to use a different path than your dynamic IGP dictates?

Use policy-based routing (PBR).

- What's the most primitive way to get traffic sourced from a single host and destined for another host to use a different path than your dynamic IGP dictates?

Use PBR.

- What are the steps to configure PBR?

Configure a route-map to match the desired traffic Apply that route-map to an interface with the _ip policy route-map_ command

- Configure PBR to send traffic that arrives on F0/0 from 10.0.0.5 destined for for 192.168.3.3 to be sent out the s0/0 interface.

R1(config)#ip access-list extended PBR-ACL1 R1(config)#permit ip host 10.0.0.5 host 192.168.3.3 R1(config)#route-map PBR-F0/0 R1(config-route-map)#match ip address PBR-ACL1 R1(config-route-map)#set interface s0/0 R1(config-route-map)#int f0/0 R1(config-if)#ip policy route-map PBR-F0/0

- What happens if you use PBR to redirect traffic to an IP that becomes unreachable?

That clause in the route-map is ignored, and the normal routing table is used.

- What difference does using _default_ make in the _set_ directive of the route-map?

If you use the _default_ parameter in the set directive, then the router will first try to use the routing table to forward traffic before using the PBR settings.  The one caveat, though, is the default chosen for the traffic cannot be the default route; a more-specific route must be in the routing table or else the PBR logic rears its head.

- What is IP SLA?

IP SLA is a feature of a Cisco IOS device where a process measures the behavior of the network.

- Why is this topic in the ROUTE book?

You can configure a track object to use IP SLAs to get a "failed" or "ok" status.  That track object can be applied to static routes and PBR so that the routing is changed if the IP SLA measures a characteristic outside of normal parameters.

- What are the steps to configure IP SLA?

Create an IP SLA operation. Define the type and parameters for the operation. Define the frequency to run the operation. Schedule when to start the operation.

- How do I use IP SLA to check if a host is pingable?

You use the icmp-echo as the operation type along with, at minimum, the IP address to ping.

- How can I use IP SLA to know whether a static route is usable or not?

First, create an IP SLA operation to ping the gateway for that route.

R1(config)#ip sla 5 R1(config-ip-sla)#icmp-echo 1.1.1.1 R1(config-ip-sla)#frequency 60  \[ in seconds \] R1(config-ip-sla)#exit R1(config)#ip sla schedule 5 start-time now life forever

Then create a track object that references the IP SLA operation you just created.

R1(config)#track 2 ip sla 5 state R1(config-track)#delay up 90 down 90 \[ up if delay is below 90, down if above 90 \]

Finally, add the track to the static route.

R1(config)#ip route 10.0.0.0 255.255.0.0 1.1.1.1 track 2

Now, if the router can't ping 1.1.1.1, the static route will be taken out of the routing table.

- What's an IP SLA responder?

That's (usually) a router that has been configured to interact with the IP SLA operation of another router to get characteristics of the connection between the two.  These characteristics include jitter and TCP establishment times.

- How can I use a track object in PBR?

In the _set_ directive, you use the track parameter.  The _sequence_ parameter is also used, but it's not a part of the tracking process; it's used to have the router go down a list of next hops until it finds on that's available.  Here's an example.

set ip next-hop verify-availability 192.168.0.1 1 track 5

- Ummm...the book doesn't have anything about that; what gives?

The cert guide leaves that part out for some reason even though it's a very important part of IP SLA and PBR.  Go figure.

**What Command Was That**

What command...

- ...shows interfaces that have PBR configured on them?

show ip policy

- ...shows the routing table and includes all the PBR configuration?

There isn't one.  You have to remember to check for PBR when traffic isn't flowing as you think it should.

- ...shows the IP SLA configuration?

show ip sla configuration \[ Duh! \]

- ...shows the IP SLA statistics?

show ip sla statistics \[ Duh, again! \]

- ...shows the track objects on a router?

show track
