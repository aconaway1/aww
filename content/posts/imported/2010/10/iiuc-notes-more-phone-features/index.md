---
title: "IIUC Notes - More Phone Features"
date: 2010-10-02
categories: 
  - "voice"
tags: 
  - "640-460"
  - "access"
  - "after-hours"
  - "blocking"
  - "call"
  - "ccna"
  - "cert"
  - "certification"
  - "cisco"
  - "digital"
  - "exam"
  - "iiuc"
  - "notes"
  - "paging"
  - "router"
  - "test"
  - "voice"
---

Here are some more notes from my IIUC studies.  As always, corrections requested.

**Paging**

- Broadcasts messages to a group for a one-way communication
- Paging groups are used to limit which phones get the broadcast
- Paging can be unicast or multicast
    - Unicast groups limited to 10 members
    - Multicast requires mcast support on the network
- Paging configurations can be unicast, multicast, or multiple-group

> !  Unicast Paging  
> !  When 1044 is dialed, ephone 1 is paged  
> R1(config)#ephone-dn 44  
> R1(config-ephone-dn)#number 1044  
> R1(config-ephone-dn)#paging  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone 1  
> R1(config-ephone)#paging-dn 44
> 
> !  Multicast Paging  
> !  When 1045 is dialed, ephone 2 is paged  
> R1(config)#ephone-dn 45  
> R1(config-ephone-dn)#number 1045  
> R1(config-ephone-dn)#paging ip 239.1.1.100 port 2000  
> R1(config-ephone-dn)#exit  
> R1(config)#ephone 2  
> R2(config)#paging-dn 45
> 
> !  Multiple Group Paging  
> !  When 1046 is dialed, both ephones 1 and 2 are dialed  
> R1(config)#ephone-dn 46  
> R1(config-ephone-dn)#number 1046  
> R1(config-ephone-dn)#paging group 44, 45

- There is a limit of 10 DNs in the paging group.

**After-hours Call Blocking**

- Allows you to configure time ranges and patterns that cannot be called during those ranges
- Three steps
    1. Defines days and/or hours that are considered after-hours
    2. Specify patterns to be blocked
    3. Create exemptions

> R1(config)#telephony-service  
> R1(config-telephony)#after-hours day mon 18:00 07:00 <- afterhours = 6pm to 7am  
> R1(config-telephony)#after-hours day tue 18:00 07:00  
> R1(config-telephony)#after-hours day wed 18:00 07:00  
> R1(config-telephony)#after-hours day thu 18:00 07:00  
> R1(config-telephony)#after-hours day fri 18:00 07:00  
> ...  
> R1(config-telephony)#after-hours date Dec 25 00:00 00:00 <- Christmas is after hours  
> ...  
> R1(config-telephony)#after-hours block pattern 1 91900....... 7-24 <- Pattern index 1 blocks 900 numbers 7day/24hours  
> R1(config-telephony)#after-hours block pattern 2 91.......... <- Pattern index 2 block all long distance after hours  
> ...  
> R1(config-telephony)#login timeout 15 clear 18:00 <- Allows logins for entering a PIN for after-hours exemption; times out in 15 minutes and clears at 18:00  
> R1(config-telephony)#exit  
> R1(config)#ephone 1  
> R1(config-ephone)#after-hours exempt <- the boss's phone can call anywhere except the 7-24 patterns  
> R1(confg-ephone)#ephone 2  
> R1(config-ephone)#ping 1234 <- Your phone can log in with this PIN for after-hours access

- Phones have to be restarted or reset for the Login key to be enabled.

**Call Accounting**

- It's important to see who is calling international numbers every day at lunch.
- Call Detail Records (CDRs) record who called what number when for how long plus more stuff.
- CME logs CDRs to the logging buffer, syslog, or both.
- Logging buffers clear when a router loses power, but it may be better than nothing.  <- Don't do this ever!  Get a syslog server!

> R1(config)#logging buffer 512000 <- Set the logging buffer size to 512000 bytes  
> R1(config)#dial-control-mib retain-timer 120 <- Roll records out in 120 minutes  
> R1(config)#dial-control-mib max-size 100 <- Only keep last 100 records

- Sending to syslog allows you to keep more records

> R1(config)#gw-accounting syslog  
> R1(config)#logging 192.168.0.2 <- Log to this server

- Account codes are used for billing.
    - Each department or unit can enter a code that appears in the CDR for use later.
- Users press the Acct key when the call is ringing or connected to enter their code.

**Music on Hold**

- Do I have to explain what MoH is?
- WAV or AU file in flash
- Files must be G.711 or G.729
    - G.711 is recommended since it is of higher quality
- Can be delivered via unicast or multicast

> R1(config-telephony)#moh piratedmusic.au <- Plays a local audio file as MoH  
> R1(config-telephony)#multicast moh 239.1.1.15 port 2001 <- multicast the MoH
