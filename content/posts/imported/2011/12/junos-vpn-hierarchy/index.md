---
title: "Junos - VPN Hierarchy"
date: 2011-12-23
categories: 
  - "junos"
tags: 
  - "configuration"
  - "hierarchy"
  - "ike"
  - "ipsec"
  - "junos"
  - "vpn"
---

Wow! A Junos post! Amazing.

We all know that the configuration on a Junos box is very hierarchical. Sometimes it doesn't make a lot of sense, but it's all a pretty cascade of code. One of the big messes that I've found is the VPN configuration hierarchy; there are way more items to configure than on an IOS device.  To reinforce the stpes in my head, I thought I'd get some of the pieces into a post. These aren't all the options, but it's all you need to get a static IPSec tunnel up and running.

**security**

**ike**

**proposal** <<<<  Think ISAKMP policy on Cisco

**authentication-method** <<<< PSK

**dh-group**

**authentication-algorithm**

**encryption-algorithm**

**lifetime-seconds**

**policy**

**mode** <<<< Main versus quick

**proposal**

**pre-shared key** <<<< The key and the proposal are bound together

**gateway** <<<< The remote peer

**ike-policy**

**address**

**external-interface** <<<< Think the if where you put the crypto map

**ipsec**

**proposal** <<<< Transform set...kinda

**protocol** (ESP)

**authentication-algorithm**

**encryption-algorithm**

**lifetime-seconds**

**policy**

**proposal**

**vpn**

**bind-interface** <<<< Complicated story

**ike**

**gateway**

**proxy-identity** <<<< Also complicated

**local**

**remote**

**ipsec-policy**

**establish-tunnels immediately** <<<< Awesome!

That'll do, pig.  I'll fire off a real configuration post later.  Feel free to add your pair of pennies since I'm a total Junos n00b.

Send any stocking stuffers questions my way.
