---
title: "Junos Configuration Groups"
date: 2012-05-21
categories: 
  - "junos"
tags: 
  - "apply"
  - "apply-groups"
  - "configuration"
  - "groups"
  - "junos"
---

It has been quite a spring so far.  I've spent the last two months at our data center racking, railing, mounting, cabling, extending, labeling, and documenting a whole pile of switches, routers, and firewalls for our new environment.  I won't and can't go into the details, but it's a huge project for the company that I'm proud to be trusted with.  Anyway, now that the physical build is finished (for definitions), I'm finally getting really deep into the configuration.  Since we're a Juniper shop, I'm finding all sorts of stuff that's fun to explore.

One cool thing I've found is the configuration group, which is a way to create a configuration template.  The classic example is to use a config group to create a default-deny template for all security policies.  No one wants to have to remember to create the deny policy every time they create a new security zone.  Or, even better,  let's say that the security team now wants us to log every time a connection is denied.  Instead of having to modify a dozen or more security policies ( it's an n(n-1) thing usually), we could just modify the group, and everything gets updated.

I'm doing this on an SRX240 running 11.4R2.14.  First, we create the template through _groups_ at the top of the hierarchy.  We'll create one called "DEFAULT\_DENY\_TEMPLATE" for the example.  Inside that group, we just configure a new security policy with the settings we want.  If you've ever done security policies, though, you'll know that you should specify both a from and to security zone.  Luckily, we're able to use wildcards in certain parts including the security zones ( don't ask me what the rules of wildcard are; I don't know.  :) ).  Instead of a zone name, you can just use "<\*>" to signify all zones.  To finish the details out, we'll do a deny from all to all on all ports and log the session initiation.

> ```
> groups {
>     DEFAULT_DENY_TEMPLATE {
>         security {
>             policies {
>                 from-zone <*> to-zone <*> {
>                     policy LOG_DENY_ALL {
>                         match {
>                             source-address any;
>                             destination-address any;
>                             application any;
>                         }
>                         then {
>                             deny;
>                             log {
>                                 session-init;
>                             }
>                         }
>                     }
>                 }
>             }
>         }
>     }
> }
> ```

Now we have to apply the group properly.  We can apply it at any number of spots in the hierarchy.  In my limited experience, I've just applied them at the top of the config.  I'm sure there are strategies that would say to apply them elsewhere, but it works there.

> ```
> apply-groups DEFAULT_DENY_TEMPLATE;
> ```

Easy enough.  Let's check our work by looking at the security policy config.

> ```
> aconaway@SRX> show configuration security policies from-zone UNTRUST to-zone TRUST
> policy ANY {
>     match {
>         source-address any;
>         destination-address any;
>         application junos-http;
>     }
>     then {
>         permit;
>         count;
>     }
> }
> ```

Wait. What? Where did the template go?  We just added another policy, right?  One of the things that drives me crazy with Junos is that any config that is inherited is not shown unless you tell it to do so with "| display inherited".  I's way too much output to include here, but you'll see a whole mess of annotate config that shows the inherited policy. Of course, we can just do a "show security policy" to see what's actually applied.

> ```
> aconaway@SRX> show security policies from-zone UNTRUST to-zone TRUST
> From zone: UNTRUST, To zone: TRUST
>   Policy: ANY, State: enabled, Index: 6, Scope Policy: 0, Sequence number: 1
>     Source addresses: any
>     Destination addresses: any
>     Applications: junos-http
>     Action: permit
>   Policy: LOG_DENY_ALL, State: enabled, Index: 19, Scope Policy: 0, Sequence number: 2
>     Source addresses: any
>     Destination addresses: any
>     Applications: any
>     Action: deny, log
> ```

Any changes you make to the group will be applied to all security policies when you commit. Pretty cool stuff.  I'm sure I'll wind up using it more down the road.

Send any BBQ tips questions to me.
