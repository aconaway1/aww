---
title: "Stubby Post - A Story on VPN Hardware Acceleration"
date: 2010-11-01
categories: 
  - "misc"
tags: 
  - "acceleration"
  - "aim"
  - "gre"
  - "hardware"
  - "ipsec"
  - "rtt"
  - "vpn"
---

We use a hosted application that requires IPSec tunnels to the provider from different properties across the country.  The ones in the lower 48 perform adequately, but the new one in Alaska is absolutely horrible.  <!--more-->The RTT from the continental US sites was around 80ms, but the same from the Frozen North was 350ms.  I immediate wrote it off to the vast distance (about 3400 miles one-way by road), but the senior engineer, working as hard as he does, wanted to see if he could shave a few milliseconds off the time.

To experiment, he turned up a new IPSec tunnel to our headquarters and found he was getting ping times of right around 350ms again.  He then turned up an unencrypted GRE tunnel back to the same router at headquarters.  Guess what?  RTT went down to 80ms.  He enabled encyption on that same tunnel, and RTT was back up to 350ms.  Coincidence?  I think not.

He did an inventory of the router in Alaska and found that it was an EoL Cisco device without an AIM module for VPN acceleration; the sites south of Canada all had fairly-modern routers decked out with the accelerator cards.  It seems that we were seeing quadruple RTT simply because we were doing the encryption and decryption in software!  I've seen an AIM module give a significant boost to VPN performance in the past, but I've never seen it quantified so clearly.  This was a big eye-opener for me on the importance of having the right hardware for the job.

A new router is on its way up to Alaska.  This one has the accelerator card in it.
