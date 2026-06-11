---
title: "ONT Notes - WLAN Management"
date: 2010-02-13
categories: 
  - "ccnp"
  - "ont"
tags: 
  - "642-845"
  - "802-1x"
  - "aes"
  - "campus"
  - "ccmp"
  - "ccnp"
  - "certification"
  - "cisco"
  - "eap"
  - "eap-fast"
  - "eap-tls"
  - "leap"
  - "lwap"
  - "lwapp"
  - "mic"
  - "ont"
  - "peap"
  - "peap-gtc"
  - "peap-mschapv2"
  - "psk"
  - "ssid"
  - "test"
  - "tkip"
  - "wifi"
  - "wireless"
---

**Elements of Cisco Unified Wireless Network**

- Client devices - Cisco compatible extensions on WLAN clients
- Mobility platform - allows configuration of LWAPs through WLCs
- Network unification - integration into the rest of the network with WLCs doing RF management, IPS, etc.
- World-class network management - centralized management through WCS
- Unified advanced services - supports advanced technologies and threat detection

**WLAN Implementation**

Autonomous and LWAP

| Category | Autonomous | LWAP |
| --- | --- | --- |
| Access Point | Autonomous APs | LWAPs |
| Control | Individual configurations | Configuration through WLCs |
| Dependency | Independent operations | Dependent on WLC |
| Management | CiscoWorks WLSE and WDS | WCS |
| Redundancy | Through APs | Through WLCs |

**Wireless LAN Services Engine (WLSE)**

- Part of CiscoWorks
- Manages autonomous APs
- Centralized configuration, firmware, and radio management
- Autoconfig of new APs
- Misconfiguration and rogue AP alerts
- Proactive monitoring of APs, bridges, and 802.1x servers
- Supports SSH, HTTP, CDP, SNMP for up to 2500 APs
- WLSE Express supports 100 devices in either automatic or manual setups

**Wireless Control System (WCS)**

- Supports 50 WLCs and 1500 APs
- Three versions
    - Base - can determine with which APs a devices in associated
    - Location - Base plus RF fingerprinting
    - Location + 2700 Series Wireless Location Appliance - Tracks devices in real time and stores historical location data
