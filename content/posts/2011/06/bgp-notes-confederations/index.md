---
title: "BGP Notes - Confederations"
date: 2011-06-11
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "bgp"
  - "ccie"
  - "confederations"
  - "written"
---

- [RFC 3065](http://www.ietf.org/rfc/rfc3065.txt)
- BGP confederations reduce the size of full mesh iBGP ASes by dividing it up into different areas.
- Confederations also remove the need for BGP synchronization since all iBGP routers will have all routes.
- In effect, your iBGP AS gets chopped up into different sub-ASes.
- Each router is a member of a sub-AS and is a neighbor with every other router in that sub-AS (full mesh).
    - Neighbors within a sub-AS are called confederation iBGP neighbors.
    - Confederation iBGP neighbors act just like any other iBGP neighbor.
- At least one member of each sub-AS is neighbored with members of different sub-ASes.
    - Neighbors in different sub-ASes are called confederation eBGP neighbors.
    - Confederation eBGP neighbors have a default TTL of 1 just like true eBGP neighbors.
    - The NEXT\_HOP PA is not changed when passing routes between sub-ASes.
    - LOCAL\_PREF is also preserved.
- Confederations use the AS\_CONFED\_SEQ and AS\_CONFED\_SET fields in the AS\_PATH PA.
    - These fields act like AS\_PATHs to prevent loops.
    - These fields are cleared out when the route is passed to an eBGP neighbor.
    - If components of a summary route (an _aggregate-address_) have different AS\_CONFED\_SEQ values, the AS\_CONFED\_SET is used.
- Confederations ASes are not included when the router decides which route is best.
- BGP confederation routers are configured to be in a private ASN.
    - The confederations should be private to avoid AS conflicts.
    - The confederation identifier defines the AS at it appears to the world.

> ```
> router bgp 65001
>  no synchronization
>  bgp confederation identifier 123
>  bgp confederation peers 65002 65003
>  neighbor 2.2.2.2 remote-as 65002
>  neighbor 3.3.3.3 remote-as 65003
> ```

---

Comment with corrections, please.
