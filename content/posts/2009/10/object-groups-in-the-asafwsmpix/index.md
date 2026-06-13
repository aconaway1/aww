---
title: "Object Groups in the ASA/FWSM/PIX"
date: 2009-10-01
tags: 
  - "acl"
  - "asa"
  - "fwsm"
  - "group"
  - "object"
  - "object-group"
  - "pix"
---

I can't believe I haven't talked about _object-groups_ yet.  I had a whole other blog entry written up, and, when I went to link things over, I realized I couldn't find an intro to it.  Here it goes.

Welcome to the modern world.  A world of wonder.  A world of quickly-advancing technology.  A world where clusters of machines sit behind load balancers for scalability and availability.  A world where those clusters need access to other clusters.  A world where your firewall rulebase gets so big that it's unreadable without some help.

Enough with the drama already.  I would say I hate the cheesy stuff, but I think my whole blog is nothing but cheesy stuff, right?  To the point.  Enterprise firewall configurations can get quite large with ACLs applied in different directions to different interfaces.  Our ACL entries number in the 6000 range, but the firewall we're running says we're only at 5% utilization in the ACLE memory space. That means that our not-top-of-the-line firewall is designed to handle 120k lines of ACLs.  That can be quite a handful to configure by hand.  There may be an easier-to-maintain solution, though.

Let's say you have a cluster of servers behind your CSM that all need to access a database.   Since there's a nice ASA, FWSM, or PIX between the servers and database (as there should be), you have to open up access for this connection.  Let's say that you have four servers with the IPs of 192.168.100.101-104 that need access to 10.10.10.1 on the mySQL port (TCP/3306).

> ```
> access-list LIST1 permit tcp host 192.168.100.101 host 10.10.10.1 eq 3306
> access-list LIST1 permit tcp host 192.168.100.102 host 10.10.10.1 eq 3306
> access-list LIST1 permit tcp host 192.168.100.103 host 10.10.10.1 eq 3306
> access-list LIST1 permit tcp host 192.168.100.104 host 10.10.10.1 eq 3306
> ```

Where are your remarks?  Why don't you document something for once in your life?

Anyway, that's easy, right.  Four configuation lines isn't so bad, but some of the server admins come to you one day and tell you that the company actually marketed the new web app and that tey are adding 37 more servers to the cluster.  Now the 37 new servers need the same rules, right?  The server dudes also tell you that, since the app has grown so much, the DBAs have set up a split-read-write scenario where the current database handles the reads and a new database handles the writes.  That's 78 new rules (37 to the old and 41 for the new).  That's a lot of rules.

_Object-groups_ to the rescue.  An object-group is a logical group of objects (duh!) that you can use to create ACLEs.  You can create a group of hosts, a group of network, or a group of ports.  For our example, let's create an object-group that includes all the hosts in the new huge cluster.

> ```
> object-group network CLUSTER1
>    description The Huge Cluster (that's what she said)
>    network-object host 192.168.100.101
>    network-object host 192.168.100.102
> ...
>    network-object host 192.168.100.141
> ```

What do we do with it, though?  You treat it (almost) just like it was a host in an ACL.  Remember we wanted to open access to the old database on TCP/3306, right?

> ```
> access-list LIST1 permit tcp object-group CLUSTER1 host 10.10.10.1 eq 3306
> ```

If you do a show access-list LIST1 now, you'll see that a new rules has been added for each object in the object-group.  It should look something like this.

> ```
> access-list LIST1 permit tcp object-group CLUSTER1 host 10.10.10.1 eq 3306
>    access-list LIST extended permit tcp host 192.168.100.101 host 10.10.10.1 eq 3306 (hitcnt=0)
>    access-list LIST extended permit tcp host 192.168.100.102 host 10.10.10.1 eq 3306 (hitcnt=0)
> ...
>    access-list LIST extended permit tcp host 192.168.100.141 host 10.10.10.1 eq 3306 (hitcnt=0)
> ```

Notice that the firewall created 41 rules for you out of your one configured line, but now the rules are indented. The indention means that the rules is generated automagically instead of by hand. Since you can only take out rules that you put in by hand, so you can't take out the line allowing 192.168.100.123 access; it's an all-or-nothing scenario.  Be aware of that.

You can use object-group for ports, too.  Let's add to our example and say that the cluster will need to access the memcached instance on the database server as well.  Those processes run on TCP ports 15000 - 15100.

First we build an object-group for the ports we need.

> ```
> object-group service DBPORTS
>    description mySQL and memcached ports
>    service-object tcp eq 3306
>    service-object tcp range 15000 15100
> ```

Now let's apply it to the ACL.

> ```
> access-list LIST1 permit tcp object-group CLUSTER1 host 10.10.10.1 object-group DBPORT
> ```

What does the ACL look like now?  Well, it's a [Duesenberg](http://en.wikipedia.org/wiki/Duesenberg "Wikipedia.com -- Dusenberg").

```
access-list LIST1 permit tcp object-group CLUSTER1 host 10.10.10.1 object-group DBPORTS
   access-list LIST extended permit tcp host 192.168.100.101 host 10.10.10.1 eq 3306 (hitcnt=0)
   access-list LIST extended permit tcp host 192.168.100.101 host 10.10.10.1 eq 15000 (hitcnt=0)
...
   access-list LIST extended permit tcp host 192.168.100.101 host 10.10.10.1 eq 15099 (hitcnt=0)
   access-list LIST extended permit tcp host 192.168.100.101 host 10.10.10.1 eq 15100 (hitcnt=0)
...
   access-list LIST extended permit tcp host 192.168.100.141 host 10.10.10.1 eq 3306 (hitcnt=0)
   access-list LIST extended permit tcp host 192.168.100.141 host 10.10.10.1 eq 15000 (hitcnt=0)
...
   access-list LIST extended permit tcp host 192.168.100.141 host 10.10.10.1 eq 15099 (hitcnt=0)
   access-list LIST extended permit tcp host 192.168.100.141 host 10.10.10.1 eq 15100 (hitcnt=0)
```

That's a lot of ACL entries for one configuration line, isn't it? Let's see. 102 ports times 41 servers is 4182 lines in the ACL. You can see how might be to your advantage to use object-groups at times.

Send any ~~candy corn~~ questions my way.
