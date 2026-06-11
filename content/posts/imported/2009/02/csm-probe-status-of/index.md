---
title: "CSM Probe Status of ???"
date: 2009-02-20
tags: 
  - "cisco"
  - "csm"
  - "probe"
---

I must be bored since I'm posting again.

A colleague asked me to change the failed value of a TCP probe today.  It was no big deal, but, when I looked to see the status of the change, I noticed interesting stati of the RIPs.

> ```
> switch#sh mod csm 7 probe name TCP80-PROBE detail
> probe           type    port  interval retries failed  open   receive
> ---------------------------------------------------------------------
> TCP80-PROBE  tcp     80    20       3       120     10
> Description: Quick fail recovery
> recover = 3
> real                  vserver         serverfarm      policy          status
> ------------------------------------------------------------------------------
> 192.168.1.45:80       VS01            FARM01        (default)       ???
> 192.168.1.44:80       VS01            FARM01        (default)       ???
> 192.168.1.43:80       VS01            FARM01        (default)       ???
> 192.168.1.42:80       VS01            FARM01        (default)       ???
> ```

It seems that when a change is made to a probe, the CSM discards the state of the probe and starts over.  If you catch it before the first probe is finished, you'll get a status of "???".  I'm just picturing the CSM saying "Uhh...I...don't...know".

It quickly cleared back to "OPERABLE", and my morning fun was gone. :(
