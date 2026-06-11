---
title: "QoS Notes - IPP and DSCP Values"
date: 2013-07-30
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "dscp"
  - "ipp"
  - "mapping"
  - "qos"
  - "tos"
---

_This is a study note post, so please don't take this as written.  I'm not the authority on the subject, so please correct me if needed._

Back in the day, [somebody decided that we all needed to have a Type of Service (ToS) field in the header of IP packets](http://www.ietf.org/rfc/rfc791.txt).  Only God knows what this spawn of Satan wanted to do with it, but we're stuck with it on the CCIE R&S exams.

Can you tell I hate QoS?

The ToS field is eight bits long, but people really only used the first three bits that we call IP Precedence (IPP).  The rest of the bits were never really used, and, since it was the first three bits, the IPP value is different than the ToS value.  Let's look at a couple examples.

> ```
> 1 | 0 | 1 | 0 | 0 | 0 | 0 | 0 << 8 bits of ToS = 160
> 1 | 0 | 1 << 3 bits of IPP = 5
> 
> 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 << 8 bits of ToS = 64
> 0 | 0 | 1 << 3 bits of IPP = 1
> ```

Now let's talk about Differentiated Services Point Code (DSCP) values.  These are the first six bits of the ToS field of which we are all more familiar.  These things are collectively called the Assured Forward (AF) Per Hop Behavior (PHB).  Each PHB has Class Selector (CS) as the first three bits, which classifies the traffic type just like IPP did.  The next two bits, called the drop preference, are used to tell which packets should be dropped first if the need arises.  The sixth bit is always zero for some reason.  If you put these together, we get values like AF31, and AF12.  We can also just reference the Class Selector like CS1 and CS4.  Let's do some more binary, though.  Binary is always fun.

> ```
> 1 | 0 | 1 | 0 | 0 | 0 | 0 | 0 << 8 bits of ToS = 160
> 1 | 0 | 1 | 0 | 0 | 0 << 6 bits of DSCP = 40 = CS5
> 1 | 0 | 1 << 3 bits of IPP = 5
> 
> 0 | 1 | 1 | 0 | 1 | 0 | 0 | 0 << 8 bits of ToS =104
> 0 | 1 | 1 | 0 | 1 | 0 << 6 bits of DSCP = 26 = AF31
> 0 | 1 | 1 << 3 bits of IPP = 3
> 
> 1 | 0 | 1 | 1 | 1 | 0 | 0 | 0 << 8 bits of ToS =184
> 1 | 0 | 1 | 1 | 1 | 0 << 6 bits of DSCP = 46 = EF
> 1 | 0 | 1 << 3 bits of IPP = 5
> ```

I think I'm going to go back to studying EIGRP or STP...you know, something I actually care about learning.

Check this stuff out:

- [IP Precedence, TOS & DSCP](http://bogpeople.com/networking/dscp.shtml)
- [A nice matrix of all the IPP, CS, AF, DSCP value](http://www.netcontractor.pl/blog/wp-content/uploads/2010/06/QoS-Values-Calculator-v21.jpg) from [netcontractor.pl](http://www.netcontractor.pl/)

Send any CCIE Written exam vouchers questions to me.
