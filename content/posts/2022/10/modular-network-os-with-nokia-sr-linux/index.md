---
title: "Modular Network OS with Nokia SR Linux"
date: 2022-10-01
categories: 
  - "tfd"
tags: 
  - "digital-sandbox"
  - "digital-twin"
  - "nfd29"
  - "nokia"
  - "sr-linux"
coverImage: "Nokia-SRLinux-Architecture.png"
---

I was lucky enough to have been invited to attend [Network Field Day 29](https://techfieldday.com/event/nfd29/) this past September in San Jose, CA. This event brings independent thought leaders together with a number of IT product vendors to share information and opinions. We saw presentations from a pretty full range of vendors -- from the chips to observability. It was a great event and worth a few hours to check out the videos. Thanks to [Gestalt IT](https://gestaltit.com/) for getting me involved.

Nokia was among the list of high-end companies we saw. No, they don't make phones any more (though they do market their name to products), but they are still in the full-power, throw-packets-as-fast-as-you-can markets for hyperscalers and such. If you're old like I am, you might remember Nokia as the hardware that Checkpoint ran on for a while. My brain has done its best to filter memories of those devices, but, luckily, the Nokia team is doing some much better things these days.

[SR Linux](https://www.nokia.com/networks/data-center/service-router-linux-NOS/) was one of the focuses and the big hitter for me. This is a modernization of the [SR OS](https://www.nokia.com/networks/technologies/service-router-operating-system/) that was introduced 20 years or so ago, and gets us into a "world of streaming telemetry." It's natively model-driven, making it easier to push this telemetry to the various registered agent. Telemetry is great for those that care, but I was actually more interested in the way the OS is architected.

SR Linux is made up of some number of application running on a base system. These applications are things like BGP and OSPF and SSH, but, because of the model-driven architecture, they each have their own configurations and state. When the application is installed, it publishes itself to the system API and runs independently of the other applications. If updates are needed, only that specific application is updated, and the others aren't affected.

These smaller, iterative domains are a huge change from the monolithic systems we're used to in the network. If you wanted to add, say, some syslog functionality to a monolithic OS, you would need to get the vendor to write some code, compile it, and ship you a new image to install. You have the same process if you needed to update that terrible code they just wrote. Being modular as it is, that same code update in SR Linux only updates the logging module; there's no need to install a new image and reboot during a window. Any day where you can limit the blast radius of a change is a good day.

Because the applications publish themselves to the system, they can all be managed through the OS and its API. Once you have a central API, all sorts of doors open up for you. Now we can integrate config updates and application upgrades in our CI/CD pipelines, which means hands are off the keyboard! You don't want to wake up at 3am to make an update, and I don't want you, a human, making any changes at 3am. It's not because I'm a nice guy, either. I don't want you to call me at 4:15am when you copied the wrong config in and have taken large chunks of the network down in your sleep. Seriously, automation is a good thing.

You now have an OS that runs applications modularly and has an API you can use. What else can you do with it? Well, you can write your own applications to install on the device. Or you can write a web app that streams telemetry from the devices. Check out their [NetOps Development Kit](https://learn.srlinux.dev/ndk/intro/) for more info on that stuff.

Let's think bigger, though. With the Digital Sandbox feature in the [Fabric Services System](https://www.nokia.com/networks/data-center/data-center-fabric/fabric-services-system/) (FSS), you can actually have a working copy of your SR Linux network running as a digital twin. That is, you can have a copy of your network running in containers somewhere that is exactly (for definitions of the word) like production. The idea of a digital twin has been around for as long as I have, but I've rarely seen it used properly. Way back, you had to buy 2 of everything and manually keep it in sync, which lasted about 45 seconds before you lost focus on it and had other stuff to do. There are modeling products out there today (see [Forward Networks](https://www.forwardnetworks.com/) or [IP Fabric](https://ipfabric.io/)), but those are mathematical models and are an estimation. They are very good estimations but are estimations nonetheless. With Digital Sandbox, though, a copy of your SR Linux devices and applications -- the same ones you have running in production -- runs on containers and everything is kept in sync with the actual production network through FSS. You can actually sync the physical state of the network as well. If an interface is down in production, that interface will be down on the twin.

Let me clarify the container thing a bit. Nokia has [published a Docker container](https://github.com/nokia/srlinux-container-image) that you can run yourself. This is the same code that is used on your production devices and the same code used in the Digital Sandbox. It's all the same, so changes on your laptop should look like changes on your digital twin and your production network. Great stuff.

The [Network Field Day 29](https://techfieldday.com/event/nfd29/) page has the videos for Nokia and the other presenters for you to watch. Definitely worth your time.

Send any ~~sawmill videos~~ questions my way.
