---
title: "BGP Notes - Route Reflectors"
date: 2011-06-11
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "bgp"
  - "ccie"
  - "reflector"
  - "route"
  - "written"
---

- Route reflectors remove the requirement of having a full mesh iBGP network.
- Any iBGP route a router reflector learns is sent to all route reflector clients.
    - Non-client iBGP neighbors do not get the new route per iBGP rules.
- RR clients are configured like normal iBGP routers.
    - All RR client config is done on the route reflector.
- RRs and clients are part of a _cluster_.
    - RRs in each cluster must be neighbors with each other.
    - Each cluster RR appends the cluster ID to the CLUSTER\_ID PA; this is used similarly to AS\_CONFED\_SEQ.
- The ORIGINATOR\_ID PA is set by and preserved by the RR.
    - If a route contains the ORIGINATOR\_ID of the receiving router, the update is ignored.
- Only best routes are passed to RR clients and non-client neighbors.

> ```
> router bgp 123
>  no synchronization
>  bgp cluster-id 1
>  neighbor 6.6.6.6 remote-as 123
>  neighbor 6.6.6.6 route-reflector-client
> ```

---

Comment with corrections, please.
