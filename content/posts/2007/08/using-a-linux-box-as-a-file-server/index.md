---
title: "Using a Linux Box as a File Server"
date: 2007-08-30
tags: 
  - "linux"
---

Ever heard of [Samba](http://us3.samba.org/samba/ "Samba -- Official Site")? You should.

Samba is an open-source project "that provides seamless file and print services to SMB/CIFS clients." That's from the project's website, but what the hell does it mean? In a nutshell, it's an open-source application that lets non-Windows machines share files and printers with Windows machines. In most cases, people use Samba to share files on a Linux box in a really simple setup. I've read about several cases, though, where superhuman admins have used Samba machines to set up a [Windows domain](http://www.samba.netfirms.com/PDC.htm "Using Samba as a Domain Controller"). I'm talking full-scale domain login, [domain machine registration](http://www.samba.org/samba/docs/man/Samba-HOWTO-Collection/domain-member.html "Samba -- Machine Trust Accounts"), and everything. I tried that once and all my Windows machines stopped working. It sucked.

I'm going to be lazy again and not tell you how to configure it. Or am I smart and efficient and saving it for another article? Either way, I wanted to talk about what Samba can get you. Samba lets you provide a way to store files on a network share. If you set up a share for everyone to use, everyone can edit an address spreadsheet or view a home inventory sheet. You can set up Samba to share your home directories to every machine on the network. Everything's on the same machine. Think about that for a second. If everyone's files are on the same box, it's easy as pie to back everything up, and, since everything's on the network, you can have access to your stuff from everywhere.

I use Samba for file sharing. Home directories for me and the wife are shared out to local drives on our laptops. If I log in to hers and I see my home drive. I also took some time and put in a [Highpoint Tech](http://www.highpoint-tech.com/ "Highpoint Tech -- Official Page") 1740 with [a set of 4 300G SATA drives attached to it](http://flickr.com/photos/aconaway/614715051/ "Flickr -- My File Server Guts") (hot-swappable and full fault-tolerant, by the way). These guys are put into a RAID5 array to provide about 900G of space to the operating system. I used Logical Volume Manager to split this beast up into a bunch of different volumes -- images, music, videos, and a share. Samba shares all these guys out, and our machines have each of these mapped to a drive. When the wife has new images to share, she just drops them on the images drive. When I finish a new video, I drop them in the videos drive.

Samba works well and is easy to set up. Give it a try. It doesn't really scale very well, though, so using it in an enterprise may cause problems. There's a whole new article brewing in there about file locking and sharing, but I won't go there yet.

\----

Remember that I mentioned backing stuff up if everything's on the same box? It's not related to Samba technically, but I wrote a quick and dirty bash script that takes a list of directories and tars them all up to a 400G external drive I have attached to the file server. If I didn't implement Samba, I'd really have no easy way to back it all up in one fell swoop.
