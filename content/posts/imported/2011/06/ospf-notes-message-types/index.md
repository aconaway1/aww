---
title: "OSPF Notes - Message Types"
date: 2011-06-01
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ccie"
  - "cisco"
  - "dbd"
  - "hello"
  - "lsa"
  - "lsack"
  - "lsr"
  - "lsu"
  - "message"
  - "ospf"
  - "type"
  - "written"
---

I have had my nose deep in several books in preparation for my CCIE R&S written exam, so I haven't been blogging much at all.  Now that I've made it to the more familiar topics, I'm hoping to get some notes posted.  I'll start with OSPF message types.

As always, please feel free to correct me here.  I'm learning just like the rest of us.

**Hello** : These messages are used to establish neighbors and serve as keepalives among other things.

> ```
> Destination:  224.0.0.5
> Important Fields:
> Hello interval
> Dead interval
> Router priority (for DR election)
>   Known DR
>   Known BDR
>   Active neighbors
> ```

**Database Descriptor (DBD or DD)** : These messages send summaries of a router's known LSAs to a new neighbor.  Receiving routers can use this information to compare to their database and ask for more details if needed.

> ```
> Destination:  Unicast IP of the new neighbor
> Important Fields:
>   LSA header
> ```

**Link State Request (LSR)** : Once a router has received a DBD, it [Pokies](http://www.blackboxrecorder.net/) parses through the info in it to see if the message is either more up-to-date or if it has some new info in it (like a new network).  If the router needs an update, it asks for the full LSA through an LSR.

> ```
> Destination:  Unicast IP of the router that sent the DBD
> Important Fields:
>   LSA type
>   LSA requested
> ```

**Link State Update (LSU)** :  When a router receives an LSR, it responds with an LSU that contains the details information for the requested LSA.  It also sends an unsolicited LSU whenever it learns of new LSAs such as when you turn up a new interface.

> ```
> Destination:  Unicast IP of the requesting router, 224.0.0.5, or 224.0.0.6 depending on who's updating whom
> Important Fields:
>   LS age
>   LS sequence number
>   Full LSA
> ```

**Link State Acknowledgement (LSAck)** : If a router receives an LSU, it responds with an LSAck to acknowledge it was received.

> ```
> Destination:  224.0.0.5
> Important Fields:
>   LS sequence number
> ```
