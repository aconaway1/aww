---
title: "Adventures in Upgrading Netbox"
date: 2022-11-07
categories: 
  - "netbox"
  - "python"
  - "sot"
tags: 
  - "netbox"
  - "python"
  - "sot"
  - "source-of-truth"
  - "upgrade"
---

I've been using [Netbox](https://github.com/netbox-community/netbox) for a while now, and, frankly, I can't live without it. If you've never heard of it, it's a Source of Truth for your network automation tasks started by [Jeremy Stretch](https://github.com/jeremystretch). I use it to document my networks (hardware inventory, subnets, physical connections, etc.), which provides my automation tasks a place to pull and push all sorts of information like management IPs, rack locations, power connections, network drops...the list goes on. In better words, your automation tools can ask Netbox what the state of your network is, and send it an update if that tool discovers something different. There are plenty of better places to discuss the benefits of a Souce of Truth, so just do the Googles for it.

My production instance is running Netbox `2.7.6`, which is very old. The [latest version of Netbox as of today is `3.3.7`](https://github.com/netbox-community/netbox/releases/tag/v3.3.7), so that should tell you how far behind we are. I've had mine running for over two years, and, in the meantime, the world has moved forward. If I update the server it's running on ([Ubuntu 20.04](https://releases.ubuntu.com/focal/)), then Netbox breaks. Yes, it's so far behind the basic OS updates break it. Luckily, snapshotting of VMs exist in the world, so I could revert back at will, but not being able to update a server is not exactly ideal.

I need to update the dang VM, both OS and app, but I also need to move the server to another data center. The data center where it lives now is slated to close in a couple months, so this looks like a great opportunity to get an updated server running Netbox `3.3.7` in the new data center and just import the data, right? "Should be easy," says the engineer writing a 9928589924-word blog post on the matter.

If you look at [the upgrade path for Netbox](https://github.com/netbox-community/netbox/blob/develop/docs/installation/upgrading.md#upgrading-to-a-new-netbox-release), you see that we have to get to version `2.11` in order to move into version `3.x`. Since I've always just ran greenfield versions of Netbox in the wild, I really had no idea how to do that and had to actually read the docs. It was a rare occasion for me, for sure. One of the cool things I found in the Netbox Github repo, though, is that all versions can be made available when you clone it. If you read the docs like I forced myself to do, you can see in the [section on Git cloning](https://github.com/netbox-community/netbox/blob/develop/docs/installation/upgrading.md#option-b-clone-the-git-repository) that you can checkout specific tags that correspond to the different available versions. If you want version 2.11.12 like I did, you would just do this.

`git clone https://github.com/netbox-community/netbox.git`  
`git checkout v2.11.12`

My novice-git-user self didn't even know tags could be used like this.

It's worth noting that the directions in the installation section (but not in the upgrading section) specify a a depth of 1 when you clone the repo. This means you only get the last commit to the repo, so, effectively, only the last effort will be available. When you run the `clone` command, leave out the `--depth 1` part to get all commits, including old versions you may want to install.

So we've got the whole repo downloaded and have version `2.11.12` checked out now. Good to try the upgrade? In theory, yes. I ran the upgrade command and got a nice error that said my Python version, which was `3.10`, needed to be greater than `3.6`. Hmmm...alright. After some Googling and complaining to some buddie, I discovered that there was [a bug in the version comparison](https://github.com/netbox-community/netbox/issues/7752). Bummer. Worse, though, was that the bug wasn't fixed until Netbox version `3.0.10`. Ugh.

In the meantime, I was sharing my progress online and got some feedback (from Jordan Villareal ([@systemmtuone](https://twitter.com/SystemMTUOne)) and Tiago Sousa ([@tiagoasousa\_](https://twitter.com/tiagoasousa_))) about the fact I had to bounce through version `2.11.12` before I could step into the land of `3.x`. \[Edit: I had totally forgotten to give credit to Markku Leiniö (@majornetwork, @markku@noc.social)! Thanks to him as well!\] A big thanks to those guys for helping me out there. Jordan also pointed me to one of his Github repos -- [Netbox-build-o-matic](https://github.com/jordanrvillarreal/netbox-build-o-matic). This is a set of scripts that can install a fresh version of Netbox per the official documentation. This is mostly for fresh installs when you want to lab some stuff out using the latest-and-greatest version of Netbox, but you ask it nicely to install whatever version you want. Just modify a the `netboxVERSION` variable in `step2.sh` with the same tags I talked about when cloning (`v2.11.12` in our case). This makes it pretty powerful for migrations where you have to bounce from A to B to C to get the right version. Seems to be right what I need.

I should note that all my upgrade efforts were made on a local lab VM on my laptop. I didn't want to destroy the production system while I was messing around with this stuff and I also didn't want to keep going back to the systems team to revert snapshots all day. The strategy was to get a version of Netbox running that was compatible with the production instance, import the data, do the conversion, rinse and repeat until I'm on 3.3.7. It seemed reasonable at the time, and I think it was.

So I ran Netbox-build-o-matic to try an install version `2.11.12`. The scripts ran fine (for definitions of those words), but they're not going to fix the Python version problem, are they? The scripts run the same upgrades that I did by hand, so it still thinks that Python 3.10 is older than 3.6. Time to switch my brain into developer mode. The only way around all this was to manually implement the bug fix from version 3.0.10, so I was preparing myself for hours and hours of work to get this thing running on 2.11.12! Then I saw [the actual change](https://github.com/netbox-community/netbox/pull/7815/commits/773fd47ca6f7a33238d63816e4a589c2f2f5687c).

![](images/image-1024x387.svg)

Two lines of code to change in the settings.py file. I got all psyched up for noting! Story of my life?

I ran the Netbox-build-o-matic again and let it fail so that I would have an installation of Netbox with files in the right place. After I modified the settings.py file with the hardcore, in-depth bug fix, I commented out the line 21 in `step2.sh` so that the script (`tar -xzf $netboxVERSION.tar.gz -C /opt`) wouldn't overwriting my changes. It ran through with no problem, and I was now running `2.11.12`! A huge milestone reached!

The next step is to get the production data in to the lab VM. Since we run an export every night, I already had the data in a dump file ready to go. "All I need to do" is get it into the database.Since I'm just replicating the data to another instance, I used the directions in the [Replicating Netbox](https://docs.netbox.dev/en/stable/administration/replicating-netbox/) document. Following that top-to-bottom worked fine. My only note here is that the `psql` commands to load the database don't say that those commands need to be run as the `postgres` user (or some other user with ownership of the database). The docs on [this page](https://docs.netbox.dev/en/stable/installation/1-postgresql/) include that...just not the migration page. No biggie for most people, but keep an eye out for that.

Where are we at, then? I've got a VM running on my laptop that has Netbox `2.11.12` running with my production data in it. Everything is kosher, so it's time to move on to the next step, which is an upgrade into the land of `3.x`. Instead of stepping through a couple `3.x` versions, I decided to just try to go straight to `3.3.7`. I went the easy route here and used the Netbox-build-o-matic again, letting it roll through a fresh install. It worked. Like with no issues at all. As if this is exactly what this tool was designed to do. LOL

After Netbox-build-o-matic ran upgrade.sh, I restarted the Netbox processes and enjoyed the goodness of an fully armed and operational battlest...I mean, an updated Souce of Truth that I can use to get my new production instance going. In the meantime, data is changing on the production instance, so I'll need to work through another lab VM upgrade to get everything to `3.3.7` (or whatever version is current at the time of migration). It should be a piece of cake now that I have a process that could almost be considered as documented thanks to my ramblings here.

For those interested, the Netbox documentation can be found [here](https://docs.netbox.dev/en/stable/). I'm not a Netbox expert at all, but I'll be happy to help with any questions you may have. I may just punt you over to Jordan, though, since that's actually his job. Ha.

Send any ~~episodes of Andor~~ questions my way.
