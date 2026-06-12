---
title: "Can't Login to Your ASA via SSH or Telnet?"
date: 2008-02-18
tags: 
  - "asa"
  - "firewall"
  - "pix"
---

I deployed a Cisco ASA at a location and couldn't get logged in via SSH. I would get prompted, but, no matter what username/password I put in, it would just reject me. After some digging, it turns out that I forgot this command.

> aaa authentication ssh console LOCAL

When I put this in, it let me right in as expected. I have no clue what the deal was. I guess I assumed that the ASA would use the local userbase if a AAA service wasn't configured. I guessed wrong.

I'm sure this will apply to telnet sessions as well. I'd also bet money that equivalent PIX OS versions do that same, so keep an eye out.
