---
title: "QoS Policing"
date: 2008-04-07
tags: 
  - "ios"
  - "qos"
---

[We covered QoS tagging](http://aconaway.com/2008/04/05/qos-tagging/ "AConaway.com -- QoS Tagging") the other day, but that just marks packets. I think you're old enough now that we should actually do some policing. Policing is where you restrict the amount of bandwidth that a flow or set of flows can use. For example, say you have a site that serves webpages to the rest of the network. HTTP is the primary function, but the SysAdmins also have to maintain the boxes via SSH, right? To make sure that their SSH sessions don't squash the bandwidth that your HTTP servers need, you can police the SSH sessions by giving the a bandwidth ceiling that they can't cross.

Let's set up a scenario. You have a router with two Ethernet ports. E0/0 goes to your webservers on 192.168.1.0/24, while E0/1 goes to SysAdmins' PCs on 192.168.2.0/24. We want to restrict all SSH access from 192.168.2.0/24 to 192.168.1.0/24 to 8k of bandwidth. That's right, you don't like your admins and want them to struggle with their daily tasks. Let's set it up.

Remember the steps? ACL, class-map, policy-map, interface. First, create an ACL that matches the traffic you want to police.

> ip access-list extended SQUASHSSH permit tcp 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255 eq 22

Next, create a class-map that matches that traffic.

> class-map SQUASHSSH-CMAP match access-group name SQUASHSSH

Create your policy-map. This is where you actually set the bandwidth restrictions.

> policy-map SQUASHSSH-PMAP class SQUASHSSH-CMAP police 8000 conform-action transmit exceed-action drop

Last time, we just did a _set dscp af43_, but this time we actually take action on a packet instead of just marking it. The long lines does three things -- it sets the ceiling to 8000 bits per second, transmits if the rate is below that number, and drops if its exceeded. So, if one of the admins tries to SCP a file over, he's gonna be in for a surprise with only an 8k window to use. If he goes over 8k, the packets are just dropped, and it's up to TCP to retransmit. It'll take a while. Remember, too, that this policy applies to anything matching the ACL, so, if two admins are SCPing files at the same time, they have to share that 8k. Wow, you're mean!

Setting it up on the interface is pretty straightforward. It's just like we did before.

> interface E0/0 service-policy output SQUASHSSH-PMAP

I set this up in a dynagen lab to test it, but, instead of SSH traffic, I policed ICMP. If I did a normal, 5-packet ping, it worked great. If I sent a 100-packet set, drop, drop, drop. Or if I increased the size of the packets. Just to be sure it was the service-policy doing the dropping, I sent a 100-packet set and removed the policy from the interface; it missed a few at first, but, when the policy was removed, it zoomed right along without a single miss or hesitation.

NOTE: I do not condone policing traffic from your coworkers machines to 8k, but it would be funny.
