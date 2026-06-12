---
title: "Home-grown IOU Scripts"
date: 2011-05-16
categories: 
  - "cisco"
tags: 
  - "bash"
  - "cisco"
  - "emulator"
  - "iou"
  - "lab"
  - "script"
---

I'm sure you've all heard of Cisco IOU by now, and I'm finally catching up with the other bloggers of the world by mentioning it.  It's an executable version of an IOS image that runs on a Unix (or Unix-like) platform and it's the backend behind [Cisco's Learning Labs](https://learningnetworkstore.cisco.com/market/prod/listSubCatLearnLab.se.work?TRGT=85&/nxt/rcrs/=2559&utm_source=go-shortcut&utm_medium=mixed&utm_content=go-url&utm_campaign=promo-cll).  Instead of running an emulator and loading up various images, you just run the executable and you're on the console of a Cisco router.  It has layer 2 support, so you can fire up switches as well.  Being a binary makes it way more efficient than GNS3 will ever be, and the layer 2 support is a wonderful, wonderful feature to have.

Being lazy and hating doing things over and over and over again, I wrote a few bash scripts to help set up any labs you may want to run.  First and foremost, I am not a bash programmer; I mostly stole and chiseled some old code I had from back in the day to get these working.  The end result, though, is pretty good for what I'm trying to do.  You can define a lab setup with various files and start up all the routers (or switches) along with terminal server-like access to the consoles through a process wrapper.

**genRouter/genSwitch**

At the heart of the system is a script I call genRouter (or genSwitch if using layer 2).  Yes, I learned to program in Java when it was cool, and that's why I name all my stuff like object methods.  Anyway, genRouter takes 4 command line parameters and, along with the variables that are set in the script, fires up a new router.  A common deployment is to use a wrapper application to bind the router process to a TCP port to use as a console, and that functionality is included in the script.  Here are the variables included.  You'll need to change a bunch of these for the script to work.

$IOUFILES - This is a variable that tells where the IOU files are kept. $L3IOU - This is variable that sets what IOU executable you want to use for a router. $WRAPPEREXE - This is the full path to the process wrapper you want to use and is defined in the script. $ETHCOUNT - This is the number of Ethernet interfaces you want to bring up on the router.  This is taken from the command line as $1. $SERCOUNT - This is the number of serial interfaces you want to bring up on the router.  This is taken from the command line as $2. $MEMCOUNT - This is the amount of memory you want to give the router.  This is taken from the command line as $3.  The script takes this variable, but it doesn't do anything with it due to memory declaration problems with the IOU image.  The default is 128M.  This is something to work on later. $INSTID - This is the IOU instance ID.  This is taken as $4 from the command line and is used to differentiate the different routers you have running simultaneously.

genSwitch does the same thing except that it uses $L2IOU to point to the layer-2 image instead of the layer-3 image.  There is a better way to do this, but I haven't given it enough thought (or asked Google) yet.

When either script is called, it generates a TCP port number by appending the instance ID to the number 10.  If you fire up instance 842, then the TCP port to which to telnet for access to this router is 10842.  This is good for 3-digit IDs, but there will be service clashes if you use instance ID 9 or something.  That also sounds like something that needs to be fixed.

Each script also takes the process ID of the router and stores it in a file for use later.  The file is actually ./pid/$INSTID.pid, and you'll have to create that directory.  This is a common technique to watch processes and will be used later.

**startProject**

The "human interface" script - the one your run from the command line - is called startProject.  This script looks in the current directory for a file called routers.db, and, using the info in there, fires off all the routers for you through genRouter.  In this file, you put in values to be used by genRouter to define $ETHCOUNT, $SERCOUNT, $MEMCOUNT, and $INSTID.  Each line is a new router, so you can use this file to start up a whole mess of them at once.

Something like this.

1 0 128 100 1 0 128 101 0 1 128 102

IOU uses a netmap file to define how router interfaces are connected to each other.  Using the same netmap file for all your labs isn't very flexible, so I just set the NETIO\_NETMAP environment variable to "./project.netmap" to run different labs from different directories.  Make sure that your routers.db and netmap files both reference the correct instance ID for each router; you'll obviously get unexpected results if they don't.

startProject doesn't take any arguments; everything it needs is in your routers.db or your netmap file.  Just run startProject from wherever you put those files.

I have a "labs" directory in my $HOME, and, under that, I created directories for each thing I'm studying.  If I'm doing an OSPFv3 lab, I create a directory called "ospfv3" or something and put my routers.db file, my netmap file, and my pid directory there.  I then fire off startProject from there to get the whole thing going.

startProject is terribly written.  It doesn't check any values at all, and just send what it reads directly to genRouter without asking any questions.  A real sysadmin would laugh hysterically at this script (as I do).  Perhaps someone can fix it up.  :)

**killProject**

I bet you know what this does.  It goes into ./pid/ and kills off all the processes that are stored there.  Nothing fancy at all.

**The Files**

Here are the scripts themselves.  Just put them somewhere in your path and change the variables as in genRouter and genSwitch as needed.  You may have to do a "Save As..." or just copy-and-paste the text into files yourself.

[genRouter](http://aconaway.com/static/genRouter) [genSwitch](http://aconaway.com/static/genSwitch) [startProject](http://aconaway.com/static/startProject) [killProject](http://aconaway.com/static/killProject) [All of them in a tar.gz](http://aconaway.com/static/allIOUfiles.tar.gz)

I don't want any trouble from anyone, so I don't have a copy of IOU to give away.  Please use your own set of sleuthing skills to find a copy on the dark parts of the InterWebs.  :)

Send any bbq recipes questions to me.
