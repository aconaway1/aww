---
title: "Filtering Outbound Traffic"
date: 2007-09-25
tags: 
  - "security"
---

I've seen a thousand \[tag\]firewalls\[/tag\] in my time, and nearly all of them are poorly configured. The biggest culprit? No \[tag\]outbound\[/tag\] \[tag\]filtering\[/tag\]. I guess a lot of people think that firewalls are there to protect the network from the Internet, but that's only part of it. The firewall is to protect every segment from every other segment -- all segments both inbound and outbound.

I guess that way back in the day that was true. You had your well-behaved network behind a firewall, and the only threat was from the evil hackers of the Internet. That's not true any more, though. What about viruses? Or spyware? You don't want those things spreading out from your network, do you? Think about liability, too. If you run a corporate network and an employee starts illegally downloading stuff from Kazaa, the company is liable for that, and the first step is to block any unneeded traffic from getting out.

Forget that your workstation sits on the inside of the firewall and remember that the intern down in development has a machine there. You know -- the guy who "learned all about computers" in school. Use your firewall to protect the Internet from him!

\----

The note: Outbound filtering doesn't keep the badies out completely. One of the first rules on your list will be to allow all users to TCP/80 on any host so everyone can surf. Any worm worth its salt these days will use TCP/80 for all its communications to take advantage of that hole, so you need to keep your antivirus and antispyware software updated to protect yourself and everyone else.
