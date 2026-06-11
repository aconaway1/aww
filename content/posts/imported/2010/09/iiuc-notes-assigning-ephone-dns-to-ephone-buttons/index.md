---
title: "IIUC Notes - Assigning Ephone-dns to Ephone Buttons"
date: 2010-09-23
categories: 
  - "voice"
tags: 
  - "640-460"
  - "ccna"
  - "cert"
  - "certification"
  - "cisco"
  - "digital"
  - "ethernet"
  - "exam"
  - "iiuc"
  - "notes"
  - "over"
  - "poe"
  - "power"
  - "test"
  - "voice"
---

These are some of my notes on my IIUC studies.  Since I am a novice as voice stuff, please let me know what I get wrong.

An **ephone** is a representation of a phone.  It's basically a structure of features that a phone will have. 

Configuration in CME:

> R1(config)#ephone 34  <-- This is just a tag and has nothing to do with an extension or phone  
> R1(config-ephone)#mac-address 1111.2222.3333    <-- Assigns this ephone to the phone with that MAC address

An **ephone-dn** is a directory number that can be assigned to one or more phone.  This is usually your extension and/or DID number.

Configuration in CME:

> R1(config)#ephone-dn 18   <-- Again, just a tag  
> R1(config-ephone-dn)#number 1000  <-- the extension

Ephone-dns (i.e., extensions) are assigned to ephones through the _button_ directive under the ephone setup.  You can have more than one assignment per button command.

Configuration in CME:

> R1(config)#ephone 34  
> R1(config-ephone)#button 1:18   <-- Assigns extension 1000 (through ephone-dn 18) to button 1

  
The colon (:) in the button line is a **separator** that means that this is a normal ring phone - when someone dials that extension, your phone rings and lights up.  There are other separator characters.

| Character | Function |
| --- | --- |
| : | Normal ring; the phone rings and lights up |
| b | Call waiting beep; the phone will light up, but there will be no ring.  If you're on the line, you'll hear a beep on the line. |
| f | Feature ring; a triple ring |
| m | Monitor mode; lets you see the status of the line without being able to use it.  Think of receptionists seeing if the boss is on the phone. |
| o | Overlay line without call waiting |
| c | Overlay line with call waiting |
| x | Overlay expansion with rollover |
| s | Silent; disable ringing and call waiting beep, but lights still flash |
| w | Watch mode; like monitor, except it monitors if any line on the phone being watched is active.  If I have 4 ephone-dns on my phone and am on line 2, if you're watching line 1 of my phone, you'll see it as active |

Configuration in CME:

> R1(config)#ephone 34  
> R1(config-ephone)#button 3m15  <-- Monitors ephone-dn on button 3  
> R1(config-ephone)#button 4s82  <-- Assigns ephone-dn 82 to button 4 but nothing will ring  
> R1(config-ephone)#button 5f31  <-- Assigns ephone-dn 31 to button 5 with a triple ring
