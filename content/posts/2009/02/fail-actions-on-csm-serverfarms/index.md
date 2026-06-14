---
title: "Fail Actions on CSM Serverfarms"
date: 2009-02-20
tags: 
  - "cisco"
  - "csm"
  - "failaction"
  - "serverfarm"
---

I've talked about [probes](/posts/2008/11/using-probes-on-the-csm/ "AConaway.com -- Using Probes on the CSM") and stuff on the CSM, but I never mentioned what happens to the connections to a server that fails.  That is, if I'm connected to server A in a cluster and that server suddenly commits [ritual seppuku](http://en.wikipedia.org/wiki/Seppuku "Wikipedia.com -- Seppuku"), what happens to my connection through the CSM?

Remember how the CSM works?  You connect to the VIP, some state tables are updated, your packet's destination IP is changed to a RIP, and the packet is forwarded.  The point I want to emphasize this time is the state table.  If you were to send another packet to the same VIP on the same port, the CSM would look in its state table and see that you're already connected to a server and just forward you on over after a NAT.  What if that server has suddenly died?

The answer is nothing by default.  You keep sending packets asking for more information, and the CSM keeps forwarding your packets to a server that's dead.  Even if the CSM knows the server is dead through probes or health checking, it still keeps the connections open.  Eventually, after a chunk of seconds, you'll stop sending packets, and the CSM will timeout your connection.  That's not really good, though.  You don't want users to have to wait 45 seconds or more for everyone to time out, do you?

The answer is to set a fail action on your serverfarms.  By default there is none, but you do have two choices -- _purge_ or _reassign_.  _Purge_ does exactly what you think it does.  Come to think of it, so does _reassign_.  I love it when stuff is easy.  For those who didn't buy a program, the _purge_ directive will just clear the connection and make the user try again (probably behind the scenes to the extent that the user won't know it happened) while the _reassign_ directive sends the current connections to another server in the farm.

Configuration is easy.  You just use the failaction command under the server with the action you want.

> switch(config)#mod csm 7 switch(config-module-csm)#serverfarm MYFARM switch(config-slb-sfarm)#failaction purge

Deciding which one to use depends on a few things -- your failure rate, the reason a box dies, your cluster's capacity, etc.  For example, if the web application is freezing up and making your server unusable, chances are that the other servers in the farm are doing the same, and reassigning may just accelerate the new server's failure with the additional connections.  If your servers fail because you are just doing maintenance, reassigning those connections probably won't hurt the cluster (assuming you have the capacity to handle all the connections).

The moral of the story is that you probably don't want to leave the users hanging around, so you should look into purging them or resassigning them when something is amiss.

Send any leprechauns questions to me.
