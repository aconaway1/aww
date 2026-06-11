---
title: "ONT Notes - Queuing"
date: 2010-01-24
tags: 
  - "642-845"
  - "cbwfq"
  - "ccnp"
  - "certification"
  - "cisco"
  - "classification"
  - "diffserv"
  - "fifo"
  - "llq"
  - "marking"
  - "ont"
  - "policing"
  - "pq"
  - "qos"
  - "queueing"
  - "queuing"
  - "round-robin"
  - "test"
  - "voice"
  - "voip"
  - "wfq"
---

Here are some more notes from my studies.  Of course, no one cares about them but me, but it's my blog.  I’m sure someone will find it useful.  Please help to correct dumbass mistakes.

- Congestion
    - Speed mismatch - traffic leaves a lower-bandwidth interface than the one it came in on
    - Aggregation problem - lots of links with one egress of equal bandwidth
    - Confluence problem - a bunch of traffic needs to egress out of the same interface
- Queuing
    - Transmit queue (TxQ) - hardware queue; there's only one you can't touch
    - Software queue - where packets wait to be sent; there are many queue-types that you modified to police traffic
- FIFO
    - If I beat you to the router, I leave the router first.
    - Possible long delays, jitter, and starvation
- Priority queuing (PQ)
    - Four queues
        - High-priority
        - Medium-priority
        - Normal-priority
        - Low-priority
    - Scheduler starts from high and work to low
    - When the high queue is empty, it processes a packet from medium, then starts all over
    - Can you say starvation?
- Round robin queuing (RR)
    - One packet from this queue, one from the next, etc., then start over again
- Custom queuing (CQ)
    - Weighted round robin
    - Queues are given weights (bandwidth guarantees)
- Weighted Fair Queuing (WFQ)
    - Default queuing on slow links ( < E1 )
    - Divides traffic into flows
    - Equal bandwidth is given to each flow
    - Provides faster scheduling to low-volume flows
    - Provides more bandwidth to higher-priority flows
    - Flows identified by a hash
        - Source IP
        - Destination IP
        - Protocol number
        - ToS
        - Source port
        - Destination port
    - Each unique has is a new flow
    - No way to allocate bandwidth among the flows
    - By default, up to 256 queues are made, but that is changeable to a power of 2 between 16 and 4096
    - If the max number of flows is reached, queues are reused for other flows
    - If a queue is full, a packet may be dropped.
    - WFQ early dropping drops packets when the queue reaches the congestive discard threshold (CDT)
    - Advantages
        - Simple configuration
        - No starvation
        - Guarantee processing of all flows
        - Drops packets from big-hitter flows
        - Faster service no low-hitters (interactive) flows
        - Standard on (nearly) all IOS devices
    - Disadvantages
        - Classification and scheduling are not configurable
        - Only on slow links
        - No guarantee of bandwidth or delay
- Class-based Weighted Fair Queuing (CBWFQ)
    - User-defined queues for flexibility
    - Configured with class-maps via MQC
    - Weights are calculated based on values give in class-map
        - Bandwidth - guarantee this much bandwidth
        - Bandwidth percent - give me this much of the available bandwidth
        - Bandwidth remaining percent
    - Advantages
        - User-defined traffic classes
        - Each queue gets its own bandwidth
        - Scalability
    - Disadvantages
        - No delay guarantee (not good for real-time application like voice)
    - Configuring
        
        > ```
        > class-map TESTCM1
        >  match access-group 100
        > !
        > class-map TESTCM2
        >  match access-group 200
        > !
        > policy-map TESTPM
        >  class TESTCM1
        >   bandwidth 64
        >  class TESTCM2
        >   bandwidth 128
        > ```
        
- Low-latency Queuing
    - Includes strict priority queue for delay-sensitive data
    - Strict priority queue is policed to avoid starvation of other queues
    - Configured the same way as normal CBWFQ, but with the _priority_ keyword
    - This configuration makes _TESTCM2_ a priority queue
    
    > ```
    > class-map TESTCM1
    >  match access-group 100
    > !
    > class-map TESTCM2
    >  match access-group 200
    > !
    > policy-map TESTPM
    >  class TESTCM1
    >   bandwidth 64
    >  class TESTCM2
    >   priority bandwidth 128
    > ```
