---
title: "Free and Awesome Network Tools"
date: 2007-11-18
tags: 
  - "cisco"
  - "linux"
  - "snmp"
  - "tools"
---

We all have limited budgets these days. Long gone are the days of unlimited resources and uncontrollable expansion of the network, so it's important that any network dude or dudette pay attention to the open-source world. Below is a list of stuff I use at the office and at home to monitor, trend, and alert the network. All this stuff is free and runs on Linux to save even more cash.

- [Cacti](http://cacti.net/ "Cacti -- Home Page") \-- This is a system for trending pretty much anything. If it has an SNMP value, Cacti can trend it. It's also really flexible, allowing multiple displays of data and even a mechanism to get values from scripts you write. At the office, we use it to monitor utilization of the circuit and Ethernet ports, CPU and memory of the gear, and the number of connections on the load-balancer. At home, I use it to watch utilization and track the number of connections to the wireless networks.
- [Nagios](http://www.nagios.org/ "Nagios -- Home Page") \-- This is a monitoring and alerting system for all sorts of stuff. It watches hosts and applications for availability and response time, then alerts based on threshold. This is one of the most complicated apps to configure, but, once it's up, it rocks. I use it at home to monitor all the network gear and systems for response times. I also use it to monitor the web servers and restart them if they're down.
- [Apache](http://www.apache.org/ "Apache -- Home Page") \-- You know what Apache is. You use it already. About 71% of webservers on the Internet are Apache.
- [Squid](http://www.squid-cache.org/ "Squid -- Home Page") \-- A caching proxy server by the same guys who do Apache. It can be configured for both inbound and outbound application acceleration. It's great to put in front of a CMS like Drupal or Joomla. It has a mess of built-in functions that can look for bad requests, do redirects, or completely rewrite requests. At work, it fronts our application and CMS servers so users don't have access directly to them. At home, it runs on the firewall to serve pages to the Internet. The real webserver actually sits on a box behind the firewall for security.
- [Subversion](http://subversion.tigris.org/ "Subversion -- Home Page") \-- This is a version control system. Subverions lets you create repositories, check out the contents, edit them, and check them back in. This is good for keeping track of configuration files or scripts you write. We use it at work to track configuration files for Apache, NTP, yum, etc. At home, I use it to keep track of my scripts and [Perl module](http://ntci.sourceforge.net "NTCI -- Sourceforge Page").
- [Rancid](http://www.shrubbery.net/rancid/ "Shrubbery Networks -- Rancid") \-- This is configuration management for Cisco (and other network) devices. It gets configs from devices and checks them for changes. It's got built-in alerting and is easy to set up. I use it at home to keep track of the configs on the switches and access points.
- [nfsen](http://nfsen.sourceforge.net/ "nfsen -- Sourceforge Page")/[nfdump](http://nfdump.sourceforge.net/ "nfdump -- Sourceforge Page") \-- These are \[tag\]netflow\[/tag\] tools. Nfdump is a suite for collecting the data, while nfsen is for displaying the information. Check out netflow if you've never worked with it...it's pretty cool.
- [Dyanmips](http://www.ipflow.utc.fr/index.php/Cisco_7200_Simulator "Dynamips -- Home Page")/[dynagen](http://www.dynagen.org/ "Dynagen -- Home Page") \-- These apps let you run virtual Cisco routers on a machine. You can set up full network deployments for testing and configuration experimentation. It takes a good bit of resources, but it's well worth it for the functionality. I use it all the time at work to test or tweak configs. I also use it to simulate certification labs.
