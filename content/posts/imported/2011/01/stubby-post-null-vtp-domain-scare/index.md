---
title: "Stubby Post - Null VTP Domain Scare"
date: 2011-01-05
categories: 
  - "switch"
tags: 
  - "cisco"
  - "domain"
  - "dtp"
  - "mode"
  - "null"
  - "vtp"
---

Remember a few weeks back when I had a bad day?  I was actually at HQ that day to do some work for a project, but that got put off due to the extenuating circumstances.  When we finally got back around to do the work, we wound up adding a switch in the data center to extend a VLAN over to a rack.<!--more-->When we cabled the new switch up, the trunks didn't come up immediately.  We got a log messages that complained that the VTP domains were mismatched and that the trunks couldn't be negotiated.  Here's what we saw.

> %DTP-5-DOMAINMISMATCH: Unable to perform trunk negotiation on port Gi0/25 because of VTP domain mismatch.  
> %DTP-5-DOMAINMISMATCH: Unable to perform trunk negotiation on port Gi0/26 because of VTP domain mismatch.

That scared me a bit, but, a few seconds later, all the trunks came up and started passing traffic.  After a little looksie, we realized that the switch we added had a null VTP domain and was in server mode.  When connected to another switch via a trunk, a switch in this configuration will listen out for any VTP information and will add itself to any VTP domain it finds.  Since we had an established domain on the upstream switch, the new switch added itself to the domain and brought the trunks up.  The scare was little more than a little naivety mixed with a dash of paranoia from the early debacle.

Obviously, this is not the way we want to do switch implementations.  Rule #1 for putting a switch on the network:  Set the VTP domain and mode before you plug it into the network.

Off topic:  This project is seriously cursed.

Send any magic potions questions my way.
