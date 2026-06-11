---
title: "BGP Route-reflectors"
date: 2008-04-17
tags: 
  - "routing"
---

If you're running iBGP, you may have run across this. What if you had three routers -- R0, R1, R2 -- that were running BGP under the same ASN, but R1 and R2 weren't peered? Any routes coming from R1 would not show up on R2 and vice versa. iBGP, by standard, does not pass on routes it learned via the same ASN. That is, if a router learns a route from another router in the same autonomous system, the route does not get forwarded. I guess it just assumes that all iBGP routers are fully meshed...I don't really know.

That sucks, right? One of several fixes for this is the _route-reflector-client_ directive under the BGP neighbor configuration. A route-reflector literally reflects the routes from one client to the others just as if you've got a fully meshed network. Here's a sample config for the reflector, R0.

`router bgp 65000 neighbor 10.0.0.11 remote-as 65000 neighbor 10.0.0.11 route-reflector-client neighbor 10.0.0.12 remote-as 65000 neighbor 10.0.0.12 route-reflector-client`

There's actually no additional config on R1 and R2 at all; the router reflection is transparent to them. They actually see the route just as though they got the update directly from the originating router, so any routes received from the reflector appear as though they came from the originator. In that same breath, you have to realize that R1 and R2 must have routes to each other since the untouched BGP routes will have a next-hop address as the originating router.

Edit: [Here's a crayon drawing](http://aconaway.com/static/labs/bgp/BGP-Route-reflector.png) I did to show an example of where you would use route-reflectors. R0 is connected to R1 and R2 via different WANs, but R1 and R2 aren't connected at all.
