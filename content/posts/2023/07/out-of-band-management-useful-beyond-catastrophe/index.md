---
title: "Out-of-band Management - Useful Beyond Catastrophe"
date: 2023-07-13
categories: 
  - "cisco"
tags: 
  - "everyday"
  - "management"
  - "neteng"
  - "oob"
  - "opengear"
---

I was lucky enough to participate in Tech Field Day Extra at Cisco Live a couple weeks months ago. This event brings independent thought leaders together with a number of IT product vendors that were at Cisco Live to share information and opinions. I was not paid to attend, but the organizers did provide some meals while I was there. There is no expectation of providing any content, so the fact that I’m mentioning it says something. It was a great event and worth a few hours to [check out the videos](https://www.youtube.com/playlist?list=PLinuRwpnsHafmM4n1UieIWxQLz8omLCxK). Thanks to [Gestalt IT](https://gestaltit.com/) for getting me involved. OpenGear was there, and it was good to see some new faces and hear some new ideas.

For those that live under a rock don't know, [OpenGear](https://opengear.com/) traditionally provides out-of-band (OOB) management solutions via hardware appliances that run independently of your network. They, like other vendors in that space, can connect to the cellular data network of choice and provide access to your gear when something fails (what OpenGear calls "worst day"). Over 99.9% of the time, though, you would never use your OOB devices. They're just going to sit there doing nothing until that day that something fails catastrophically. No one likes idle resources, - especially those that are paying for it - so how else can you use that thing that has 84729 cables coming out of it?

You can use your OOB gear for provisioning using [ZTP](https://opengear.com/solution/zero-touch-provisioning) or the like. This is what OpenGear was calling "first day" operations. When a new device is connected to the OOB network, that device can be upgraded and configured through the magic of DHCP options. OS image upgrade and config all downloaded automatically. Sounds like a phone, eh?

This will be great for a new network, but this first day operation doesn't really need to be on the first day of the greenfield. You can use it when you add new switches to you fabric or new edge routers to the Internet. You can even use it to push a config when a device needs to be replaced.

_Related: I had a project several years ago that implemented white box switching in an existing data center, and I wound up writing some Ansible playbooks to push configurations to everything. It started off as a way to do VLAN changes and whatnot, but I realized I could use some the playbooks a la ZTP to have the switches boot up and run that same playbook to do the initial config. Man, I wish I could have blogged about that project._

That's some progress, but we still have devices that sit there doing nothing for 99% of their lives. Can we use our OOB networks to regularly configure or monitor our devices? That is, when we make changes to the configs or get stats for [Grafana](https://grafana.com/), can we use the OOB network? Well, sure.

In the simplest form, you can just connect to the console of your devices via the OOB network and make your changes from there. Easy. No one likes the old, clunky interfaces of their OOB gear, though, so let's try something else. How about a SSH port-forwarding session where you wind up SSHing into the console of your gear? Let's think fancier.

Another way to use that OOB network on a more-daily basis is to connect management interfaces of everything up to the OOB devices. [Some have Ethernet switching built in](https://opengear.com/products/om2200-operations-manager/) for such things, so plug your stuff directly into those ports. Your network gear, iDRAC/ILO/CIMC/IPMI, PDUs, UPSes...all that stuff. That sets up your processes for using the OOB network instead of mixing traffic with production.

I mentioned that I had a project that used Ansible playbooks to do config updates, and you can do the same here. I'm sure there's [some Ansible magic to proxy your connections](https://www.jeffgeerling.com/blog/2022/using-ansible-playbook-ssh-bastion-jump-host) through an OOB device, but [OpenGear](https://galaxy.ansible.com/opengear) (and [ZPE](https://github.com/ZPESystems)) has Ansible collections that you can use to configure your stuff through those OOB devices. And I won't even go into the fact that you can run containers on a lot of the OOB gear; that's a whole blog post in itself (that I'm probably not qualified to talk about)!

I need to mention some generic benefits of an OOB network before we wrap up. With true OOB connectivity, any changes you make to a device don't affect your connectivity to the device your destroyed. If you accidentally did a "wri erase; reboot" instead of setting the NTP server, you still have connectivity to that device, which means you can roll back or fix the issues remotely. It also means that you can do things like check the status of your BGP neighbors after your intern swears that only the SNMP community was updated but an entire site went offline.

OOB is worth your time. It's always been worth the investment for catastrophes, but, with some added functionality, it might be worth the investment for day-to-day operations. A couple companies I know are shrinking offices thanks to WFH benefits, and there's a concern that that one person who always helps you with problems at the site (we all know the one!) doesn't come to the office any more. How are you going to troubleshoot those problems now? Well, an OOB device won't plug in a cable for you, but it will surely help.

Send any LTE data plans questions to me.

#OOB #Management #OpenGear #EveryDay #NetEng
