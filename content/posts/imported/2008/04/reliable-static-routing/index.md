---
title: "Reliable Static Routing"
date: 2008-04-24
tags: 
  - "routing"
  - "static"
---

Here's a scenario I ran into long ago. We had several sites that had a frame relay link back to headquarters and a DSL line. Each link was terminated into a different router on a flat LAN with the users. The DSL was for Internet access, but also terminated a VPN as a backup to the frame circuit. The requirements were something like this.

- Corporate traffic had to go across the frame relay link during normal operations.
- Internet traffic had to go across the DSL line during normal operations.
- If the DSL circuit went down, Internet traffic should be moved over to the frame relay circuit to use the corporate Internet link.
- If the frame went down, traffic should be sent out the VPN tunnel for access to corporate stuff.

We set the default routes of the machines (via DHCP) to the frame relay router. That router's default route sent traffic to the DSL router, which, of course, had a default route towards the provider. Both routers were participating in EIGRP with the rest of the corporate network, so they all knew where to route traffic destined for corporate traffic. If there was a frame outage, the default routes kicked in and sent traffic to the DSL router, which had the VPN tunnels. The problem came when there was a DSL outage.

At first, we were just monitoring the DSL IP and manually changing default routes when there was an outage, but you know DSL. We were lucky to have only 3 or 4 a day go down, so it was taking up a lot of our time just moving default routes around. We had to go to an automated solution, so we looked at doing object tracking but came up just short; it just didn't do what we wanted. We had to go one more step and discovered reliable static routing (RSR).

In normal object tracking, you can track an interface or a route to an IP. With RSR, you can track reachability to an IP via ICMP (or a whole list of other things). This is just what we needed. We figured Yahoo was a site that would always be up, so we went about tracking one of Yahoo's IPs from the frame routers, and, if it went down, we could send traffic back across the frame relay cloud.

To set up RSR, you have to set up an IP SLA entry, build a tracking object, then have the default route track the object. First, the IP SLA entry that we made.

`ip sla monitor 1 type echo protocol ipIcmpEcho 66.94.234.13 frequency 10`

This built IP SLA entry 1 to ping 66.94.234.13 every 10 seconds. With IP SLA, you also have to set a schedule for it to run. We wanted it to start ASAP and run forever, so we just did one of these.

`ip sla monitor schedule 1 start-time now`

This schedules entry 1 to start now, so every time the box came up, the IP SLA process started pinging away. You then combine the entry with a tracking object like this.

`track 100 rtr 1 reachability`

We built object 100 to monitor the reachability of SLA entry 1, so now we have an object to track if a host is unreachable. Sweet. How about the static part of the route? We set the default route of the frame router as the LAN IP of the DSL router tracking object 100. We also set a weighted default route pointing out the frame relay circuit to use when the object fails. Assume 10.0.0.0/24 for the frame cloud and 192.168.0.0/30 for the LAN.

`ip route 0.0.0.0 0.0.0.0 192.168.0.1 track 100 ip route 0.0.0.0 0.0.0.0 10.0.0.254 250`

In this setup, the default route is the DSL router, and, if the tracking object fails, it rolls to the frame cloud. Can you spot the flaw, though? When the route changes over, the object has recovered since it can reach Yahoo through the corporate Internet, and the default route get moved back to the DSL router...which then fails again...and rolls back...and...ack! An easy fix is to set a static route for the tracked IP to the DSL router.

`ip route 66.94.234.13 255.255.255.255 192.168.0.1`

This route also lets the frame monitor monitor the DSL service, so, when it comes back up, the tracking object recovers. Of couse, if someone at the site tried to get to Yahoo on that IP during a DSL outage, they would time out (shouldn't they be working?), but, when the DSL recovered, the router would know.
