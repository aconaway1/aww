---
title: "ISCW Notes - Role-based Views"
date: 2009-11-05
tags: 
  - "ccnp"
  - "cli"
  - "iscw"
  - "roles"
  - "security"
  - "user"
  - "view"
---

I'm at training for the ISCW test this week, and this topic came up yesterday.  Since it came up last week at the office, I figure it was a sign from $deity that it was time for a blog entry.

An admin in another business unit was trying to set up command access for some of his techs.  He was going through a couple of routers and assigning commands to privilege levels so that his techs could access them.  He was having a boat load of problems, though, and couldn't get it to work

He was trying to allow his guys to run a _show ip route_, but they also wanted to run _show ip route x.x.x.x_.  He was assigning commands to privilege level 7 then giving his tech's user accounts the same privilege.

> ```
> Router(config)#privilege exec all level 7 show ip route
> Router(config)#username user1 privilege 7 secret his.password
> ```

For some reason, this wasn't working, though.  The user could log into the router, but they couldn't get authorized to run the subcommands as expected.  I blamed it on his non-standard 7600 running a non-standard IOS version (sorry, I can't give any more detail without revealing too much about the company), but I came across a much easier way to do it today in class with role-based views.

A _view_ is a set of commands that can be assigned to users, and, to give a user access to those commands, you make them a member of that view.  You'll see that in a second.  You also have a _superview_, which is a set of views, so a user can be a member of multiple views.

There are some prerequisites to using views.  First of all, you have to have the enable secret set.  You should already have that on a production router, but, if you're working in a lab or something, you may have issues.  You also need to have AAA enabled.  That's beyond the scope here, but I'm sure you can figure it out.

To configure a view, you must first be in the root view.  How do you do that?  Just enable to it.

> ```
> Router#enable view
> ```

You'll enter the enable secret, and nothing special will happen, but now you can use the _parser view_ command to create a new view.  This takes you into the view submode which is where you list what commands you want to let users run.  You also set a secret (password) so you can call up the view later.

Let's create a view called "TechView" for my guy.  We'll give members of that view access to the "show ip route" commands to include all the subcommands.  We'll put the user "tech1" in that view, too.

> ```
> Router(config)#parser view TechView
> Router(config-view)#secret view.pass
> Router(config-view)#command exec include all show ip route
> Router(config)#username tech1 view TechView secret tech.pass
> ```

Every time that "tech1" logs in, that user will have access to all the _show ip route_ commands.  If you have a user who is not in that view but wants access to it, they can run the _enable view TechView_ command and enter the secret.  On the console, you'll see a message saying that user has switched to the view.  If the user does a _show parser view_, they can see what view they're in.

> ```
> Router#enable view TechView
> Password:
> Router#
> *Mar  1 00:09:04.047: %PARSER-6-VIEW_SWITCH: successfully set to view 'TechView'.
> Router#sh parser view
> Current view is 'TechView'
> ```

Send any test vouchers questions my way.
