---
title: "Separation of Function"
date: 2007-08-15
tags: 
  - "security"
---

Separation of function is another important security concept that people often overlook.  It can mean that a single person is only responsible for one part of a process.  Or it can mean that one server only does one function.  Or it can mean that one network is used for servers of one type.  Or it can mean that a whole data center is for only one production and not development.  It depends on your scope and your point of view.

What does it give you?  Think about it for a second.

- If a server is a web server and not a database server, then you don't have to open up any type of access from the Internet to the database server.  If you had them both on one box and the webserver was owned, guess what?  The database server is owned, too.
- If you have a network segment that's only for web servers and one of them gets owned, then they only have access to other web servers.  If you had web servers and database servers on the same network, a compromise of a web server has direct access to the database server.
- If a developer can only write code and someone else deploys it or tests it, then he can't deploy code without telling anyone or deploy code that emails credit card numbers to himself.
- If a data center blows up but contains only development servers, you don't lose any production time since all those boxes are on the other side of town.

You can see there are some advantages, but everything has a downside.  In this case, you have to pay for the extra whatever -- be it a new server, new firewall, new data center, new tester.  The advantages, though, can often outweigh the costs since you have a while mess more controls in in place.

NOTE:  Most or all compliance programs like [PCI](http://en.wikipedia.org/wiki/Payment_card_industry "Wikipedia Article") or [SarBox](http://en.wikipedia.org/wiki/Sarbox "Wikipedia Article") can REQUIRE separation of function on a lot of areas like server function separation, and developer/tester/deployer separation.
