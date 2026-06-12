---
title: "ROUTE Notes - Branch Office Routing"
date: 2010-07-05
categories: 
  - "ccnp"
  - "cisco"
  - "route"
tags: 
  - "642-902"
  - "branch"
  - "ccnp"
  - "certification"
  - "cisco"
  - "route"
  - "routing"
---

Corrigeme, por favor.

**Study Notes**

- What do IPSec tunnels give you when a branch office is on a broadband connection?

Privacy through encryption Authentication of the remote peer through ISAKMP Delivery of private data over the public Internet

- What do you need to configure to get your branch router talking to the Internet?

ISP connection configuration such as PPPoE or PPPoA DHCP server configuration for internal users NAT Firewall services like inspection and filtering

- What kind of routes would you normally see on a small branch router with a single IPSec tunnel home?

You would usually just see a default route to the ISP; IPSec will intercept interesting traffic and take care of sending the packets home without having routes for home networks configured.

- What's a really easy way to get routes to fail from a WAN link to a GRE tunnel when the WAN link dies?

Floating static routes

- What do GRE tunnels allow you to do that native IPSec tunnels don't?

Run a routing protocol

- Your DSL provider has given you a VPI/VCI of 1/50 to use on your branch router's ATM 0/0 interface.  Show me the full configuration to get basic web surfing working (ignore DNS and DHCP).

interface ATM0/0 no ip address pvc 1/50 encapsulation aal5mus ppp dialer dialer pool-member 1 ! interface Dialer9 encapsulation ppp ip address negotiated dialer pool 1 ppp authentication chap callin ppp chap password MYPASSWORD ip nat outside ! interface E0/0 ip add 192.168.1.1 255.255.255.0 ip nat inside ! ip route 0.0.0.0 0.0.0.0 Dialer9

- For what would you use an ACL when configuring IPSec tunnels?

You define interesting traffic with ACLs.

- What are the two basic configuration items in a crypto map for an IPSec tunnel?

Matching ACL IPSec peer IP
