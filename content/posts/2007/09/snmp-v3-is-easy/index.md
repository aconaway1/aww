---
title: "SNMP v3 is Easy!"
date: 2007-09-16
tags: 
  - "cisco"
  - "security"
  - "snmp"
---

I finally got around to looking into \[tag\]SNMP\[/tag\] v3 and was shocked at how easy it actually is. When I first looked up info on it so many moons ago, I saw table after tables of views and privilege levels and thought I would have to put in a billion hours getting it customized. I settled down and went through some Google results and found a [blog post by](http://taosecurity.blogspot.com/2006/08/snmp-v3-on-cisco-switch.html "Taosecurity -- SNP v3 on Cisco Switch") [Richard Bejtlich](http://taosecurity.blogspot.com/2006/08/snmp-v3-on-cisco-switch.html "Taosecurity -- SNP v3 on Cisco Switch") that shows the simplest of configurations. Works like a champ!

SNMP v3 gives you a few things that you'll like. First of all, the transactions can be encrypted, so you don't have to worry about people sniffing your traffic on the evil Internet. You also get username and password combos for authentication. Older versions use the community, which serves as a password. My buddy has a story about using the default communities on his cable modem to find upstream hosts, the using the same on his ISP's network gear. That's pretty lackluster security that could be hardened with v3.

This version of SNMP is very complicated, and the key to starting off is to forget about the views. Views make SNMP v3 ultra-powerful, but you don't need them in a simple setup. Obviously, we all evolve, and you'll probably use it later, but there's no need to worry yourself to start.

Let's do this, then. First, you need to define a v3 group and user/pass. Just something simple will do. Let's choose a group name of "snmp-group" and a user of "snmp-user" with the password "user-pass". Now all we have to do is configure the thing. I'm assuming you're setting it up on a Cisco IOS device of some kind, but SNMP v3 has been available for quite a while on a lot of platforms and OSes.

> snmp-server group snmp-group v3 auth snmp-server user snmp-user snmp-group v3 auth md5 user-pass priv des56 encryption-key

Note that we're using an MD5 hash for the password right now. If you have the right code, you can do DES56 encryption, but every version of \[tag\]IOS\[/tag\] that supports SNMP v3 also supports MD5.

That's it, actually. By default, you have read-only access to the whole MIB tree. If you want to set up more granular access, you can look at views, but that's beyond the scope here. I'm sure I'll have an article about that later.

Let's test our new setup with an snmpwalk. There's some new flags you need to pass to use v3, but it's pretty straightforward. "a" is for encyrption; "u" is for user; "A" is for password; "l" is for the authorization level (another advanced SNMP v3 topic).

> snmpwalk -v 3 -a MD5 -u snmp-user -A user-pass -X encryption-key -l authPriv hostname .1

You should see a whole list of stuff scrolling by. If you don't, check the username and password and try again. Let me know if you need any help getting it running.

---

Here's my usual note. If you still have your "snmp-server community" line configured, then v1 or v2c is still available. If you're converting completely to v3, then just remove the community line. This will disable the old school versions and let you enjoy your encrypted goodness.

Also note that I'm not sure if Cacti or Nagios actually supports SNMP v3 encryption.  You're on your own with that one.  If you decide to not use encryption, you can just take out the "priv" section of the user configuration to go with authentication only.
