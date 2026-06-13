---
title: "CSCtd31622 - CSM, Cookies, and the year 2010"
date: 2010-01-08
categories: 
  - "csm"
tags: 
  - "2010"
  - "bug"
  - "cisco"
  - "cookie"
  - "csm"
---

It seems that we have another piece of evidence that Cisco doesn't like the CSM.  From what I'm able to creatively interpret, the software developers didn't think anyone would be running the CSM for very long, so they set a variable that expires CSM-inserted cookies at 01:01:50GMT on 1 January 2010[1](#1).  If you're using cookies to make connections sticky, that means you may see some unexpected results; this shouldn't affect the web servers' cookies.

The bug tookit lists 4.3(3) as the "first found in" version, but I'm fairly confident that it exists in every version before 4.3(3).  If you want to be sure you have the bug, you can run the _show mod csm # variable_ command and look for the **COOKIE\_INSERT\_EXPIRATION\_DATE** value.  It should look something like this.

> ```
> Switch#sh mod csm 2 variable
> 
> variable                        value
> ----------------------------------------------------------------
> ARP_INTERVAL                    300
> ARP_LEARNED_INTERVAL            14400
> ARP_GRATUITOUS_INTERVAL         15
> ARP_RATE                        10
> ARP_RETRIES                     3
> ARP_LEARN_MODE                  1
> ARP_REPLY_FOR_NO_INSERVICE_VIP  0
> ADVERTISE_RHI_FREQ              10
> AGGREGATE_BACKUP_SF_STATE_TO_VS 0
> COOKIE_INSERT_EXPIRATION_DATE   Fri, 1 Jan 2010 01:01:50 GMT
> ...
> ```

The "real fix" is to upgrade to 4.3(3.1) or 4.2(12.1).  Of course that means a reboot of the CSM and an outage and all that.  A workaround includes setting the **COOKIE\_INSERT\_EXPIRATION\_DATE** variable to some time in the future.  The bug text gives an example of some time in 2020, but any time in the distant future will do.  Assuming your CSM is in slot 2 and you've selected 1 Jan 2020 at 00:00:00 for your expiration, you would do this.

> ```
> Switch(config)#mod csm 2
> Switch(config-module-csm)#variable COOKIE_INSERT_EXPIRATION_DATE "Web, 1 Jan 2020 00:00:00 GMT"
> ```

That's much easier than upgrading the CSM, eh?  If you're still using your CSM by 2020, you can set it again if you want, but you'll be well past the EOL on that guy (4.1 goes EOL on 13 Oct 2012[2](#2))

Send any ~~[space shuttle launch tickets](http://www.kennedyspacecenter.com/event.aspx?id=e8813adf-2f3a-4a72-a919-0f196ddf167a&calendar=2010/2/7/ea22fa6a-c5ea-486e-98ea-c80a8cce4773 "Space Shuttle Launch Tickets")~~ questions my way.

Sources:

1: [CSCtd31622](http://tools.cisco.com/Support/BugToolKit/search/getBugDetails.do?method=fetchBugDetails&bugId=CSCtd31622 "Bug CSCtd31622") \* 2: [Cisco EOL/EOS for CSM 3.1.x and 4.1.x](http://www.cisco.com/en/US/prod/collateral/modules/ps2706/ps780/end_of_life_notice_c51-526249.html "CSM 3.1/4.1 EOL/EOS") \*

\*  May require CCO access
