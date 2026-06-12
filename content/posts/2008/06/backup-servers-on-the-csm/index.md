---
title: "Backup Servers on the CSM"
date: 2008-06-26
tags: 
  - "csm"
---

On the CSM, you can configure a _vserver_ to use a main and backup _serverfarm_ which is used if a serverfarm is toast.  If all the RIPs in the main farm are out-of-service, the CSM will start to treat the backup farm just as if it's configured to be the main one.  Once one or more of the main farm RIPs have recovered, the CSM reverts back and uses those again.  "Give me an example when I'd use it!," you say?  Since the CSM is made for HTTP connections, we'll assume that you are using it for such. 

The example I've found over and over again involves HTTP redirect servers.  If the whole main serverfarm is dead, you can send requests to a set of redirect servers.  Once connections land there, you can send the user to another site, such as your west coast center, your European center, your main corporate page, or wherever.

You can also send traffic to a server that does nothing but show an error page.  Let's say you set up a group of servers that only display a "We're sorry, but we suck and can't handle your request" page.  If your main serverfarm is unavailable, the user won't get the page they're looking for, but it's better than just timing out, right?  (Remind me to do this for some customers.)

How about if you're moving your serverfarm to another rack and you have to do it all at once (for some unknown reason)?  You can use the backup serverfarm to move traffic to another set of server (say, the dev boxes) until you get the main serverfarm back online.

These backup farms are quite easy to configure.  When you assign the _serverfarm_ to the _vserver_, you simply give it a backup like this.

> vserver TEST-VS  
> serverfarm MAIN-SF backup BACKUP-SF

You can also use a backup on a [policy](http://aconaway.com/2008/06/23/intro-to-policies-on-the-csm/ "AConaway.com -- Intro to Policies on the CSM").

> policy TEST-POL  
> serverfarm POL-SF backukp BACKUP-SF

Bon appetit.
