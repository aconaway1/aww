---
title: "ISCW Notes - Access List Resequencing"
date: 2009-11-08
---

I don't know if this really pertains to the ISCW test per se, but this is something I learned in my class last week.  I'm sure I should have learned this years ago, but, alas, I didn't.

Access lists get messy.   You build one, apply it to an interface, and think all is well.  Then, ask for more access, so you may have to insert new entries between existing lines.  Your security team may ask you to deny access from a host while allowing it from others.  The next thing you know, you ACL looks something like this.

> ```
> Router#sh access-lists
> Extended IP access list MyACL
> 5 deny tcp host 192.168.0.38 any eq www
> 6 deny tcp host 192.168.0.39 any eq www
> 10 permit tcp 192.168.0.0 0.0.0.255 any eq www
> 15 deny tcp host 192.168.0.39 any eq 443
> 17 deny tcp host 192.168.0.85 any eq 443
> 20 permit tcp 192.168.0.0 0.0.0.255 any eq 443
> 30 deny ip any any log
> ```

That looks horrible, doesn't it?  The sequence numbers are all out of whack, and you may run out of head room if you have to insert more lines.  To quickly clean up your ACL, you can run the _ip access-list resequence_ command.

> ```
> Router(config)#ip access-list resequence MyACL 10 10
> ```

This command will take our example ACL and resequence it starting at 10 and incrementing 10 for each line.  You can start at any number you want (within reason) and increment the same (within reason again).  Using 10 and 10 seems pretty universal, so, once you run that command, your ACL looks like this.

> ```
> Router#sh access-list
> Extended IP access list MyACL
> 10 deny tcp host 192.168.0.38 any eq www
> 20 deny tcp host 192.168.0.39 any eq www
> 30 permit tcp 192.168.0.0 0.0.0.255 any eq www
> 40 deny tcp host 192.168.0.39 any eq 443
> 50 deny tcp host 192.168.0.85 any eq 443
> 60 permit tcp 192.168.0.0 0.0.0.255 any eq 443
> 70 deny ip any any log
> ```

Cool, eh?  I think I'll spend the week doing this to all our routers at work.

Send any holiday turkeys questions my way.
