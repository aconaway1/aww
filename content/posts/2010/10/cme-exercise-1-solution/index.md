---
title: "CME Exercise #1 Solution"
date: 2010-10-12
categories: 
  - "voice"
---

Here's my solution to [the exercise I posed last week](/posts/2010/10/cme-exercise-1/).  Let's see if we can get this right.

I'm going to assume you know how to give a router an IP address.  If you don't, let me know and I'll help you out.  We'll use 10.10.10.1/24 for our CME router.

Let's work on the telephony section first.  We need to limit our CME to 10 ephone-dns and 5 ephones.  Easy enough.  While we're at it, we'll have to give the telephony service a source IP address.  We might as well do the music on hold, too.

> R1(config)#telephony-service  
> R1(config-telephony)#max-dn 10  
> R1(config-telephony)#max-ephones 5  
> R1(config-telephony)#ip source-address 10.10.10.1  
> R1(config-telephony)#moh flash:/moh.au

Now the DHCP scope.

> R1(config)#ip dhcp excluded-address 10.10.10.1 10.10.10.10  
> R1(config)#ip dhcp pool MYPOOL  
> R1(dhcp-config)#network 10.10.10.0 255.255.255.0  
> R1(dhcp-config)#option 150 ip 10.10.10.1  <- Use 10.10.10.1 for the TFTP server

You can't configure any of the buttons on an ephone without having the DNs configured, so let's do that first.  We're going to need 3 DNs for the line buttons of each phone, 2 for the intercom, and 2 for call parking.  Here are the line DNs first.  Make sure to remember to configure the pickup groups with each.

> R1(config)#ephone-dn 1 dual-line  
> R1(config-ephone-dn)#number 1001  
> R1(config-ephone-dn)#name Sales Phone A  
> R1(config-ephone-dn)#pickup-group 3001  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone-dn 2 dual-line  
> R1(config-ephone-dn)#number 1002  
> R1(config-ephone-dn)#name HR Phone A  
> R1(config-ephone-dn)#pickup-group 3001  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone-dn 3 dual-line  
> R1(config-ephone-dn)#number 1003  
> R1(config-ephone-dn)#name [where do i buy electronic cigarettes in chicago](http://www.primetimecomedyonline.com/) Sales Phone B  
> R1(config-ephone-dn)#pickup-group 3002

Now the intercom DNs.  The wording on this one was murky at best.  In my mind, the boss's phone answers muted while the lackey's phone answers unmuted.

> R1(config)#ephone-dn 4  
> R1(config-ephone-dn)#number A4  
> R1(config-ephone-dn)#intercom A5 label Lackey <- Ring number A5 when you press the button  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone-dn 5  
> R1(config-ephone-dn)#number A5  
> R1(config-ephone-dn)#intercom A4 no-mute label Boss

Now the parking

> R1(config)#ephone-dn 6  
> R1(config-ephone-dn)#number 5006  
> R1(config-ephone-dn)#park-slot  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone-dn 7  
> R1(config-ephone-dn)#number 5007  
> R1(config-ephone-dn)#park-slot reserved-for 1002

And the paging DNs.

> R1(config)#ephone-dn 8  
> R1(config-ephone-dn)#number 1008  
> R1(config-ephone-dn)#name Sales Paging  
> R1(config-ephone-dn)#paging  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone-dn 9  
> R1(config-ephone-dn)#number 1009  
> R1(config-ephone-dn)#name HR Paging  
> R1(config-ephone-dn)#paging  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone-dn 10  
> R1(config-ephone-dn)#number 1010  
> R1(config-ephone-dn)#name Global Paging  
> R1(config-ephone-dn)#paging  
> R1(config-ephone-dn)#paging group 8,9

On to the ephones.  We'll need to create the ephones, assign the buttons, and put the ephone in the right paging group.

> R1(config)#ephone 1  
> R1(config-ephone)#mac-address 1111.2222.3333  
> R1(config-ephone)#button 1:1  
> R1(config-ephone)#button 2:4  
> R1(config-ephone)#paging-dn 8  
> R1(config-ephone)#exit  
> R1(config)#ephone 2  
> R1(config-ephone)#mac-address 1111.2222.3334  
> R1(config-ephone)#button 1:2  
> R1(config-ephone)#paging-dn 9  
> R1(config-ephone)#exit  
> R1(config)#ephone 3  
> R1(config-ephone)#mac-address 1111.2222.3335  
> R1(config-ephone)#button 1:3  
> R1(config-ephone)#button 2m1  
> R1(config-ephone)#button 3:5  
> R1(config-ephone)#paging-dn 8

Now for the after-hours call blocking.

> R1(config-telephony)#after-hours day mon 19:00 07:00  
> R1(config-telephony)#after-hours day tue 19:00 07:00  
> R1(config-telephony)#after-hours day wed 19:00 07:00  
> R1(config-telephony)#after-hours day thu 19:00 07:00  
> R1(config-telephony)#after-hours day fri 19:00 07:00  
> R1(config-telephony)#after-hours block pattern 1 1003  
> R1(config-telephony)#after-hours block pattern 2 1002 7-24

That should do it.

Send any ~~640-level exam vouchers~~ questions my way.

Audio Commentary  
![Audio: CME Exercise #1 Solution](images/audio-unavailable.svg)
