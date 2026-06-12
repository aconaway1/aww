---
title: "IIUC Notes - Inbound Dial Peer Matching"
date: 2011-01-19
categories: 
  - "voice"
tags: 
  - "640-460"
  - "dial"
  - "did"
  - "dtmf"
  - "exam"
  - "iiuc"
  - "inbound"
  - "ivr"
  - "matching"
  - "notes"
  - "peer"
  - "precedence"
---

More IIUC notes.  As always, feel free to correct as needed.

To match inbound calls to a dial peer, CME (and CUCM?) uses the following steps.

1. Match DNIS (the dialed number) with the _incoming called-address_ config in the dial peer
2. Match the ANI (the calling number or caller ID) with the _answer-address_ config in the dial peer
3. Match the ANI with the _destination-pattern_ config in the dial peer
4. Match an incoming POTS call to the _port_ config in the dial peer
5. Match dial peer 0

Matching dial peer 0 is bad, and it took me an inquiry on Twitter and a buddy to realize why.  Here are a few highlights as to why.  I believe the full scope of the badness of dial peer 0 is really beyond the IIUC exam.

1. It takes whatever codec is sent to it and can't be hard-coded.
2. DTMF is sent in the audio stream, so, if you wind up with a G.729 or other highly-compressed codec, you may have problems getting DTMF across successfully.
3. IP precedence values are stripped out of the packets, so it's just plain data now.
4. RSVP is disabled.
5. No application support, so you can't do IVR.  \[Will AA work?\]
6. No DID support.  This means the wife can't dial your desk with your published number.
