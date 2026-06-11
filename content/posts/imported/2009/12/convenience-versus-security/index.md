---
title: "Convenience versus Security"
date: 2009-12-01
tags: 
  - "convenience"
  - "security"
---

I coworker sent over [a link](http://www.kb.cert.org/vuls/id/261869 "Cert.org - US-CERT Vulnerability Note VU#261869") today that got me thinking about an old adage that I've been sharing for years.  The link actually has nothing to do with the philosophy but did trigger a random spewing of words from my brain.

Here's what I tell everyone.  When I deliver these lines, I usually picture myself as Socrates talking to a bunch of Greeks in togas.

> There's a line.  On one end of the line is security; on the other end is convenience.  You have to figure out where the best place for your users/application/system/etc. to sit on the line to be both secure and convenient enough to function.

I usually follow that up with an extreme example.

What's the most convenient configuration for a public webserver?  One solution would be to have it cabled to an Internet switch in front of a firewall with every network service enabled and all security software disabled in case it interferes with operation.  Quite convenient, but not very secure.

What the most secure configuration?  The server is powered down, disassembled, all parts shredded to bits, and the bits put into a dozen different boxes that are shipped to the ends of the world.  Nobody's going to get unauthorized access to that, but it's not very convenient,  is it?

In both cases, being too far to one side actually interferes with functionality.  How long will it be before the convenient server get owned by a script kiddie and no longer functions?  How long before someone wants to access the secure server and finds it doesn't function at all?  We should probably make a compromise, right?

This is nothing new.  We've all been saying this for years, right?

What's my point?  I don't think I have one, really.  I guess I just wanted to refresh this in everyone's mind today.
