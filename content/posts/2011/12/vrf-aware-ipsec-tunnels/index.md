---
title: "VRF-Aware IPSec Tunnels"
date: 2011-12-13
categories: 
  - "cisco"
tags: 
  - "bgp"
  - "ipsec"
  - "leaking"
  - "mpbgp"
  - "tunnel"
  - "vpn"
  - "vrf"
---

Man, time is hard to come by of late.  I've had so little time to rest that's it's hard to get my thoughts together.  It's a good thing in this case, though, since it's my fantastic job that's taking all my time.  It's great to see new network and learn their internals...especially when they were designed by some long-time CCIEs who actually knew what they were doing.

One of the big things that I'm dealing with lately is VRFs.  I've implemented some VRF-lite stuff, but I've never had any practical experience with the full force of them.  I'm definitely learning here.  Since the blog here is really about my sharing what I've learned, let's go through something that came up recently - terminating VPNs on one VRF while passing traffic to another.

What I'm talking about is the old-school, static IPSec VPNs that we've all configured a million (or so) times.  You know the ones with crypto maps applied to interfaces?  Well, we're going to configured one of those for the VRF called "CUSTOMER1" terminated on an interface in the "INTERNET" VRF.

There's some terminology for these VRFs, actually.  The INTERNET VRF, which has the tunnel endpoint is called the front VRF (FVRF); CUSTOMER1 is called the internal VRF (IVRF).  I'll try to remember to use those terms, but I make no promises.

First, we need to create the VRFs themselves.  Since the endpoints are in two different VRFs, we'll need to have some routes leaked from the IVRF to the FVRF.  I could write 847829843828 words on route leaking and not cover everything in my limited experience, so you'll have to look that up on your own if you don't know what I'm talking about.  Route-target 65000:1 is exported from INTERNET and imported into CUSTOMER1

> ```
> ip vrf INTERNET
> rd 65000:1
> route-target export 65000:1
> !
> ip vrf CUSTOMER1
> rd 65000:101
> route-target import 65000:1
> ```

At this point, we just put the interfaces in the right VRF along with their addresses.  We'll also configure an ISAKMP policy just like we've done a million times.

> ```
> crypto isakmp policy 100
>  encr aes
>  authentication pre-share
>  group 2
> !
> interface Ethernet0/0
>  ip vrf forwarding INTERNET
>  ip address 192.0.2.1 255.255.255.0
> !
> interface Ethernet0/1.1
>  encapsulation dot1Q 1
>  ip vrf forwarding CUSTOMER1
>  ip address 192.168.201.1 255.255.255.0
> ```

Next we'll create a keyring that's referenced by the FVRF.  This will make the key for the remote end available for use by that VRF.

> ```
> crypto keyring KEY1 vrf INTERNET
>   pre-shared-key address 192.0.2.101 key TEST.KEY
> ```

Now we create and ISAKMP profile, which is really the blood and guts that make all this work.  An ISAKMP profile references some of the important pieces of the tunnel - the IVRF in which to place the traffic, the keyring to use, and tunnel endpoint, and the FVRF where the tunnel terminates.

> ```
> crypto isakmp profile CUSTOMER1-PROFILE
>    vrf CUSTOMER1
>    keyring KEY1
>    match identity address 192.0.2.101 255.255.255.255 INTERNET
> ```

We'll then create the ACL for interesting traffic. I'll save some trees and not go through that since this should be pretty easy by now.

Now we can create the crypto map. This will be just like any other crypto map you've ever made with one exception; this is where you include that nifty ISAKMP profile we just made.

```
crypto map CM 100 ipsec-isakmp
 set peer 192.0.2.101
 set transform-set TS
 set isakmp-profile CUSTOMER1-PROFILE
 match address CUSTOMER1-TRAFFIC
```

Just like in other cases, we need to add a static route to make sure the router sends the packets destined for the remote end of the tunnel out the right interface. Since the FVPN is INTERNET, we'll add static routes for that VRF. We'll do the same for the tunnel endpoint just in case the default routes doesn't go the right way.

> ```
> ip route vrf INTERNET 192.0.2.101 255.255.255.0 192.0.2.2
> ip route vrf INTERNET 10.0.0.0 255.255.255.0 192.0.2.2
> ```

Now the tunnel should be up, right? Probably not. If you take a close look, you'll see that the FVRF has the route to the remote network, but the IVRF - the one that will use the tunnel - doesn't. We'll need to use MPBGP to leak those routes from one VRF to another. Did I mention that route leaking can get long-winded and that I'm not going to get into it? Yeah...it can get that bad. Just trust me that this works.

What we're going to do is to start up BGP for both VRFs. At the same time, we'll redistribute the static routes that we added above from the FVRF into the IVRF. Since we set up our imported and exported route-targets in the VRF definition, the static routes will magically appear in both VRFs.

> ```
> router bgp 65000
> bgp router-id 192.0.2.1
> !
> address-family ipv4 vrf INTERNET
>  redistribute static
>  exit-address-family
> !
> address-family ipv4 vrf CUSTOMER1
>  exit-address-family
> ```

If we do a _show ip route vrf CUSTOMER1_, we'll see the static routes from the INTERNET VRF. They're real easy to spot. :)

> ```
> ...
> B        10.0.0.0 [20/0] via 192.0.2.102 (INTERNET), 00:00:05
> ...
> B        192.0.2.1 [20/0] via 192.0.2.102 (INTERNET), 00:00:05
> ...
> ```

That should do it. Now you should be able to talk from your local network in the CUSTOMER1 VRF and talk through a tunnel that's established on the INTERNET VRF.

Send any Juniper configs questions my way.

_Update_ : Here's an embarrassingly-sloppy logical diagram for Andrew.

[![IPSec-Aware VPN Logical](images/IPSec-Aware-VPN-Logical-300x187.svg)](http://aconaway.com/wp-content/uploads/2011/12/IPSec-Aware-VPN-Logical.png)
