---
title: "EIGRP Notes - Message Types"
date: 2011-06-05
categories: 
  - "cisco"
---

Please correct if I'm being stupid...which is a lot of the time.

**Hello** : Discovers and maintains neighbors

> ```
> Destination:  224.0.0.10
> Important fields:
>   K values
> ```

**Update** : An update to the topology such as a route withdrawal or a metric change

> ```
> Destination:  224.0.0.10 -or- unicast during neighbor discovery
> Important fields:
>   Message sequence number
>   Route being updated including k values to compute metric
> ```

**Query** : Used to ask a neighbor if it has a route to a certain network; see [casino online for free](http://tangoessentials.com/) stuck-in-active

> ```
> Destination:  224.0.0.10
> Important fields:
>   Message sequence number
>   Network being queried
> ```

**Reply** : Sent in response to a query message telling a router whether or not it has a route

> ```
> Destination:
> Important fields:
>   Message sequence number
>   Sequence number of message to ack
>   Network being queried along with k values to compute metric
> 
> ```

**Ack** : Used to acknowledge updates, queries, replies

> ```
> Destination: Unicast address of router
> Important fields:
>   Sequence number of message to ack
> ```
