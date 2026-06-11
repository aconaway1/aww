---
title: "ROUTE Notes - IGP Redistribution"
date: 2010-06-22
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eigrp"
  - "ospf"
  - "redistribution"
---

As always, feel free to correct.

**Study Questions**

- When you redistribute OSPF into EIGRP, what are you really redistributing?

Routes knows via OSPF Networks of OSPF-enabled interfaces

- What's the default cost of an EIGRP route redistributed into OSPF?

20

- What's the default metric of an OSPF route redistributed into EIGRP?

There is none since EIGRP has all those nifty k-values that have to be processed.  Routes actually won't redistribute without them.

- How do you set the metrics of a route redistributed into EIGRP?

Set the default metric with the _default-metric_ subcommand Set the metric in the _redistribute ... metric_ subcommand Set the metric with a route-map in the _redistribute ... route-map_ subcommand

- If you have a default metric set under EIGRP and a metric set on a redistribution of OSPF, which does the router use?

The router uses the metric for the redistribution.

- What's special about the metric when redistributing one EIGRP AS into another?

The metric is copied from one AS to the other.

- What is I redistribute one OSPF domain into another?

The same thing happens - the metric is copied from the originating domain.

- What's the difference in AD between an EIGRP and an external EIGRP route?

EIGRP: 90 External EIGRP: 170  \[Didn't I do a blog post about this last month?\]

- What's the difference between an external type 1 and  an external type 2 OSPF route?

External 2 routes, only the external cost is used; no router increments the cost.  For external 1 routes, the external cost is incremented by each route with the internal cost.

- Which of O E1 and O E2 routes is more preferred and why?

E1s are preferred because they're considered more accurate.  \[Didn't I blog on this last month, too?\]

- I have _redistribute eigrp 1_ configured in my OSPF config, but 10.0.0.0/24 isn't showing up in OSPF.  What gives?

OSPF only redistributes classful routes unless you add the _subnets_ option to the redistribution command.

- What are the options in the _redistribute_ directive when redistributing OSPF into EIGRP?

redistribute ospf _process-id_ \[ metric _bandwidth delay reliabilityload  mtu_ \] \[ match { _internal_ | _nssa-external_ | _external 1_ | _external 2_ } \] \[ tag _tag-value_ \] \[ route-map _route-map_ \]

- What are the options in the _redistribute_ directive when redistributing EIGRP into OSPF?

redistribute ospf _process-id_ \[ metric _metric_ \] \[ metric-type _metric-type_ \] \[ match { _internal_ |_nssa-external_ | _external 1_ | _external 2_ } \] \[ tag _tag-value_ \] \[ route-map _route-map_ \] \[ subnets \]

- What do type-4 LSAs do?

If an external route comes from another area, the ABR uses type-4 LSAs to advertise the cost of the route from the ABR to the ASBR.  Routes use this cost as a tie breaker if the internal cost is the same from two ABRs.

- What type of LSA are used to flood routing advertisements from an external NSSA area into area 0?

The NSSA ASBR uses type-7s to flood into the NSSA, but the ABR to area 0 converts those to type-5s.

**What Command Was That**

What command...

- ...show all the EIGRP routes that originated from other routing protocols?

show ip route eigrp | inc ^D EX

- ...shows all the OSPF routes that originated from other routing protocols?

show ip route ospf | incl ^O E\[12\]

- ...show all the type-4 LSAs floating around in an OSPF area?

show ip ospf database asbr-summary

- ...show the cost to get from a router to an ASBR?

show ip ospf border-routers
