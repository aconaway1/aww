---
title: "Using SPF Records To Build Objects"
date: 2009-10-16
tags: 
  - "_netblock"
  - "asa"
  - "dig"
  - "dns"
  - "fwsm"
  - "google"
  - "http"
  - "mail"
  - "object"
  - "object-group"
  - "pix"
  - "records"
  - "security"
  - "spf"
  - "txt"
---

My biggest complain about modern firewalls is their lack of the ability to create rules based on URLs or HTTP streams; you have to open access between IP addresses.  Yes, I know there are other means to do that, but I want my ASA/PIX/FWSM to do it without making me do so much work.

Anyway, the fact that you have to use IPs brings up some interesting problems.  Let's say you have a server in a DMZ that needs to query Google for some content.  Since you're a hard-ass network guy like I am, you tell the admin that they have provide the data flow they want to use -- source IP, destination IP, protocol, port.  They come back and tell you that they need their server to connect via HTTP to 74.125.45.100.  You put in the rules as given, but the IP has suddenly changed on you.

Google (and lots of other big sites) uses some tricks to keep the load down on their servers and to help with availability, and one such trick is to use round robin DNS, which rotates the A record so everyone doesn't slam the same boxes.  You can query google.com once and get an address, but, when you query it again, you may get a different address.  That means that when your new rules don't work, you have to check the logs, see what got denied, open that up, rinse, and repeat.  That sucks.

An easier way might be to create an [_object-group_](http://aconaway.com/tag/object-group/ "AConaway.com -- Tag/object-group") that includes IPs as you discover them.  You put in rules based on an object-group, then, when it fails, you just add to the object-group so you don't have to put in any more rules.  The problem is that you'll spend a lot of time building up a good baseline.  If only there were a way to get a list of IP addresses that Google uses.  Hmmm.  \*segue\*

Have you ever heard of [SPF](http://www.openspf.org/ "OpenSPF.org - SPF: Project Overview") netblock records?  SPF is an email security mechanism that allows an email server to verify that an email message is coming from an authorized email source.  In other words, when a mail server receives mail, it can check to see if the sending server is actually allowed to send mail on behalf of the source domain.  It supposed to cut down on spam and whatnot, but I don't follow it closely enough to know if it's working.  The moral of the story is that is involves a list of IP addresses that an organization maintains; Google happens to be a participant in SPF.

If you query for the TXT record \_netblocks.google.com, you get back a text record that looks like this.

> \[jac@holland ~\]$ dig +short txt \_netblocks.google.com "v=spf1 ip4:216.239.32.0/19 ip4:64.233.160.0/19 ip4:66.249.80.0/20 ip4:72.14.192.0/18 ip4:209.85.128.0/17 ip4:66.102.0.0/20 ip4:74.125.0.0/16 ip4:64.18.0.0/20 ip4:207.126.144.0/20 ip4:173.194.0.0/16 ?all"

This record includes all IP addresses that Google says is authorized to send email from google.com.  That's a lot of IP addresses, isn't it?  It might make sense that this list might also be the definitive list of Google production IPs.

My company has used this TXT record in the past to open access to Google.  We had an app that needed to query Google maps, and one of our engineers was tired of nickel and diming it to death, so he found the SPF block and put them all in.  Works like a champ.

There are always dangers when you rely on information from somebody else, though, right?  Google's usually pretty good about stuff like this, but what if you did the same for another company who only half-heartedly kept their records up-to-date?  You may only have half of their IPs in your object-gropu.  You might even wind up opening access to or from a cable modem system or from another company who bought the IP addresses.

I'll also  note that there aren't that many domains using this technique, so finding SPF netblock records may be a challenge.  It's worth the time to do a simply query, though; it might save you some time.

Send any carved pumpkins questions my way.
