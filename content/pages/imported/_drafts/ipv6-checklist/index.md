---
title: "IPv6 Checklist"
draft: true
---

IPv6 is here (and has been for a long time), and the IPv4 space is on pace to be depleted on 1 Feb 2011.  To convert over, it's not a matter of getting new address spaces and going at it.  There are lots of nodes and devices in a network that need to be reviewed to be sure they're compatible.  Here's an ever-growing list of items which need to be reviewed during the planning stages of your IPv6 rollout.  Some are obvious...some not so much.

- Network Devices
    - Routers
    - Switches  
        - Core
        - Distribution
        - Access
        - Virtual (like the Nexus 1000V)
    - Firewalls
    - Load balancers
    - VPN devices
    - IDSes
- Network Services
    - Syslog
    - SNMP
    - NTP
    - AAA
        - Radius
        - TACACS+
        - LDAP
        - Tokens
    - IP address management applications
    - DNS
    - DHCP
- Provider Services
    - Internet circuits
    - WAN circuits
- Corporate Services
    - Email
        - Inbound infrastructure
        - Outbound infrastructure
        - Email clients
        - IMAP
        - POP
    - Business applications
    - Printing services
    - Voice services  
        - IP phones
        - SIP trunks

Links to check out

[Etherealmind.com's article on IPv6 impact on TCAM tables  
](http://etherealmind.com/tcam-detail-review/)

[Jeff Doyle's article on IPv6 Address Design  
](http://www.networkworld.com/community/blog/ipv6-address-design)
