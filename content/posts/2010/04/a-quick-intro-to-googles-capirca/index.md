---
title: "A Quick Intro to Google's Capirca"
date: 2010-04-11
categories: 
  - "misc"
tags: 
  - "acl"
  - "automation"
  - "capirca"
  - "cisco"
  - "google"
  - "linux"
  - "python"
---

Yeled left a comment earlier this week asking if I'd seen [Google's Capirca](http://code.google.com/p/capirca/).  I'd heard of it and checked out some presentation slides on it, but I'd never actually tried it out, so, in keeping with the script, I downloaded it to see what it could do.  Remember, now, that I've been playing with it for about 2 hours now, so I'm no expert on its use.

Capirca is a Python-based solution that Google came up with to automate ACL creation on their many thousands of routers around the world.  You can't blame them for wanting to automate it, either.  How many times do you think they ran into problems with typos or keying errors from their network guys across those devices?

Capirca is configured similarly to Snort.  The concept is that you define objects like hosts, networks, groups, and services, then you define policies based on those objects.  You run the app against your definitions, and it pukes out ACLs for you.  It can do Cisco ACLs, Juniper ACLs, or IPTables rules, so that may come in handy, but I only care about the Cisco stuff right now.

Like I said, I haven't messed with it before, but I got it working with very little frustration.  From the root of the application (_~/capirca-1.0_ in my case), I edited _def/NETWORK.net_ and added some custom objects to it to mess around.  I added my home networks, my public IP address at home, and some of the work networks that I would use to access home services.  Here's what I added to the sample file.

> ```
> ...SNIP...
> GUESTS = 10.0.2.0/24
> USERS = 10.84.8.0/24
> HOMENETS = GUESTS
>           USERS
> 
> WORKNETS = 192.0.2.0/24
> 
> MYPUBLICIP = 192.0.2.1
> ...SNIP...
> ```

Now I can use those objects to define some rules for the ACL.  The rules are defined in the _policies/_ directory and are a little more complicated than the objects, but it's not that hard.  There are two types of entries in the policy files - headers and terms.  Headers define the beginning of a new ACL and define what the platform (Cisco, Juniper, IPTables) you're using and the name of the list.  The terms sections define the details of the ACL like source, destination, protocol, port, and action.  Here's the policy file (that I called _home.pol_) that I added to simulate letting my work IPs get to SSH on my public IP, everybody get to HTTP on the same, and deny everything else.

> ```
> header {
>   comment:: "F0/0 Inbound"
>   target:: cisco F00IN
> }
> 
> term permit-ssh-services {
>   destination-address:: MYPUBLICIP
>   protocol:: tcp
>   destination-port:: SSH
>   source-address:: WORKNETS
>   action:: accept
> }
> 
> term permit-http-services {
>   destination-address:: MYPUBLICIP
>   protocol:: tcp
>   destination-port:: HTTP
>   action:: accept
> }
> 
> term default-deny {
>   action:: deny
> }
> ```

That creates a new Cisco ACL called F00IN (for F0/0 inbound) that allows our interesting traffic and denies everything else.  Now that the policy is configured, all I had to do was run the executable and see what happens.  In the root directory of the application, there's a Python file called _aclgen.py_ that you run.  Since we put all our definitions and policies in the default location, all I had to do is run that with no arguments.  The output told me to look in _filters/home.acl_ for my new ACL.  That's where I found this.

> ```
> no ip access-list extended F00IN
> ip access-list extended F00IN
> remark F0/0 Inbound
> 
> remark permit-ssh-services
>  permit 6 192.0.2.0 0.0.0.255  host 192.0.2.1 eq 22
> 
> remark permit-http-services
>  permit 6 any  host 192.0.2.1 eq 80
> 
> remark default-deny
>  deny ip any  any
> ```

Looks fine to me.  I pasted this into a lab router, and it worked like a champ.  I think I'll put some more time into Capirca to see if I can find a use for it at work.

Send any ~~misspelled Battlestar Galactica references~~ questions my way.

Director's Commentary:  I need to get a better mic if I want to keep doing this. ![Audio: A Quick Intro to Google's Capirca](images/audio-unavailable.svg)
