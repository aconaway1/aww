---
title: "ONT Notes - 802.1x and Encryption on LWAPs"
date: 2010-02-12
categories: 
  - "ccnp"
  - "ont"
tags: 
  - "642-845"
  - "802-1p"
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

- Traditional WLAN weaknesses
    - SSID for security
    - Vulnerable to rogue APs
    - MAC filtering for security
    - WEP
- WEP weaknesses
    - Disribution of static keys is not scalable
    - WEP keys can be cracked easily
    - Vulnerable to dictionary attacks
    - No protection against rogue APs
- Benefits of 802.1x
    - Centralized authentication through Radius via AAA
    - Mutual authentication between client and auth server
    - Can use multiple encryption algorithms (AES, WPA, TKIP, WEP)
    - Automatic dynamic WEP keys
    - Roaming
- Requirements of 802.1x
    - EAP-capable client (supplicant)
    - 802.1x-capable AP (authenticator)
    - EAP-capable auth server

Table 1. Characteristics of the EAP variants
| Feature | Cisco LEAP | EAP-FAST | EAP-TLS | PEAP-GTC | PEAP-MSCHAPv2 |
| --- | --- | --- | --- | --- | --- |
| User authentication DB | AD | AD, LDAP | OTP, LDAP, NDS, AD | OTP, LDAP, NDS, AD | AD |
| Requires server certs | No | No | Yes | Yes | Yes |
| Requires client certs | No | No | Yes | No | No |
| Single sign-on | Yes | Yes | Yes | No | Yes |
| Roaming | Yes | Yes | No | No | No |
| Works with WPA/WPA2 | Yes | Yes | Yes | Yes | Yes |

- WPA
    - Features
        - Authenticated key management - auths prior to key management
        - Unicast and broadcast key management - keys are distributed and stored on the client and the AP
        - TKIP and MIC
            - Temporal Key Integrity Protocol (TKIP) - per-packet keying
            - Message Integrity Checking (MIC) - integrity checking
        - Initialization vector (IV) expansion - from 24 bits to 48 bits
    - Shortcomings
        - Relies on RC4
        - Firmware support required in NICs, APs
        - Susceptible to DoS attacks
        - Dictionary attacks can discover PSKs
- WPA2
    - Features
        - 802.1x authentication or PSK
        - Key distribution and renewal
        - Proactive Key Caching (PKC) - allows roaming
        - IDS for rogue APs and attacks
    - Shortcomings
        - Supplicant must have WPA2-compliance firmware
        - AAA server must support EAP
        - WPA2 uses more CPU, so a hardware upgrade may be required
        - Older devices may not be upgradeable and must be replaced

Table 2. WPA/WPA2 Enterprise and Personal Modes
| Mode | WPA | WPA2 |
| --- | --- | --- |
| Enterprise | Auth: 802.1x/EAP Encryption: TKIP/MIC | Auth: 802.1x/EAP Encryption: AES-CCMP |
| Personal | Auth: PSK Encryption: TKIP/MIC | Auth: PSK Encryption: AES-CCMP |
