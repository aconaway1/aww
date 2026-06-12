---
title: "Services on an IOS Device"
date: 2007-12-11
---

Have you even looked at the first few lines of your \[tag\]Cisco\[/tag\] \[tag\]switch\[/tag\] or \[tag\]router\[/tag\] \[tag\]config\[/tag\] and wondered what those "service" lines were? Yeah, me, too, so I did a little research through the web and through some audit tools to figure a few out. Here's some to pay attention to the next time you're configuring that guy. As always, _?_ is your friend.

- _service timestamps \[ \[tag\]debug\[/tag\] | \[tag\]log\[/tag\] \]_. This line deals with timestamps. Wow...what an eye-opener. You use this service to put timestamps in the debug or logging output instead of the standard uptime. You've seen it, haven't you? The log says "5w8d: A HACKER GOT IN", but there's no way to know exactly when that really happened, eh? I actually set itmy gear up to record all the way down to the millisecond level.
- _service \[tag\]password\[/tag\]-encryption_. This guy encrypts your system passwords when your list the config. This is default nowadays (I think), but make sure it's there so you won't accidentally send someone your single enable password that you use as your user and enable mode passwords on all your devices. This isn't a good encryption cypher at all, though, so don't rely on it for password security.
- _service \[tag\]compress\[/tag\]-config_. If you have a huge config, you may run out of room in \[tag\]NVRAM\[/tag\] for the config. It's not a good thing to see that your config buffer is full, and the config can't be saved. Enable this guy to compress the config down so it can fit.
- _service \[ \[tag\]tcp-small-service\[/tag\] | \[tag\]udp-small-services\[/tag\] \]_. Auditors love it when you have a "no" in front of these. This turns off the antiquated services like chargen, finger, and echo. Luckily, the default is to turn these off, but always make sure the intern didn't enable them.
- _service \[tag\]prompt\[/tag\] config_. This is a good one to play tricks on your buddies. Doing a "no service prompt config" actually turns off the prompt on the box. You don't see anything, but the box still takes commands. Funny when it's not done to you.
- _service \[tag\]dhcp\[/tag\]_. Pretty easy -- it turns on the DHCP server on the device. Of course you'll have to configure the details, but it might be convenient.

That's all.  Carry on.
