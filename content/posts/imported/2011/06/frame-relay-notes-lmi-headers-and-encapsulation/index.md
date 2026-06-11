---
title: "Frame Relay Notes - LMI, Headers, and Encapsulation"
date: 2011-06-23
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "ansi"
  - "ccie"
  - "encapsulation"
  - "frame"
  - "header"
  - "ietf"
  - "lmi"
  - "q933a"
  - "relay"
  - "written"
---

**Local Management Interface**

- Manages link between the router and frame relay switch
- Routers send _Status Enquiry_ to the switch
- The switch responds with a _Status_ message informing the router of the DLCIs available
- Serves as a keepalive
- Default keepalive is 10 seconds, 3 misses is failed
- Three types
    - cisco <- default
    - ansi (Annex D)
    - q933a

> ```
> R1(config)#interface s1/0
> R1(config-if)#frame-relay lmi-type ansi
> ```

**Headers and Encapsulation**

- Link Access Procedure for Frame-mode Bearer Services (LAPF) is the first header
    - Includes DLCI, DE, FECN, BECN
    - To be read by the frame relay switch
- Frame relay encapsulation header is next
    - To be read by the router on the other end of the VC
- Two types
    - cisco : proprietary <- default
    - ietf : IETF RFC 2427

> ```
> R1(config)#interface s1/0
> R1(config-if)#frame-relay encapsulation ietf <- for all DLCIs
> - or -
> R1(config-if)#frame-relay interface-dlci 100 ietf <- for specific DLCIs
> - or - 
> R1(config-if)#frame-relay map ip 10.0.0.1 ietf <- for specific DLCis
> ```
