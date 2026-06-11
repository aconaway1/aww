---
title: "Migrating CSM Serverfarms to Other Server VLANs"
date: 2010-01-25
categories: 
  - "csm"
tags: 
  - "cisco"
  - "csm"
  - "datacenter"
  - "dcoas"
  - "mdcoas"
  - "migration"
  - "stick"
  - "vlan"
---

A coworker brought an interesting problem to me the other day.  He wanted to move a serverfarm from one server VLAN to another without taking an outage.  Since I didn't want to have to come into the office late at night to do work, I decided to see what we could do.

It turned out to be pretty easy.  We tend to think of CSM VLANs as pairs -- you have the client VLAN for the web servers where the vserver sits and the server VLAN where the serverfarm sits.  The CSM doesn't know about these relationships; all it cares about is whether the servers are in a server VLAN, and we can use that to our advantage here.

Here's a snippet of what the original config looked like (not really since I'm not telling you how my company's network is set up). The original serverfarm included a serverfarm called SFARM-ORIG that included 192.168.0.10\[12\]. That farm is used by the vserver VSERV-ORIG that listens  to 1.1.1.1 on HTTP.  The probe is in there, too.

> ```
> probe HTTP tcp
>  port 80
> !
> serverfarm SFARM-ORIG
>  real 192.168.0.101
>   inservice
>  real 192.168.0.102
>   inservice
>  probe HTTP
> !
> vserver VSERV-ORIG
>  virtual 1.1.1.1 tcp http
>  vlan 100
>  serverfarm SFARM-ORIG
>  inservice
> ```

To make the move, we start by creating a new vserver  and serverfarm that contains all the IPs invovled -- both the original IPs that are already in service as well as the new IPs to which the servers will migrate.  The new vserver listens for 2.2.2.2.  In this case, we're moving the servers to 10.10.1.10\[12\].

> ```
> serverfarm SFARM-NEW
>  real 192.168.0.101
>   inservice
>  real 192.168.0.102
>   inservice
>  real 10.10.1.101
>   inservice
>  real 10.10.1.102
>   inservice
>  probe HTTP
> !
> vserver VSERV-NEW
>  virtual 2.2.2.2 tcp http
>  vlan 200
>  serverfarm SFARM-NEW
>  inservice
> ```

When you first drop in the config, the original RIPs should come up as operational, and the new ones should fail since they don't exist yet (duh!).  When everyone's ready, you then move the service over to the new VIP and run off of that for a while to make sure everything's working as expected.  When all the parties involved are happy, you can then start moving over the servers one at a time.  The probe should fail out a server pretty quickly, then, when the server is reconfigured and put on the right VLAN, the CSM should eventually see the new RIP come up and put it back in the available server pool for the farm.

Configured like that, you can move the servers whenever you would like, and the CSM will automatically detect the changes and act accordingly.  You just have to remember to remove the old IPs out of the serverfarm when a server moves.

Send any alternative study techniques questions my way.
