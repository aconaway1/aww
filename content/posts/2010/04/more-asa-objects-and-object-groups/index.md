---
title: "More ASA Objects and Object-groups"
date: 2010-04-05
categories: 
  - "asa"
tags: 
  - "asa"
  - "object"
  - "object-group"
---

A few years ago, I developed a Perl-based application that take a template file and pukes out standardized access rules for new hosts as they're added to the network.  This works great for making sure that each host is able to be managed properly.  This solution, however, is not very flexible.  If I need to remove a host's access, I may have to take out 20 rules individually.  That's not really cool, so, at the suggestion of a coworker, I'm working on a solution that uses objects, object-groups, and nested object-groups.  This should minimize the configured rules and allow new host rules to be added and removed by simply adding hosts to object-groups.

Example time.  Let's say you have a bunch of RFC1918 addresses behind your firewall that all need HTTP access to one network on the InterTubes.  First thing to do is to create the objects that will be involved; in this case, that's all the networks and/or ranges.  To be more specific, 192.0.2.0/24 is the public IP to which the hosts need access.   The internal hosts are 192.168.0.0/24 and the IP range 10.0.0.1-25.  Yes, I know the names are terrible.

> ```
> object network NET1
>  subnet 192.0.2.0 255.255.255.0
> object network NET2
>  subnet 192.168.0.0 255.255.255.0
> object network NET3
>  range 10.0.0.1 10.0.0.25
> ```

Now, we can use some Snort-like configuration to create object-groups that include the objects we just created.  In this case, we're creating an InterWebs-based object-group and another for local addresses.

> ```
> object-group network REMOTE-NETS
>  network-object object NET1
> object-group network LOCAL-NETS
>  network-object object NET2
>  network-object object NET3
> ```

Now we can use these object-groups to create ACLs.   You've done this before, right?

> ```
> access-list TEST-ACL extended permit tcp object-
>    group LOCAL-NETS object-group REMOTE-NETS eq www
> ```

To be sure it worked as expected, let's take a look at the ACLs.  The format sucks because the lines are so long; sorry about that.

> ```
> firewall# show access-list TEST-ACL
> access-list TEST-ACL; 7 elements; name hash: 0x5329ed72
> access-list TEST-ACL line 1 extended permit tcp object-group LOCAL-NETS object-group REMOTE-NETS eq www 0x1abfa4a0
>   access-list TEST-ACL line 1 extended permit tcp 192.168.0.0 255.255.255.0 192.0.2.0 255.255.255.0 eq www (hitcnt=0) 0x50797e0c
>   access-list TEST-ACL line 1 extended permit tcp host 10.0.0.1 192.0.2.0 255.255.255.0 eq www (hitcnt=0) 0xa2159c9d
>   access-list TEST-ACL line 1 extended permit tcp 10.0.0.2 255.255.255.254 192.0.2.0 255.255.255.0 eq www (hitcnt=0) 0x93f1c362
>   access-list TEST-ACL line 1 extended permit tcp 10.0.0.4 255.255.255.252 192.0.2.0 255.255.255.0 eq www (hitcnt=0) 0x512fc827
>   access-list TEST-ACL line 1 extended permit tcp 10.0.0.8 255.255.255.248 192.0.2.0 255.255.255.0 eq www (hitcnt=0) 0x7b11e96f
>   access-list TEST-ACL line 1 extended permit tcp 10.0.0.16 255.255.255.248 192.0.2.0 255.255.255.0 eq www (hitcnt=0) 0xc302aa0e
>   access-list TEST-ACL line 1 extended permit tcp 10.0.0.24 255.255.255.254 192.0.2.0 255.255.255.0 eq www (hitcnt=0) 0x2ea75962
> ```

Cool.  Everything looks great, and everyone should have the access they need.  If a new host with the IP of 172.16.0.28 comes online inside the network, you add a new nested object-group that includes that host.  Access is automagically updated, so there's no need for more ACL lines.  Another method is to add the new host directly to the LOCAL-NETS object-group, but that's going to limit the ways to address that box and related hosts in an ACL.  I suggest you just add the new object to the object-group.

As a bonus, you can also nest object-groups into each other.  For example, we can create an object-group that includes our the LOCAL-NETS and REMOTE-NETS object-groups.

> ```
> object-group network ALL-NETS
>  group-object LOCAL-NETS
>  group-object REMOTE-NETS
> ```

I don't know where you'd ever use that specific object-group, but you could use this technique in other ways.  I'm looking to create object-groups for each interface of the firewall and creating a super-object (my term) to allow the standard access stuff.  You could do the same for office networks; each office has it's own object-group for access that is also nested in an object that provides basic access to the TubeWebs or something.  Use your imagination.  :)

Send any questions my way.

This article is based on an ASA 5505 running 8.3.1.  Most of the config above should be portable to any 8.x except for declaring the objects. In other versions of 8.x, you may have to add host directly to the object-group.  Running on 7.x and below may be a different story.

Other reading:  [http://aconaway.com/2009/10/01/object-groups-in-the-asafwsmpix/](http://aconaway.com/2009/10/01/object-groups-in-the-asafwsmpix/)

Director's Commentary: ![Audio: More ASA Objects and Object-groups](images/audio-unavailable.svg)
