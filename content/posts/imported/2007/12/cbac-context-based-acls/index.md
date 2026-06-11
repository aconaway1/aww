---
title: "CBAC -- Context-based ACLs"
date: 2007-12-23
---

Let's set up a scenario. You have a single \[tag\]router\[/tag\] that terminates your T1 to the Internet for your company. You serve your own website and email, but you'd like to be as secure as possible and use ACLs on the router to lock stuff down. Your router has two interfaces -- S0/0 for the T1 and F0/0 for the LAN connectivity. Here's a simple configuration showing the interfaces and an ACL to let you host your stuff.

> ```
> access-list 101 remark *** Allow everyone to the website ***
> access-list 101 permit tcp any host 192.168.1.11 eq 80
> access-list 101 remark *** Allow everyone to the mail server ***
> access-list 101 permit tcp any host 192.168.1.12 eq 25
> !
> interface Serial0/0
>   ip address 1.1.1.2 255.255.255.252
>   ip access-group 101 in
> !
> interface FastEthernet0/0
>   ip address 192.168.1.1 255.255.255.0
> !
> ip route 0.0.0.0 0.0.0.0 1.1.1.1
> ```

So, that's all, right? Yeah, not really. Remember that router ACLs are based on _packets_, not _connections_, so every packet is compared to the \[tag\]ACL\[/tag\]. Let's say your workstation is at 192.168.1.201, and you try to go to aconaway.com to get some more great information (heh). What's going to happen?

First, your packet will hit F0/0 on the router, and, since there's no inbound ACL, the packet passes. The routing table send it across S0/0, which has no outbound ACL, so the packet keeps going and eventually lands on the web server. The return packet is generated and lands on S0/0 of the router. The router checks the ACL since we have one configured to look at packets inbound, but it doesn't match. The only thing that's allowed in is HTTP and SMTP to the appropriate hosts, so...\*plunk\*...right in the bit bucket.

The very-old-school way would be to allow all tcp established in (permit tcp any any established), but that's pretty much worthless on today's wonderfully-safe Internet. A better way would be to use \[tag\]CBAC\[/tag\] to dynamically allow access back in for your outbound connection.

The first thing you do is configure the _inspect_ statements to create an inspection object that can be applied to interfaces just like an ACL. There are about a dozen things to inspect, but let's keep it simple and only inspect TCP and UDP connections.

> ```
> ip inspect name INSPECT-OUTBOUND tcp
> ip inspect name INSPECT-OUTBOUND udp
> ```

In our simple case, we want to watch any traffic coming into F0/0 since this is the "trusted" interface, so we'll apply the \[tag\]inspect\[/tag\]ion object inbound on F0/0. If you have a bunch of interfaces, you may have several inspection objects and have to apply each to the outbound direction of specific interfaces, but we're just starting out, so here's what' we'll do in our setup.

> ```
> interface FastEthernet0/0
>   ip inspect INSPECT-OUTBOUND in
> ```

That's it! From now on, when you send TCP or UDP packets from your workstation to the Internet, the router will dynamically add entries in ACL 101 to allow the return traffic back in.

Running CBAC requires the firewall feature set of \[tag\]Cisco\[/tag\] \[tag\]IOS\[/tag\], so keep that in mind. [Here](http://www.cisco.com/en/US/products/ps6350/products_configuration_guide_chapter09186a00804a41c5.html "Cisco.com -- CBACs")'s Cisco's typical technical page on CBACs. Depending on your IOS version, you should be able to do a "show access-list 101" and see the dynamic rules. You can also do a "show inspect ?" to see a bunch of inspection stuff. Just check out the Cisco page for what to do.

\---

NOTE: CBAC is very CPU and memory intensive. Depending on your traffic load, you may have to upgrade your router to get it working without taking the router down.

NOTE: Using CBACs is safer to use than the very-old-school way I mentioned, but it is not a replacement for a firewall even if you're running the firewall feature set. The real security solution is to implement a firewall to filter traffic like this.
