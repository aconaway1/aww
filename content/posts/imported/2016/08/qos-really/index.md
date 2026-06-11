---
title: "QoS?  Really?"
date: 2016-08-20
categories: 
  - "cisco"
tags: 
  - "apic-em"
  - "cisco"
  - "day"
  - "easyqos"
  - "extra"
  - "field"
  - "live"
  - "qos"
  - "tech"
---

I wrote this post during Cisco Live and said "I'll just give it a once-over tonight and publish it."  That was something like 6 weeks ago now. What a loser I am.

* * *

Yes, really. QoS has actually gotten some attention this year. After how many years of living in the dark and being feared by junior and senior engineers alike, we're seeing some really cool technologies coming out for it.

I was honored to be invited to [Tech Field Day Extra](http://techfieldday.com/event/clus16/) this morning while I'm at Cisco Live.  If you don't know about TFD, you're missing out.  A group of influencers gather in a room and get very deep and very technical presentations from vendors.  Today, Cisco came and talked about a couple of topics including branch security and QoS.  Obviously, the QoS was the big hitter for me.

Tim Szigeti ([@tim\_szigeti](https://twitter.com/tim_szegeti)) kicked off the QoS conversation by talking about some of the recent advancements in QoS in both hardware and software. In hardware, he discussed the programmability of the new ASICs that Cisco is using in their switches and routers.  These ASICs are dumb out of the box, but they are very willing to learn.  Want it to do IP? Program it to understand IP.  Want to add QoS?  Program it to add QoS.  We've seen programmable ASICs in devices for quite a while, but having these in your switches and routers opens a lot of doors.

How about software, then?  IOS-XE 16.5 takes advantage of these hardware advancements and simplifies the configuration.  When I say simplies, I'm not joking.  In older code, for example, a standard QoS config using MQC that takes 1622 lines of code is now only 2 lines.  That's all.  How's that for simplified?

The drop-the-mic moment, though, came during the APIC-EM discussion with Ramit Kanda. Now, I knew absolutely nothing about APIC-EM going into the session, but I'm going to make it a point to learn more.  This device is a VM that provides programmability, automation, and abstraction for your network gear.  In this case, it provides abstraction for QoS.  Read that again.  Abstraction for QoS.  That means you don't need log into each devices in the path and configure each one.  More importantly, though, you don't have to generate configs for your different switch and router models.  You just say "make QoS like this," and everything is configured end-to-end.  EasyQoS is the name of that technology, and, boy, is it named appropriately.

The moral of the story today is that QoS has been a bastard step-child for years (decades?) and is finally getting some attention.

Thanks to Tom Hollingsworth and Stephen Foskett for hosting TFD today.  It was my first time and quite a blast.  If you get the oppotunity to participate, do it.

* * *

Since it took so long to publish the post, the videos for the TFD session have long been available.  You can see the EasyQoS video here.  The others are readily available from there.

https://youtu.be/JNSL\_s2qiIM
