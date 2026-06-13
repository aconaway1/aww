---
title: "Overlay Management"
date: 2023-07-12
categories: 
  - "dhcp"
  - "dns"
  - "ipam"
  - "tools"
tags: 
  - "ddi"
  - "dhcp"
  - "dns"
  - "ipam"
  - "management"
  - "menandmice"
  - "neteng"
  - "overlay"
---

I was lucky enough to participate in Tech Field Day 27 a couple weeks months ago. This event brings independent thought leaders together with a number of IT product vendors to share information and opinions. I was not paid to attend, but the organizers did provide travel, room, and meals while I was there. There is no expectation of providing any content, so the fact that I'm mentioning it says something. It was a great event and worth a few hours to [check out the videos](https://www.youtube.com/playlist?list=PLinuRwpnsHafAJ1Gc3Bt8B7GEy_A69Bb9). Thanks to [Gestalt IT](https://gestaltit.com/) for getting me involved.

One of the companies that presented was Men & Mice. They have a product called Micetro (great name!) that manages your DHCP, DNS, and IPAM for you. The product doesn't provide DHCP, DNS, or IPAM services; it manages it. That is, it configures and monitors those services for you, whether it's running on your local network, in cloud, remotely, whatever. This is what they call overlay management.

What does that really mean, though? Since overlay management doesn't provide endpoint services, your endpoints don't see anything different. Your DHCP servers stays the same. DNS servers stays the same. IPAM stays the same. The only thing that's different is the way changes to those systems are made. For example, instead of touching a DNS server (or more than one) to add an A record to a domain, you click around in Micetro and, through some fancy magic, the entry appears in the correct zones on the correct servers.

So, let's summarize the benefits of using a management overlay like Micetro.

1. Systematic changes : All changes are made from a central point using standard (I hate that word sometime) techniques to make sure it's done the same way everywhere.

3. Deferred service expertise : You don't need to be an expert on each server platform - only on the management system.

5. Source of Truth : Because it controls each system, the overlay knows the status of everything and can be used by other tools (or just people clicking) to know what the system looks like right now.

7. Easy Uninstall : Since you're not changing the server platforms themselves, you can just stop using the overlay and go back to updating things directly.

9. Scalability : If you add 8472928 more servers to the stack, you still only have one update to make.

Do you use a management overlay? Probably. Do those Python scripts your use count? How about those Ansible playbooks? vCenter? NDS Manager (bonus points for knowing what that is!)?

Send any ~~overdue library books~~ questions my way.

#overlay #management #dhcp #dns #ipam #NetEng
