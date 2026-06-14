---
title: "Intro to Policies on the CSM"
date: 2008-06-23
tags: 
  - "csm"
---

The [CSM](/tags/csm/ "AConaway.com -- Category:CSM") is pretty bad little box.  It not only watches layer 4 items like TCP connections, but also talks HTTP, which you can use to do some custom, or policy-based, load balancing.

Policies are the objects that make custom balancing work.  Like everything else (it seems) on the CSM, a policy is an object made up of other objects -- _maps_ and _serverfarms_.  A _map_ matches patterns based on a number of things including the URL and HTTP header values, while the _serverfarm_ directive tells where to send traffic that matches the _map_.  If, for example, you want to send all requests with "/admin" in the URL to a management server instead of the regular web servers, you can do it with a policy.

Let's try one.  We first build our serverfarm and vserver for normal traffic.  This configuration will simply serve HTTP on 1.1.1.1 via two servers in a serverfarm.

> ```
> serverfarm TEST-FARM
>  real 192.168.0.101
>   inservice
>  real 192.168.0.102
>   inservice
> 
> vserver TEST-VS
>  virtual 1.1.1.1 tcp http
>  vlan 1
>  serverfarm TEST-FARM
>  inservice
> ```

Now let's set up our policy-based load balancing for this vserver.  Let's say that we want to send all traffic with "/admin" in the URL to go to the management server at 192.168.0.150, so we first create a serverfarm with the management server in it.

> ```
> serverfarm TEST-MGMTFARM
>  real 192.168.0.150
>   inservice
> ```

Next, we create a map that matches the URL.

> ```
> map TEST-MAP url
>  match protocol http url */admin*
> ```

Notice the pretty wildcards?  If you don't include those, the URL would have to match exactly for the map to apply.  Since most apps don't actually go to the same URL over and over, we have to use wildcards to make sure that everything matches.  Obviously, in practice, this can get complicated depending on what you're trying to match.

Next, we create the policy itself.  Basically, we just combine the map and the serverfarm into a new object.

> ```
> policy TEST-POL
>  url-map TEST-MAP
>  serverfarm TEST-MGMTFARM
> ```

With me so far?  Good.  There's only one thing left to do -- apply the policy to the vserver.

> ```
> vserver TEST-VS
>  slb-policy TEST-POL
> ```

Everytime the CSM gets a request to the virtual IP of 1.1.1.1 on HTTP, it will check the URL for the string "/admin", and, if it's in the request, it will send it over to the management server.  If it doesn't match, it simply goes to the main serverfarm just as if the policy wasn't even applied.  If you do a "[show mod csm X vservers](/posts/2008/06/getting-something-out-of-the-csm/ "AConaway.com -- Getting Something Out of the CSM")" now, you'll see the policy listed under the vserver we just made along with stats on the number of packets that were matched.

There are caveats.  The CSM can only support a limited number of policies for each vserver.  According to a TAC case I opened a while back, you can only have 10 policies configured per vserver, so keep that in mind when designing everything out.  There's also a 30k memory limit for policies on a serverfarm; I have no clue how to calculate how much memory a policy is using, but using wildcards definitely adds to the memory footprint.

Send me any questions since I still haven't found anyone who uses the CSM.  :)
