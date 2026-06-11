---
title: "Syncing IOS Versions on a 3750 Stack"
date: 2010-08-16
categories: 
  - "cisco"
tags: 
  - "3750"
  - "cisco"
  - "image"
  - "ios"
  - "stack"
  - "stackwise"
  - "sync"
---

For those that don't know, when I say "stack", I mean a group of 3750s connected together using the [StackWise technology](http://www.cisco.com/en/US/prod/collateral/switches/ps5718/ps5023/prod_white_paper09186a00801b096a.html).  When you use a very expensive and very proprietary cable, your individual switches are combined into a single logical device.  This means you configure one device to control potentially many switches.

To the point.  I've spent the last few weeks replacing a mess of 3750s in stacks.  These guys are very easy to replace, but the big problem I find is getting the IOS version in sync.  When the RMA comes, it's inevitably got a different version on it, and you'll see something like this.

> ```
> Switch#  Role      Mac Address     Priority     State
> --------------------------------------------------------
>  1       Member    0023.33ad.a500     1         Version Mismatch
> *2       Master    0023.5eac.e900     15        Ready
> ```

In this case, switch 2, running 12.2(25)SEE3 is the master, and switch 1, running 12.2(35)SEB, is the new member.

A switch in the stack needs to have the same version of IOS as the master to be brought into the fold, so you've got to do get the code on the switch somehow.  You can use traditional methods to get the right code on the box (like TFTPing one up), but there are easier ways.

If the code versions are close (I'm not sure as to the rules about how close), then you'll be able to check out the flash of all the switches in the stack through the master.  If you do a _dir ?_, you'll see _flash1:_, _flash2:_, etc.  Those are the individual flash drives for each switch in the stack.  The numbering is based on the stack number, so all you have to do is copy the IOS image over from the master.  Well, maybe.

I found that a lot (maybe all) of my 3750s actually have a directory structure to include the image and HTML files for the device manager.  You don't need the HTML files for the switch to function in the stack, but having the IOS image in different places will force you to change the boot variable for each switch.  I like consistency, so I put everything in the same place when I can, so that means creating the directory by hand.

That method works fine, but there's an easier way using the _archive_ command.  You can tell your switch to tar, copy, and extract all of the files from one switch to the others by giving it one of these.

> ```
> archive copy-sw [ /destination-system X ] Y
> ```

I used this command to copy the image from switch 2 (the master) to switch 1 (the replaced member) and got a whole bunch of output.  The whole process took about 3 minutes.

> ```
> Switch#archive copy-sw /destination-system 1 2
> System software to be uploaded:
> System Type:             0x00000000
> archiving c3750-ipbasek9-mz.122-25.SEE3 (directory)
> archiving c3750-ipbasek9-mz.122-25.SEE3/c3750-ipbasek9-mz.122-25.SEE3.bin (7206732 bytes)
> archiving c3750-ipbasek9-mz.122-25.SEE3/html (directory)
> archiving c3750-ipbasek9-mz.122-25.SEE3/html/toolbar.js (7084 bytes)
> archiving c3750-ipbasek9-mz.122-25.SEE3/html/title.js (577 bytes)
> ...
> extracting c3750-ipbasek9-mz.122-25.SEE3/html/images/141280.gif (3053 bytes)
> extracting c3750-ipbasek9-mz.122-25.SEE3/html/images/meter_yellow.gif (59 bytes)
> extracting c3750-ipbasek9-mz.122-25.SEE3/html/images/legend_off.gif (1158 bytes)
> extracting c3750-ipbasek9-mz.122-25.SEE3/info (682 bytes)
> extracting info (108 bytes)
> 
> Installing (renaming): `flash1:update/c3750-ipbasek9-mz.122-25.SEE3' ->
>                                        `flash1:c3750-ipbasek9-mz.122-25.SEE3'
> New software image installed in flash1:c3750-ipbasek9-mz.122-25.SEE3
> 
> All software images installed.
> ```

I left out a large chunk, but you get the idea.  If you'll notice, all the HTML files are copied along with the IOS image, so you get exactly the same structure on all your switches.  It beats doing it all manually.

Send any RMA packages questions my way.
