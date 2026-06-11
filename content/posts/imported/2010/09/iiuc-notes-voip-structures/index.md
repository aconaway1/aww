---
title: "IIUC Notes - VoIP Structures"
date: 2010-09-21
categories: 
  - "cisco"
  - "voice"
tags: 
  - "640-460"
  - "ccna"
  - "cert"
  - "certification"
  - "cisco"
  - "digital"
  - "exam"
  - "iiuc"
  - "notes"
  - "test"
  - "voice"
---

Feel free to correct.  No need to sugar-coat it; I'm pretty new at this stuff.  :)

- Advantages of VoIP
    - Reduces costs of communications:  Eliminates/reduces long distance and international call tolls
    - Reduces costs of cabling:  No need for second network of phone lines
    - Integrates all voice into one large network:  All your remote offices can be implemented/maintained/controlled centrally
    - Provides mobility:  Moves, adds, and changes (MACs) are (nearly) eliminated since your phone is just a network node
    - Allows use of IP Softphones
    - Unifies emails, voice mails, and faxes:  All these can be treated as a single box for user messages
    - Increases productivity:  Ringing multiple devices at the same time eliminates phone tag.   <--- pushing it, eh?
    - Enhances communications:  Applications can be launched/updated from a voice call through application servers
    - Provides open, compatible standards:  You can connect different vendor devices into the same VoIP network.   <--- I've never seen that happen

- Cisco VoIP Structure
    - Infrastructure:  Switches, routers, firewalls, etc.
        - QoS!
    - Call processing:  Call signaling, routing, etc.
    - Applications:  Additional functionality like IM support and unified messaging
    - Endpoints:  Phones

- Cisco Call Processing
    - Unified Communications 500 (UC500): Standalone device with switch, router, firewall, voice processing, voice mail all built in
    - Communications Manager Express (CME):  Voice capabilities contained in ISR router
    - Communications Manager Business Edition:  Server solution with most voice capabilities integrated
    - Communications Manager (CM): Full server-cluster solution to support many thousands of phones

- Cisco Applications
    - Interactive Voice Response (IVR):  Those troublesome menus where you say your account number but it never understands you
    - Auto attendant:  Interactive interface where users direct themselves to the correct person/group/team/department by using touch tones.
    - Cisco Unified Contact Center:  Provides IVR, auto attendant, automatic call distribution (ACD), computer telephony integration (CTI), chat/web/email integration
    - Cisco Unity Express:  Linux-based appliance in a router for limited voice mail, IVR, and auto attendant
    - Cisco Unity Connection:  Server-based solution for more robust VM, IVR, and auto attendant
    - Cisco Unity:  Fully-integrated solution running on server clusters

- Phones
    - Entry-level
        - 3911
            - Inline power
            - Fixed buttons
            - Half-duplex speakerphone
        - 7906G/7911G
            - Inline power
            - Onscreen soft keys
            - Basic XML support
            - 7911G has built-in switch
        - 7931G
            - Inline power
            - Onscreen soft keys
            - Basic XML support
            - Built-in switch
    - Business-class
        - 7940G
            - Built-in switch
            - Inline power
            - Broader XML support
            - Onscreen soft keys
            - Full-duplex speaker
            - Headset support
        - 7941G = 7940G + better display with backlight
        - 7941G-GE = 7941G + 10/100/1000 switch
        - 7942G = 7941G + high-fidelity audio and Internet Low Bitrate Codec (ILBC)\]
        - 7945G = 7941G-GE + 16-bit color display
        - 794X phones support 2 lines; the same 796X phones support 6 lines.
    - Touchscreen phones
        - 7970G:  7940G + touchscreen
        - 7971G-GE: 7941G-GE + touchscreen
        - 7975G: 7945G + touchscreen + 5" display
    - Specialty phones
        - 7985G:  Video phone
        - 7921G:  Wireless VoIP phone
        - 7937G:  Conference station
        - ATA 186/188:  Converts analog phones to VoIP
        - Cisco IP Communicator:  Softphone
        - VT Advantage:  Integrates webcam and computer with phone
        - 7914/7915/7916:  Expansion modules for 796X and 797X phones
