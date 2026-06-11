---
title: "IIUC Notes - Voice Ports and Dial Peers"
date: 2010-10-04
categories: 
  - "voice"
tags: 
  - "640-460"
  - "cas"
  - "ccna"
  - "ccs"
  - "cert"
  - "certification"
  - "cisco"
  - "clock"
  - "dial-peer"
  - "digital"
  - "ds0"
  - "e1"
  - "exam"
  - "framing"
  - "fxo"
  - "fxs"
  - "iiuc"
  - "notes"
  - "port"
  - "pri"
  - "router"
  - "signaling"
  - "t1"
  - "test"
  - "voice"
  - "voice-port"
---

More of my IIUC study notes.  As always, feel free to correct.  I really need to have a real post, don't I?

_**show voice port summary**_

- Shows the voice ports available for use

> ```
> R1#show voice port summary
>                                           IN       OUT
> PORT           CH   SIG-TYPE   ADMIN OPER STATUS   STATUS   EC
> ============== == ============ ===== ==== ======== ======== ==
> 50/0/1         1      efxs     up    up   on-hook  idle     y
> 50/0/1         2      efxs     up    up   on-hook  idle     y
> 50/0/2         1      efxs     up    up   on-hook  idle     y
> 50/0/2         2      efxs     up    up   on-hook  idle     y
> 50/0/3         1      efxs     up    up   on-hook  idle     y
> 50/0/4         1      efxs     up    up   on-hook  idle     y
> 50/0/5         1      efxs     up    up   on-hook  idle     y
> 
> ```

- An ephone-dn shows up as efxs, so all these are ephone-dns.
- Channels are numbered 0-23; timeslots are numbered 1-24

**FXS Ports**

- Connect to end stations like analog phones and fax machines
- Signaling
    - Ground start: New connections started by grounding wires
        - Typically used when tied to PBXes
    - Loop start:  New connections started by sending DC voltage
        - Default
        - Typically used when connecting to analog devices
- Call progress tones
    - Audible tones to let the user know the status of a call
        - Dial tone, busy, call waiting, etc.
        - Different in each geographical area
- Caller ID
    - Identifies the name and number that calls on this line should appear

> R1(config)#voice-port 0/0/0  
> R1(config-voiceport)#signal loopStart <- Use loopstart signaling  
> R1(config-voiceport)#cptone PE <- Uses CP tones from Peru  
> R1(config-voiceport)#station-id name Corporate Fax  
> R1(config-voiceport)#station-id number 5551212

**FXO Ports**

- Connects to CO or PBX
- A lot of the same configurations as FXS ports
- Two additional to discuss
    - dialt-type:  DTMF or pulse dialing
    - ring:  The number of rings to wait before answering; usually 1
        - Think of allowing a home user to answer the phone before the fax machine picks up

> R1(config)#voice-port [Reverse Phone](http://reverselookupservice.com/) 0/0/1  
> R1(config-voiceport)#dial-type dtmf <- touch tone  
> R1(config-voiceport)#ring 3 <- wait 3 rings before answering

**Digital Voice Ports**

- Unlike analog voice ports, digital voice ports must be configured to function with the network to which they are attached.
- Voice and WAN interface cards (VWICs) provide digital voice port
- _show controllers t1_
- Framing:  defines how to format the frames
    - SF or ESF
- Line coding:  encodes the signal in a way to maintain sychronization  
    - AMI or B8ZS
- Clock source:  defines who dictates the clocking
- Signaling:  channel signaling
    - CAS:  use _ds0-group_
        - Ports show up as 0/0:1, where 0/0 is the physical port and 1 is the ds0 group
    - CCS:  use _pri-group_
        - Ports shows up as 0/0:23, where 0/0 is the physical port and 23 is the signaling channel (16 in E1)

> R1(config)#isdn switch-type primary-5ess <- If using CCS  
> R1(config)#controller t1 0/0  
> R1(config-controller)#framing esf  
> R1(config-controller)#linecode b8zs  
> R1(config-controller)#clock source line <- get clocking from provider  
> For CAS:  
> R1(config-controller)#ds0-group 1 timeslots 1-24 type fxo-loop-start <- Using FXO loopstart signaling  
> \-or- 
> For CCS:  
> R1(config-controller)#pri-group 1 timeslots 1-24 <- assumes signaling from CCS and ISDN switch-type

**Dial Peers  
**

- "Routing" for phone numbers
- Tells a voice gateway where to send calls based on dialed number
- Two types dial peers
    - POTS:  Traditional connections like T1 and analog phone lines
    - VOIP:  Connections to an IP address
- _show dial-peer voice summary_

> R1(config)#dial-peer voice 1101 pots  
> R1(config-dial-peer)#destination-pattern 1101  <- This number...  
> R1(config-dial-peer)#port 0/0/0  <-  ...is on this FXS port.
> 
> R1(config)#dial-peer voice 1102 pots  
> R1(config-dial-peer)#destination-pattern 1102  <- This number...  
> R1(config-dial-peer)#port 1/0:23  <-  ...is on this T1 PRI port.
> 
> R1(config)#dial-peer voice 1103 voip  
> R1(config-dial-peer)#destination-pattern 1103  <- This number...  
> R1(config-dial-peer)#session target ipv4:10.10.10.1  <- ...is at this IP address...  
> R1(config-dial-peer)#codec g711ulaw  <- ...and use this codec when you get there.
