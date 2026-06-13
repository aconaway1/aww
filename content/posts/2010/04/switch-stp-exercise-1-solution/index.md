---
title: "SWITCH – STP Exercise #1 Solution"
date: 2010-04-24
categories: 
  - "ccnp"
  - "switch"
tags: 
  - "642-813"
  - "bridge"
  - "designated"
  - "election"
  - "port"
  - "root"
  - "spanning-tree"
  - "stp"
---

Did you guys have any trouble with the solution to the STP exercise?  Let's work through it and see what happens.  I got a few responses to the solution, and everyone seems to get the same answer, so I assume we're all right.

Before we get started, I wanted to mention the tie breakers since there can be ties in STP.  If there is a tie in any calculation, the same tie breakers are used, so I'll list them here to use as we move through the calculations.

1. Lowest root bridge ID (which I hope we'll never have to use)
2. Lowest root path cost
3. Lowest bridge ID
4. Lowest port ID

In our exercise, we only have to go to the tie breakers once, but, in the wild, you may run into more.  I would guess that this is a requirement to know for the SWITCH test.

Before we get started, you can assume that a bridge is a switch since, technically, a switch is a many-ported bridge.  Let's get started.  There are only 5 steps to take, so it shouldn't take long.

**1\.  Find the path costs of each segment**

This is done with a table lookup.  Each segment (link between switches) gets a cost based on the technology that it is using.  For example, GigabitEthernet has a cost of 4 while FastEthernet has a cost of 19.  There are other values, but these are the only ones that are used in the network here.  I did find a [good table on STP path costs at HowStuffWorks.com](http://computer.howstuffworks.com/lan-switch14.htm) if you want to take a look at more of them.  Let's put the _path costs_ on the diagram.

[![](images/STP-Exercise-1-Solution-Path-Costs-300x266.svg "STP Exercise #1 Solution - Path Costs")](http://aconaway.com/wp-content/uploads/2010/04/STP-Exercise-1-Solution-Path-Costs.png)

**2\.  Find the root bridge**

After we find the path costs, we find the root bridge.  The root bridge is the switch that has the lowest bridge ID, which is the switch priority and the MAC address concatenated together.  Since all the bridges have the same priority, we can just look at the MACs to figure out which one has the lowest bridge ID.  Switch E has the lowest MAC (0000.0000.0003), so that's the _root bridge_.

[![](images/STP-Exercise-1-Solution-Root-Bridge-300x274.svg "STP Exercise #1 Solution - Root Bridge")](http://aconaway.com/wp-content/uploads/2010/04/STP-Exercise-1-Solution-Root-Bridge.png)

**3\.  Find the root ports of each bridge**

The root port is a port on a switch that has the lowest total path cost back to the root bridge; every switch has a one and only one root port except for the root bridge itself which has none (it's the root; it already knows where it is).  The root bridge sends out BPDUs that are passed to each switch.  When a switch receives it (not sends it), it increments the root path cost field and passes that along,  The port on which the lowest root path costs is received is the root port, and the value in the root path cost field is the root path cost for that switch.  The finger trace method works for us humans for now; there's not that many switches involved.  Let's add the _root ports_ along with the _root path costs_ to the diagram.

[![](images/STP-Exercise-1-Solution-Root-Path-Costs-300x275.svg "STP Exercise #1 Solution - Root Path Costs")](http://aconaway.com/wp-content/uploads/2010/04/STP-Exercise-1-Solution-Root-Path-Costs.png)

**4\.  Find the designated ports**

Each segment of the network has a designated port.  This is a port on the bridge with the lowest root path cost.  Since the root bridge always has the shortest path to itself, all of its ports are designated ports.  Root ports can't be designated ports, too, so it's pretty easy to find the designated ports on segments with root ports.  There are two segments (A-B and B-C) that we need to calculate, though.  Let's go to the tie breakers!

All the bridges have the same root bridge ID, so that's a wash.  C has a lower root path cost than B, so it wins that segment.  A and B both have the same root path cost, though, so we move down the list to bridge ID.  Since A has a lower bridge ID, it wins that segment.

Let's put all the _designated ports_ on the diagram so you can see them.

[![](images/STP-Exercise-1-Solution-Designated-Ports-300x275.svg "STP Exercise #1 Solution - Designated Ports")](http://aconaway.com/wp-content/uploads/2010/04/STP-Exercise-1-Solution-Designated-Ports.png)

**5\.  Find the blocked ports**

There aren't many ports left, are there?  There are three actually, and all those go into a blocked state.  To get rid of the loops, any port that's not a root port or a designated port is blocked.  Here's the network with the _blocked ports_ marked.

[![](images/STP-Exercise-1-Solution-Blocked-Ports-300x275.svg "STP Exercise #1 Solution - Blocked Ports")](http://aconaway.com/wp-content/uploads/2010/04/STP-Exercise-1-Solution-Blocked-Ports.png)

And we're done.  That wasn't so hard.

Send me any summer home rentals questions.

Audio commentary:

![Audio: STP Exercise 1 Solution](images/audio-unavailable.svg)
