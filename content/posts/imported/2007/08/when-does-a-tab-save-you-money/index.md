---
title: "When Does a Tab Save You Money?"
date: 2007-08-31
tags: 
  - "bandwidth"
  - "efficiency"
---

I was talking to some guys at work today about scalability and data efficiency, and an example came up that I had to think about for a second. One of the guys, a lead developer, started talking about the difference between 5 spaces and a tab. He said that the programming standard says that everyone should use spaces to standardize formatting of source code, but, if we want to conserve some bandwidth, we should look at using a tab instead. That sounds boring, doesn't it? Well, it is until you do the math.

I downloaded the index page for one of our largest customers, which turned out to be 48kB. I opened it up in a text editor and replaced the spaces at the beginning of lines to tabs and checked out the size again -- 44.1kB. The difference is 3.9kB...big deal. That's not a very big savings at all, is it? Is it worth changing a programming standard for 3.9kB? It is if you finish up the math.

This website happens to be pretty big. It gets about 60 million page impressions every month, so, if we say that we'll average a 3.9kB savings for each page, that translates to about 234GB of data per month. That's 2.8TB of data per year. Since we pay for bandwidth based on \[tag\]usage\[/tag\], this could possibly save us several thousands of dollars per month. I don't know the exact dollar amount, but it'll be significant.

The moral of the story is that all data transfers should be as efficient as possible. Something that is seemingly insignificant can translate into a behemoth when you think about the scope properly.
