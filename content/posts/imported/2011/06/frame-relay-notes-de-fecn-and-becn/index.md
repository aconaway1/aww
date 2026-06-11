---
title: "Frame Relay Notes - DE, FECN, and BECN"
date: 2011-06-23
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "becn"
  - "ccie"
  - "de"
  - "fecn"
  - "frame"
  - "frts"
  - "lapf"
  - "relay"
  - "written"
---

- All are part of the frame relay congestion management suite.
- Frame relay switches monitor links for CIR or oversubscription congestion on links.
    - If the VC has a CIR of 256k, the switch knows there is congestion if the customer is sending more than 256k down that VC.
- Discard Eligible
- - Flag in the LAPF header
    - Marks a frame as eligible to be dropped in case of congestion
    - Marked via the MQC
- Forward Explicit Congestion Notification
    - Flag in the LAPF header
    - Set by the switch when the frame is about to enter a link with congestion on a VC
        - Congestion in one direction
        - FECNs are set when the frame is going into the congestion.
    - Receiving router can see that there was congestion on the way.
    - FECNs can be used to activate adaptive shaping via FRTS.
    - Plain English:  If Router B receives a frame with the FECN flag set, that means that there is congestion on the path from Router A to this Router B, and that Router B should expect delays.
- Backward Explicit Congestion Notification
    - Flag in the LAPF header
    - Set by the switch when a frame has just left the link with congestion
        - Congestion is the opposite direction.
        - BECNs are set when the frame has just left a link that has congestion on it.
    - Notifies the original sending router that there is congestion along that VC.
    - Plain English:  If Router A receives a frame with the BECN flag set, that means that there is congestion from Router A towards Router B and that the sending host should calm down a little bit.
    

http://www.sinclair.org.au/keith/networking/frame\_relay.html -- Corrections requested.
