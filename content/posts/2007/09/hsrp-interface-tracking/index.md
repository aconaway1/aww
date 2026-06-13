---
title: "HSRP Interface Tracking"
date: 2007-09-23
tags: 
  - "cisco"
  - "hsrp"
---

Remember the article on [router-on-a-stick](http://aconaway.com/2007/08/20/router-on-a-stick/ "AConaway.com -- Router-on-a-stick")? And the one on [HSRP](http://aconaway.com/2007/08/21/running-hsrp-for-availability/ "AConaway.com -- Running HSRP for Availability")? Let's add to that example network, shall we? Let's make those routers into edge routers so they connect your internal network to the Internet with some size circuit. Let's just say they each terminate [DS3s](http://en.wikipedia.org/wiki/DS3 "Wikipedia -- Digital Signal 3") to different providers.

Here's our network now (I'm experimenting with Visio alternatives, so excuse the diagram footer there). Let's assume that we have \[tag\]HSRP\[/tag\] set up like the HSRP article and that we have many sub-interfaces on the Ethernet side of the routers like the ROAS article. Also, Router1 is the HSRP active peer and each router has a default route pointing to the upstream ISP through interface Serial 0/0.

<script src="http://www.best4c.com/js/pasteblog.js" type="text/javascript"></script>

<script type="text/javascript">file="/Best4cUserFiles/20070924/11511_1190581940640";showImage();</script>

This looks pretty good, but what happens if the DS3 on Router1 goes down? We won't be able to pass traffic to the Internet at all since all the hosts are using the HSRP IP as their gateways. Oh, God...that sucks. What can we do? HSRP has a tracking feature, and we can use it to monitor the DS3 and decrement \[tag\]priority\[/tag\] if something happens to it.

Like everything in the network world, this is a piece of cake. All we have to do is one single line to each of our HSPR standby groups to set this all up. Remember to do each standby group on both routers.

> standby 1 track Serial 0/0 decrement 55

Now, when the \[tag\]interface\[/tag\] goes down, the HSRP priority will be decremented by 55. If Router1 gets decremented by 55, Router2 will be the active peer since Router1's new priority will be 45. If Router2 loses the interface and gets decremented by 55, nothing will really happen since Router2 is already the standby peer.

---

The note: An interface has to be down for this to take place. If you lose line protocol but the interface stays up (the interface is up/down), HSRP won't decrement the priority. Look out for an article on object tracking later to fix this problem.
