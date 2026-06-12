---
title: "Ideas That Seems Good At the Time"
date: 2007-09-12
tags: 
  - "cable"
  - "port"
  - "vlan"
---

When I started in IT, I tried to get my gear as standardized as possible to impress everyone. I worked at it and worked at it until I realized that there were a handful of things that sound good but just won't work. If you're just getting started in the field, you may not agree, but come back in 5 years and see how right I am. Heh.

- **Assigning switchports to VLANs in chunks just doesn't work.** This seems like a great idea. You can put client A on port 1 through 12 and client B on ports 13 through 24. Then client A winds up with 13 servers, and B only has 3, so your whole scheme is in pieces on the floor. It's just easier to plug servers into the next available port and forget physically organizing the ports. The switches don't care if the ports are in order by VLAN. Just keep it simple.
- **Color-coding cables only works for a while.** Let's cable web servers with green cables and application boxes with blue cables and the database servers with pink and the mail servers with aubergine. I promise you, though, that you will run out of cables of one color or another and wind up having a database server in green. Then you'll have something else wrong. It won't be long before the color standard only applies on paper.
- **Labeling switchports by name only works if you buy servers all the time.** If you're in an environment where servers change roles and names, I guarantee you that your ports are mislabeled. The only time that labeling really works is if you're lucky enough to work for a company with enough money to buy new stuff for every project. I've actually resorted to labeling ports with serial numbers instead of names since those won't change.
- **Complicated naming schemes don't work.** They may sound cool, but simpler names are almost always better. Name your router "r1" or something.  Don't try "rtr001prod1" or something as ludicrous. I once made up this awesome naming scheme, and it worked until the business took on other projects that didn't fall into the standard, so I was screwed. Save yourself some problems and keep it simple.
