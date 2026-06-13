---
title: "CME Exercise #1"
date: 2010-10-07
categories: 
  - "voice"
tags: 
  - "640-460"
  - "ccna"
  - "ccs"
  - "cert"
  - "certification"
  - "cisco"
  - "exam"
  - "exercise"
  - "iiuc"
  - "notes"
  - "router"
  - "test"
  - "voice"
---

I tried something like this earlier this year with STP.  It got rave reviews (from my mother), so I figured I try it again.  

Below is a list of requirements for configuring a router as a call processor.  In a lab or in your head, configure the router to support the features as listed.  This isn't a contest or anything like that.  If you get it right, a virtual thumbs up is all I can afford to give you.  There are some licensing issues for running this stuff in GNS3/dynamips, so I can't help you out on that.  I'll just hint that GNS3 and dynamips will bind to real networks and that copies of a compatible IP softphone are available.

Here we go.

- Telephony  
    - Maximum of 10 DNs
    - Maximum of 5 ephones
    - DHCP server that provides the appropriate DHCP scope option for getting the phones online
- Phones  
    - Phone 1
        - Sales Phone A
        - Button 1: extension 1001
        - Button 2: intercom to phone 3 labeled as "Lackey"
        - Pickup Group [jane krakowski pokies](http://nwcustomtimbers.com/) 3001
    - Phone 2
        - HR Phone A
        - Button 1: extension 1002
        - Pickup Group 3001
    - Phone 3
        - Sales Phone B
        - Button 1: extension 1003
        - Button 2:  monitor button 1 on phone 1
        - Button 3: intercom to phone 1 labeled as "Boss" that answers unmuted
        - Pickup Group 3002
- Paging
    - Each department should have its own paging group.
    - All phones should be in a paging group for broadcasting emergencies to all employees.
- Call Parking
    - 2 call parking DNs
    - 1 CP DN should be dedicated to phone 2
- Music on Hold
    - MOH should play when a user is on hold or in a park slot
- After-hours
    - After hours should be Mon - Fri from 7pm to 7am
    - No one should be able to dial 1003 after hours
    - No one should be able to dial 1002 any day at any time

I'll get my own answer together and post the consensus result in a few days.  In the meantime, let me know how terribly I did.

Send any ~~unlicensed CIPC phones~~ questions my way.
