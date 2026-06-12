---
title: "ROUTE Notes - EIGRP Neighbor Relationships"
date: 2010-06-17
categories: 
  - "route"
tags: 
  - "642-902"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eigrp"
  - "route"
  - "test"
---

Or neighborships, as they call it in the book.  What a terrible word.

**Study Questions**

- What settings must match between two routers in order to become EIGRP neighbors?

Both routers must be in the same primary subnet Both routers must be configured to use the same k-values Both routers must in the same AS Both routers must have the same authentication configuration (within reason) The interfaces facing each other must not be passive

- What are the default hello and hold times in EIGRP?

On links with bandwidth > 1.544Mbps: Hello:  5 sec Hold:  15 sec

On links with bandwidth <= 1.544 Hello:  60 sec Hold:  180 sec

- How do you change the hello and hold times?

You set these values at the interface.

R1(config-if)#ip hello-interval eigrp 1 X R1(config-if)#ip hold-tim eigrp 1 X

- How do you keep an interface from being used for EIGRP discovery?

Don't configure a network statement that includes that interface Make the interface passive Configure static neighbors for that interface

- Why might NTP be a good thing to use in regards to EIGRP?

EIGRP uses key chains for authentication.  Key chains can be configured with a range of valid dates and times.  If the time on two routers was off by even a few seconds, some keys would expire, causing neighbor relationships to drop.

- How do you configure EIGRP authentication?

In each interface participating in EIGRP, you configure the authentication mode and the key chain to use.

R1(config-if)#ip authentication mode eigrp 1 md5 R1(config-if)#ip authentication key-chain eigrp 1 KEYCHAIN1

- What are the k-values that EIGRP uses?

k1 = bandwidth k2 = load k3 = delay k4 = reliability k5 = MTU

- How does a router choose its router ID in EIGRP?

First, it looks for a configured router-id in the EIGRP configuration.  If none exists, it uses the highest (largest) IP address configured on a loopback interface.  If no loopbacks exist, it uses the highest IP configured on the other interfaces.

**What Command Was That?**

What command tells you...

- ...whether a neighbor was discovered or statically configured?

show ip eigrp neighbor detail

- ...what interfaces are involved in EIGRP?

show ip eigrp interfaces

- ...what k-values your router is using?

show ip protocols

- ...how long your router has been neighbored with another router?

show ip eigrp neighbors

- ...what your router ID is?

show ip eigrp topology show ip eigrp accounting

- ...a summary of the configured network statements?

show ip protocols

- ...the configured hello interval?

show ip eigrp interface detail

- ...the configured hold time?

There's isn't a way to do it directly.  You have to check your neighbors several times over the course of a few seconds to see where the hold timers drop to before resetting.
