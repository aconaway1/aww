---
title: "ONT Notes - VOIP Networks"
date: 2010-01-10
tags: 
  - "642-845"
  - "analog"
  - "ccnp"
  - "certification"
  - "cisco"
  - "conversion"
  - "digital"
  - "ont"
  - "test"
  - "voice"
  - "voip"
---

Here are some of the notes I've been taking while reading over the ONT book. I hope it benefits somebody.  Feel free to correct any stupid mistakes as a paraphrase to avoid a lawsuit.

There's way too much info here.  I'll refine the process a little better for the next topics.

**Benefits of Packet Telephony Networks**

- More efficient use of bandwidth and equipment - Packet telephony networks don't dedicate channels or a static bandwidth to a call; it's just another network application.
- Consolidate network expense - The common infrastructure (IP-based networks) keeps you from having to support another distinct network for voice like in traditional PBX implementations.
- Improved employee productivity - The phone can be used for more than just phone calls by utilizing the XML interface to run applications or provide content from the network.
- Access to new communications devices - IP phones can communicate with computers, network gear, PDAs, etc., and not just the PBX.

**Packet Telephony Components**

- Phones - These include analog phone, digital phones, IP phones, softphones, etc.
- Gateways - These devices connect the different devices that cannot access the IP network.  For example, making a 911 call from your IP phone requires a gateway that switches and converts your VOIP conversation to the PSTN.
- Gatekeepers - These are devices that handle call routing (resolving an IP to an extension/phone number) and call admission control (CAC, grants permission to make the call).
- Multipoint control units (MCUs) - These are conference bridges that connect a bunch of streams together and present it to all participants.  Some can do video as well.
- Call agents - These are devices used in a centralized model that handle the call routing, address translation, call setup, call maintenance, and call termination.
- Application and database servers - These provide required and optional services to the packet telephony network and include TFTP servers for configuration and OS download and XML servers for application use.
- Digital signal processors (DSPs) - These guys converts signals from one form to another.  They convert analog to digital signals, digital to packetized data in the form of a codec, from codec to codec, etc.

**Analog Interfaces**

- Foreign Exchange Office (FXO) - These are interfaces that expect to connect to a CO or equivalent.  You connect these to your wall jack to get access to the PSTN.
- Foreign Exchange Station (FXS) - You connect your analog devices (phones, modems, faxes, etc.) to these guys to get dial tone.
- Ear and Mouth (E&M) - These are the old-school way to connect PBXes together.

**Digital Interfaces**

- Basic Rate ISDN (BRI) - These give you 2 64kbps channels (bearer channels) to run voice over.  It also includes a 16kbps D (delta) channel with 48kbps of framing overhead to give you 192kbps.

- T1 (North America) - This is a channelized T1 or a Primary Rate ISDN (PRI).
    - Common Channel Signaling (CCS) - The D channel is dedicated to signaling, giving you 23 64kbps channels.
    - Channel Associated Signaling (CAS)  - There is no D channel, but every bearer channel dedicates a few data bits for its own signaling.

- - E1 (North America) - This is a channelized E1 or a Primary Rate ISDN (PRI).
        - Common Channel Signaling (CCS) - The D channel is dedicated to signaling, giving you 30 64kbps channels.
        - Channel Associated Signaling (CAS)  - There is still a dedicated D channel, so you still have 30 64kbps channels to use.

**VOIP Signaling**

- H323. - ITU Standard that uses a whole mess of RFCs; distributed model
- Media Gateway Control Protocol (MGCP) - IETF RFC 3435; centralized model
- Session Initiation Protocol (SIP) - IETF standard; distributed model

**Phone Call Stages**

- Call setup - connects the call between the endpoints
    - Call routing - figures out where the call is going
    - CAC (optional) - Do you have enough resources (i.e., an available channel or bandwidth) to make the call?
    - Call negotiation - negotiates the source and destination IPs, source and destination UDP ports, and codec.
- Call maintenance - collects call statistics for on-demand or historical use
- Call teardown - hanging up and terminating the connection

**Digitizing Analog Signals**

- Sampling - Periodic capturing and recording of voice resulting in a pulse amplitude modulation (PAM) signal
- Quantization - Assigning numerical values to the PAM signal
- Encoding - Converting the quantization to binary
- Compression (optional) - compressing the binary stream
- Pulse code modulation (PCM) converts analog to digital, but it doesn't use compression.  It takes 8000 samples per second and converts each sample to an 8-bit number, giving 64kbps of capacity.

**Digital to Analog**

- Decompression (optional)
- Decoding and filtering - binary is converted back to a PAM signal; filtering removes any noise from the conversion
- Reconstructing the analog signal

**The Nyquist Theorem**

- The number of samples required to accurately encode (and decode) a signal is twice the highest frequency of the signal.
- Since telephone lines can only transmit up to 3400 Hz (4000 Hz for simplicity), the sample rate should be 8000 samples/second.

**Measuring Compression Qualities**

- Mean opinion score (MOS) - ITU standard technique for measuring quality of codec; subjective score from 1 to 5
- Perceptual speech quality measurement (PSQM) - Another ITU standard technique for measuring quality of codec; test equipment score from 0. to 6.5
- Perceptual analysis measurement system (PAMS) - Developed by BT; predictive system
- Perceptual evaluation of speech quality (PESQ) - Another ITU standard; combines PSQM and PAMS; objective measurement of factors including subjective values

**Digital Signal Processors (DSPs)**

- Provide 3 major services - voice termination, transcoding, conferencing
- Also performs compression (codec), echo cancellation, voice activity detection (VAD), comfort noise generation (CNG), and jitter handling
- Conferencing among participants with the same codec is called a single-mode conference.
- Conferencing among participants with different codecs is called a mixed-mode conference.

**Protocols**

- VOIP calls run over Real Time Protocol (RTP).
- RTP provides sequence reordering, time-stamping, and multiplexing
- Rides on UDP ports 16384-32767
- Voice does not need the reliability (retransmission) of TCP since retransmitted data is no longer useful (I already said that).
- VOIP packets headers:
    - IP - 20 bytes
    - UDP - 8 bytes
    - RTP - 12 bytes
    - L2 headers vary depending on technology (Ethernet = 12 bytes, MPLS, etc.)
- 2 10-ms packages are usually in one packet (20ms of voice)
- G.711 (64kbps) produces 160 bytes from 20 ms of voice.
- G.729 (8kbps) produces 20 bytes from 20 ms of voice.

**cRTP**

- Compressed RTP (cRTP) reduces the headers
- After the first packet lands, the IP, UDP, and RTP headers won't change, so why send them again?
- The headers are reduced to a hash.
- cRTP reduces the headers to 4 bytes with a UDP checksum and 2 bytes without a UDP checksum.
- Slow links only
- Processing overhead
- Finite delay in packetization

**Packet Size Effect on Bandwidth**

- The size of a voice frame depends on:
    - Packet rate and packetization size - rate is inversely proporational to size
    - IP overhead - RTP, UDP, IP, cRTP overhead
    - L2 overhead -
    - Tunneling overhead - IPSec, GRP, MPLS, etc.
- Codecs have different bandwidth
    - G.711 (PCM) - 8000 samples per second @ 8 bits per sample = 64 kbps
    - G.726 (Adaptive Differencial PCM - ADPCM) - Variable bit rate of 32 kbps, 24 kbps, or 16 kbps
    - G.722 (Wideband Speech Encoding) - 2 subbands using modified ADPCM of 64 kpbs, 56kbps, or 48 kbps
    - G.728
    - G.729 - 10 samples per 10-bit code = 8 kbps

**Calculating Total Bandwidth**

- Step 1 - Determine codec and packetization period: What does the codec require in bandwidth?  How many samples per packet (usually 2)?
- Step 2 - Determine link-specific overhead:  Encapsulation?  cRTP?
- Step 3 - Calculate packetization size:  Size of voice payload; codec bandwidth \* packetization period / 8 = voice payload in bytes
- Step 4 - Calculate total frame size: IP + UDP + RTP + Tunneling + data link + packetization size
- Step 5 - Calculate packet rate: 1 / packetization period (ex., 20ms packetization period is 1/0.020 = 50 packets per second)
- Step 6 - Calculate total bandwidth:  Total frame size \* packet rate

**VAD and Bandwidth**

- Common for 1/3 of conversation to be silence
- VAD bandwidth savings depends on:
    - Type of audio: regular phone call (two-way), conf call (one-way), music on hold (MOH)
    - Background noise: noise may be detected as voice
    - Other factors:  language, culture may influence amount of silence

**Enterprise VOIP Implementations**

- Consists of gateways, gatekeepers, Cisco Unified CallManagers (CCM), Cisco IP Phones
- Routers can provide the voice gateway function by connecting the IP network to the WAN (and other gateways), PSTN, PBXes, etc.
- Survivable Remote Site Telephony (SRST) allows local calling and use of PSTN while services are down

**Functions of CCM**

- Call processing - routing, signaling, accounting
- Dial plan administration -  call routing
- Signaling and device control - configuration and instruction in case of events
- Phone feature administration - button programming, profiles, etc.
- Directory and XML
- API for interface - allows custom programming for IP phones

**Enterprise Deployment Models**

- Single-site: You have one site, and everything is there.
- Multisite with centralized call processing: You have multiple sites, but the main site has the CCM cluster.
- Multisite with distributed call processing: You have multiple sites, and each site has its own CCM cluster.
- Clustering over WAN: You have multiple sites, and each site has a part of one big CCM cluster.

**IOS Voice Commands**

> ```
> ----- R1 -----
> ! FXS on 1/1/2
> Dial-peer voice 1 POTS
>  destination-pattern 120
>  port 1/1/2
> 
> ! Extension 230 is on R2
> Dial-peer voice 2 R2
>  destination-pattern 230
>  session target ipv4:10.1.1.2
> 
> ----- R2 -----
> ! FXS on 2/2/1
> Dial-peer voice 1 POTS
>  destination-pattern 230
>  port 2/2/1
> 
> ! Extension
> Dial-peer voice 2 R2
>  destination-pattern 120
>  session target ipv4:10.1.1.1
> ```

**Call Admission Control (CAC)**

- QoS can guarantee bandwidth but can only reserve so much (say, for 2 simultaneous calls).
- CAC make sure that resources are available (denies a new call if 2 calls are already placed).
- Dropped packets affect every call - not just the new ones

\-----

Additional Reading

1. [H.323 Sources on Wikipedia](http://en.wikipedia.org/wiki/H.323#References "Wikipedia's Sources for H.323")
2. [MGCP - RFC 3435](http://tools.ietf.org/html/rfc3435 "IETF RFC 3435")
3. [SIP - RFC 3261](http://tools.ietf.org/html/rfc3261 "IETF RFC 3261")
4. [Nyquist Theorem on Wikipedia](http://en.wikipedia.org/wiki/Nyquist%E2%80%93Shannon_sampling_theorem "Nyquist Theorem")
5. [MPLS on Wikipedia](http://en.wikipedia.org/wiki/Multiprotocol_Label_Switching#How_MPLS_works "MPLS on Wikipedia")
