---
title: "IIUC Notes - Getting Phones on the LAN"
date: 2010-09-30
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
  - "dhcp"
  - "digital"
  - "dtp"
  - "ethernet"
  - "exam"
  - "iiuc"
  - "notes"
  - "ntp"
  - "over"
  - "poe"
  - "power"
  - "router"
  - "switch"
  - "switchport"
  - "test"
  - "trunk"
  - "voice"
---

More study notes.  Correct if wrong, though I hope I get some of it right since I already since I'm an R&S guy.  :$

**Switchport Configuration  
**

- **switchport mode access**:  This config makes the port an access port that carries the primary and voice VLAN traffic
- **switchport mode trunk**:  This config akes the port a trunk unconditionally, but it will still send DTP messages
- **switchport nonegotiate**:  This config keeps the port from sending DTP messages.
- **switchport mode dynamic auto**:  If the port receives DTP messages, it will become a trunk.  If not, it will be an access port.
- **switchport mode dynamic desirable**:  The port actively sends DTP messages trying to become a trunk.  This is the default configuration on a Cisco switch.

**Cisco IP Phone Boot Process**

1. Phone connects to an Ethernet switch and gets power if needed
2. Switch tells the phone the correct voice VLAN through CDP
3. Phone sends DHCP request for its voice VLAN
4. DHCP offer includes the TFTP server from which to download the config
5. Phone downloads the config from the TFTP server
6. Phone contacts the call processing server as dictated in the config file

**DHCP Settings on a Cisco Router or L3 Switch**

> R1(config)#ip dhcp pool MYPOOL  
> R1(dhcp-config)#network 192.168.0.0 255.255.255.0  
> R1(dhcp-config)#default-router 192.168.0.1  
> R1(dhcp-config)#dns-server 192.168.0.10  
> R1(dhcp-config)#option 150 ip 192.168.0.20  <-- Tells the phone to download the config from this TFTP server  
> R1(dhcp-config)#exit  
> R1(config)#ip dhcp excluded-address 192.168.0.1 192.168.0.100  <-- Don't use these IPs when handing out DHCP

**NTP**

Why should you use NTP for a CME setup?

- Phones display correct time
- Voicemails have the correct time
- CDRs are timestamped accurately
- Router logs are timestamped accurately
- Time-based access worked predictably

> R1(config)#ntp server 1.1.1.1  
> R1(config)#clock timezone MYTZ -5  <-- Sets the timezone to a zone called MYTZ that's 5 hours behind UTC
