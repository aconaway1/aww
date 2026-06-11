---
title: "IIUC Notes - Phone Features"
date: 2010-10-01
categories: 
  - "voice"
tags: 
  - "640-460"
  - "access"
  - "boot"
  - "ccna"
  - "cert"
  - "certification"
  - "cisco"
  - "digital"
  - "directory"
  - "exam"
  - "forward"
  - "iiuc"
  - "intercom"
  - "notes"
  - "ntp"
  - "park"
  - "router"
  - "test"
  - "transfer"
  - "voice"
---

Here are some more notes from my IIUC studies.  As always, corrections requested.

**Local Directory**

- Allows users to look up names
- Allows names to show up when dialing or receiving a call
- Most phones have a directory button; some have a menu options for the directory

> R1(config)#ephone-dn 1  
> R1(config-ephone-dn)#name Roger Smith

- Directory entries can be added manually

> R1(config-telephony)#directory entry 1 1700 Corporate Fax  
> R1(config-telephony)#directory entry 2 1701 HR Fax

- By default, sorting is done alphabetically by first name.
- Sorting can be changed

> R1(config-telephony)#directory last-name-first

**Call Forwarding**

- Can be done by the user or through CLI
- User presses CFwdAll button, enters a number, and #; pressing CFwdAll again cancels forwarding.
- CLI forwarding is more flexible

> R1(config-ephone-dn)#call-forward busy 1800  
> R1(config-ephone-dn)#call-forward noan 1800 timeout 25 <- if no answer after 25 seconds  
> R1(config-ephone-dn)#call-forward max-length 0 <- disabled forwarding  
> R1(config-ephone-dn)#call-forward max-length 4 <- restricts forwarded number to a length of 4 digits

- H.450.3: A voice gateway redirects the forward to another gateway instead of using the phone as a proxy
    - Direct path from originator to destination
    - Frees up network resources by keeping path direct
    - Keeps latency and jitter down by avoiding long looping paths and a hairpin turn at the phone
- Forwarding patterns can help restrict where calls can be forwarded

> R1(config-telephony)#call-forward pattern 1... <- allows forwarding to a 4-digit number starting with 1

**Call Transfer**

- H.450.2: A voice gateway redirects transfers to another gateway instead of using the phone as a proxy.
    - The user doing the transfer is dropped from the conversation after transfer is complete.
- Generically, there are two types of forwarding.
    - Blind: sends the caller to the number blindly
    - Consult:  allows you to talk to the endpoint before transferring the call
- CME has three types of forwarding.
    - full-blind:  blind transfers using H.450.2 or SIP REFER
    - full-consult:  consult transfers using H.450.2 or SIP REFER if second line is available; if not, fall back to full-blind
    - local-consult:  Cisco-proprietary method for full-consult

> R1(config-telephony)#transfer-system full-consult  
> \- or - 
> R1(config-ephone-dn)#transfer-mode consult

- Transfer patterns work similarly to forwarding patterns

> R1(config-telephony)#transfer-patter 1...

**Call Park**

- Call parking allows a user to retrieve a call from any phone by "parking" the call to an extension.
- The call can be picked up from any phone able to dial that extension.
- Park numbers can be assigned randomly or manually.

> R1(config-ephone-dn)#park-slot <- makes this DN a park slot

- Call parking has several options.
    - reserved-for _dn_:  Only that DN can use this park-slot
    - timeout _seconds_:  Ring the phone phone that parked the call after that many seconds to remind them of the park
    - limit _count_:  After that many timeout intervals, drop the call.  Not good for customers.
    - notify _dn \[ only \]_:  Notify that DN when a timeout is reached
    - recall:  Sends the call back to the original phone when the timeout is reached
    - transfer _dn_:  Sends the call to this DN when the timeout is reached
    - alternate _dn_:  If the DN in the transfer command is not available, go here
    - retry _seconds_:  Try to transfer again after this many seconds
- The phone must be reset for call parking to take effect.

**Call Pickup**

- Allows users to pick up other ringing phones
- Best to use pickup groups so the sales guys don't pick up support calls by accident

> R1(config-ephone-dn)#pickup-group 5000

- There are three methods to pickup a call.
    - Directed pickup:  A user picks up a ringing phone by pressing PickUp followed by the target DN.
    - Local group pickup:  A user picks up a ringing phone in his pickup group by pressing GPickUp then \*.
    - Other group pickup:  A user picks up a ringing phone in another pickup group by pressing GPickUp then the other group number.

**Intercom**

[http://www.youtube.com/watch?v=3P2dbwrT\_fQ](http://www.youtube.com/watch?v=3P2dbwrT_fQ)

- Technically is a speed dial and auto-answer combination
- Intercom button is pressed, which dials a DN bound to another phone; that phone automatically answers on mute.
- The DNs involved usually (?) can't be dialed.
    - e.g., A101

> R1(config)#ephone-dn 99  
> R1(config-ephone-dn)#number A99  
> R1(config-ephone-dn)#intercom A98 label "Boss"  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone-dn 98  
> R1(config-ephone-dn)#number A98  
> R1(config-ephone-dn)#intercom A99 label "Lackey"  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone 54  
> R1(config-ephone)#button 5:99  
> R1(config-ephone)#restart  
> R1(config)#ephone 73  
> R1(config-ephone)#button 5:98  
> R1(config-ephone)#restart

- Other options
    - barge-in:  Places existing calls on hold on the other end and barges n
    - no-auto-answer:  Rings instead of auto answers
    - no-mute:  Doesn't mute when auto answering.  Can you say spying?
