---
title: "BGP Notes - Message Types"
date: 2011-06-07
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "bgp"
  - "ccie"
  - "message"
  - "notes"
  - "types"
---

Corrigeme, por favor.

**Open** : When a neighbor is configured, the router sends an open to that neighbor to get the ball rolling.

> ```
> Destination:  The neighbor's configured IP
> Important fields:
>   My AS
> ```

**Update** : The routing  information

> ```
> Destination:  The neighbor's configured IP
> Important fields:
>   Advertised network Klonopin Online
>   Path attributes
> ```

**Keepalive** : Sent every 60 seconds by default

> ```
> Destination:  The neighbor's configured IP
> Important fields:
>   Nothing, really
> ```

**Notification** : When something is amiss, the router sends a notification message.  The receiver then closes the connection.

> ```
> Destination:  The neighbor's configured IP
> Important fields:
>   Error code
> ```
