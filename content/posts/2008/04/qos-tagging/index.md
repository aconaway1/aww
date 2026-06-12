---
title: "Qos Tagging"
date: 2008-04-06
tags: 
  - "ios"
  - "qos"
---

I've been trying to get some experience on Cisco VOIP, and, as you probably know, Quality of Service (QoS) is quite important in that realm. Since VOIP is very time-sensitive, you have to be sure your gear delivers the voice packets first. A packet in an HTTP transaction can wait another 200ms without any problems. A voice packet with another 200ms on it means static and digital artifact on the line. Not good. There are lots of things you can do in the world of QoS, but I'll talk about tagging this time (I may get to some of the other topics later, though).

What is tagging, though? There is a header in an IP packet that, paraphrased from the standard, set the importance and drop-sensitivity of the packet in regard to the flow/application/data stream. A ping packet is quite unimportant in the grand scheme of things, and dropping the packet will have very little impact on the stuff going on in the network. Voice, however, needs every packet to arrive in order, so each packet's importance is high and a drop hurts. To differentiate between these two, we set the Differentiated Service Code Point (DSCP) value in the pakcet. This is just the packet header thing I was talking about earlier.

Let me pause for a second to discuss something, though. Just tagging the packet does nothing to prioritize the packet. I can tag a packet with whatever I want, but, if a router or switch isn't configured to use the tag, my packet just goes into the FIFO queue like everything else. Actually doing something different to a tagged packet is one of the many other parts of QoS that I may get to later.

Time to configure? Of course. Like everything in the Cisco world, it always usually starts with an access-list to define interesting traffic. You then create a class-map that defines what traffic you want to tag (usually with the ACL you created), then you create a policy-map that defines what to do with that traffic. Finally, you apply the policy-map to the interface.

Let's try one. Let's say we just put in a new VOIP system and want to tag the packets appropriately. The IP of the system is 10.0.0.10, and you want the gateway on that network to tag the packets as AF41 as they come into it (Google up DSCP if you want to learn about the AF41 thing). What comes first? The ACL...duh!. I like named ACLs, so we'll create one called VOIP.

> ip access-list extended VOIP permit ip host 10.0.0.10 any

Time for the class-map. All that a class-map is used for is to create a list of values that are interesting to you, but a list can have an or or an and associated it. Basically, do you want to match all the items or just any of them. In our case, we just want to match the ACL we created (a single item), so it's no big deal, but keep in mind that you can use the match-all or match-any keywords if you want. Here's the class-map to match our ACL.

> class-map match-any MYCLASSMAP match access-group name VOIP

Next is what we want to do to the traffic, which is tag it to AF41. In the policy-map, you actually reference the class-map you made, so keep that in mind when you're configuring.

> policy-map MYPOLMAP class MYCLASSMAP set dscp af41

That says that anything that matches the class-map MYCLASSMAP gets tagged to AF41. Now we have to apply it to the gateway. Assuming that's F0/0 of your router, give this a shot.

> interface F0/0 service-policy input MYPOLMAP

That's pretty much it. Now, when a packet lands on F0/0 that matches the ACL, it will be tagged as AF41 and sent on. I say again that this just tags packets. If you want to actually prioritize those packets, you have to do more, but I'm too sleepy to get into that right now.
