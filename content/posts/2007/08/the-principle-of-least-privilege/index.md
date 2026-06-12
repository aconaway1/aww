---
title: "The Principle of Least Privilege"
date: 2007-08-10
tags: 
  - "linux"
  - "security"
---

[The Principle of Least Privilege](http://en.wikipedia.org/wiki/Principle_of_least_privilege "Wikipedia Article") says that users or applications should only have access to the what it needs to access and that access should be as limited as possible.  This idea can be applied to any number of things, but it is a very important topic when talking about security.

The idea is that processes, users, modules, or whatever can only access what they need to in order to function.   This keeps users in check since they don't have any access to anything outside their home directories (or whatever).  It keeps developers in check since their code can only access a small set of files or processes.  It keeps hackers in check since the Apache server they're hacking can't access the password file.  It even keeps administrators in check since it forces them to use _sudo_, which is logged to syslog.

Like everything, this is a simplistic description, but it's very important for _every_ administrator to adopt this idea.

You may also want to check out [SELinux](http://en.wikipedia.org/wiki/Selinux "Wikipedia Article") if you're a Linux admin, which is a system that is used to enforce least privileges for nearly everything on a Linux box.
