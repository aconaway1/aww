---
title: "ONT Notes - Intro to QoS"
date: 2010-01-21
tags: 
  - "642-845"
  - "autoqos"
  - "ccnp"
  - "certification"
  - "cisco"
  - "diffserv"
  - "intserv"
  - "ont"
  - "qos"
  - "test"
  - "voice"
  - "voip"
---

I'll try to keep it a little shorter this time.

**Major issues for converged enterprise networks**

- Available bandwidth: competition among applications
    - Fixes
        - Increase bandwidth: More power!
        - Properly queue based on classification and marking: QoS
        - Compress: cRTP, TCP header compression, etc.
- Delay: Lead time to get a packet to the destination
    - Types of delay
        - Processing delay: routing, switch delay
        - Queuing delay: how long a frame stays in an output queue
        - Serialization delay:  how long to put the frame on the wire
        - Propagation delay: the time to cross the physical medium
- Jitter (delay variation): Variation is the delay
    - Different delays mean different arrival times
    - De-jitter buffers save up packets to reduce jitter (like the old CD writers)
    - Fixes
        - More bandwidth
        - Prioritize sensitive data and forward first
        - Remark (reclassify) packets based on sensitivity
        - Enable L2 payload compression: make sure compression delay isn't worse than the jitter
        - Use header compression
- Packet loss: Packets are lost in the network somewhere
    - Fixes
        - More bandwidth
        - Increase buffers space: more room for the queue on the interface
        - Provide guaranteed bandwidth: Queuing and QoS
        - Congestion avoidance
            - Random Early Detection (RED) and weighted RED (WRED) drop packets before the queue is full
            - Selective dropping is better than FIFO or LIFO dropping

**QoS History**

- Priority queuing: gives certain data the right-of-way for transmission
- Weighted Fair Queuing (WFQ): prevents small packets from waiting too long for big packets
- RTP priority queuing: Gives voice packets the right-of-way
- CAC: Makes sure we don't fill up the queue or pipe with voice traffic

**Implementing QoS**

- Step 1: Identify traffic types and requirements
    - Network audit
    - Business audit
    - Define bandwidth requirements for each class found
- Step 2: Classify the traffic
    - Common classes
        - VOIP
        - Mission-critical
        - Signal traffic: for VOIP
        - Transactional application: SAP, ERP
        - Best-effort: Everything else
        - Scavenger: Crap you don't care about like P2P and your boss's email
- Step 3: Define policies for each class
    - Tasks for each class
        - Set max bandwidth
        - Set min bandwidth
        - Assign relative priorities
        - Apply congestion avoidance, congestion management, etc.

**QoS Models**

- Best-effort: no QoS
    - Scalable
    - Easy
    - No service guarantee: doesn't care what you're trying to do
    - No service differentiation: all traffic is equal
- Integrated Service (IntServ)
    - Hard-QoS
    - Uses RSVP to guarantee bandwidth through the entire path
    - Requires
        - Admission control
        - Classification
        - Polices the traffic (ceiling)
        - Queuing
        - Scheduling
    - Advantages
        - End-to-end resource admission control
        - Per-request policy admission control
        - Signaling of dynamic ports
    - Disadvantages
        - Continuous signaling
        - Not scalable
- Differentiated Services (DiffServ)
    - Soft-QoS
    - Configured on each hop
    - Traffic is classified
    - Enforces different treatment on different classes
    - Defined based on business requirements
    - Benefits
        - Scalable
        - Supports lots of service levels
    - Drawbacks
        - No absolute guarantee of service
        - Complex configuration throughout network

**QoS Implementation Methods**

- CLI
    - Old school
    - Not used any more
- Modules QoS CLI (MQC)
    - Step 1: _class-map_
    - Step 2: _policy-map_
    - Step 3: _service-policy_
- AutoQoS
    - Automatically generates classes and policies based on traffic it sees
    - Super-simple
    - Requires CEF, NBAR, and correct bandwidth statements
- SDM QoS Wizard
    - Next, next, next
    - Can be used to implement, monitor, or troubleshoot QoS
