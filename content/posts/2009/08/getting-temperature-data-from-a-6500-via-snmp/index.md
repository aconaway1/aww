---
title: "Getting Temperature Data from a 6500 via SNMP"
date: 2009-08-19
tags: 
  - "cisco"
  - "ios"
  - "oid"
  - "sensor"
  - "snmp"
  - "temperature"
---

I apologize to my adoring fans (both of you) for the lack of posting.  I'm in the middle of moving, buying a new house, selling my current house, getting a mortgage, etc.  I've up until 11:30 nearly every night filling out forms and going through red tape.  Don't get me started on getting money from a 401k!  Anyway...

I got in this morning, and a coworker was telling me that the data center's HVAC was crippled due to an oil leak, and it was 90F in there.  D'oh!  It wasn't quite that high, but it was warm.  Luckily, all of our network gear is on the end of the rows with AC, so we're safe, but it got me thinking about monitoring temperature of our 6500s via SNMP.  I've done it via Cacti, but I never really looked how to do it manually.

I read [this article](http://www.cisco.com/en/US/tech/tk648/tk362/technologies_tech_note09186a0080094b9e.shtml "Cisco.com -- How To Get the Environment Temperature on a Catalyst 6500/6000 Using SNMP - Cisco Systems") on Cisco's site which made it clear as mud.  Basically, you have to query the _entPhysicalIndex_ OID branch to get an integer that represents the physical sensor, then you query the _entSensorValue_ to get the temperature.  I made the lazy choice to use grep on _entPhysicalDesc_ OID to find the module I wanted and use that to grep the _entSensorValue_ for the temp.

Let's say I'm looking for the outlet temperature (the temperature of the air on the way out) on module 3.  First, let's find out what th _entPhysicalIndex_ is by walking the _entPhysicalDesc_ OID and grepping out "module 3".

> ```
> [myserver]$ snmpwalk -c <COMM> -v 2c <HOST> 1.3.6.1.2.1.47.1.1.1.1.2 | grep "module 3"
> SNMPv2-SMI::mib-2.47.1.1.1.1.2.3001 = STRING: "module 3 power-output-fail Sensor"
> SNMPv2-SMI::mib-2.47.1.1.1.1.2.3002 = STRING: "module 3 insufficient cooling Sensor"
> SNMPv2-SMI::mib-2.47.1.1.1.1.2.3003 = STRING: "module 3 outlet temperature Sensor"
> SNMPv2-SMI::mib-2.47.1.1.1.1.2.3004 = STRING: "module 3 inlet temperature Sensor"
> SNMPv2-SMI::mib-2.47.1.1.1.1.2.3005 = STRING: "module 3 device-1 temperature Sensor"
> SNMPv2-SMI::mib-2.47.1.1.1.1.2.3006 = STRING: "module 3 device-2 temperature Sensor"
> ```

Since we're looking for the outlet temperature, we can see the index in question is 3003.  Now, we use that to query the _entSensorValue_.

> ```
> [myserver]$ snmpwalk -c <COMM> -v 2c <HOST> 1.3.6.1.4.1.9.9.91.1.1.1.1.4.3003
> SNMPv2-SMI::enterprises.9.9.91.1.1.1.1.4.3003 = INTEGER: 19
> ```

Ah...a cool 19C.

This is not a very graceful way to do it, but it gets the job done in a pinch.  I haven't tested it, but I would imagine this technique would work on 7600s as well.  I know it doesn't work on 2950s and the like.

Send any house keys questions my way.
