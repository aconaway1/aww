---
title: "IIUC Notes - Powering Cisco Phones"
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

Feel free to correct anything that is wrong or incomplete.

- Power over Ethernet (PoE)
    - Can provide power to a Cisco phone, access point, security camera, etc., through the network cabling, eliminating the need to plug the phone into the wall for power.
    - Generic term for providing power on the Ethernet cable
    - Provides centralized power that can be put on a UPS
    - Allows devices to be located away from power outlets
    - Removes cabling clutter at the user's desk
    - Can be provided through PoE-enabled switches, power panels or inline couplers (power injectors)
    - Oversubscription is common
        - If every device on a switch asks for full power, the switch may not be able to handle the load.
    - Of course, devices can be powered with a power brick at the desk
- 802.3af  
    
    - IEEE standard for PoE from 2003
    - Defines power classes so different devices can ask for different power levels
        - Class 0:  15.4W allocated
            - Used for el cheapo devices that just want power
        - Class 1:  4.0W
        - Class 2:  7.0W
        - Class 3:  15.4W
    - Uses all 4 pairs of wire, so works on gig links
    - Power procedure
    
    > 1. Small DC current is applied to the line
    > 2. If an 802.3af device is attached, it runs the current through a resistor
    > 3. The resistance is detected by the switch which can determine the class of power
    > 4. Power is applied to the device
    

- Cisco Inline Power
    
    - Cisco's version of PoE created in 2000 (before 802.3af)
    - Each device tells the switch what its power needs are
    - Power procedure
    
    > 1. PoE device connected to the switch
    > 2. Switch sends Fast Link Pulse (FLP)
    > 3. If FLP is received back, 6.3W of power are applied
    > 4. Device boots off of 6.3W and tells the switch what its real power requirements are via CDP
